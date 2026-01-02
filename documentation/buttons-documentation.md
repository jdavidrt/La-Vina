# Shopify Shape Variant Selector Documentation

## Overview

This implementation adds custom shape-based visual selectors for product variants in Shopify. Instead of traditional pill-shaped buttons, variants are displayed as geometric shapes (hearts, circles) with labels underneath.

**Use Case:** Perfect for products where the variant itself represents a shape (e.g., jewelry pendants, decorative items).

---

## Features

- ✅ **Auto-detection**: Automatically identifies shape variants based on keywords
- ✅ **Pure CSS**: Uses SVG backgrounds for shapes, no JavaScript DOM manipulation
- ✅ **Responsive**: Text wraps to multiple lines when needed
- ✅ **Accessible**: Maintains all Shopify functionality and screen reader compatibility
- ✅ **Extensible**: Easy to add new shapes

---

## How It Works

### Technical Architecture

The solution consists of two files that work together:

1. **`product-variant-picker.liquid`** - Template Structure

   - Scans product options for shape keywords
   - Adds special CSS classes to shape variants
   - Adds `data-shape-type` attributes to labels
   - Preserves all Shopify's native HTML structure

2. **`variant-shape-selector.liquid`** - Styling & Display
   - Contains pure CSS for shape rendering
   - Uses SVG data URLs for precise shapes
   - No JavaScript modification of DOM elements
   - Only observes changes (doesn't interfere)

### Detection Logic

```liquid
{% for value in option.values %}
  {% if value contains 'corazón' %}
    {% assign shape_type = 'heart' %}
  {% elsif value contains 'redondo' %}
    {% assign shape_type = 'circle' %}
  {% endif %}
{% endfor %}
```

**Trigger words:**

- `corazón` → Heart shape
- `redondo` → Circle shape

### CSS Approach

Uses CSS pseudo-elements (`::before`) with SVG backgrounds:

```css
.shape-variant-group label[data-shape-type="heart"]::before {
  background-image: url("data:image/svg+xml,...");
}
```

**Why SVG data URLs?**

- No external file dependencies
- Scales perfectly at any size
- Easy to modify colors (white/black)
- Works in all modern browsers

---

## Installation

### Step 1: Backup Your Theme

Before making changes:

1. Go to **Online Store → Themes**
2. Click **Actions → Duplicate** on your live theme
3. Work on the duplicate first

### Step 2: Edit Code

1. Go to **Online Store → Themes → Actions → Edit code**

### Step 3: Modify Product Variant Picker

**Location:** `snippets/product-variant-picker.liquid`

**Action:** Replace the entire file with the code provided in this implementation.

**Key sections:**

```liquid
{% comment %} Auto-detect shape variants {% endcomment %}
{% assign has_shapes = false %}
{% for value in option.values %}
  {% if value contains 'corazón' or value contains 'redondo' %}
    {% assign has_shapes = true %}
  {% endif %}
{% endfor %}
```

### Step 4: Create/Update Shape Selector Snippet

**Location:** `snippets/variant-shape-selector.liquid`

**Action:**

- If file exists: Replace it
- If file doesn't exist: Create new snippet with this name

**What it contains:**

- CSS styling for shapes
- SVG definitions for heart and circle
- Hover and checked states

### Step 5: Verify Integration

The variant picker should already include:

```liquid
{% render 'variant-shape-selector' %}
```

This line loads the styling snippet.

### Step 6: Test

1. Preview your theme
2. Navigate to a product with shape variants
3. Verify shapes appear correctly
4. Test variant selection functionality
5. Check mobile responsiveness

---

## File Structure

```
your-theme/
├── snippets/
│   ├── product-variant-picker.liquid  ← Modifies HTML structure
│   └── variant-shape-selector.liquid  ← Contains CSS styling
└── sections/
    └── main-product.liquid            ← Calls the picker snippet
```

---

## Customization Guide

### Adding New Shapes

To add a new shape (e.g., square):

#### 1. Update Detection Logic (`product-variant-picker.liquid`)

```liquid
{% elsif value contains 'cuadrado' %}
  {% assign shape_type = 'square' %}
```

#### 2. Add CSS for New Shape (`variant-shape-selector.liquid`)

```css
/* Square shape */
.shape-variant-group label[data-shape-type="square"]::before {
  content: "" !important;
  position: absolute !important;
  top: 0 !important;
  left: 50% !important;
  transform: translateX(-50%) !important;
  width: 50px !important;
  height: 50px !important;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'%3E%3Crect x='10' y='10' width='80' height='80' fill='white' stroke='black' stroke-width='2'/%3E%3C/svg%3E") !important;
  background-size: contain !important;
  background-repeat: no-repeat !important;
  background-position: center !important;
  transition: all 0.3s ease !important;
}

/* Square checked state */
.shape-variant-group
  input[type="radio"]:checked
  + label[data-shape-type="square"]::before {
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'%3E%3Crect x='10' y='10' width='80' height='80' fill='black' stroke='black' stroke-width='2'/%3E%3C/svg%3E") !important;
}

/* Square hover */
.shape-variant-group label[data-shape-type="square"]:hover::before {
  transform: translateX(-50%) scale(1.1) !important;
}
```

### Changing Shape Size

Modify these values in `variant-shape-selector.liquid`:

```css
width: 50px !important; /* Change to desired size */
height: 50px !important; /* Keep same as width for consistency */
```

Also adjust label padding:

```css
.shape-variant-group label {
  padding-top: 60px !important; /* Should be ~10px more than shape height */
}
```

### Changing Colors

#### For unchecked state:

Find the SVG with `fill='white'` and change to desired color.

#### For checked state:

Find the SVG with `fill='black'` and change to desired color.

**Example - Blue when selected:**

```css
background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'%3E%3Ccircle cx='50' cy='50' r='45' fill='%230066cc' stroke='black' stroke-width='2'/%3E%3C/svg%3E") !important;
```

**Note:** URL-encode color codes (`#` becomes `%23`)

### Adjusting Text Appearance

```css
.shape-variant-group label .variant-label-text {
  font-size: 12px !important; /* Text size */
  line-height: 1.3 !important; /* Line spacing */
  color: #000 !important; /* Text color */
  padding-top: 5px !important; /* Space from shape */
  word-break: keep-all !important; /* Prevent breaking within words */
  hyphens: none !important; /* Prevent hyphenation */
}
```

**Important:** The `word-break: keep-all` and `hyphens: none` properties prevent text like "corazón" from being hyphenated into "cora-zón" across multiple lines.

### Changing Label Width

To accommodate longer text:

```css
.shape-variant-group label {
  width: 65px !important; /* Default: 65px - increase for longer text */
}
```

**Note:** The default width (65px) is optimized for two-line Spanish labels like "Dije corazón". If your labels are longer, increase this value.

---

## How It Preserves Shopify Functionality

### No DOM Manipulation

Unlike many custom variant solutions, this implementation:

- **Does NOT** modify Shopify's HTML structure with JavaScript
- **Does NOT** remove or replace native elements
- **Does NOT** intercept click events

### What It Does Instead

1. **Adds CSS classes** via Liquid (server-side)
2. **Adds data attributes** via Liquid (server-side)
3. **Styles with pure CSS** (no JS interference)

### Why This Matters

- ✅ Shopify's variant selection logic remains intact
- ✅ URL updates work correctly
- ✅ Inventory tracking works
- ✅ Price updates work
- ✅ "Add to cart" functionality preserved
- ✅ Analytics and tracking continue working
- ✅ Theme updates won't break functionality

---

## Troubleshooting

### Shapes Don't Appear

**Possible causes:**

1. **Snippet not included**

   - Check that `{% render 'variant-shape-selector' %}` is present
   - Should be at the end of `product-variant-picker.liquid`

2. **Wrong keyword in variant value**

   - Verify product variant contains "corazón" or "redondo"
   - Check for typos or extra spaces

3. **CSS conflict**

   - Your theme may have conflicting styles
   - Use browser inspector to check applied styles
   - May need to increase CSS specificity

4. **File not saved**
   - Ensure you clicked "Save" after editing
   - Clear browser cache and reload

### Text Still Shows Ellipsis (...)

Increase label width:

```css
.shape-variant-group label {
  width: 100px !important; /* Or larger */
}
```

### Shapes Overlap Text

Increase padding-top:

```css
.shape-variant-group label {
  padding-top: 70px !important; /* Increase this value */
}
```

### Variant Selection Breaks After Adding Code

**This was a major issue during development.** If variant selection redirects to "undefined":

1. Verify you're using the **latest version** of the code
2. Check that you **didn't modify** Shopify's form structure
3. Ensure `data-url="{{ product.url }}"` is present in `<variant-selects>`
4. Check browser console for JavaScript errors

### Shapes Look Distorted

SVG `viewBox` may be incorrect. Ensure:

```css
background-size: contain !important;
background-repeat: no-repeat !important;
```

### Wrong Shape Appears

Check the detection logic and ensure keywords match exactly:

```liquid
{% if value contains 'corazón' %}  ← Case sensitive!
```

---

## Browser Compatibility

| Browser       | Version    | Support |
| ------------- | ---------- | ------- |
| Chrome        | 90+        | ✅ Full |
| Firefox       | 88+        | ✅ Full |
| Safari        | 14+        | ✅ Full |
| Edge          | 90+        | ✅ Full |
| Mobile Safari | iOS 14+    | ✅ Full |
| Chrome Mobile | Android 5+ | ✅ Full |

**Note:** SVG data URLs are supported in all modern browsers.

---

## Performance Considerations

### File Size

- Total additional code: ~5KB
- SVG data URLs are inlined (no extra HTTP requests)
- No external dependencies

### Load Time Impact

- **Negligible** - CSS loads with page
- No JavaScript execution delay
- No external file fetches

### Render Performance

- Pure CSS rendering is extremely fast
- No JavaScript layout calculations
- No DOM manipulation during runtime

---

## Best Practices

### 1. Use Consistent Naming

Keep variant value names consistent across products:

- ✅ "Dije corazón" (all products)
- ❌ "Corazón dije", "Dije de corazón", "Heart pendant" (inconsistent)

### 2. Test on Mobile

Shapes should be clearly tappable on mobile devices:

- Minimum tap target: 44x44px
- Current implementation: 50x50px ✅

### 3. Consider Accessibility

- Screen readers still read variant text correctly
- Keyboard navigation still works
- Color contrast meets WCAG standards (black on white)

### 4. Don't Mix Shape and Non-Shape Variants

If a product has both shaped and non-shaped variants, only apply to the shape option:

```liquid
{% if option.name == 'Diseño de collar' %}
  <!-- Apply shape styling -->
{% endif %}
```

### 5. Version Control

Keep a copy of your customized files outside Shopify:

- Use Git for version control
- Document any customizations
- Note which theme version you're using

---

## Advanced: Multi-Language Support

To support multiple languages:

### Method 1: Multiple Keywords

```liquid
{% if value contains 'corazón' or value contains 'heart' or value contains 'coeur' %}
  {% assign shape_type = 'heart' %}
{% endif %}
```

### Method 2: Translation Files

Use Shopify's translation system:

```liquid
{% if value contains 'shape.heart' | t %}
  {% assign shape_type = 'heart' %}
{% endif %}
```

Create translations in `locales/`:

```json
{
  "shape": {
    "heart": "corazón"
  }
}
```

---

## Extension Ideas

### 1. Add Animation on Selection

```css
.shape-variant-group
  input[type="radio"]:checked
  + label[data-shape-type="heart"]::before {
  animation: heartbeat 0.3s ease-in-out;
}

@keyframes heartbeat {
  0%,
  100% {
    transform: translateX(-50%) scale(1);
  }
  50% {
    transform: translateX(-50%) scale(1.2);
  }
}
```

### 2. Color Variant Swatches

The implementation includes color variant swatches that align with shape selectors:

```css
.color-variant-group {
  display: flex !important;
  gap: 60px !important;           /* Matches shape selector spacing */
  padding-left: 20px !important;  /* Aligns with shapes above */
}

.color-variant-group label {
  width: 50px !important;         /* Matches shape icon size */
  height: 50px !important;
}
```

**Color definitions:**
- `plateado` (Silver): Gradient from #E8E8E8 to #A8A8A8
- `dorado` (Gold): Gradient from #F5D061 to #C49B0C

### 3. Show Variant-Specific Images

Use JavaScript to show different product images based on selected shape.

### 4. Add Tooltips

Show additional information on hover:

```css
.shape-variant-group label::after {
  content: attr(data-tooltip);
  /* Position and style tooltip */
}
```

---

## Maintenance

### When Updating Your Theme

1. **Export customizations** before updating
2. **Test on duplicate theme** first
3. **Reapply customizations** after theme update
4. **Verify functionality** before publishing

### Regular Checks

- [ ] Verify shapes render correctly (monthly)
- [ ] Test variant selection (after any changes)
- [ ] Check mobile responsiveness (after theme updates)
- [ ] Validate accessibility (quarterly)

---

## Support & Resources

### Official Shopify Documentation

- [Liquid Reference](https://shopify.dev/docs/api/liquid)
- [Theme Architecture](https://shopify.dev/docs/themes/architecture)
- [Variant Selectors](https://shopify.dev/docs/themes/architecture/templates/product#variant-selectors)

### SVG Resources

- [SVG Path Documentation](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths)
- [Data URL Encoder](https://yoksel.github.io/url-encoder/)
- [SVG Shape Generator](https://www.flaticon.com/free-icons/shapes)

### CSS Resources

- [CSS Pseudo-elements](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)
- [Flexbox Guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

---

## Credits

**Implementation Date:** December 2024  
**Shopify Theme Compatibility:** Dawn, Debut, and most modern themes  
**Language:** Liquid, CSS, SVG

**End of Documentation**
