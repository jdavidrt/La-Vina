# Customization Warning System Documentation

## Overview

The `aviso-personaliza.liquid` snippet provides a dynamic warning message that alerts customers when they need to complete image uploads before adding a product to their cart. It works in conjunction with the validation system to provide clear, contextual feedback.

**Use Case:** Displays a helpful reminder when image uploads are required but not yet completed, improving user experience and reducing confusion about why the cart button is disabled.

---

## Features

- ✅ **Conditional display**: Only shows when image validation fails
- ✅ **Real-time updates**: Responds immediately to validation state changes
- ✅ **Targeted messaging**: Specifically checks image upload validations
- ✅ **Automatic hiding**: Disappears once all images are uploaded
- ✅ **MutationObserver integration**: Monitors page changes continuously

---

## How It Works

### Technical Architecture

The warning system operates independently but relies on the global `validador` object to determine when to show/hide the warning message.

### HTML Structure

```html
<p id="customization-warning" style="font-size: 12px; color: #800000; display: none;">
  Sube tu foto para añadir el producto al carrito
</p>
```

**Key attributes:**
- **ID**: `customization-warning` for JavaScript targeting
- **Inline style**: Hidden by default (`display: none`)
- **Color**: Dark red (`#800000`) for visual prominence
- **Message**: "Upload your photo to add the product to cart"

---

## Validation Logic

### Image Validation Check

```javascript
function updateWarning() {
  if (typeof validador !== "undefined") {
    const imgValid = Object.entries(validador)
      .filter(([key]) => key.startsWith("Img"))
      .every(([, value]) => value === true);

    warningElement.style.display = imgValid ? "none" : "block";
  } else {
    console.error("El objeto 'validador' no está definido.");
  }
}
```

**Process breakdown:**

1. **Check validador exists**
```javascript
if (typeof validador !== "undefined")
```
- Ensures validador object is loaded
- Prevents errors if snippet loads before validador.liquid

2. **Extract image validations**
```javascript
Object.entries(validador)
  .filter(([key]) => key.startsWith("Img"))
```
- Converts validador object to key-value pairs
- Filters only keys starting with "Img" (e.g., `Img1`, `Img2`)
- Ignores other validations (text fields, etc.)

3. **Check all images valid**
```javascript
.every(([, value]) => value === true)
```
- Returns `true` only if ALL image validations are `true`
- Returns `false` if any image validation is `false`

4. **Update display**
```javascript
warningElement.style.display = imgValid ? "none" : "block";
```
- Hide warning if all images valid
- Show warning if any image invalid

---

## Key Functions

### `updateWarning()`

**Purpose:** Checks image validation state and updates warning visibility

**Called:**
- On page load (initial state)
- Whenever DOM changes (via MutationObserver)
- After any validation update

**Logic:**
```javascript
imgValid === true  → Hide warning (display: none)
imgValid === false → Show warning (display: block)
```

---

## MutationObserver Integration

### Real-Time Monitoring

```javascript
const observer = new MutationObserver(() => {
  updateWarning();
});

observer.observe(document.body, {
  attributes: true,
  childList: true,
  subtree: true
});
```

**What it monitors:**
- `attributes: true` - Watches for attribute changes (e.g., style, class)
- `childList: true` - Watches for added/removed elements
- `subtree: true` - Monitors entire DOM tree, not just direct children
- Target: `document.body` - Observes the whole page

**Why it's needed:**
- Validation changes don't trigger events
- Image uploads modify DOM (add previews, change styles)
- Variant selection may add/remove image fields
- Ensures warning updates immediately

### Initialization

```javascript
document.addEventListener("DOMContentLoaded", function () {
  // Set up observer...

  // Initial check
  updateWarning();
});
```

**Flow:**
1. Wait for DOM to load
2. Create MutationObserver
3. Start observing
4. Run initial check (set correct state on page load)

---

## Validation State Examples

### Example 1: No Images Required
```javascript
validador = {
  DiezPalabras: false,
  DocePalabras: true
}
// No keys starting with "Img"
// imgValid = true (empty array passes .every())
// Warning hidden
```

### Example 2: One Image Required (Not Uploaded)
```javascript
validador = {
  Img1: false,
  DiezPalabras: true
}
// Img1 = false
// imgValid = false
// Warning shown: "Sube tu foto para añadir el producto al carrito"
```

### Example 3: One Image Required (Uploaded)
```javascript
validador = {
  Img1: true,
  DiezPalabras: true
}
// Img1 = true
// imgValid = true
// Warning hidden
```

### Example 4: Two Images Required (One Uploaded)
```javascript
validador = {
  Img1: true,
  Img2: false,
  DiezPalabras: true
}
// Img1 = true, Img2 = false
// imgValid = false (not all are true)
// Warning shown
```

### Example 5: Two Images Required (Both Uploaded)
```javascript
validador = {
  Img1: true,
  Img2: true,
  DiezPalabras: true
}
// Img1 = true, Img2 = true
// imgValid = true
// Warning hidden
```

---

## Integration with Other Components

### Works With

1. **upload-images-custom.liquid**
   - Monitors `validador.Img1`
   - Shows warning until image uploaded

2. **upload-images-custom-optional-2.liquid**
   - Monitors `validador.Img2`
   - Shows warning if "Doble Marcado" selected but not uploaded

