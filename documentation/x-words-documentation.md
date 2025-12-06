# Text Input Word Limit System Documentation

## Overview

The word-limit snippets (`5-words.liquid`, `7-words.liquid`, `10-words.liquid`, `12-words.liquid`, `20-words-grabado.liquid`) provide customizable text input fields with automatic word count validation. These allow customers to enter personalized phrases for engraving, printing, or embroidery on products.

**Use Case:** Perfect for personalized jewelry, gifts, or accessories where customers provide custom text with specific length restrictions based on physical space limitations.

---

## Features

- ✅ **Real-time word counting**: Validates as user types
- ✅ **Conditional display**: Shows/hides based on variant selection ("Con Marcación" vs "Sin Marcación")
- ✅ **Dynamic error messages**: Shows different messages for empty vs. over-limit
- ✅ **Validation integration**: Works with validador system to control cart button
- ✅ **Multiple variants**: Support for different word limits (5, 7, 10, 12, 20 words)
- ✅ **Emoji blocking**: Inherits emoji blocking from validador.liquid

---

## How It Works

### Technical Architecture

All word-limit snippets follow the same pattern with different maximum word counts:

| Snippet | Max Words | Validator Key | Typical Use Case |
|---------|-----------|---------------|------------------|
| `5-words.liquid` | 5 | `CincoPalabras` | Short pendants |
| `7-words.liquid` | 7 | `SietePalabras` | Medium engravings |
| `10-words.liquid` | 9 | `DiezPalabras` | Standard phrases |
| `12-words.liquid` | 11 | `DocePalabras` | Longer messages |
| `20-words-grabado.liquid` | 20 | `VeintePalabras` | Extended text |

**Note:** Despite file names, actual limits are sometimes one less (e.g., "10-words" allows 9 words)

---

## Standard Implementation

### HTML Structure

```liquid
<div class="field-container" style="display: none;">
  <div class="field">
    <input
      form="{{ product_form_id }}"
      id="input-10palabras"
      class="field__input"
      type="text"
      name="properties[Tu frase personalizada]"
    >
    <label class="field__label" for="input-10palabras">
      Escribe tu frase personalizada: (máximo 9 palabras)
    </label>
  </div>
  <p id="error-message" class="error-message" style="display: none;">
    Solo se permiten hasta 9 palabras
  </p>
  <p id="empty-message" class="empty-message" style="display: none;">
    Por favor ingresa la frase personalizada <span class="question-mark">!</span>
  </p>
</div>
```

**Key elements:**
- **Container**: Hidden by default (`display: none`)
- **Input field**: Text input linked to product form
- **Label**: Shows maximum word limit
- **Error message**: Shows when word limit exceeded
- **Empty message**: Shows when field is empty

---

## Validation Flow

### 1. Initialization

```javascript
document.addEventListener("DOMContentLoaded", function () {
  validador.DiezPalabras = true; // Default: not required (hidden)
});
```

**Default state:**
- Validation set to `true` (auto-pass)
- Field hidden until variant requires it

### 2. Variant Selection Logic

```javascript
const conMarcacionOption = document.getElementById("template--16560389324869__main-2-1");
const sinMarcacionOption = document.getElementById("template--16560389324869__main-2-0");

function toggleCustomTextField() {
  const fieldContainer = document.querySelector(".field-container");

  if (conMarcacionOption.checked) {
    fieldContainer.style.display = "block";
    validador.DiezPalabras = false; // Now required
    validarTexto(); // Validate immediately
  } else if (sinMarcacionOption.checked) {
    fieldContainer.style.display = "none";
    validador.DiezPalabras = true; // Not required
    errorMessage.style.display = "none";
    emptyMessage.style.display = "none";
  }
}

// Initialize on load
toggleCustomTextField();

// Listen for changes
conMarcacionOption.addEventListener("change", toggleCustomTextField);
sinMarcacionOption.addEventListener("change", toggleCustomTextField);
```

**Variant options:**
- **Sin Marcación** (No engraving): Field hidden, validation auto-passes (`true`)
- **Con Marcación** (With engraving): Field shown, validation required (`false` until valid)

### 3. Word Count Validation

