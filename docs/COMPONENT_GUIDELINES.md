# Component Guidelines

This document defines the standards and conventions for creating Vue components in this design system.

---

## 1. When to Create a Component

Create a new component when:

### ✅ DO create a component if:
- The element is reused **2+ times** across the application
- The element has **complex logic** that should be isolated
- The element is part of the **design system** (buttons, inputs, cards, etc.)
- The element has **multiple variants** or states

### ❌ DON'T create a component if:
- Used only once and has simple HTML (keep it in the page)
- It's just a wrapper with no logic or styling
- Over-abstracting would make the code harder to understand

### Examples:
- ✅ `MyComponentA` - Reused everywhere, multiple variants
- ✅ `MyComponentB` - Complex layout, used in lists
- ❌ `MySimpleWrapper` - Just `<h1>{{ title }}</h1>`, keep inline

---

## 2. File Structure

### Standard structure
Every component must follow this structure:

```
components/
└── MyComponent/
    ├── MyComponent.vue           # Component implementation
    ├── MyComponent.stories.ts    # Storybook documentation
    └── MyComponent.spec.ts       # Unit tests
```

### Rules
- **Always use a folder** for each component
- **Always include** all three files (`.vue`, `.stories.ts`, `.spec.ts`)
- **Component name** must match the folder name
- **One component per folder** - no nesting
- **All files must be TypeScript** (`.ts` extension)
- **Sub-components** must be separate components with their own folder

### Examples

✅ **Correct:**
```
components/
├── MyComponentA/
│   ├── MyComponentA.vue
│   ├── MyComponentA.stories.ts
│   └── MyComponentA.spec.ts
├── MyComponentAIcon/           # Sub-component as separate component
│   ├── MyComponentAIcon.vue
│   ├── MyComponentAIcon.stories.ts
│   └── MyComponentAIcon.spec.ts
```

❌ **Incorrect:**
```
components/
├── MyComponentA.vue            # Missing folder
├── MyComponentB/
│   ├── MyComponentB.vue
│   └── MyComponentB.stories.js # Wrong extension (.js instead of .ts)
└── MyComponentC/
    ├── MyComponentC.vue
    ├── MyComponentC.stories.ts
    ├── MyComponentC.spec.ts
    └── ComponentIcon.vue       # Sub-component in same folder
```

---

## 3. Naming Conventions

### Component names
- **Always use PascalCase** with `My` prefix
- **Multi-word names** (avoid single words)
- **Descriptive and specific**

```typescript
// ✅ Good
MyComponentA.vue
MyComponentB.vue
MyComponentC.vue

// ❌ Bad
Component.vue         // Missing prefix
my-component.vue      // Wrong case
MyCmp.vue            // Abbreviation
MyData.vue           // Too generic (card? list? table?)
```

### Props naming
- **camelCase** in JavaScript/TypeScript
- **kebab-case** in templates

```vue
<script setup lang="ts">
// camelCase in script
interface Props {
  userName: string
  isDisabled: boolean
}
</script>

<template>
  <!-- kebab-case in template -->
  <MyComponent :user-name="name" :is-disabled="true" />
</template>
```

### Emits naming
- **Always use kebab-case**
- **Use verb prefixes** (click, change, update, submit, etc.)

```typescript
// ✅ Good
const emit = defineEmits<{
  (e: 'item-clicked'): void
  (e: 'value-changed', value: string): void
  (e: 'data-submitted', data: FormData): void
}>()

// ❌ Bad
const emit = defineEmits<{
  (e: 'click'): void           // Too generic
  (e: 'itemClicked'): void     // camelCase
  (e: 'SUBMIT'): void          // UPPERCASE
}>()
```

### CSS class naming
- **Use BEM methodology** (Block Element Modifier)
- **Prefix with component name** in lowercase with hyphens
- **Always scoped styles**

