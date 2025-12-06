# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Shopify theme customization repository for "La Vina" - an e-commerce store selling personalized jewelry and accessories. The codebase consists of custom Liquid snippets that extend Shopify's native functionality with specialized product customization features.

## Core Architecture

### Validation System

The codebase uses a global JavaScript validation system that controls the "Add to Cart" button state:

- **`validador.liquid`**: Core validation script that initializes a global `validador` object
  - Disables the "Add to Cart" button by default
  - Uses MutationObserver to watch for DOM changes
  - Enables cart button only when all validation fields are `true`
  - Blocks emojis in text inputs
  - All custom input snippets extend this validator by adding their own validation keys

### Product Customization Components

The repository contains modular Liquid snippets for different product customization types:

1. **Text Input Snippets** (`X-words.liquid` files)
   - Pattern: `5-words.liquid`, `7-words.liquid`, `10-words.liquid`, `12-words.liquid`, `20-words-grabado.liquid`
   - Each validates character/word count limits
   - Adds validation keys like `validador.DiezPalabras = true/false`
   - Shows/hides based on variant selection (e.g., "Con Marcación" vs "Sin Marcación")

2. **Image Upload Snippets**
   - `upload-images-custom.liquid`: Main image uploader with modal
   - `upload-images-custom-optional-2.liquid`: Variant with optional second image
   - Both use `validador.Img1`, `validador.Img2` keys
   - Display image preview on selection

3. **Variant Selector**
   - `product-variant-picker.liquid`: Modified Shopify variant picker
   - Auto-detects shape variants by keywords ("corazón", "redondo")
   - Adds `data-shape-type` attributes and CSS classes
   - Preserves all native Shopify functionality (URL updates, inventory, pricing)

4. **Shape Selector Styling**
   - `variant-shape-selector.liquid`: Pure CSS implementation using SVG data URLs
   - Renders heart and circle shapes for variant options
   - No JavaScript DOM manipulation - only CSS styling

5. **Warning Messages**
   - `aviso-personaliza.liquid`: Conditional warning display based on validator state

## Key Technical Patterns

### Validation Flow
```javascript
// 1. Initialize validator in validador.liquid
let validador = {}

// 2. Each snippet adds its validation key
validador.DiezPalabras = false  // or true

// 3. Helper function checks all keys
function sonTodosVerdaderos(objeto) {
  return Object.values(objeto).every(valor => valor === true);
}

// 4. Enable cart when all validations pass
if(sonTodosVerdaderos(validador)){
  addToCartButton.disabled = false;
}
```

### Shopify Integration
- Uses `product_form_id` pattern: `'product-form-' | append: section.id`
- Form inputs use `name="properties[...]"` to pass custom data to Shopify cart
- Variant selectors maintain Shopify's native HTML structure to preserve functionality

### ID Naming Convention
- Variant option IDs follow pattern: `template--{theme-id}__main-{option-position}-{value-index}`
- Example: `template--16560389324869__main-2-1` for option 2, value 1

## File Structure

```
La Vina/
├── snippets/              # All Liquid snippet files
│   ├── validador.liquid   # Core validation system (must be included first)
│   ├── product-variant-picker.liquid  # Shape variant detection
│   ├── variant-shape-selector.liquid  # Shape styling (CSS only)
│   ├── upload-images-custom.liquid    # Image upload functionality
│   ├── 5-words.liquid, 7-words.liquid, etc.  # Text input validators
│   ├── aviso-personaliza.liquid       # Validation warnings
│   └── base.css           # Base styling
└── documentation/
    └── buttons-documentation.md  # Complete shape selector implementation guide
```

## Development Guidelines

### Adding New Custom Input Fields

1. Create new snippet following naming pattern (`X-words-feature.liquid`)
2. Initialize validator key: `validador.YourFeature = false`
3. Add validation logic that sets key to `true` when valid
4. Use form attribute: `form="{{ product_form_id }}"`
5. Use name pattern: `name="properties[Description]"`
6. Show/hide based on variant selection if needed

### Modifying Validation

- Always use the global `validador` object
- Never directly enable/disable cart button outside validation flow
- Use descriptive keys (e.g., `validador.Img1`, `validador.DiezPalabras`)
- Test with all validation scenarios before deployment

### Shape Variant Customization

- Add detection keywords in `product-variant-picker.liquid:26-29`
- Define SVG shapes in `variant-shape-selector.liquid` using data URLs
- Use CSS pseudo-elements (::before) for shape rendering
- Maintain absolute positioning to avoid layout shifts
- Refer to [buttons-documentation.md](documentation/buttons-documentation.md) for detailed customization guide

### Testing Checklist

- [ ] Validator object initializes correctly
- [ ] All custom fields update validator keys
- [ ] Cart button enables only when all validations pass
- [ ] Shopify variant selection works (price updates, inventory, URL)
- [ ] Image uploads display previews correctly
- [ ] Text inputs respect word/character limits
- [ ] Mobile responsiveness for all custom inputs

## Important Notes

- **Order matters**: `validador.liquid` must load before any snippet that uses the validator
- **No emoji policy**: All text inputs automatically strip emojis via the validator
- **Shopify compatibility**: Never modify Shopify's form structure or intercept native events
- **MutationObserver**: Used extensively - be aware of performance when adding new observers
- **Spanish language**: All user-facing text is in Spanish
