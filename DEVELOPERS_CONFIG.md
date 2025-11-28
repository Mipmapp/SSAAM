# How to Update Developer Information

## Adding Facebook Links
Edit the `developers` array in:
- `src/views/Dashboard.vue`
- `src/views/Login.vue`
- `src/views/Register.vue`

Replace the Facebook URLs:
```javascript
const developers = [
  { name: 'Jullan Maglinte', initials: 'JM', role: 'Backend Dev', facebook: 'https://facebook.com/jullan', image: '' },
  // ... etc
]
```

## Adding Developer Images
You have two options:

### Option 1: Upload to imgbb (Recommended)
1. Go to https://imgbb.com and upload your image
2. Copy the image URL
3. Paste it in the `image` field:
```javascript
{ name: 'Jullan Maglinte', ..., image: 'https://ibb.co/xxxxx' }
```

### Option 2: Store images locally in assets
1. Create `src/assets/developers/` folder
2. Add your images (e.g., `jullan.jpg`)
3. Import and use:
```javascript
import jullanImage from '../assets/developers/jullan.jpg'
{ name: 'Jullan Maglinte', ..., image: jullanImage }
```

## Dashboard Responsiveness
The dashboard is now fully responsive:
- **Mobile**: Single column layout, sidebar appears at bottom
- **Tablet**: 2-column developer grid
- **Desktop**: Full layout with sidebar on left

The layout automatically adjusts based on screen size!
