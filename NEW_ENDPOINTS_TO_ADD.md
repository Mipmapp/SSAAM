# New Endpoints to Add to ssaam-api.vercel.app

Add these two new endpoints to your existing backend. Copy and paste them into your Vercel backend server code.

---

## 1. GET `/apis/students/stats` - Dashboard Statistics

Add this endpoint after your existing `/apis/students` GET endpoint:

```javascript
// GET STATISTICS - Separate endpoint for dashboard counters (Protected)
app.get('/apis/students/stats', studentAuth, async (req, res) => {
    try {
        const stats = {
            BSCS: { '1st year': 0, '2nd year': 0, '3rd year': 0, '4th year': 0, total: 0 },
            BSIS: { '1st year': 0, '2nd year': 0, '3rd year': 0, '4th year': 0, total: 0 },
            BSIT: { '1st year': 0, '2nd year': 0, '3rd year': 0, '4th year': 0, total: 0 }
        };

        const allStudents = await Student.find();

        allStudents.forEach(student => {
            const program = student.program;
            const yearLevel = student.year_level;
            
            if (stats[program] && stats[program][yearLevel] !== undefined) {
                stats[program][yearLevel]++;
                stats[program].total++;
            }
        });

        res.json({
            stats,
            totalStudents: allStudents.length
        });
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});
```

**Response Format:**
```json
{
  "stats": {
    "BSCS": { "1st year": 5, "2nd year": 3, "3rd year": 2, "4th year": 1, "total": 11 },
    "BSIS": { "1st year": 4, "2nd year": 2, "3rd year": 3, "4th year": 2, "total": 11 },
    "BSIT": { "1st year": 6, "2nd year": 4, "3rd year": 1, "4th year": 2, "total": 13 }
  },
  "totalStudents": 35
}
```

---

## 2. GET `/apis/students/search` - Search/Filter with Pagination

Add this endpoint after the stats endpoint:

```javascript
// SEARCH/FILTER STUDENTS - Separate endpoint for search with pagination (Protected)
app.get('/apis/students/search', studentAuth, async (req, res) => {
    try {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 10;
        const skip = (page - 1) * limit;
        const search = req.query.search || '';
        const program = req.query.program || '';
        const yearLevel = req.query.yearLevel || '';

        // Build query filter
        const filter = {};

        // Text search - search in multiple fields
        if (search.trim()) {
            filter.$or = [
                { student_id: { $regex: search, $options: 'i' } },
                { first_name: { $regex: search, $options: 'i' } },
                { last_name: { $regex: search, $options: 'i' } },
                { email: { $regex: search, $options: 'i' } },
                { rfid_code: { $regex: search, $options: 'i' } }
            ];
        }

        // Program filter
        if (program) {
            filter.program = program;
        }

        // Year level filter
        if (yearLevel) {
            filter.year_level = yearLevel;
        }

        // Execute query with pagination
        const students = await Student.find(filter)
            .skip(skip)
            .limit(limit)
            .sort({ created_date: -1 });

        const total = await Student.countDocuments(filter);
        const totalPages = Math.ceil(total / limit);

        res.json({
            data: students,
            pagination: {
                currentPage: page,
                limit,
                total,
                totalPages,
                hasNextPage: page < totalPages,
                hasPrevPage: page > 1
            }
        });
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});
```

**Request Example:**
```
GET /apis/students/search?search=juan&program=BSCS&yearLevel=1st year&page=1&limit=10
Authorization: Bearer SSAAMStudents
```

**Response Format:**
```json
{
  "data": [
    {
      "_id": "...",
      "student_id": "24-A-12345",
      "first_name": "Juan",
      "last_name": "Dela Cruz",
      "email": "juan@example.com",
      "program": "BSCS",
      "year_level": "1st year",
      "rfid_code": "ABC123",
      "photo": "https://..."
    }
  ],
  "pagination": {
    "currentPage": 1,
    "limit": 10,
    "total": 45,
    "totalPages": 5,
    "hasNextPage": true,
    "hasPrevPage": false
  }
}
```

---

## Frontend is Already Ready

Your frontend at `src/views/Dashboard.vue` is already configured to use these endpoints:
- It fetches page 1 (10 students) from `/apis/students`
- It fetches complete stats from `/apis/students/stats`
- It uses `/apis/students/search` when filtering/searching

Just add these two endpoints to your Vercel backend and deploy!
