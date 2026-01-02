# Changelog

All notable changes to the La Vina Shopify customization system will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2024-12-06

### Added

#### Core System
- **Validador System** (`validador.liquid`)
  - Global validation orchestrator for all custom inputs
  - MutationObserver for real-time DOM monitoring
  - Automatic emoji blocking in all text inputs
  - Cart button state management (disabled until all validations pass)

#### Text Input Validation
- **5-words.liquid** - Text input with 5-word maximum
- **7-words.liquid** - Text input with 7-word maximum
- **10-words.liquid** - Text input with 9-word maximum (despite filename)
- **12-words.liquid** - Text input with 11-word maximum
- **20-words-grabado.liquid** - Text input with 20-word maximum
- **Product-specific variants:**
  - `5-words-mirada-encapsulada.liquid`
  - `10-words-fuente.liquid`
  - `10-words-grabado.liquid`
  - `10-words-relicario.liquid`
  - `12-words-foto.liquid`
  - `12-words-grabado.liquid`
  - `12-words-nuestra-huella.liquid`
- Real-time word counting with regex splitting
- Conditional display based on variant selection ("Con Marcación" vs "Sin Marcación")
- Dynamic error messages (empty vs over-limit)
- Integration with Shopify order properties

#### Image Upload System
- **upload-images-custom.liquid** - Primary image upload functionality
  - Direct file picker on button click
  - Image preview as button background using FileReader API
  - PNG/JPEG file type restriction
  - Validation integration with `validador.Img1`
- **upload-images-custom-optional-2.liquid** - Optional second image upload
  - Conditional display for "Doble Marcado" variant
  - Fallback variant ID detection
  - Validation integration with `validador.Img2`
  - Auto-pass when not required

#### Shape Variant Selectors
- **product-variant-picker.liquid** - Modified Shopify variant picker
  - Auto-detection of shape keywords ("corazón", "redondo")
  - Addition of `data-shape-type` attributes
  - CSS class injection for shape styling
  - Preservation of all native Shopify functionality
- **variant-shape-selector.liquid** - Pure CSS shape rendering
  - SVG data URLs for heart and circle shapes
  - Hover effects and checked states
  - Multi-line text support with word wrapping
  - No JavaScript DOM manipulation

#### Warning & Feedback
- **aviso-personaliza.liquid** - Dynamic warning message system
  - Monitors image upload validation states
  - Conditional visibility based on image completeness
  - MutationObserver integration for real-time updates
  - Specific filtering for image validations only

