# Image Upload System Documentation

## Overview

The image upload snippets (`upload-images-custom.liquid` and `upload-images-custom-optional-2.liquid`) provide custom image upload functionality for products requiring customer photos. These allow customers to upload personalized images (e.g., photos for engraved lockets, photo pendants) that get attached to the Shopify order.

**Use Case:** Perfect for personalized products where customers need to provide their own photos for engraving, printing, or embedding in jewelry.

---

## Features

- ✅ **Direct file picker**: Click button to open file selection dialog
- ✅ **Image preview**: Shows uploaded image as button background
- ✅ **Validation integration**: Works with validador system to control cart button
- ✅ **Conditional display**: Shows/hides based on product variant selection
- ✅ **File type restriction**: Only accepts PNG and JPEG images
- ✅ **Multiple image support**: Can handle one or two images per product

---

## How It Works

### Technical Architecture

The system consists of two related snippets:

#### 1. **Single Image Upload** (`upload-images-custom.liquid`)
- Used for products requiring **one photo**
- Validator key: `validador.Img1`

#### 2. **Optional Second Image** (`upload-images-custom-optional-2.liquid`)
- Used for products with **"Doble Grabado" (double engraving)** option
- Validator key: `validador.Img2`
- Only appears when specific variant is selected

---

## Single Image Upload

### HTML Structure

```liquid
<div class="file_container">
  <p class="line-item-property__field">
    <button type="button" id="openModalButton" class="input-preview">
      📷 Sube tu foto
    </button>
  </p>
</div>

<input
  form="{{ product_form_id }}"
  id="img"
  type="file"
  name="properties[Agrega tu imagen]"
  accept="image/png, image/jpeg"
  style="display:none;"
>
```

**Key elements:**
- **Button** (`#openModalButton`): Visible upload trigger
- **Hidden input** (`#img`): Actual file input (hidden)
- **Form attribute**: Links input to product form
- **Name property**: `properties[Agrega tu imagen]` - becomes Shopify order property

### Upload Flow

1. **Initialization**
```javascript
validador.Img1 = false;
const fileImage = document.querySelector('#img');
const openModalButton = document.getElementById("openModalButton");
```

2. **Button Click → File Picker**
```javascript
openModalButton.onclick = function() {
  fileImage.click();
};
```
- Clicking visible button triggers hidden file input
- Opens native file selection dialog

3. **File Selection → Preview & Validation**
```javascript
fileImage.onchange = function() {
  const reader = new FileReader();
  reader.onload = function(e) {
    // Update button appearance
    filePreview.style.height = "170px";
    filePreview.style.backgroundImage = "url(" + e.target.result + ")";
    filePreview.classList.add("has-image");

    // Update validation
    validador.Img1 = true;

    // Check if all validations pass
    if(sonTodosVerdaderos(validador)){
      addToCartButton.disabled = false;
    }
  };
  reader.readAsDataURL(this.files[0]);
};
```

**What happens:**
- FileReader converts image to base64 data URL
- Button background becomes the uploaded image
- Button height increases to show preview
- `has-image` class added for styling
- Validation key set to `true`
- Cart button enabled if all other validations pass

---

## Optional Second Image Upload

### Conditional Display Logic

The second image uploader only appears for specific variant selections:

```javascript
let conMarcacionOption = document.getElementById("template--16577217953861__main-3-1");
let sinMarcacionOption = document.getElementById("template--16577217953861__main-3-0");
let dobleMarcadoOption = document.getElementById("template--16577217953861__main-3-2");

// Fallback IDs if primary not found
if (!conMarcacionOption || !sinMarcacionOption || !dobleMarcadoOption) {
  conMarcacionOption = document.getElementById("template--16577217953861__main-1-1");
  sinMarcacionOption = document.getElementById("template--16577217953861__main-1-0");
  dobleMarcadoOption = document.getElementById("template--16577217953861__main-1-2");
}
```

**Variant options:**
- **Sin Marcación** (No engraving): No images required
- **Con Marcación** (Single engraving): One image required (Img1)
- **Doble Marcado** (Double engraving): Two images required (Img1 + Img2)

### Show/Hide Function

```javascript
function toggleFileContainer() {
  const isDobleGrabadoSelected = dobleMarcadoOption.checked;
  fileContainer2.style.display = (isDobleGrabadoSelected) ? "block" : "none";

  if (dobleMarcadoOption.checked) {
    validador.Img2 = false;  // Required - awaiting upload
  } else if (sinMarcacionOption.checked || conMarcacionOption.checked) {
    validador.Img2 = true;   // Not required - auto-pass
    if(sonTodosVerdaderos(validador)){
      addToCartButton.disabled = false;
    }
  }
}

// Initialize on page load
toggleFileContainer();

// Listen for variant changes
conMarcacionOption.addEventListener("change", toggleFileContainer);
sinMarcacionOption.addEventListener("change", toggleFileContainer);
dobleMarcadoOption.addEventListener("change", toggleFileContainer);
```

**Behavior:**
- Shows uploader only when "Doble Marcado" selected
- Sets `Img2 = true` (auto-pass) for other variants
- Sets `Img2 = false` (requires upload) for double engraving

---

## Key Functions

### FileReader Upload Process

**Purpose:** Converts uploaded image file to displayable format

**Implementation:**
```javascript
const reader = new FileReader();
reader.onload = function(e) {
  // e.target.result contains base64 data URL
  button.style.backgroundImage = "url(" + e.target.result + ")";
};
reader.readAsDataURL(file);
```

