# Quy ước Code

**Author:** hoangtph <hoangtph@fabbi.com.vn>  
**Last Updated:** May 2024  
**Version:** 1.0.0

## Mục lục
1. [Cấu trúc Dự án](#cấu-trúc-dự-án)
2. [Quy tắc Đặt tên](#quy-tắc-đặt-tên)
3. [Vue 3 & TypeScript Best Practices](#vue-3--typescript-best-practices)
4. [Quản lý State](#quản-lý-state)
5. [Xử lý API & Services](#xử-lý-api--services)
6. [Styling & UI Components](#styling--ui-components)
7. [Performance & Optimization](#performance--optimization)
8. [Testing & Quality Assurance](#testing--quality-assurance)
9. [Linting & Formatting](#linting--formatting)
10. [Security Best Practices](#security-best-practices)
11. [Documentation](#documentation)
12. [Git Workflow](#git-workflow)

## Cấu trúc Dự án

```
raichu_web/
├── assets/              # Static assets (images, fonts, etc.)
├── components/         # Vue components
├── composables/       # Vue composables (hooks)
├── constants/         # Constant values and configurations
├── layouts/           # Page layouts
├── lib/              # Utility functions và shared logic
├── pages/            # Nuxt pages
├── public/           # Public static files
├── schemas/          # Zod schemas for validation
├── server/           # Server-side code
├── styles/           # Global styles và UnoCSS config
├── types/            # TypeScript type definitions
└── utils/            # Helper functions
```

## Quy tắc Đặt tên

### 1. Tổng Quan
- Sử dụng tên có ý nghĩa, dễ đọc và dễ tìm kiếm
- Tránh viết tắt không cần thiết
- Tên phải phản ánh mục đích và chức năng

### 2. Quy tắc Cụ thể

#### Components
```vue
// Tên file: PascalCase
UserProfile.vue
CourseCard.vue

// Tên component: PascalCase
<script setup lang="ts">
defineProps<{
  userId: string
  onSuccess?: () => void
}>()
</script>
```

#### Composables
```typescript
// Tên file: camelCase
useAuth.ts
useDarkMode.ts

// Tên composable: camelCase, bắt đầu bằng "use"
export const useAuth = () => {
  // ...
}
```

#### Types & Interfaces
```typescript
// Tên file: PascalCase
User.types.ts
Course.types.ts

// Tên type/interface: PascalCase
interface UserProfile {
  id: string
  name: string
}

type CourseStatus = 'active' | 'completed' | 'pending'
```

#### Constants
```typescript
// Tên file: camelCase
constants.ts

// Tên constant: SCREAMING_SNAKE_CASE
export const API_BASE_URL = 'https://api.example.com'
export const MAX_RETRY_COUNT = 3
```

#### Functions
```typescript
// Tên function: camelCase
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
- Luôn sử dụng TypeScript strict mode
- Tránh sử dụng `any`
- Sử dụng type inference khi có thể
- Định nghĩa rõ ràng props types cho components

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

## Quản lý State

### 1. Local State
- Sử dụng `ref` cho primitive values
- Sử dụng `reactive` cho objects
- Sử dụng `computed` cho derived state

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

## Xử lý API & Services

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
- Sử dụng UnoCSS utility classes
- Tạo custom components cho các patterns lặp lại
- Sử dụng `@apply` cho các styles phức tạp

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
- Sử dụng dynamic imports cho các components lớn
- Lazy load các routes không cần thiết ngay lập tức

### 2. Memoization
```typescript
// Sử dụng computed cho derived values
const memoizedValue = computed(() => computeExpensiveValue(a.value, b.value))

// Sử dụng watchEffect cho side effects
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
- Sử dụng ESLint và Prettier
- Chạy type checking trước khi commit
- Review code trước khi merge

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
- Không lưu trữ sensitive data trong localStorage
- Sử dụng environment variables cho sensitive configs
- Validate và sanitize user input

### 2. Authentication
- Sử dụng secure HTTP-only cookies
- Implement proper CSRF protection
- Validate tokens và permissions

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
- Cập nhật README khi thêm dependencies mới
- Document các breaking changes
- Cung cấp setup instructions rõ ràng 

## Git Workflow

### 1. Git Configuration
```bash
# Cấu hình Git cho project
git config user.name "hoangtph"
git config user.email "hoangtph@fabbi.com.vn"
```

### 2. Branch Naming
Format: `{type}/{parent-task}/{task-number}-{short-description}`

Ví dụ:
- `feature/RAICHU-50/RAICHU-100-add-user-authentication`
- `bugfix/RAICHU-75/RAICHU-101-fix-login-form-validation`
- `hotfix/RAICHU-90/RAICHU-102-resolve-api-timeout`

Trong đó:
- `type`: feature, bugfix, hotfix
- `parent-task`: Mã task cha (nếu có)
- `task-number`: Mã task từ Jira (RAICHU-XXX)
- `short-description`: Mô tả ngắn gọn về task, sử dụng kebab-case

### 3. Commit Messages
Format: `{type}({task-number}): {description}`

Ví dụ:
```
feat(RAICHU-100): add user authentication
fix(RAICHU-101): resolve login form validation
docs(RAICHU-102): update README with new setup instructions
```

Các loại commit type:
- `feat`: Thêm tính năng mới
- `fix`: Sửa lỗi
- `docs`: Thay đổi documentation
- `style`: Thay đổi format code (không ảnh hưởng đến code)
- `refactor`: Refactor code
- `test`: Thêm/sửa tests
- `chore`: Thay đổi build process hoặc tools

### 4. Pull Request Template
```markdown
## Description
[Describe changes made]

## Related Issue
Closes #RAICHU-100
Parent Task: #RAICHU-50

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Self-test Checklist
- [ ] Code build thành công
- [ ] Không có lỗi lint
- [ ] Tất cả test pass
- [ ] Code đã được format
- [ ] Đã test thủ công các chức năng đã thay đổi
- [ ] Đã review lại code của mình

## Review Checklist
- [ ] Code follows project style
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] All tests passing
- [ ] Branch name follows convention
- [ ] Commit messages follow convention
``` 