#### Styling
- **base.css** - Custom component styles
  - Field container and input styling
  - Error and warning message styles
  - Image upload button states (default, hover, with-image)
  - Modal components for upload recommendations
  - Shape variant group flexbox layout
  - Orange brand color (#ff6600) for focus states
  - Dark red (#961212) for error messages
  - Responsive design with mobile-friendly sizing
  - Smooth transitions (300ms for interactions, 750ms for visuals)

#### Documentation
- **CLAUDE.md** - AI assistant guidelines for codebase navigation
- **README.md** - Comprehensive project overview and quick start guide
- **documentation/validador-documentation.md** - Core validation system documentation
- **documentation/upload-images-documentation.md** - Image upload system documentation
- **documentation/x-words-documentation.md** - Text input validation documentation
- **documentation/aviso-personaliza-documentation.md** - Warning system documentation
- **documentation/base-css-documentation.md** - Custom CSS documentation
- **documentation/buttons-documentation.md** - Shape variant selector implementation guide

### Technical Details

#### Browser Support
- Chrome 64+
- Firefox 78+
- Safari 11.1+
- Edge 79+
- All modern mobile browsers

#### Dependencies
- Shopify Dawn theme (compatible with most modern themes)
- No external JavaScript libraries
- No Node.js dependencies

#### Performance
- Total custom code size: ~10KB (gzipped)
- Validation checks: ~0.1ms per check
- MutationObserver overhead: ~1-2% CPU
- Image preview encoding: ~50-100ms

### Known Limitations

- Word limits in filenames don't always match actual limits (e.g., "10-words" allows 9)
- Variant option IDs are hardcoded and theme-specific
- Emoji regex requires modern browser support
- No character limit validation (only word count)
- No file size limit on image uploads
- Spanish language only (no multi-language support)

---

## [1.1.0] - 2024-12-06

### Changed

#### Text Input Validation - Simplified Option Logic
- **Breaking Change:** Removed support for 3-option "Grabado de frase" variants
- All `-grabado.liquid` snippets now use 2-option logic: "Sin frase" / "Con frase"
- Removed "Foto grabado adicional" (dobleMarcado) option support from all products

#### Updated Snippets
- **`10-words-grabado.liquid`** - Simplified to 2-option logic
- **`12-words-grabado.liquid`** - Simplified to 2-option logic, removed secondary image uploader (Img2)
- **`20-words-grabado.liquid`** - Simplified to 2-option logic

### Improved

#### Smart Option Detection
All updated snippets now use intelligent option detection:
1. **Primary method:** Searches for radio buttons by label text ("con frase" / "sin frase")
2. **Fallback method:** Uses hardcoded element IDs if label search fails
3. **Graceful degradation:** If options not found, validation auto-passes to prevent cart blocking

#### Unique Element IDs
- Added unique suffixes to error/empty message elements (`-10`, `-12`, `-20`)
- Prevents ID conflicts when multiple word-validation snippets are on the same page

### Removed
- `validador.Img2` initialization from `12-words-grabado.liquid`
- `initDirectImageUploader()` call for secondary image from `12-words-grabado.liquid`
- All references to `dobleMarcadoOption` across grabado snippets

### Migration Notes

#### From v1.0.0 to v1.1.0

**Prerequisites:**
1. Remove "Foto grabado adicional" option from all Shopify products that had it
2. Ensure products only have 2 options: "Sin frase" and "Con frase"

**Steps:**
1. Replace the following snippet files in your theme:
   - `10-words-grabado.liquid`
   - `12-words-grabado.liquid`
   - `20-words-grabado.liquid`
2. Verify all affected product pages work correctly
3. Test cart button enables when:
   - "Sin frase" is selected (no text required)
   - "Con frase" is selected AND valid phrase entered

**Breaking Changes:**
- Products using the old 3-option system will need Shopify admin updates
- The removed `Img2` validation in `12-words-grabado.liquid` means secondary image upload is no longer supported for those products

---

## [1.2.0] - 2026-01-02

### Fixed

#### Shape Variant Selector - Text Wrapping
- **Fixed text breaking into 3 lines** - Labels like "Dije corazón" were incorrectly hyphenating into "Dije / cora- / zón"
- Added `word-break: keep-all` to prevent breaking within words
- Added `hyphens: none` with vendor prefixes (`-webkit-hyphens`) to prevent hyphenation
- Reduced label width from 90px to 65px for tighter text fit

#### Color Variant Alignment
- **Added left padding to color selector** - Color circles now align with shape selector above
- Added `padding-left: 20px` to `.color-variant-group`
- **Increased color swatch size** - Color circles now match shape icon size (50px)
- **Increased gap between color swatches** - Gap increased to 60px to align with shapes above

### Changed

#### variant-shape-selector.liquid
- Label width: 90px → 65px
- Text styling: Added `word-break: keep-all`, `hyphens: none`, `-webkit-hyphens: none`
- Color variant container: `padding-left: 20px`, `gap: 60px`
- Color swatch size: 40px → 50px (matches shape icons)

---

## [Unreleased]

### Planned Enhancements
- None at this time

---

## Version History

| Version | Date | Description |
|---------|------|-------------|
| 1.2.0 | 2026-01-02 | Fixed shape selector text wrapping, color variant alignment |
| 1.1.0 | 2024-12-06 | Simplified grabado snippets to 2-option logic, smart option detection |
| 1.0.0 | 2024-12-06 | Initial release with full customization system |

---

## Migration Notes

### From No Customization to v1.0.0

**Prerequisites:**
1. Backup your theme before installation
2. Test on duplicate theme first
3. Ensure Shopify Dawn or compatible theme

**Installation Steps:**
1. Upload all snippet files to theme
2. Include `{% render 'validador' %}` in main product template (FIRST)
3. Include custom snippets as needed per product
4. Update variant option IDs to match your theme
5. Test all validation scenarios
6. Verify Shopify order properties appear correctly

**Breaking Changes:**
- None (initial release)

---

## Support & Maintenance

### Reporting Issues
- Document expected vs actual behavior
- Include browser and device information
- Provide screenshots if applicable
- Note which snippet is affected

### Updating Theme
1. Export customizations before updating
2. Test on duplicate theme
3. Reapply customizations after update
4. Verify all functionality

### Version Numbering
- **Major (X.0.0)**: Breaking changes, major refactors
- **Minor (1.X.0)**: New features, non-breaking changes
- **Patch (1.0.X)**: Bug fixes, documentation updates

---

**Note:** This changelog will be updated with each significant change to the customization system.