```javascript
function validarTexto() {
  const words = inputField.value.trim().split(/\s+/).filter(word => word.length > 0);
  const wordCount = words.length;

  if (wordCount === 0 || wordCount > 9) {
    validador.DiezPalabras = false;
    errorMessage.style.display = wordCount > 0 ? "block" : "none";
    emptyMessage.style.display = wordCount === 0 ? "flex" : "none";
  } else {
    validador.DiezPalabras = true;
    errorMessage.style.display = "none";
    emptyMessage.style.display = "none";
    if(sonTodosVerdaderos(validador)){
      addToCartButton.disabled = false;
    }
  }
}

inputField.addEventListener("input", validarTexto);
```

**Validation logic:**
- Split input by whitespace
- Filter out empty strings
- Count resulting words
- **Invalid if:** `wordCount === 0` OR `wordCount > limit`
- **Valid if:** `1 <= wordCount <= limit`

**Message display:**
- Empty field → Show "Por favor ingresa..."
- Over limit → Show "Solo se permiten hasta X palabras"
- Valid → Hide all messages

---

## Key Functions

### Word Counting Algorithm

**Purpose:** Extract and count words from user input

**Implementation:**
```javascript
const words = inputField.value.trim().split(/\s+/).filter(word => word.length > 0);
const wordCount = words.length;
```

**Process:**
1. `.trim()` - Remove leading/trailing whitespace
2. `.split(/\s+/)` - Split by one or more whitespace characters
3. `.filter(word => word.length > 0)` - Remove empty strings
4. `.length` - Count resulting array

**Examples:**
```javascript
"Hola mundo" → ["Hola", "mundo"] → 2 words
"  Hola   mundo  " → ["Hola", "mundo"] → 2 words
"Hola" → ["Hola"] → 1 word
"" → [] → 0 words
"Palabra1 palabra2 palabra3" → 3 words
```

### Error Message Management

**Purpose:** Show appropriate feedback based on validation state

**States:**
| Input State | Error Message | Empty Message | Validation |
|-------------|---------------|---------------|------------|
| Empty | Hidden | **Visible** | `false` |
| 1-9 words | Hidden | Hidden | `true` |
| 10+ words | **Visible** | Hidden | `false` |

**Implementation:**
```javascript
if (wordCount === 0 || wordCount > 9) {
  validador.DiezPalabras = false;
  errorMessage.style.display = wordCount > 0 ? "block" : "none";
  emptyMessage.style.display = wordCount === 0 ? "flex" : "none";
} else {
  validador.DiezPalabras = true;
  errorMessage.style.display = "none";
  emptyMessage.style.display = "none";
}
```

---

## Variant-Specific Implementations

### Standard Pattern (10-words, 12-words)

**File:** `10-words.liquid`, `12-words.liquid`

**Features:**
- Toggles between "Con Marcación" and "Sin Marcación"
- Field hidden by default
- Single validation key

### Photo Variant (12-words-foto)

**File:** `12-words-foto.liquid`

**Special features:**
- Integrates with image upload products
- Field always visible (different styling)
- Empty message shown from start:
```javascript
emptyMessage.style.display = 'flex'; // Show immediately
```

### Extended Text (20-words-grabado)

**File:** `20-words-grabado.liquid`

**Features:**
- Higher word limit (20 words)
- Designed for products with more engraving space
- Same validation pattern

### Product-Specific Variants

Some snippets include specific product IDs:
- `5-words-mirada-encapsulada.liquid`
- `10-words-fuente.liquid`
- `10-words-grabado.liquid`
- `10-words-relicario.liquid`

These follow the same pattern but may have customized labels or styling.

---

## Styling System

### Input Field

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
  border-color: #ff6600;  /* Orange border on focus */
  outline: none;
}
```

### Container Layout

```css
.field-container {
  display: flex;
  flex-direction: column;
  gap: 5px;
  max-width: 300px;
}

.field {
  position: relative;
  display: flex;
  flex-direction: column;
}
```

### Label Styling

```css
.field__label {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  margin-bottom: 5px;
}
```

### Error Messages

```css
.error-message, .empty-message {
  color: #961212;  /* Dark red */
  font-size: 12px;
}

.error-message {
  display: none;  /* Hidden by default */
}

.empty-message {
  display: flex;
  align-items: center;
}

.question-mark {
  font-weight: bold;
  color: #961212;
  margin-right: 5px;
}
```

---

## Integration with Shopify

### Order Properties

```html
<input
  name="properties[Tu frase personalizada]"
  type="text"
  form="{{ product_form_id }}"