```vue
<template>
  <div class="my-component my-component--primary">
    <span class="my-component__icon">→</span>
    <span class="my-component__label">Content here</span>
  </div>
</template>

<style scoped>
/* Block */
.my-component { }

/* Element */
.my-component__icon { }
.my-component__label { }

/* Modifier */
.my-component--primary { }
.my-component--disabled { }
</style>
```

**Pattern:**
- Block: `.my-component`
- Element: `.my-component__element`
- Modifier: `.my-component--modifier`

---

## 4. Props and Emits

### Props validation
Always define props with TypeScript interfaces and validation rules.

```typescript
interface Props {
  // Required prop
  title: string
  
  // Optional prop with default
  size?: 'small' | 'medium' | 'large'
  isActive?: boolean
  
  // Complex type
  data?: {
    id: string
    value: string
  }
}

const props = withDefaults(defineProps<Props>(), {
  size: 'medium',
  isActive: false
})
```

### Props rules
- **Always use TypeScript interfaces** for type safety
- **Use union types** for limited options (`'small' | 'medium' | 'large'`)
- **Provide defaults** for optional props with `withDefaults()`
- **Use `?`** for optional props
- **Avoid complex objects** as props when possible (prefer primitives)
- **Order props**: required first, optional after

### Emits declaration
Always declare emits with TypeScript for type safety.

```typescript
interface Emits {
  (e: 'item-clicked'): void
  (e: 'value-changed', value: string): void
  (e: 'data-updated', data: DataType): void
}

const emit = defineEmits<Emits>()

// Usage
const handleAction = () => {
  emit('item-clicked')
}

const handleUpdate = (newValue: string) => {
  emit('value-changed', newValue)
}
```

### Emits rules
- **Always declare all emits** explicitly
- **Always type the payload** (avoid `any`)
- **Use descriptive names** with verb prefix
- **Use kebab-case** for event names
- **Document when the event is emitted** in comments

### Examples

✅ **Good:**
```typescript
interface Props {
  /** The component title */
  title: string
  /** Size variant */
  size?: 'small' | 'medium' | 'large'
}

interface Emits {
  /** Emitted when user interacts with the component */
  (e: 'item-selected'): void
  /** Emitted when internal state changes */
  (e: 'state-changed', isActive: boolean): void
}
```

❌ **Bad:**
```typescript
// No types
const props = defineProps({
  title: String,
  size: String
})

// No emit declaration
const emit = defineEmits()

// Generic event name
emit('click')

// Missing types
(e: 'update', data: any): void
```

---

## 5. Tests

### Test file structure
Every component must have a test file with the `.spec.ts` extension.

```
MyComponent/
├── MyComponent.vue
├── MyComponent.stories.ts
└── MyComponent.spec.ts
```

### Testing tool
Use **Vitest** with `@vue/test-utils` for all component tests.

```bash
# Install dependencies
bun add -D vitest @vue/test-utils happy-dom
```

### Code coverage
- **Minimum**: 70% coverage
- **Target**: 85%+ coverage

### What to test

#### ✅ Always test:
- **Props rendering**: Component displays correctly with different prop values
- **Emits**: Events are emitted with correct payload
- **User interactions**: Clicks, inputs, keyboard events trigger expected behavior
- **Conditional rendering**: Elements appear/disappear based on props or state
- **Computed values**: Derived values calculate correctly
- **CSS classes**: Correct classes are applied based on props/state

#### ❌ Don't test:
- **Implementation details**: Internal variable names, private methods
- **Third-party libraries**: Assume they work (e.g., Vue reactivity)
- **Visual appearance**: CSS rendering, colors, spacing

### Test structure

```typescript
import { mount } from '@vue/test-utils'
import MyComponent from './MyComponent.vue'

describe('MyComponent', () => {
  test('renders with required props', () => {
    const wrapper = mount(MyComponent, {
      props: { title: 'Test Title' }
    })
    
    expect(wrapper.text()).toContain('Test Title')
  })
  
  test('emits event on user action', async () => {
    const wrapper = mount(MyComponent, {
      props: { title: 'Test' }
    })
    
    await wrapper.find('[data-testid="action-trigger"]').trigger('click')
    
    expect(wrapper.emitted('item-clicked')).toBeTruthy()
  })
  
  test('applies correct CSS class', () => {
    const wrapper = mount(MyComponent, {
      props: { variant: 'primary' }
    })
    
    expect(wrapper.classes()).toContain('my-component--primary')
  })
})
```

