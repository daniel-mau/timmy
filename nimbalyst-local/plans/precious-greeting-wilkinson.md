# Loading Screen with Bubbles

## Context
The website currently sets the video `src` on `DOMContentLoaded` but doesn't wait for it to be ready before showing the main UI. The user wants a branded loading screen with animated bubbles that preloads videos in the background, then transitions to the main screen.

## File to Modify
`/Users/daniel2/Documents/Timmy/nimbalyst-local/mockups/timmy-alive.mockup.html`

## Approach

### 1. Loading Screen HTML
Add a `#loadingScreen` div as the first child of `<body>`, covering the full viewport. Sits above the main content (`z-index: 100`). Blue background: `#4A9FCA`.

```html
<div id="loadingScreen">
  <div class="bubbles-container">
    <!-- 10–12 bubble divs, varying sizes -->
  </div>
</div>
```

### 2. Bubble CSS Animation
- Bubbles are circles (`border-radius: 50%`) with semi-transparent white or light-blue fill and a subtle border
- Keyframe `rise`: translate from bottom of screen to above top, with slight horizontal wobble
- Each bubble gets different `animation-duration` (4–8s), `animation-delay` (0–4s), `left` position, and `size` (15px–60px)
- `animation-iteration-count: infinite`

### 3. Video Preloading
- Set `src` on the `<video>` element and also create a hidden second `<video>` or `<link rel="preload">` for the dead video
- Listen for `canplaythrough` on the alive video (primary gate)
- Once ready → fade-out loading screen → show main content

```js
// In DOMContentLoaded:
videoEl.src = './alive_vid.mov';
videoEl.addEventListener('canplaythrough', hideLoader, { once: true });

// Fallback: if takes too long (e.g. slow connection), hide after 8s
setTimeout(hideLoader, 8000);

function hideLoader() {
  const loader = document.getElementById('loadingScreen');
  loader.style.opacity = '0';
  setTimeout(() => loader.style.display = 'none', 600);
}
```

### 4. Initial State
- Main content (`#titleText`, `#checkBtn`, `#videoBackground`) starts hidden or with `visibility: hidden`
- After `hideLoader()` fires, set `visibility: visible` or fade in

### Transition
- Loading screen: `opacity: 1 → 0` over 600ms, then `display: none`
- Main content: `opacity: 0 → 1` simultaneously via CSS transition

## Verification
- Open the mockup in Nimbalyst — loading screen should appear with bubbles rising
- After video buffered (or 8s fallback), loading screen fades out and main screen appears
- Then copy to `index.html` and commit