>
```

**In Shopify admin:**
```
Order #1234
Properties:
  - Tu frase personalizada: "Siempre en mi corazón"
```

### Form Association

```liquid
{%- assign product_form_id = 'product-form-' | append: section.id -%}
```

Links input to product form even if rendered separately.

---

## Variant ID Patterns

### Example IDs
```javascript
// Option 2, Value 0 (Sin Marcación)
"template--16560389324869__main-2-0"

// Option 2, Value 1 (Con Marcación)
"template--16560389324869__main-2-1"
```

**Pattern:** `template--{THEME_ID}__main-{OPTION_POSITION}-{VALUE_INDEX}`

**Position numbers:**
- Position 1: Usually first option (e.g., Size)
- Position 2: Usually second option (e.g., Engraving type)
- Position 3: Usually third option

---

## Common Use Cases

### Scenario 1: Simple Toggle
```javascript
// "Sin Marcación" selected
validador.DiezPalabras = true;  // Auto-pass
fieldContainer.style.display = "none";  // Hidden

// "Con Marcación" selected
validador.DiezPalabras = false;  // Requires validation
fieldContainer.style.display = "block";  // Visible
```

### Scenario 2: Multiple Text Fields
```javascript
// Product with two engraving areas
validador.DiezPalabras = false;  // First area
validador.DocePalabras = false;  // Second area
// Both must be valid to enable cart
```

### Scenario 3: Combined with Images
```javascript
validador.DiezPalabras = false;  // Text required
validador.Img1 = false;          // Image required
// Both must pass validation
```

---

## Important Notes

### Word vs. Character Limits
- Current implementation counts **words** not characters
- "Hola mundo" = 2 words (not 10 characters)
- Single long word still counts as 1 word

### Whitespace Handling
- Multiple spaces treated as one separator
- Leading/trailing spaces ignored
- Tabs and newlines treated as whitespace

### Emoji Blocking
- Inherits emoji blocking from `validador.liquid`
- Emojis automatically removed as user types
- No special handling needed in these snippets

### Real-time Validation
- Validates on every keystroke (`input` event)
- Immediate feedback to user
- No "submit" button needed

---

## Browser Compatibility

| Feature | Browser Support |
|---------|----------------|
| `split(/\s+/)` regex | Universal |
| `filter()` array method | IE 9+ |
| `addEventListener("input")` | IE 9+ |
| `trim()` | IE 9+ |

All features supported in modern browsers.

---

## Troubleshooting

### Field Doesn't Show When Variant Selected

**Check:**
1. Variant option IDs correct?
2. `toggleCustomTextField()` being called?
3. CSS `display: block` being set?
4. JavaScript errors in console?

### Validation Not Working

**Check:**
1. `validador` object exists?
2. Correct validation key name?
3. Word count calculation correct?
4. Other validations also passing?

### Wrong Word Limit Applied

**Check:**
1. Correct snippet included?
2. `wordCount > X` matches desired limit?
3. Label text matches actual limit?

### Cart Button Won't Enable

**Check:**
1. Word count within valid range (1 to limit)?
2. All other validations passing?
3. `sonTodosVerdaderos()` returning `true`?
4. MutationObserver active?

### Error Message Always Shows

**Check:**
1. `validarTexto()` being called?
2. `errorMessage.style.display` being set correctly?
3. Input value actually valid?

---

## Customization Guide

### Changing Word Limit

1. Update validation logic:
```javascript
if (wordCount === 0 || wordCount > 15) { // Change from 9 to 15
```

2. Update label:
```html
<label>(máximo 15 palabras)</label>
```

3. Update error message:
```html
<p>Solo se permiten hasta 15 palabras</p>
```

### Changing Property Name

```html
<input name="properties[Your Custom Label]">
```

This changes how it appears in Shopify order properties.

### Adding Character Limit

```javascript
const charCount = inputField.value.length;
if (charCount > 50) {
  validador.YourKey = false;
}
```

---

## Code Locations

**Files:**
- `snippets/5-words.liquid`
- `snippets/7-words.liquid`
- `snippets/10-words.liquid`
- `snippets/12-words.liquid`
- `snippets/20-words-grabado.liquid`
- (Plus product-specific variants)

**Dependencies:**
- `validador.liquid` (must load first)
- Product form with matching ID

**Used in:** Product pages with text customization options

---

**End of Documentation**
