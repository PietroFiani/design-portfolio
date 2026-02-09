# Storybook Component Documentation Guidelines

## 0. Core Principles

**Goal:** Each component must be understandable and usable without reading its source code.

**Required documentation structure:**
1. **Name**: Component name
2. **Description**: What the component does
3. **Specifications**: Variants, Interactions, Interface (Props/Slots/Events)
4. **Usage**: When to use / when not to use

**Rule:** 1 `.stories.ts` file per `.vue` component in the same folder.
```
components/
├─ [ComponentName]/
│  ├─ [ComponentName].vue
│  ├─ [ComponentName].stories.ts
```

---

## 1. .stories.ts File Template
```typescript
import type { Meta, StoryObj } from '@storybook/vue3'
import { userEvent, within, fn } from '@storybook/test'
import [ComponentName] from './[ComponentName].vue'

const meta = {
  title: '[Category]/[ComponentName]',  // Category optional
  component: [ComponentName],
  tags: ['autodocs'],
  
  parameters: {
    docs: {
      description: {
        component: `
[Component description]

**When to use:**
- [Use case]

**When NOT to use:**
- [Anti-pattern]
        `,
      },
    },
  },
  
  argTypes: {
    // Props with options
    [propName]: {
      control: { type: 'select' },  // or 'radio', 'boolean', 'text', 'number', 'color'
      options: ['[option1]', '[option2]'],
    },
    // Events
    [eventName]: {
      table: { category: 'Events' },
    },
    // Slots
    [slotName]: {
      table: { category: 'Slots' },
    },
  },
  
  args: {
    [propName]: '[defaultValue]',
    [eventName]: fn(),
  },
} satisfies Meta<typeof [ComponentName]>

export default meta
type Story = StoryObj<typeof meta>

// Stories
export const Default: Story = {
  args: {},
}

// Variants
export const [VariantName]: Story = {
  args: { [prop]: '[value]' },
}

// Interactions (args)
export const Disabled: Story = {
  args: { disabled: true },
}

// Interactions (play functions - only hover/focus/active)
export const Hover: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement)
    await userEvent.hover(canvas.getByRole('[role]'))
  },
}
```

---

## 2. Functional Categories

- `Navigation`, `Forms`, `Layout`, `Display`, `Feedback`, `Content`
- No category if generic component

---

## 3. Story Creation Rules

**1 story = 1 visual state**

- **Default**: required, always first
- **Variants**: hierarchy, size, options
- **Interactions via args**: disabled, error, success, loading
- **Interactions via play functions**: hover, focus, active only

**Naming:** PascalCase descriptive (`Primary`, `Disabled`, `WithIcon`)

**Order:** Default → Variants → Interactions (args) → Interactions (play functions)

---

## 4. argTypes: When to Customize

- **Enums/unions** → `select` or `radio` with `options`
- **Events** → `table: { category: 'Events' }`
- **Slots** → `table: { category: 'Slots' }`
- **Disable** → `control: false`

---

## 5. Play Functions

**Imports:**
```typescript
import { userEvent, within } from '@storybook/test'
```

**Usage:**
- Only for hover, focus, active
- Always `await` actions
- Maximum 3-4 lines

---

## 6. Validation Checklist

- [ ] `.stories.ts` file with same name as `.vue`
- [ ] `title`, `component`, `tags: ['autodocs']`
- [ ] `parameters.docs.description.component` with usage
- [ ] `Default` story
- [ ] Stories for each variant and interaction
- [ ] `argTypes` for enums/events/slots
- [ ] Events with `fn()`

---

## Files Created

To document a component, create **1 file**:
```
components/
├─ Button/
│  ├─ Button.vue              ← Component (already exists)
│  ├─ Button.stories.ts       ← TO CREATE
```

The `.stories.ts` file automatically generates in Storybook:
- Complete documentation page
- List of variants (stories)
- Interactive controls
- Props/events/slots table