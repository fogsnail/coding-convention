# Code Conventions 

**Author:** hoangtph <hoangtph@fabbi.com.vn>  
**Last Updated:** May 2024  
**Version:** 1.0.0

## Table of Contents
1. [Project Structure](#project-structure)
2. [Naming Conventions](#naming-conventions)
3. [Vue 3 & TypeScript Best Practices](#vue-3--typescript-best-practices)
4. [State Management](#state-management)
5. [API & Services](#api--services)
6. [Styling & UI Components](#styling--ui-components)
7. [Performance & Optimization](#performance--optimization)
8. [Testing & Quality Assurance](#testing--quality-assurance)
9. [Linting & Formatting](#linting--formatting)
10. [Security Best Practices](#security-best-practices)
11. [Documentation](#documentation)
12. [Git Workflow](#git-workflow)

## Project Structure

```
raichu_web/
├── assets/              # Static assets (images, fonts, etc.)
├── components/         # Vue components
├── composables/       # Vue composables (hooks)
├── constants/         # Constant values and configurations
├── layouts/           # Page layouts
├── lib/              # Utility functions and shared logic
├── pages/            # Nuxt pages
├── public/           # Public static files
├── schemas/          # Zod schemas for validation
├── server/           # Server-side code
├── styles/           # Global styles and UnoCSS config
├── types/            # TypeScript type definitions
└── utils/            # Helper functions
```

## Naming Conventions

### 1. Overview
- Use meaningful, readable, and searchable names
- Avoid unnecessary abbreviations
- Names should reflect purpose and functionality

### 2. Specific Rules

#### Components
```vue
// File name: PascalCase
UserProfile.vue
CourseCard.vue

// Component name: PascalCase
<script setup lang="ts">
defineProps<{
  userId: string
  onSuccess?: () => void
}>()
</script>
```

#### Composables
```typescript
// File name: camelCase
useAuth.ts
useDarkMode.ts

// Composable name: camelCase, starts with "use"
export const useAuth = () => {
  // ...
}
```

#### Types & Interfaces
```typescript
// File name: PascalCase
User.types.ts
Course.types.ts

// Type/interface name: PascalCase
interface UserProfile {
  id: string
  name: string
}

type CourseStatus = 'active' | 'completed' | 'pending'
```

#### Constants
```typescript
// File name: camelCase
constants.ts

// Constant name: SCREAMING_SNAKE_CASE
export const API_BASE_URL = 'https://api.example.com'
export const MAX_RETRY_COUNT = 3
```

#### Functions
```typescript
// Function name: camelCase
const getUserById = (id: string): Promise<User> => {
  // ...
}

// Event handlers: handle + EventName
const handleSubmit = (e: Event) => {
  // ...
}
```

## Vue 3 & TypeScript Best Practices

### 1. Component Structure
```vue
<script setup lang="ts">
// 1. Imports
import { ref, onMounted } from 'vue'
import { useAuth } from '@/composables/useAuth'

// 2. Props & Emits
const props = defineProps<{
  userId: string
  onSuccess?: () => void
}>()

const emit = defineEmits<{
  (e: 'update', value: string): void
}>()

// 3. Composables
const { user } = useAuth()

// 4. State
const isLoading = ref(false)

// 5. Lifecycle
onMounted(() => {
  // ...
})

// 6. Methods
const handleSubmit = async () => {
  // ...
}
</script>

<template>
  <!-- Template content -->
</template>
```

### 2. Type Safety
- Always use TypeScript strict mode
- Avoid using `any`
- Use type inference when possible
- Define clear prop types for components

### 3. Error Handling
```typescript
try {
  const data = await fetchData()
} catch (error) {
  if (error instanceof ApiError) {
    // Handle API error
  } else {
    // Handle unexpected error
  }
}
```

## State Management

### 1. Local State
- Use `ref` for primitive values
- Use `reactive` for objects
- Use `computed` for derived state

### 2. Global State (Pinia)
```typescript
// stores/user.ts
export const useUserStore = defineStore('user', {
  state: () => ({
    user: null as User | null,
  }),
  actions: {
    setUser(user: User) {
      this.user = user
    },
  },
})
```

## API & Services

### 1. API Structure
```typescript
// composables/useApi.ts
export const useApi = () => {
  const get = async <T>(url: string): Promise<T> => {
    // ...
  }
  
  const post = async <T>(url: string, data: unknown): Promise<T> => {
    // ...
  }
  
  return {
    get,
    post,
  }
}

// composables/useUser.ts
export const useUser = () => {
  const api = useApi()
  
  const getUser = (id: string) => api.get<User>(`/users/${id}`)
  const updateUser = (id: string, data: UpdateUserData) => 
    api.post<User>(`/users/${id}`, data)
    
  return {
    getUser,
    updateUser,
  }
}
```

### 2. Error Handling
```typescript
class ApiError extends Error {
  constructor(
    public status: number,
    public message: string,
    public data?: unknown
  ) {
    super(message)
  }
}
```

## Styling & UI Components

### 1. UnoCSS
- Use UnoCSS utility classes
- Create custom components for repeating patterns
- Use `@apply` for complex styles

### 2. Component Styling
```vue
<script setup lang="ts">
defineProps<{
  variant?: 'primary' | 'secondary'
}>()
</script>

<template>
  <button
    :class="[
      'px-4 py-2 rounded-md',
      variant === 'primary' ? 'bg-blue-500 text-white' : 'bg-gray-200 text-gray-800'
    ]"
  >
    <slot />
  </button>
</template>
```

## Performance & Optimization

### 1. Code Splitting
- Use dynamic imports for large components
- Lazy load non-critical routes

### 2. Memoization
```typescript
// Use computed for derived values
const memoizedValue = computed(() => computeExpensiveValue(a.value, b.value))

// Use watchEffect for side effects
watchEffect(() => {
  doSomething(a.value, b.value)
})
```

## Testing & Quality Assurance

### 1. Unit Tests
```typescript
// __tests__/Button.test.ts
describe('Button', () => {
  it('renders correctly', () => {
    const wrapper = mount(Button, {
      props: {
        variant: 'primary',
      },
    })
    expect(wrapper.text()).toBe('Click me')
  })
})
```

### 2. Code Quality
- Use ESLint and Prettier
- Run type checking before commit
- Review code before merge

## Linting & Formatting

### 1. ESLint Rules
```javascript
// eslint.config.js
export default {
  extends: [
    '@nuxtjs/eslint-config-typescript',
    'plugin:vue/vue3-recommended',
  ],
  rules: {
    'no-console': 'warn',
    'vue/multi-word-component-names': 'error',
  },
}
```

### 2. Prettier Config
```json
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

## Security Best Practices

### 1. Data Handling
- Don't store sensitive data in localStorage
- Use environment variables for sensitive configs
- Validate and sanitize user input

### 2. Authentication
- Use secure HTTP-only cookies
- Implement proper CSRF protection
- Validate tokens and permissions

## Documentation

### 1. Code Comments
```typescript
/**
 * Fetches user data from the API
 * @param userId - The ID of the user to fetch
 * @returns Promise resolving to User data
 * @throws {ApiError} If the API request fails
 */
const fetchUser = async (userId: string): Promise<User> => {
  // ...
}
```

### 2. README Updates
- Update README when adding new dependencies
- Document breaking changes
- Provide clear setup instructions

## Git Workflow

### 1. Git Configuration
```bash
# Project-specific Git configuration
git config user.name "hoangtph"
git config user.email "hoangtph@fabbi.com.vn"
```

### 2. Branch Naming
Format: `
