---
name: figma
description: Integrate with Figma API for design automation and code generation. Use when extracting design tokens, generating React/CSS code from Figma components, syncing design systems, or building Figma plugins.
license: MIT
source: hoodini/ai-agents-skills
---

# Figma API Integration

## Quick Start

```typescript
const FIGMA_TOKEN = process.env.FIGMA_TOKEN;
const headers = { 'X-Figma-Token': FIGMA_TOKEN };

const response = await fetch(
  `https://api.figma.com/v1/files/${FILE_KEY}`,
  { headers }
);
```

## Core API Endpoints

```typescript
// Get all components in a file
GET https://api.figma.com/v1/files/:file_key/components

// Export nodes as images
GET https://api.figma.com/v1/images/:file_key?ids=1:2&format=svg
```

## Extract Design Tokens

```typescript
async function extractColors(fileKey: string) {
  const response = await fetch(
    `https://api.figma.com/v1/files/${fileKey}/styles`,
    { headers }
  ).then(r => r.json());
  
  const colors = {};
  for (const style of response.meta.styles) {
    if (style.style_type === 'FILL') {
      // Extract color values
    }
  }
  return colors;
}
```

## Generate Tailwind CSS

```typescript
function figmaToTailwind(node: FigmaNode): string {
  const classes: string[] = [];
  
  if (node.absoluteBoundingBox) {
    const { width, height } = node.absoluteBoundingBox;
    classes.push(`w-[${Math.round(width)}px]`, `h-[${Math.round(height)}px]`);
  }
  
  if (node.fills?.length) {
    const fill = node.fills.find(f => f.type === 'SOLID');
    if (fill?.color) {
      classes.push(`bg-[${rgbaToHex(fill.color)}]`);
    }
  }
  
  return classes.join(' ');
}
```

## Webhooks

```typescript
POST https://api.figma.com/v2/webhooks
{
  "event_type": "FILE_UPDATE",
  "team_id": "123456",
  "endpoint": "https://your-server.com/figma-webhook"
}
```

## Resources

- **Figma API Docs**: https://www.figma.com/developers/api
- **Plugin API Docs**: https://www.figma.com/plugin-docs/
