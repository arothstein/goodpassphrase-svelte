# GoodPassphrase SvelteKit Migration Plan

This plan details how we will re-implement the Quasar application `goodpassphrase` using SvelteKit 2 and Bits UI.

## Goal Description
The objective is to perfectly replicate the functionality and look-and-feel of the original Vue 2/Quasar site (https://github.com/arothstein/goodpassphrase) using a modern SvelteKit stack. This involves replicating Passphrase generation logic (Diceware method), the layout (Header & Drawer), and static information pages (About & Privacy).

## User Review Required
> [!NOTE]
> Bits-UI is a completely headless library, meaning it provides accessibility and functionality, but zero styling by default. We will use **Tailwind CSS** to style these components to match the original Quasar material-like aesthetic. 
> Please confirm if adding Tailwind CSS to the stack is acceptable since it's the standard companion to headless UI libraries in Svelte.

## Proposed Changes

### 1. Dependencies and Setup
- **Install Tailwind CSS**: `npm install -D tailwindcss postcss autoprefixer` and setup configs.
- **Install Bits UI & Icons**: `npm install bits-ui lucide-svelte`

---
### 2. Assets & Utilities
#### [NEW] `src/lib/words.js`
- Migrate the [public/words.js](file:///C:/tmp/goodpassphrase-quasar/public/words.js) EFF dictionary from the Quasar source. Port it to ESM (`export const words = [...]`).

---
### 3. Layout & Navigation
#### [NEW] [src/routes/+layout.svelte](file:///c:/code/goodpassphrase-svelte/src/routes/+layout.svelte)
- Re-implement [MainLayout.vue](file:///C:/tmp/goodpassphrase-quasar/src/layouts/MainLayout.vue).
- Create a header with a hamburger menu (Lucide `Menu` icon), Site Title, and GitHub link.
- Implement a responsive Left Drawer for navigation.

#### [NEW] `src/lib/components/LeftDrawer.svelte`
- Re-implement navigation links pointing to `/`, `/about`, and `/privacy`.

---
### 4. Application Pages
#### [NEW] [src/routes/+page.svelte](file:///c:/code/goodpassphrase-svelte/src/routes/+page.svelte)
- Re-implement [Index.vue](file:///C:/tmp/goodpassphrase-quasar/src/pages/Index.vue) containing the main generator introduction and embedding the generator component.

#### [NEW] `src/routes/about/+page.svelte`
- Port raw HTML and content from [About.vue](file:///C:/tmp/goodpassphrase-quasar/src/pages/About.vue).

#### [NEW] `src/routes/privacy/+page.svelte`
- Port raw HTML and content from [Privacy.vue](file:///C:/tmp/goodpassphrase-quasar/src/pages/Privacy.vue).

---
### 5. Core Component
#### [NEW] `src/lib/components/PassphraseGenerator.svelte`
- Port all data states (`ppOptions`, `ppResults`), computed properties ([disableReset](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#99-111), [avgTimeToGuess](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#111-271)), and methods ([getPassphrases](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#274-288), [generatePassphrase](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#289-307), [getWords](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#308-320), [rollWordNumber](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#321-334), [resetPpOptions](file:///C:/tmp/goodpassphrase-quasar/src/components/PassphraseGenerator.vue#335-343)) into Svelte 5 Runes standard (`$state`, `$derived`).
- **Bits UI Integration**:
  - Replace `<q-slider>` with Bits UI `<Slider.Root>` styled with Tailwind.
  - Replace `<q-checkbox>` with Bits UI `<Checkbox.Root>` and `<Checkbox.Indicator>`.
  - Replace `<q-btn>` with native buttons or Bits UI `<Button.Root>`.
- Replicate `localStorage` logic inside `onMount` or an effect.

## Verification Plan

### Automated/Build Verification
- Run `npm run check` using the Svelte-check utility.
- Build the app with `npm run build` and ensure the Cloudflare pages adapter compiles without errors.

### Manual Testing
1. **Passphrase Generator**:
   - Verify that toggling the "Word Count" slider generates the exact quantity of words specified.
   - Verify checking the "Number" / "Special character" options appends correctly to the generated passphrase.
   - Confirm "Average time to guess" text matches exactly the Quasar implementation for equivalent option sets.
2. **Layout and Router**:
   - Test the Sidebar Drawer opening/closing on smaller screens.
   - Navigate to the About and Privacy pages. Ensure layout remains persistent.
3. **Persisted State**:
   - Change options (e.g. 7 words, add special character), refresh the page, and ensure the UI state has restored from `localStorage`.
