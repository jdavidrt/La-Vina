# Validador System Documentation

## Overview

The `validador.liquid` snippet is the **core validation system** for all product customization features in the La Vina Shopify store. It acts as a central validator that controls the "Add to Cart" button state based on whether all required customization fields have been completed correctly.

**Use Case:** Prevents customers from adding products to cart until all required customizations (text inputs, image uploads, etc.) are properly filled out.

---

## Features

- ✅ **Global validation object**: Creates a shared `validador` object used by all custom snippets
- ✅ **Automatic cart control**: Disables "Add to Cart" button by default, enables only when all validations pass
- ✅ **Real-time monitoring**: Uses MutationObserver to detect DOM changes and re-validate
- ✅ **Emoji blocking**: Automatically removes emojis from all text inputs site-wide
- ✅ **Extensible**: Other snippets easily add their own validation keys

---

## How It Works

### Technical Architecture

The validador system operates in four stages:

#### 1. **Initialization**
```javascript
let validador = {}
const addToCartButton = document.querySelector('[name="add"]');
addToCartButton.disabled = true;
```
- Creates empty global `validador` object
- Finds the Shopify "Add to Cart" button
- Disables it by default

#### 2. **Validation Key Registration**
Other snippets add their own keys to the `validador` object:
```javascript
validador.DiezPalabras = false;  // From 10-words.liquid
validador.Img1 = false;          // From upload-images-custom.liquid
validador.DocePalabras = false;  // From 12-words.liquid
```

#### 3. **Validation Check Function**
```javascript
function sonTodosVerdaderos(objeto) {
  return Object.values(objeto).every(valor => valor === true);
}
```
- Checks if **all** values in the `validador` object are `true`
- Returns `true` only if every validation has passed

#### 4. **Real-Time Monitoring**
```javascript
const observer = new MutationObserver((mutationsList) => {
  if(sonTodosVerdaderos(validador)){
    addToCartButton.disabled = false;
  } else {
    addToCartButton.disabled = true;
  }
});

observer.observe(document.body, { childList: true, subtree: true });
```
- Watches the entire page for DOM changes
- Re-validates whenever content changes
- Updates button state accordingly

---

## Key Functions

### `sonTodosVerdaderos(objeto)`

**Purpose:** Checks if all validation keys are `true`

**Parameters:**
- `objeto` - The validador object to check

**Returns:**
- `true` if all values are `true`
- `false` if any value is `false`

**Usage:**
```javascript
if(sonTodosVerdaderos(validador)){
  addToCartButton.disabled = false;
}
```

### Emoji Blocking System

**Purpose:** Prevents emoji characters in all text inputs

**Implementation:**
```javascript
function eliminarEmojis(text) {
  return text.replace(/\p{Emoji}/gu, "");
}

function validarInputSinEmojis(event) {
  const input = event.target;
  const valorSinEmojis = eliminarEmojis(input.value);
  if (input.value !== valorSinEmojis) {
    input.value = valorSinEmojis;
  }
}

document.querySelectorAll("input[type='text'], textarea").forEach(input => {
  input.addEventListener("input", validarInputSinEmojis);
});
```
- Uses Unicode property escapes to detect emojis
- Strips emojis immediately as user types
- Applies to all text inputs and textareas on the page

---

## Integration with Other Snippets

### Standard Pattern for Custom Snippets

All custom input snippets follow this pattern:

```javascript
// 1. Initialize validation key
document.addEventListener("DOMContentLoaded", function() {
  validador.YourFeatureName = false;
});

// 2. Perform validation
function validateYourFeature() {
  if (/* validation passes */) {
    validador.YourFeatureName = true;

    // 3. Check if all validations pass
    if(sonTodosVerdaderos(validador)){
      addToCartButton.disabled = false;
    }
  } else {
    validador.YourFeatureName = false;
  }
}
```

### Example: Complete Integration Flow

1. Page loads → `validador = {}`
2. Validador.liquid disables cart button
3. 10-words.liquid adds: `validador.DiezPalabras = false`
4. upload-images-custom.liquid adds: `validador.Img1 = false`
5. User fills text field → `validador.DiezPalabras = true`
6. MutationObserver detects change → checks `sonTodosVerdaderos(validador)`
7. Still returns `false` (Img1 is still false)
8. User uploads image → `validador.Img1 = true`
9. MutationObserver checks again → `sonTodosVerdaderos(validador)` returns `true`
10. Cart button enabled

