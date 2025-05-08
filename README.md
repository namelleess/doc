# Template Documentation

## File Structure
Required files:
1. `index.html` - The main template file using [Mustache templating](https://mustache.github.io/)
2. `template.json` - Configuration file defining the template structure

Additional assets:
- All other files (CSS, JavaScript, images, fonts, etc.) will be copied as-is to the output directory during the build process
- You can organize these in folders (e.g., `/css`, `/js`, `/images`, `/fonts`) as needed

## Global Context

All templates have access to a global context object in Mustache that includes:
- `seoId` - The unique identifier for the site in Adkey SEO API

## JSON Structure

### Top-Level Structure

```json
{
  "sections": [
    /* Array of Section objects */
  ],
  "placeholders": {
    /* API response placeholders for preview mode */
  }
}
```

### Section Object

```json
{
  "id": "hero",           // Unique identifier (required)
  "name": "Hero Section", // Human-readable name for the editor (required)
  "fields": [             // Array of field definitions (required, min 1)
    /* Field definitions */
  ]
}
```

### Field Definition

```json
{
  "id": "title",        // Unique identifier within section (required)
  "name": "Title",      // Human-readable label (required)
  "type": "text",       // Field type (required)
  "default": "Welcome!" // Default value (required, type depends on field type)
  // Additional type-specific properties
}
```

## Field Types

### Primitive Types

| Type | Default Type | Description |
|------|--------------|-------------|
| `text` | string | Single-line text input |
| `textarea` | string | Multi-line text input |
| `image` | string (URL) | Image URL |
| `boolean` | boolean | True/false toggle |
| `number` | number | Numeric value |
| `color` | string (hex) | Color picker (format: `#RRGGBB`) |

### Select Type

Dropdown with predefined options:

```json
{
  "id": "level",
  "name": "Experience Level",
  "type": "select",
  "options": [
    { "value": "beginner", "label": "Beginner" },
    { "value": "intermediate", "label": "Intermediate" },
    { "value": "expert", "label": "Expert" }
  ],
  "default": "intermediate"
}
```

### Font Type

Font selector with optional custom input:

```json
{
  "id": "fontPrimary",
  "name": "Primary Font",
  "type": "font",
  "options": [
    { "value": "Inter", "label": "Inter" },
    { "value": "Roboto", "label": "Roboto" },
    { "value": "Open Sans", "label": "Open Sans" }
  ],
  "default": "Inter",
  "allowCustom": true
}
```

### Object Type

Grouped fields:

```json
{
  "id": "ctaButton",
  "name": "Call to Action Button",
  "type": "object",
  "fields": [
    { "id": "label", "name": "Button Text", "type": "text", "default": "Learn More" },
    { "id": "url", "name": "Button URL", "type": "text", "default": "#" },
    { "id": "color", "name": "Button Color", "type": "color", "default": "#3366FF" }
  ],
  "default": {
    "label": "Get Started",
    "url": "#signup",
    "color": "#3366FF"
  }
}
```

### Array Type

Repeatable items:

```json
{
  "id": "testimonials",
  "name": "Testimonials",
  "type": "array",
  "itemType": {
    "type": "object",
    "fields": [
      { "id": "name", "name": "Customer Name", "type": "text", "default": "" },
      { "id": "quote", "name": "Quote", "type": "textarea", "default": "" },
      { "id": "avatar", "name": "Avatar", "type": "image", "default": "http://localhost:3000/images/default-avatar.png" }
    ]
  },
  "default": [
    {
      "name": "Jane Doe",
      "quote": "This product changed my life!",
      "avatar": "(http://localhost:3000/images/default-avatar.png"
    }
  ]
}
```

## Additional Points

1. `id` field would be converted into a JSON object during build and preview, so it would be scope unique and follow principles for json object keys.
2. Maximum nesting depth: 3 levels of object/array nesting
4. All IDs must be unique within their scope
5. All required fields must be provided
6. Values must match their specified types

## Placeholders

Placeholders are used during preview rendering to simulate API responses:

```json
{
  "placeholders": {
    "https://api.adkey-seo.com/api/website/get-website/{{seoId}}": { /* server response json */},
}
```

Key points:
- Placeholders only used during preview, and will not affect the final build
- Each key is the URL of a potential API request
- The value is the mocked JSON response
- All XHR requests must have a corresponding placeholder or they will be blocked in preview mode

## Complete Example

Here's a complete example of a simple template:

### template.json
```json
{
  "sections": [
    {
      "id": "header",
      "name": "Header",
      "fields": [
        {
          "id": "logo",
          "name": "Logo",
          "type": "image",
          "default": "/images/logo.png"
        },
        {
          "id": "navigation",
          "name": "Navigation",
          "type": "array",
          "itemType": {
            "type": "object",
            "fields": [
              { "id": "label", "name": "Label", "type": "text", "default": "" },
              { "id": "url", "name": "URL", "type": "text", "default": "#" }
            ]
          },
          "default": [
            { "label": "Home", "url": "/" },
            { "label": "About", "url": "/about" },
            { "label": "Contact", "url": "/contact" }
          ]
        }
      ]
    },
    {
      "id": "hero",
      "name": "Hero Section",
      "fields": [
        {
          "id": "title",
          "name": "Title",
          "type": "text",
          "default": "Welcome to Our Website"
        },
        {
          "id": "subtitle",
          "name": "Subtitle",
          "type": "textarea",
          "default": "We provide the best services for your needs"
        },
        {
          "id": "backgroundImage",
          "name": "Background Image",
          "type": "image",
          "default": "/images/hero-bg.jpg"
        },
        {
          "id": "cta",
          "name": "Call to Action",
          "type": "object",
          "fields": [
            { "id": "text", "name": "Button Text", "type": "text", "default": "Get Started" },
            { "id": "url", "name": "Button URL", "type": "text", "default": "#contact" },
            { "id": "style", "name": "Button Style", "type": "select", "options": [
              { "value": "primary", "label": "Primary" },
              { "value": "secondary", "label": "Secondary" },
              { "value": "outline", "label": "Outline" }
            ], "default": "primary" }
          ],
          "default": {
            "text": "Learn More",
            "url": "#about",
            "style": "primary"
          }
        }
      ]
    },
    {
      "id": "features",
      "name": "Features",
      "fields": [
        {
          "id": "sectionTitle",
          "name": "Section Title",
          "type": "text",
          "default": "Our Features"
        },
        {
          "id": "items",
          "name": "Feature Items",
          "type": "array",
          "itemType": {
            "type": "object",
            "fields": [
              { "id": "icon", "name": "Icon", "type": "image", "default": "/images/icon-default.svg" },
              { "id": "title", "name": "Title", "type": "text", "default": "" },
              { "id": "description", "name": "Description", "type": "textarea", "default": "" }
            ]
          },
          "default": [
            {
              "icon": "/images/icon-fast.svg",
              "title": "Lightning Fast",
              "description": "Our solution is optimized for speed and performance."
            },
            {
              "icon": "/images/icon-secure.svg",
              "title": "Secure",
              "description": "Your data is always protected with enterprise-grade security."
            }
          ]
        }
      ]
    }
  ],
  "placeholders": {
    "https://api.adkey-seo.com/api/website/get-website/{{seoId}}": {
      "id": "demo-site",
      "name": "Demo Website",
      "domain": "example.com",
      "settings": {
        "primaryColor": "#3366FF",
        "language": "en"
      }
    },
    "https://api.adkey-seo.com/api/content/get-pages/{{seoId}}": [
      {
        "id": "home",
        "title": "Home",
        "slug": "/"
      },
      {
        "id": "about",
        "title": "About Us",
        "slug": "/about"
      },
      {
        "id": "services",
        "title": "Services",
        "slug": "/services"
      },
      {
        "id": "contact",
        "title": "Contact",
        "slug": "/contact"
      }
    ]
  }
}
```

### index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{header.title}} - Website Template</title>
  <link rel="stylesheet" href="css/styles.css">
</head>
<body>
  <!-- Header Section -->
  <header>
    <div class="container">
      <div class="logo">
        <img src="{{header.logo}}" alt="Logo">
      </div>
      <nav>
        <ul>
          {{#header.navigation}}
            <li><a href="{{url}}">{{label}}</a></li>
          {{/header.navigation}}
        </ul>
      </nav>
    </div>
  </header>

  <!-- Hero Section -->
  <section class="hero" style="background-image: url('{{hero.backgroundImage}}')">
    <div class="container">
      <h1>{{hero.title}}</h1>
      <p>{{hero.subtitle}}</p>
      <a href="{{hero.cta.url}}" class="btn btn-{{hero.cta.style}}">{{hero.cta.text}}</a>
    </div>
  </section>

  <!-- Features Section -->
  <section class="features">
    <div class="container">
      <h2>{{features.sectionTitle}}</h2>
      <div class="feature-grid">
        {{#features.items}}
          <div class="feature-item">
            <img src="{{icon}}" alt="{{title}} icon">
            <h3>{{title}}</h3>
            <p>{{description}}</p>
          </div>
        {{/features.items}}
      </div>
    </div>
  </section>

  <!-- Example of using the global seoId -->
  <footer>
    <div class="container">
      <p>Website ID: {{seoId}}</p>
    </div>
  </footer>

  <script src="js/main.js"></script>
</body>
</html>
```
