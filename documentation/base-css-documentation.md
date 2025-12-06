# Base CSS Custom Styles Documentation

## Overview

The `base.css` file is a Shopify theme stylesheet that contains the foundational CSS for the La Vina store. While most of the file consists of standard Shopify Dawn theme styles, this documentation focuses on the **custom classes and styles** that support the product customization system.

**Use Case:** Provides consistent styling for custom input fields, image upload buttons, warning messages, and shape variant selectors across all product pages.

---

## Features

- ✅ **Modal components**: Styled modal dialogs for image upload recommendations
- ✅ **Consistent branding**: Orange accent color (#ff6600) for focus states
- ✅ **Responsive design**: Mobile-friendly layouts
- ✅ **Accessibility**: Proper focus states and visibility toggles
- ✅ **Reusable components**: Standardized field, button, and container styles

---

## Custom Component Styles

### Text Input Fields

#### Field Container
```css
.field-container {
  display: flex;
  flex-direction: column;
  gap: 5px;
  max-width: 300px;
}
```
**Usage:** Wrapper for custom text input fields with labels and error messages

**Properties:**
- Vertical layout (column)
- 5px spacing between elements
- Max width prevents overly wide inputs
- Used in: All x-words.liquid snippets

#### Field Input
```css
.field__input {
  width: 100%;
  padding: 8px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  transition: border-color 0.3s;
}

.field__input:focus {
  border-color: #ff6600;  /* Orange brand color */
  outline: none;
}
```
**Usage:** Styled text input for personalization fields

**Key features:**
- Full width within container
- Smooth border color transition
- **Orange focus state** (brand color)
- Rounded corners

#### Field Label
```css
.field__label {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  margin-bottom: 5px;
}
```
**Usage:** Labels for custom input fields

**Properties:**
- Bold weight for emphasis
- Dark gray color
- Small margin below for spacing

---

### Error & Warning Messages

#### Error Message
```css
.error-message {
  color: #961212;  /* Dark red */
  font-size: 12px;
  display: none;  /* Hidden by default */
}
```
**Usage:** Shown when word limit exceeded in text inputs

**Usage in code:**
```javascript
errorMessage.style.display = wordCount > limit ? "block" : "none";
```

#### Empty Message
```css
.empty-message {
  display: flex;
  align-items: center;
  color: #961212;
  font-size: 12px;
}
```
**Usage:** Shown when required text field is empty

**Contains:**
```html
<span class="question-mark">!</span>
```

#### Question Mark Icon
```css
.question-mark {
  font-weight: bold;
  color: #961212;
  margin-right: 5px;
}
```
**Usage:** Exclamation mark icon in empty messages

---

### Image Upload Buttons

#### File Container
```css
.file_container {
  width: 200px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding: 0;
}
```
**Usage:** Wrapper for image upload button(s)

**Properties:**
- Fixed width for consistency
- Vertical layout
- Left-aligned items

#### Input Preview Button
```css
.input-preview {
  font-family: Assistant, sans-serif;
  border-radius: 30px;
  background-color: black;
  color: white;
  font-size: 14px;
  width: auto;
  padding: 10px 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  transition: ease-in-out 750ms;
  position: relative;
  margin: .7rem .5rem .2rem 0;
  white-space: nowrap;
}

.input-preview:hover {
  background-color: #333;
}
```
**Usage:** Clickable button that triggers file picker and shows image preview

**Key features:**
- Pill-shaped (30px border-radius)
- Black background, white text
- Smooth 750ms transitions
- Background becomes uploaded image
- No text wrapping

#### Image Preview States

##### Hidden File Input
```css
.input-preview__src {
  display: none;
}
```
**Usage:** Hides the actual file input element

##### With Image Overlay
```css
.has-image::before {
  border-radius: 20px;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(50, 50, 50, 0.2);  /* Dark overlay */
  content: " ";
  transition: ease-in-out 750ms;
}

.has-image::after {
  color: white;  /* Ensures text visible over image */
}
```
**Usage:** Applied when image is uploaded to maintain text visibility

---

### Modal Components

The base.css file contains modal styling for image upload recommendations.

#### Modal Wrapper
```css
.modal {
  display: none;
  position: fixed;
  z-index: 9999;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
}
```
**Usage:** Full-screen overlay container for modals

**Properties:**
- Fixed positioning (overlays page)
- Highest z-index (appears on top)
- Hidden by default
- Full viewport coverage

#### Modal Content
```css
.modal-content {
  background-color: #fefefe;
  margin: 10% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
  max-width: 500px;
  border-radius: 10px;
  text-align: center;
}

.modal-content h2 {
  margin-top: 0;
}
```
**Usage:** Centered content box within modal

**Properties:**
- White background
- Centered with auto margins
- Responsive width (80% up to 500px max)
- Rounded corners
- Center-aligned text

#### Modal Buttons
```css
#confirmButton, #closeButton {
  background-color: black;
  color: white;
  font-size: 14px;
  padding: 10px 20px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  width: 70%;
  margin: 10px auto;
  display: block;
  text-align: center;
  white-space: nowrap;
}

#confirmButton:hover, #closeButton:hover {
  background-color: #333;
}
```
**Usage:** Action buttons within upload modals

**Properties:**
- Black pill-shaped buttons
- 70% width (responsive)
- Centered
- Hover state (lighter gray)

---

### Shape Variant Selectors

The shape variant styles are defined in `variant-shape-selector.liquid` but may reference base classes.

#### Shape Variant Group
```css
.shape-variant-group {
  display: flex !important;
  gap: 20px !important;
  flex-wrap: wrap !important;
  align-items: flex-start !important;
}
```
**Usage:** Container for shape-based variant options

**Properties:**
- Flexbox horizontal layout
- 20px spacing between shapes
- Wraps on small screens
- Top-aligned items

#### Shape Labels
```css
.shape-variant-group label {
  display: inline-block !important;
  position: relative !important;
  cursor: pointer !important;
  width: 90px !important;
  padding-top: 60px !important;
  text-align: center !important;
}
```
**Usage:** Individual shape option labels

**Properties:**
- Relative positioning (for absolute ::before shapes)
- Fixed width for consistency
- Top padding to accommodate shape
- Centered text below shape

---

## Color Palette

### Brand Colors

| Color | Hex Code | Usage |
|-------|----------|-------|
| Orange (Primary) | `#ff6600` | Input focus states |
| Black | `#000000` | Buttons, text |
| Dark Gray | `#333333` | Button hover, secondary text |
| Dark Red | `#961212` | Error messages |
| Maroon | `#800000` | Warning text |
| Light Gray | `#cccccc` | Input borders |
| White | `#ffffff` | Button text, backgrounds |

### Semantic Colors

```css
/* Focus states */
border-color: #ff6600;

/* Error states */
color: #961212;

/* Warnings */
color: #800000;

/* Disabled/inactive */
border: 1px solid #ccc;
```

---

## Responsive Considerations

### Mobile-Friendly Sizes

All custom components use relative or flexible sizing:
- `max-width: 300px` on field containers
- `width: 80%` on modals
- `padding: 10px 20px` maintains tap targets
- `font-size: 14px` readable on mobile

### Touch Targets

Buttons and interactive elements meet minimum size:
- Image upload buttons: `padding: 10px 20px` (>44px height)
- Input fields: `padding: 8px` + `font-size: 14px`
- Shape variants: `50px x 50px` explicit sizing

---

## Animation & Transitions

### Transition Timing

```css
/* Input focus */
transition: border-color 0.3s;

/* Image preview */
transition: ease-in-out 750ms;

/* Hover states */
transition: background-color 0.3s;
```

**Philosophy:**
- Quick feedback (300ms) for interactive states
- Slower (750ms) for visual changes (image previews)
- Ease-in-out for smooth, natural motion

---

## Important Notes

### !important Usage

Some custom styles use `!important` to override theme defaults:
- Shape variant selectors use `!important` extensively
- Necessary to override Shopify Dawn theme specificity
- Use sparingly in new custom styles

### Font Family

```css
font-family: Assistant, sans-serif;
```
**Assistant** is the primary font for custom components, matching Shopify theme.

### Z-Index Layers

```css
z-index: 9999;  /* Modals */
```
Ensures modals appear above all other content, including Shopify's navigation.

---

## Browser Compatibility

All custom CSS uses widely-supported properties:
- Flexbox (IE 11+)
- Border-radius (IE 9+)
- Transitions (IE 10+)
- RGBA colors (IE 9+)
- `::before`/`::after` pseudo-elements (IE 8+)

Full modern browser support with graceful degradation.

---

## Maintenance Guidelines

### Adding New Custom Styles

1. **Use existing class patterns**
   - `.field-container` for form groups
   - `.input-preview` for upload buttons
   - `.error-message` for validation feedback

2. **Follow color palette**
   - Use `#ff6600` for interactive focus states
   - Use `#961212` for errors
   - Use black/white for primary UI

3. **Maintain responsive design**
   - Use `max-width` instead of fixed widths
   - Test on mobile devices
   - Ensure minimum 44px tap targets

4. **Keep transitions consistent**
   - 300ms for quick feedback
   - 750ms for visual changes
   - Use `ease-in-out` timing

---

## Common Customizations

### Changing Brand Color

Replace all instances of `#ff6600`:
```css
/* From */
border-color: #ff6600;

/* To */
border-color: #0066cc;  /* New brand color */
```

### Adjusting Button Style

```css
.input-preview {
  border-radius: 4px;  /* Square corners */
  background-color: #0066cc;  /* Different color */
  padding: 12px 24px;  /* Larger */
}
```

### Changing Error Message Color

```css
.error-message, .empty-message {
  color: #d32f2f;  /* Material Design red */
}
```

---

## Integration Notes

### Works With

- All custom Liquid snippets (10-words, upload-images, etc.)
- Shopify Dawn theme base styles
- Shape variant selector system

### Dependent Files

Custom snippets that rely on these styles:
- `validador.liquid`
- `upload-images-custom.liquid`
- `upload-images-custom-optional-2.liquid`
- All `x-words.liquid` variants
- `aviso-personaliza.liquid`
- `variant-shape-selector.liquid`

---

## Troubleshooting

### Styles Not Applying

**Check:**
1. base.css loaded in theme?
2. More specific selectors overriding?
3. Inline styles taking precedence?
4. Browser cache cleared?

### Inconsistent Appearance

**Likely causes:**
1. Theme update overwrote custom styles
2. Missing `!important` where needed
3. CSS load order incorrect

### Mobile Display Issues

**Check:**
1. Viewport meta tag present?
2. Fixed widths instead of max-widths?
3. Font sizes too small (<12px)?
4. Tap targets too small (<44px)?

---

## File Information

**Location:** `snippets/base.css`

**Size:** ~77KB (3456 lines)

**Type:** Stylesheet (CSS)

**Loaded:** Globally across all pages

**Custom sections:** Interspersed with Shopify Dawn theme defaults

---

## Best Practices

1. **Document custom additions**
   - Add comments for custom classes
   - Note which snippets use specific styles

2. **Preserve on theme updates**
   - Back up custom styles before updating theme
   - Use separate custom.css if possible

3. **Test across devices**
   - Desktop (Chrome, Firefox, Safari)
   - Mobile (iOS Safari, Chrome Android)
   - Tablet views

4. **Validate CSS**
   - Use CSS validator
   - Check for unused selectors
   - Remove deprecated properties

---

**End of Documentation**
