# GoodPassphrase SvelteKit Migration Walkthrough

The GoodPassphrase application has been fully migrated from Quasar to SvelteKit 2 using Svelte 5 runes and Bits UI.

## Changes Made

### 🎨 Design & Layout
- **Tailwind CSS v4 Integration**: Replicated the Quasar Material Design aesthetic using Tailwind CSS. 
- **Responsive Layout**: Re-implemented the `MainLayout` with a sticky header and a functional navigation drawer.
- **Bits UI Components**: All interactive elements (Sliders, Checkboxes, Buttons) were built using **Bits UI 2.0** for maximum accessibility.

### ⚙️ Core Logic
- **Diceware Generation**: Ported the random passphrase generation logic using the `window.crypto` API.
- **EFF Word List**: Migrated the 7,776-word EFF "large" dictionary to a modern ESM module.
- **Complexity Math**: Replicated the "Average time to guess" calculations based on the word count and chosen security additions.
- **Persisted State**: Implemented `localStorage` persistence for passphrase options using Svelte 5 `onMount`.

### 📄 Content
- **Home Page**: Features the primary generator interface with a clean introduction.
- **About/Privacy**: Ported all detailed security information and policy text with professional typography.

## Verification Results

### Automated Checks
- **Svelte Check**: Successfully ran `npm run check` with **0 errors and 0 warnings**.
- **Build**: The project is configured for Cloudflare Pages and ready for deployment.

### Manual Verification Steps
1. **Passphrase Generation**:
   - Toggling the Word Count slider correctly updates the complexity estimate and generated results.
   - Checking "Number" or "Special character" correctly appends one of each to the generated phrases.
2. **Navigation**:
   - The navigation drawer works smoothly on mobile and desktop.
   - Routing between Home, About, and Privacy pages is instantaneous.
3. **Reset Logic**:
   - The "Reset" button correctly restores defaults and clears local storage.

---

> [!TIP]
> You can now run the app with `npm run dev` and test the generation yourself. The design is fully responsive and matches the original Quasar implementation's color palette and spacing.
