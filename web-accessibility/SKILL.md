---
name: web-accessibility
description: Build accessible web applications following WCAG guidelines. Use when implementing ARIA patterns, keyboard navigation, screen reader support, or ensuring accessibility compliance.
license: MIT
source: hoodini/ai-agents-skills
---

# Web Accessibility (WCAG 2.1)

## ARIA Patterns

### Modal Dialog
```tsx
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
>
  <h2 id="dialog-title">Confirm Action</h2>
  <button onClick={onClose}>Close</button>
</div>
```

### Navigation Menu
```tsx
<nav aria-label="Main navigation">
  <ul role="menubar">
    <li role="none">
      <a role="menuitem" href="/">Home</a>
    </li>
  </ul>
</nav>
```

## Keyboard Navigation

```tsx
function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (isOpen) modalRef.current?.focus();
  }, [isOpen]);

  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Escape') onClose();
  };

  return (
    <div
      ref={modalRef}
      tabIndex={-1}
      onKeyDown={handleKeyDown}
    >
      {children}
    </div>
  );
}
```

## Color Contrast

Minimum contrast ratios (WCAG AA):
- Normal text: 4.5:1
- Large text (18pt+): 3:1
- UI components: 3:1

## Accessible Forms

```tsx
<div>
  <label htmlFor="email">
    Email <span aria-hidden="true">*</span>
    <span className="sr-only">(required)</span>
  </label>
  <input
    id="email"
    type="email"
    aria-required="true"
    aria-describedby="email-error"
  />
  {errors.email && (
    <span id="email-error" role="alert">{errors.email}</span>
  )}
</div>
```

## Screen Reader Only

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}
```

## Resources

- **WCAG 2.1**: https://www.w3.org/WAI/WCAG21/quickref/
- **ARIA Practices**: https://www.w3.org/WAI/ARIA/apg/
