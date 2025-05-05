# Template File Structure
For now required two files:
1. `index.html` - the template file itself, written with `mustache`
2.  `template.json` - the `json` structure in template, described below

# JSON Top-Level Structure

The root object must contain two properties:

1. `sections` (array of Section objects)
2. `placeholders` (object)

```json
{
  "sections": [ /* … see Section below … */ ],
  "placeholders": { /* custom placeholders for fetch, required, can pass empty object if not used */ }
}
```

# Section Object

Each entry in "sections" is an object with:

1. `id` (string, required). Unique identifier for the section (e.g. "header", "faq").
2. `name` (string, required). Human-readable name displayed in the editor.
3. `fields` (array of FieldDefinition, required, min len 1) The list of fields (controls) in this section.

```json
{
  "id": "hero",
  "name": "Hero Section",
  "fields": [ /* FieldDefinition … */ ]
}
```

# FieldDefinition

Each field in a section must be an object with:

1. `id` (string, required)
2. `name` (string, required)
3. `type` (string, required)
4. `default` (type-dependent, required)
5. Additional type-specific props

```json
{
  "id": "title",
  "name": "Title",
  "type": "text",
  "default": "Welcome!"
}
```

# Supported Field Types

1. Primitive Types:
 
| Type    | Default Type     | Rules                         |
| :------ | :--------------- | :---------------------------- |
| `text`  | string           | any string                    |
| `textarea` | string           | any string                    |
| `image` | string (URL)     | must be a valid URL           |
| `boolean` | boolean          | `true` or `false`             |
| `number`| number           | any numeric value             |
| `color` | string (hex, `#RRGGBB`) | must match `^#[0-9A-Fa-f]{6}$` |

2. `select`
	- A dropdown with predefined options `options (array, required)`
	- Each option ```{ "value": "<any>", "label": "<string>" }```
```json
{
  "id":"level",
  "name":"Level",
  "type":"select",
  "options":[
    { "value":"beginner","label":"Beginner" },
    { "value":"expert","label":"Expert" }
  ],
  "default":"expert"
}
```
3. `font`
	- Like select, but with an optional free-form value:
```json
{
	id: "fontPrimary",
	name: "Primary Font",
  type: "font",
  options: [
	  //value would be replaced with font path or link, depending on template internal structure
	  { value: "Inter", label: "Inter" }, 
	  { value: "Roboto", label: "Roboto" },
	  { value: "Open Sans", label: "Open Sans" },
	  { value: "Montserrat", label: "Montserrat" },
	 ],
	 default: "Inter",
	 allowCustom: true,
}
```
4. `object`
	- A grouped sub-form
	- `fields` (array of FieldDefinition, required)
	- `default`
```json
{
  "id":"ctaButton",
  "name":"CTA Button",
  "type":"object",
  "fields":[
    { "id":"label","name":"Label","type":"text","default":"Click" },
    { "id":"url","name":"URL","type":"text","default":"#"} 
  ],
  "default": { "label":"Learn More","url":"#learn" }
}
```

5. `array`
	- A repeatable list of items of one schema
	- `itemType` (object, required)
		- A FieldDefinition-like schema but without id/name.
		-	It must include type and, if type is:
			-	object → an inner "fields" array
			-	other types → same props as above (e.g. options)
	- default (array)
		- Each element must validate against itemType
```json
{
  "id":"faq",
  "name":"FAQ",
  "type":"array",
  "itemType": {
    "type":"object",
    "fields":[
      { "id":"question","name":"Question","type":"text","default":"" },
      { "id":"answer","name":"Anwser","type":"textarea","default":"" },
    ]
  },
  "default":[
    { "question":"How does it work?","answer":"it doesn't" },
  ]
}
```

# Additional validation rules


1. Max Nesting Depth: 3 levels of object/array nesting.