### Testing rules
- **One `describe` block** per component
- **Clear test names**: Describe what is tested, not how
- **Arrange-Act-Assert pattern**: Setup, execute, verify
- **Test user behavior**, not implementation
- **Use `async/await`** for interactions that update the DOM
- **Use `data-testid`** for reliable element selection

### Test naming conventions

```typescript
// ✅ Good: Describes behavior from user perspective
test('displays error message when validation fails')
test('emits value-changed event with new value')
test('disables action when state is invalid')

// ❌ Bad: Describes implementation
test('sets isError to true')
test('calls handleClick function')
test('updates data property')
```

### Common test patterns

#### Testing props
```typescript
test('displays content from prop', () => {
  const wrapper = mount(MyComponent, {
    props: { content: 'Hello World' }
  })
  
  expect(wrapper.text()).toContain('Hello World')
})
```

#### Testing emits
```typescript
test('emits event with correct payload', async () => {
  const wrapper = mount(MyComponent)
  
  await wrapper.find('[data-testid="submit-action"]').trigger('click')
  
  expect(wrapper.emitted('data-submitted')).toBeTruthy()
  expect(wrapper.emitted('data-submitted')?.[0]).toEqual([{ id: 1 }])
})
```

#### Testing conditional rendering
```typescript
test('shows icon when prop is true', () => {
  const wrapper = mount(MyComponent, {
    props: { showIcon: true }
  })
  
  expect(wrapper.find('[data-testid="icon"]').exists()).toBe(true)
})

test('hides icon when prop is false', () => {
  const wrapper = mount(MyComponent, {
    props: { showIcon: false }
  })
  
  expect(wrapper.find('[data-testid="icon"]').exists()).toBe(false)
})
```

#### Testing user interactions
```typescript
test('updates display when user types', async () => {
  const wrapper = mount(MyComponent)
  
  await wrapper.find('[data-testid="input-field"]').setValue('New Value')
  
  expect(wrapper.emitted('value-changed')?.[0]).toEqual(['New Value'])
})
```

#### Testing CSS classes
```typescript
test('applies variant class', () => {
  const wrapper = mount(MyComponent, {
    props: { variant: 'primary' }
  })
  
  expect(wrapper.classes()).toContain('my-component--primary')
})
```

---

## 6. Documentation

### Documentation location
All component documentation is handled through **Storybook** in the `.stories.ts` file.

```
MyComponent/
├── MyComponent.vue
├── MyComponent.stories.ts    # Documentation here
└── MyComponent.spec.ts
```

### Documentation requirements

Every component must document:

1. **Component purpose**: What the component does
2. **Props**: All props with descriptions
3. **Emits**: All events with descriptions
4. **Variants**: All visual/behavioral variants
5. **States**: All possible states (disabled, loading, error, etc.)
6. **Edge cases**: Long content, empty states, etc.

### JSDoc comments

Add JSDoc comments to props and emits in the `.vue` file:

```typescript
interface Props {
  /** The main title displayed in the component */
  title: string
  
  /** Visual style variant */
  variant?: 'primary' | 'secondary' | 'ghost'
}

interface Emits {
  /** Emitted when user clicks the component */
  (e: 'item-clicked'): void
}
```

### Storybook guidelines

For detailed Storybook setup, story structure, and best practices, see:
→ **[STORYBOOK_GUIDELINES.md](./STORYBOOK_GUIDELINES.md)**

This includes:
- Story file template
- Naming conventions
- Required stories
- ArgTypes configuration
- Controls setup

---

## 7. Component Template