---

## Validation States

| State | Button Status | Meaning |
|-------|---------------|---------|
| `validador = {}` | Disabled | No validations registered yet |
| `validador = {Img1: false}` | Disabled | Validation registered but not passed |
| `validador = {Img1: true, Text: false}` | Disabled | Some validations passed, others pending |
| `validador = {Img1: true, Text: true}` | **Enabled** | All validations passed |

---

## MutationObserver Configuration

```javascript
const config = { childList: true, subtree: true };
const observer = new MutationObserver(callback);
observer.observe(document.body, config);
```

**What it monitors:**
- `childList: true` - Detects when child elements are added/removed
- `subtree: true` - Monitors the entire DOM tree, not just direct children
- Target: `document.body` - Watches the entire page

**Why it's needed:**
- Some validations trigger DOM updates (showing/hiding error messages)
- Variant selection may change which validations are required
- Ensures button state updates immediately after any page change

---

## Common Use Cases

### Scenario 1: Single Required Field
```javascript
validador.OnlyField = false;
// When field is valid
validador.OnlyField = true;
// Button enables immediately
```

### Scenario 2: Multiple Required Fields
```javascript
validador.Field1 = false;
validador.Field2 = false;
validador.Field3 = false;
// Button only enables when ALL are true
```

### Scenario 3: Conditional Validations
```javascript
if (conMarcacionOption.checked) {
  validador.CustomText = false; // Required
} else {
  validador.CustomText = true;  // Not required
}
```

---

## Important Notes

### Load Order
- `validador.liquid` must be included **before** any snippet that uses the validator
- Typically rendered in the main product template or section

### Naming Conventions
- Use descriptive camelCase names for validation keys
- Examples: `DiezPalabras`, `Img1`, `DocePalabras`
- Avoid generic names like `valid`, `check`, `field`

### Performance Considerations
- MutationObserver watches the entire page - can be expensive
- Minimize DOM changes in validation logic
- Use `if(sonTodosVerdaderos(validador))` check before enabling button to prevent excessive updates

### Debugging
```javascript
console.log('Validador:', validador);
console.log('All valid:', sonTodosVerdaderos(validador));
```

---

## Browser Compatibility

| Feature | Browser Support |
|---------|----------------|
| MutationObserver | Chrome 18+, Firefox 14+, Safari 6+, Edge 12+ |
| Unicode emoji regex (`\p{Emoji}`) | Chrome 64+, Firefox 78+, Safari 11.1+, Edge 79+ |
| `Object.values()` | Chrome 54+, Firefox 47+, Safari 10.1+, Edge 14+ |

---

## Security Considerations

### Why Emojis are Blocked
- Prevents encoding issues in Shopify order properties
- Avoids display problems in order confirmations
- Ensures consistent data formatting

### Button Disabling Strategy
- "Fail closed" approach - button disabled by default
- Requires explicit validation to enable
- Prevents accidental orders with incomplete customizations

---

## Troubleshooting

### Button Won't Enable

**Check:**
1. Is `validador.liquid` loaded before other snippets?
2. Are all validation keys present? `console.log(validador)`
3. Are all values `true`? `console.log(sonTodosVerdaderos(validador))`
4. Is MutationObserver running? Check console for errors

### Button Enables Too Early

**Possible causes:**
1. Validation key set to `true` before checking conditions
2. Missing validation key (empty object returns `true` on `every()`)
3. Conditional validation not properly initialized

### Emojis Not Being Blocked

**Check:**
1. Browser supports Unicode property escapes
2. Event listener is attached: `console.log('Emoji blocking active')`
3. Input is `type="text"` or `<textarea>` (not other input types)

---

## Code Location

**File:** `snippets/validador.liquid`

**Included in:** Main product template/section (must load first)

**Dependencies:** None (standalone)

**Used by:** All custom input snippets (10-words, upload-images, etc.)

---

**End of Documentation**