# Full Example
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
          "default": "http://localhost/slot-machine-logo.svg",
          "description": "Upload your casino logo image"
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
          "default": "Win Big at Our Online Casino!"
        },
        {
          "id": "subtitle",
          "name": "Subtitle",
          "type": "textarea",
          "default": "Experience the thrill of top-notch slots and table games."
        },
        {
          "id": "backgroundImage",
          "name": "Background Image",
          "type": "image",
          "default": "http://localhost/casino-background.jpg"
        },
        {
          "id": "ctaButton",
          "name": "CTA Button",
          "type": "object",
          "fields": [
            {
              "id": "label",
              "name": "Label",
              "type": "text",
              "default": "Play Now"
            },
            {
              "id": "url",
              "name": "URL",
              "type": "text",
              "default": "#games"
            },
            {
              "id": "enabled",
              "name": "Enabled",
              "type": "boolean",
              "default": true
            }
          ]
        }
      ]
    },
    {
      "id": "about",
      "name": "About Section",
      "fields": [
        {
          "id": "title",
          "name": "Title",
          "type": "text",
          "default": "Why Choose Us?"
        },
        {
          "id": "content",
          "name": "Content",
          "type": "textarea",
          "default": "We offer a safe and exciting gaming environment with a wide variety of games and generous bonuses."
        },
        {
          "id": "profileImage",
          "name": "Profile Image",
          "type": "image",
          "default": "http://localhost/trust-badges.png"
        },
        {
          "id": "skills",
          "name": "Skills",
          "type": "array",
          "itemType": {
            "type": "object",
            "fields": [
              {
                "id": "name",
                "name": "Name",
                "type": "text",
                "default": "Fair Play"
              },
              {
                "id": "level",
                "name": "Level",
                "type": "select",
                "options": [
                  { "value": "beginner", "label": "Beginner" },
                  { "value": "intermediate", "label": "Intermediate" },
                  { "value": "advanced", "label": "Advanced" },
                  { "value": "expert", "label": "Expert" }
                ],
                "default": "expert"
              }
            ]
          },
          "default": [
            { "name": "Security", "level": "expert" },
            { "name": "Customer Support", "level": "advanced" },
            { "name": "Game Variety", "level": "expert" },
            { "name": "Fast Payouts", "level": "advanced" }
          ]
        }
      ]
    },
    {
      "id": "projects",
      "name": "Projects Section",
      "fields": [
        {
          "id": "title",
          "name": "Title",
          "type": "text",
          "default": "Popular Games"
        },
        {
          "id": "projects",
          "name": "Projects",
          "type": "array",
          "itemType": {
            "type": "object",
            "fields": [
              {
                "id": "title",
                "name": "Title",
                "type": "text",
                "default": "Slot Game"
              },
              {
                "id": "description",
                "name": "Description",
                "type": "textarea",
                "default": "Spin the reels for a chance to win big!"
              },
              {
                "id": "image",
                "name": "Image",
                "type": "image",
                "default": "http://localhost/slot-game-thumbnail.jpg"
              },
              {
                "id": "url",
                "name": "URL",
                "type": "text",
                "default": "#"
              },
              {
                "id": "featured",
                "name": "Featured",
                "type": "boolean",
                "default": true
              }
            ]
          },
          "default": [
            {
              "title": "Mega Moolah",
              "description": "Progressive jackpot slot with massive payouts.",
              "image": "http://localhost/mega-moolah.jpg",
              "url": "#mega-moolah",
              "featured": true
            },
            {
              "title": "Blackjack",
              "description": "Classic card game with exciting variants.",
              "image": "http://localhost/blackjack.jpg",
              "url": "#blackjack",
              "featured": false
            },
            {
              "title": "Starburst",
              "description": "Popular slot with vibrant graphics and expanding wilds.",
              "image": "http://localhost/starburst.jpg",
              "url": "#starburst",
              "featured": true
            }
          ]
        }
      ]
    },
    {
      "id": "contact",
      "name": "Contact Section",
      "fields": [
        {
          "id": "title",
          "name": "Title",
          "type": "text",
          "default": "Get in Touch"
        },
        {
          "id": "email",
          "name": "Email",
          "type": "text",
          "default": "support@examplecasino.com"
        },
        {
          "id": "phone",
          "name": "Phone",
          "type": "text",
          "default": "+1 (800) 555-1234"
        },
        {
          "id": "address",
          "name": "Address",
          "type": "textarea",
          "default": "Virtual Casino Lane, Internet City, Cyberspace"
        },
        {
          "id": "socialLinks",
          "name": "Social Links",
          "type": "array",
          "itemType": {
            "type": "object",
            "fields": [
              {
                "id": "platform",
                "name": "Platform",
                "type": "select",
                "options": [
                  { "value": "twitter", "label": "Twitter" },
                  { "value": "facebook", "label": "Facebook" },
                  { "value": "instagram", "label": "Instagram" },
                  { "value": "linkedin", "label": "LinkedIn" },
                  { "value": "github", "label": "GitHub" }
                ],
                "default": "twitter"
              },
              {
                "id": "url",
                "name": "URL",
                "type": "text",
                "default": "https://twitter.com/examplecasino"
              }
            ]
          },
          "default": [
            {
              "platform": "twitter",
              "url": "https://twitter.com/examplecasino"
            },
            {
              "platform": "facebook",
              "url": "https://facebook.com/examplecasino"
            }
          ]
        }
      ]
    },
    {
      "id": "footer",
      "name": "Footer",
      "fields": [
        {
          "id": "copyright",
          "name": "Copyright Text",
          "type": "text",
          "default": "© 2023 Example Casino. All rights reserved. Gamble Responsibly."
        },
        {
          "id": "showSocialLinks",
          "name": "Show Social Links",
          "type": "boolean",
          "default": true
        },
        {
          "id": "backgroundColor",
          "name": "Background Color",
          "type": "color",
          "default": "#0d47a1"
        },
        {
          "id": "textColor",
          "name": "Text Color",
          "type": "color",
          "default": "#e3f2fd"
        }
      ]
    },
    {
      "id": "seo",
      "name": "SEO Settings",
      "fields": [
        {
          "id": "title",
          "name": "Page Title",
          "type": "text",
          "default": "Example Casino | Play Online Slots & Games"
        },
        {
          "id": "description",
          "name": "Meta Description",
          "type": "textarea",
          "default": "Play the best online casino games including slots, blackjack, roulette, and more. Generous bonuses and fast payouts."
        },
        {
          "id": "keywords",
          "name": "Keywords",
          "type": "text",
          "default": "online casino, slots, blackjack, roulette, gambling, real money games, casino bonuses"
        },
        {
          "id": "ogImage",
          "name": "Social Share Image",
          "type": "image",
          "default": "http://localhost/casino-share-image.jpg"
        }
      ]
    },
    {
      "id": "theme",
      "name": "Theme Settings",
      "fields": [
        {
          "id": "primaryColor",
          "name": "Primary Color",
          "type": "color",
          "default": "#ffb300"
        },
        {
          "id": "secondaryColor",
          "name": "Secondary Color",
          "type": "color",
          "default": "#f57f17"
        },
        {
          "id": "fontPrimary",
          "name": "Primary Font",
          "type": "font",
          "options": [
            { "value": "Inter", "label": "Inter" },
            { "value": "Roboto", "label": "Roboto" },
            { "value": "Open Sans", "label": "Open Sans" },
            { "value": "Montserrat", "label": "Montserrat" },
            { "value": "custom", "label": "Custom Font" }
          ],
          "default": "Montserrat",
          "allowCustom": true
        },
        {
          "id": "fontSecondary",
          "name": "Secondary Font",
          "type": "font",
          "options": [
            { "value": "Inter", "label": "Inter" },
            { "value": "Roboto", "label": "Roboto" },
            { "value": "Open Sans", "label": "Open Sans" },
            { "value": "Montserrat", "label": "Montserrat" },
            { "value": "custom", "label": "Custom Font" }
          ],
          "default": "Open Sans",
          "allowCustom": false
        }
      ]
    },
    {
      "id": "analytics",
      "name": "Analytics",
      "fields": [
        {
          "id": "googleAnalyticsId",
          "name": "Google Analytics ID",
          "type": "text",
          "default": "UA-XXXXXX-X"
        },
        {
          "id": "facebookPixelId",
          "name": "Facebook Pixel ID",
          "type": "text",
          "default": "XXXXXXXXXXXXXXX"
        },
        {
          "id": "hotjarId",
          "name": "Hotjar ID",
          "type": "text",
          "default": "XXXXXX"
        }
      ]
    }
  ],
  "placeholders": {}
}
```