3. **validador.liquid**
   - Depends on global validador object
   - Uses same MutationObserver pattern

### Independent From

- Text input validations (ignored via `key.startsWith("Img")`)
- Cart button state (warning is purely informational)
- Variant selection logic (responds to validation changes, not variants directly)

---

## Styling

### Current Styling

```html
style="font-size: 12px; color: #800000; display: none;"
```

**Properties:**
- **font-size: 12px** - Small, non-intrusive text
- **color: #800000** - Dark red/maroon for visibility
- **display: none** - Hidden by default

### Customization Options

#### Change Color
```html
color: #FF0000;  /* Bright red */
color: #D32F2F;  /* Material Design red */
color: #C62828;  /* Darker red */
```

#### Add Background
```css
background-color: #FFEBEE;  /* Light red background */
padding: 10px;
border-radius: 4px;
```

#### Make More Prominent
```css
font-size: 14px;
font-weight: bold;
border: 1px solid #800000;
padding: 8px 12px;
```

#### Add Icon
```html
<p id="customization-warning">
  ⚠️ Sube tu foto para añadir el producto al carrito
</p>
```

---

## Common Use Cases

### Scenario 1: Single Image Product
```javascript
// Page loads → validador.Img1 = false
// Warning shows: "Sube tu foto..."
// User uploads image → validador.Img1 = true
// Warning hides automatically
```

### Scenario 2: Optional Second Image
```javascript
// "Con Marcación" selected → Img1 = false, Img2 = true
// Warning shows (Img1 false)
// User uploads first image → Img1 = true, Img2 = true
// Warning hides

// "Doble Marcado" selected → Img1 = false, Img2 = false
// Warning shows (both false)
// User uploads first → Img1 = true, Img2 = false
// Warning still shows (Img2 still false)
// User uploads second → Img1 = true, Img2 = true
// Warning hides
```

### Scenario 3: Text + Image Product
```javascript
validador = {
  DiezPalabras: false,  // Text not filled
  Img1: false           // Image not uploaded
}
// Warning shows: "Sube tu foto..."
// User fills text → DiezPalabras = true
// Warning still shows (Img1 still false)
// User uploads image → Img1 = true
// Warning hides (only cares about images)
```

---

## Important Notes

### Only Monitors Images
- Warning specifically checks image validations (`Img` prefix)
- Does not show messages for text fields, other validations
- Focused on image upload completion

### Load Order Dependency
- Must load **after** `validador.liquid`
- Checks `typeof validador !== "undefined"` for safety
- Shows console error if validador not available

### Performance Consideration
- MutationObserver watches entire page
- Runs `updateWarning()` on every DOM change
- Lightweight function, minimal performance impact

### Message is Static
- Current implementation has one fixed message
- Could be enhanced to show specific messages:
  - "Sube la primera foto" (Upload first photo)
  - "Sube la segunda foto" (Upload second photo)
  - "Sube ambas fotos" (Upload both photos)

---

## Browser Compatibility

| Feature | Browser Support |
|---------|----------------|
| MutationObserver | Chrome 18+, Firefox 14+, Safari 6+, Edge 12+ |
| `Object.entries()` | Chrome 54+, Firefox 47+, Safari 10.1+, Edge 14+ |
| `Array.filter()` | IE 9+ |
| `Array.every()` | IE 9+ |
| `startsWith()` | Chrome 41+, Firefox 17+, Safari 9+, Edge 12+ |

All modern browsers fully supported.

---

## Troubleshooting

### Warning Doesn't Show

**Check:**
1. `validador` object exists?
2. Image validation keys present? (`Img1`, `Img2`)
3. Image validations actually `false`?
4. Element ID correct? (`customization-warning`)
5. JavaScript errors in console?

### Warning Doesn't Hide After Upload

**Check:**
1. Image validation actually set to `true`?
2. `updateWarning()` being called?
3. MutationObserver active?
4. CSS `display` property being set?

### Console Error: "validador no está definido"

**Solution:**
- Ensure `validador.liquid` loads before `aviso-personaliza.liquid`
- Check snippet include order in template

### Warning Shows for Non-Image Products

**Expected behavior:**
- If no `Img` keys exist, `imgValid = true` (warning hidden)
- If warning shows, an `Img` key exists and is `false`
- Check product variants - may require images you're not aware of

---

## Enhancement Ideas

### Multi-language Support
```javascript
const messages = {
  es: "Sube tu foto para añadir el producto al carrito",
  en: "Upload your photo to add the product to cart"
};
```

### Specific Image Feedback
```javascript
if (!validador.Img1) {
  message = "Sube la primera foto";
} else if (!validador.Img2) {
  message = "Sube la segunda foto";
}
```

### Progress Indicator
```javascript
const totalImages = keys.filter(k => k.startsWith("Img")).length;
const uploadedImages = keys.filter(k => k.startsWith("Img") && validador[k]).length;
message = `Fotos subidas: ${uploadedImages}/${totalImages}`;
```

---

## Code Location

**File:** `snippets/aviso-personaliza.liquid`

**Dependencies:**
- `validador.liquid` (required - must load first)

**Used in:** Product pages with image upload requirements

**Renders:** Single warning paragraph, conditionally visible

---

**End of Documentation**