**Process:**
1. Create FileReader instance
2. Define `onload` callback (executes when read completes)
3. Call `readAsDataURL()` to start reading
4. Callback receives base64-encoded image
5. Use as CSS background image

### Validation State Management

**Three states per image:**

| State | When | Validation Value |
|-------|------|------------------|
| Not required | Variant doesn't need image | `validador.ImgX = true` |
| Required but empty | Variant needs image, not uploaded | `validador.ImgX = false` |
| Required and uploaded | Image successfully uploaded | `validador.ImgX = true` |

---

## Styling System

### Button States

#### Default State
```css
.input-preview {
  border-radius: 30px;
  background-color: black;
  color: white;
  font-size: 14px;
  padding: 10px 20px;
  cursor: pointer;
}
```

#### Hover State
```css
.input-preview:hover {
  background-color: #333;
}
```

#### With Image Uploaded
```css
.input-preview {
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  height: 170px;  /* Expanded to show preview */
}

.has-image::before {
  /* Dark overlay over image */
  background: rgba(50, 50, 50, 0.2);
  border-radius: 20px;
}

.has-image::after {
  color: white;  /* Text stays visible over image */
}
```

### Container Styling

```css
.file_container {
  width: 200px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding: 0;
}
```

---

## Integration with Shopify

### Order Properties

Uploaded images are sent to Shopify as order properties:

```html
<input
  name="properties[Agrega tu imagen]"
  type="file"
  form="{{ product_form_id }}"
>
```

**In Shopify admin, appears as:**
```
Order #1234
Properties:
  - Agrega tu imagen: [uploaded_file.jpg]
  - Segunda imagen: [uploaded_file2.jpg]
```

### Form Association

```liquid
{%- assign product_form_id = 'product-form-' | append: section.id -%}
<input form="{{ product_form_id }}" ... >
```

**Why it matters:**
- Input can be anywhere on page
- `form` attribute links it to main product form
- Submits with "Add to Cart" action

---

## Variant ID Patterns

### Primary IDs
```javascript
template--16577217953861__main-3-0  // Sin Marcación
template--16577217953861__main-3-1  // Con Marcación
template--16577217953861__main-3-2  // Doble Marcado
```

### Fallback IDs
```javascript
template--16577217953861__main-1-0  // Alternative positions
template--16577217953861__main-1-1
template--16577217953861__main-1-2
```

**Pattern:** `template--{THEME_ID}__main-{OPTION_POSITION}-{VALUE_INDEX}`

---

## Common Use Cases

### Scenario 1: Single Photo Product
```javascript
// Only include upload-images-custom.liquid
validador.Img1 = false;  // Required
// User uploads → validador.Img1 = true
```

### Scenario 2: Optional Double Engraving
```javascript
// Include both snippets
// If "Sin Marcación" → Img1 = true, Img2 = true (auto-pass)
// If "Con Marcación" → Img1 = false (required), Img2 = true (not needed)
// If "Doble Marcado" → Img1 = false, Img2 = false (both required)
```

### Scenario 3: Always Two Photos
```javascript
// Include both snippets, no variant logic
validador.Img1 = false;
validador.Img2 = false;
// Both must be uploaded
```

---

## File Type Restrictions

```html
accept="image/png, image/jpeg"
```

**Accepted formats:**
- PNG (.png)
- JPEG (.jpg, .jpeg)

**Not accepted:**
- GIF, WebP, SVG, HEIC, etc.

**Why restricted:**
- Ensures consistent image quality
- Prevents issues with engraving/printing processes
- Standard formats universally supported

---

## Important Notes

### Image Size Considerations
- **No size validation** in current implementation
- Large images may slow upload
- Consider adding file size check for production:
```javascript
if (file.size > 5000000) { // 5MB limit
  alert('Archivo muy grande');
  return;
}
```

### Browser Compatibility
- FileReader API: All modern browsers
- Base64 data URLs: Universal support
- File input `accept` attribute: Widely supported

### Mobile Behavior
- File picker opens camera or photo library
- Preview works on all devices
- Touch-friendly button size (minimum 44x44px recommended)

---

## Troubleshooting

### Image Preview Doesn't Show

**Check:**
1. FileReader `onload` callback executing?
2. `has-image` class being added?
3. CSS `background-image` property set?
4. Image file valid format?

### Validation Not Working

**Check:**
1. `validador` object exists? (validador.liquid loaded first?)
2. Correct validation key? (`Img1` vs `Img2`)
3. `sonTodosVerdaderos()` function available?
4. Other validations also passing?

### Wrong Uploader Shows for Variant

**Check:**
1. Variant option IDs correct?
2. Try fallback IDs if primary not found
3. Check console for error: "No se pudieron encontrar las opciones"
4. Verify variant option position in Shopify admin

### File Not Submitting to Cart

**Check:**
1. `form` attribute matches product form ID?
2. Input has `name` attribute starting with `properties[`?
3. File input not disabled?
4. Shopify theme supports file properties?

---

## Code Locations

**Files:**
- `snippets/upload-images-custom.liquid` (Primary image upload)
- `snippets/upload-images-custom-optional-2.liquid` (Secondary image upload)

**Dependencies:**
- `validador.liquid` (must load first)
- Product form with ID matching `product-form-{section.id}`

**Used in:** Product pages with image customization options

---

**End of Documentation**
