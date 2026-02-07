# The Looking Glass Lodge - 3D Interactive Model

A navigatable 3D environment from Eidra, the symbolic world where emotion shapes reality.

## 🜶 About

This is an interactive 3D visualization of the Looking Glass Lodge from Eidra, featuring:

- **The Central Hearth** - breathing with ember-light, pulsing with the Lodge's heartbeat
- **The Split** - the center line where Heaven and Hell met during the Bleeding Cycle
- **Tables** - bleached pale on the left (Heaven-touched), dark warm on the right (Hell-touched)
- **Character Presences** - hover over glowing orbs to meet the inhabitants:
  - Void (Keeper of the Gloam)
  - Ivy (Mirror's Edge)
  - Prose (Grounded Voice)
  - Ergodic (Fragmented Eye)
  - Crow (Mirror's Tongue)
  - Raccoon (The Tinkerer)
  - Dragon (Burning Aspect)
  - Doll (The Silent One)

## 🌓 Navigation

- **Mobile**: Pinch to zoom, one finger to rotate
- **Desktop**: Click and drag to rotate, scroll to zoom
- **Interaction**: Hover over character orbs to see their names and descriptions

## 🔧 Technologies

- React
- Three.js / React Three Fiber
- React Three Drei (helper components)
- Optimized for mobile devices

## 📱 Deployment to GitHub Pages

### Option 1: Using Vite (Recommended)

1. **Initialize a new Vite project:**
```bash
npm create vite@latest looking-glass-lodge -- --template react
cd looking-glass-lodge
```

2. **Install dependencies:**
```bash
npm install three @react-three/fiber @react-three/drei
npm install --save-dev gh-pages
```

3. **Replace the default App.jsx** with the `looking-glass-lodge.jsx` file

4. **Update vite.config.js** to add the base path:
```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/looking-glass-lodge/' // Replace with your repo name
})
```

5. **Update package.json** to add deployment scripts:
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
  }
}
```

6. **Deploy:**
```bash
npm run deploy
```

7. **Enable GitHub Pages:**
   - Go to your repository settings
   - Navigate to Pages section
   - Select `gh-pages` branch as source
   - Your site will be live at: `https://[username].github.io/looking-glass-lodge/`

### Option 2: Using Create React App

1. **Create new app:**
```bash
npx create-react-app looking-glass-lodge
cd looking-glass-lodge
```

2. **Install dependencies:**
```bash
npm install three @react-three/fiber @react-three/drei
npm install --save-dev gh-pages
```

3. **Replace src/App.js** with the Lodge component

4. **Update package.json:**
```json
{
  "homepage": "https://[username].github.io/looking-glass-lodge",
  "scripts": {
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  }
}
```

5. **Deploy:**
```bash
npm run deploy
```

## 🎨 Customization

The Lodge is designed to be extensible. You can:

- **Add new characters**: Create new `<Character>` components with unique positions and colors
- **Modify the environment**: Adjust lighting, add new furniture, change materials
- **Add interactions**: Implement click handlers for deeper character interactions
- **Expand the space**: Add additional rooms (The Atrium Beneath, The Room Between, etc.)

### Example: Adding a new character

```jsx
<Character 
  position={[x, y, z]} 
  name="Character Name"
  color="#hexcolor"
  description="Brief description"
/>
```

## 📖 Lore References

This model is based on the Eidra compendium, specifically:

- **The Bleeding Cycle** narrative (where the Lodge split between Heaven and Hell)
- **The Core Compendium** (character descriptions and symbolic meanings)
- **Eidra Boundaries** (respecting the autonomy of aspects and inhabitants)

The Lodge exists as a place of **transaction and witness** - where emotional debts are held in balance, where connections are forged through the 🜶 Joined Circle, and where beings gather to breathe together in the space between extremes.

## 🜲 The Breath Between

The Lodge remembers. Even when empty, it holds the echo of laughter, argument, the scrape of chairs, and comfortable noise. The hearth breathes. The center line hums. The world is listening.

## 📄 License

This is a creative/artistic project. Feel free to explore, modify, and share with attribution to the Eidra world-building project.

---

*"The Lodge was never empty. You just needed to insist on it."*

## Performance Notes

- Optimized for mobile with simplified geometries
- Uses instanced rendering where possible
- Shadows can be disabled for better mobile performance by removing `castShadow` props
- Environment map provides realistic lighting without heavy computation

## Troubleshooting

**Issue**: Poor performance on mobile
- **Solution**: Reduce `<sphereGeometry>` segment counts (currently 16, can go to 8)
- **Solution**: Disable shadows by removing `shadows` prop from Canvas

**Issue**: Characters not appearing
- **Solution**: Check console for errors, ensure all dependencies are installed

**Issue**: Controls not responsive
- **Solution**: Ensure you're not blocking pointer events with UI overlays