Use this template as the starting point for every new component.

### Basic template structure

```vue
<template>
  <!-- Component HTML structure -->
</template>

<script setup lang="ts">
// Props with TypeScript validation
interface Props {
  /** Description of the prop */
  requiredProp: string
  
  /** Description of optional prop */
  optionalProp?: 'option1' | 'option2'
}

const props = withDefaults(defineProps<Props>(), {
  optionalProp: 'option1'
})

// Events emitted to parent
interface Emits {
  /** Description of when this event is emitted */
  (e: 'event-name'): void
  
  /** Description with payload */
  (e: 'event-with-payload', value: string): void
}

const emit = defineEmits<Emits>()

// Functions
const handleAction = (): void => {
  // Logic here
  emit('event-name')
}
</script>

<style scoped>
/* Component styles using BEM */

/* Block */
.my-component {
  /* Base styles */
}

/* Elements */
.my-component__element {
  /* Element styles */
}

/* Modifiers */
.my-component--modifier {
  /* Modifier styles */
}
</style>
```

### File template order

1. `<template>` - HTML structure
2. `<script setup lang="ts">` - Component logic
   - Props interface
   - Props definition with defaults
   - Emits interface
   - Emits definition
   - Computed (if needed)
   - Functions
3. `<style scoped>` - Component styles (BEM)

### Slots vs Props

**Use Props for:**
- Simple text content
- Type validation needed
- Data from API

**Use Slots for:**
- HTML markup
- Other components inside
- Full layout control

**Best practice:** Provide both for flexibility
```vue
<div><slot>{{ label }}</slot></div>
```

### Dynamic CSS Classes

**Always use computed** for dynamic classes:

```typescript
import { computed } from 'vue'

const componentClass = computed(() => ({
  'my-component': true,
  [`my-component--${props.variant}`]: true,
  'my-component--disabled': props.isDisabled
}))
```

**Exception:** Single simple class can stay in template:
```vue
<div :class="`my-component--${size}`">
```

### Local State (ref)

**Use `ref` when component manages internal state:**
- Hover/focus states
- Dropdown open/closed
- Internal form values before emit
- Temporary UI feedback

```typescript
import { ref } from 'vue'

const isOpen = ref(false)
const internalValue = ref('')
```

**Don't use `ref` for:**
- Displaying props only
- No user interaction
- State controlled by parent

### Complete template example

```vue
<template>
  <div :class="componentClass">
    <span class="my-component__label">{{ label }}</span>
    <div 
      class="my-component__content"
      :disabled="isDisabled"
      @click="handleClick"
    >
      <slot>Default content</slot>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  /** The component label */
  label: string
  
  /** Visual variant */
  variant?: 'primary' | 'secondary'
  
  /** Disables interaction */
  isDisabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  isDisabled: false
})

interface Emits {
  /** Emitted when content is clicked */
  (e: 'content-clicked'): void
}

const emit = defineEmits<Emits>()

// Computed for dynamic classes
const componentClass = computed(() => ({
  'my-component': true,
  [`my-component--${props.variant}`]: true,
  'my-component--disabled': props.isDisabled
}))

// Event handler
const handleClick = (): void => {
  if (!props.isDisabled) {
    emit('content-clicked')
  }
}
</script>

<style scoped>
.my-component {
  display: flex;
  align-items: center;
  gap: 8px;
}

.my-component__label {
  font-weight: 500;
}

.my-component__content {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.my-component--primary .my-component__content {
  background: blue;
  color: white;
}

.my-component--secondary .my-component__content {
  background: gray;
  color: white;
}

.my-component--disabled {
  opacity: 0.5;
  pointer-events: none;
}
</style>
```

---

## Summary

These guidelines ensure:
- **Consistency** across all components
- **Maintainability** through clear structure
- **Type safety** with TypeScript
- **Quality** with mandatory tests
- **Documentation** via Storybook
- **Reusability** through proper design

Follow these guidelines for every component to maintain a high-quality design system.