**Author:** HoàngTPH  
**Last Updated:** May 2025  
**Version:** 2.1.1

# Quy ước Code - Raichu Web Project


## Mục lục
1. [Cấu trúc Dự án](#cấu-trúc-dự-án)
2. [Quy tắc Đặt tên](#quy-tắc-đặt-tên)
3. [TypeScript & React Best Practices](#typescript--react-best-practices)
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
├── app/                    # Next.js App Router pages
├── components/            # Shared components
├── hooks/                # Custom React hooks
├── lib/                  # Utility functions và shared logic
├── stores/              # Zustand stores
├── styles/              # Global styles và Tailwind config
├── types/               # TypeScript type definitions
├── utils/               # Helper functions
└── public/              # Static assets
```

## Quy tắc Đặt tên

### 1. Tổng Quan
- Sử dụng tên có ý nghĩa, dễ đọc và dễ tìm kiếm
- Tránh viết tắt không cần thiết
- Tên phải phản ánh mục đích và chức năng

### 2. Quy tắc Cụ thể

#### Components
```typescript
// Tên file: PascalCase
UserProfile.tsx
CourseCard.tsx

// Tên component: PascalCase
const UserProfile: React.FC<UserProfileProps> = () => {
  // ...
}
```

#### Hooks
```typescript
// Tên file: camelCase
useAuth.ts
useDarkMode.ts

// Tên hook: camelCase, bắt đầu bằng "use"
const useAuth = () => {
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
  id: string;
  name: string;
}

type CourseStatus = 'active' | 'completed' | 'pending';
```

#### Constants
```typescript
// Tên file: camelCase
constants.ts

// Tên constant: SCREAMING_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_COUNT = 3;
```

#### Functions
```typescript
// Tên function: camelCase
const getUserById = (id: string): Promise<User> => {
  // ...
}

// Event handlers: handle + EventName
const handleSubmit = (e: React.FormEvent) => {
  // ...
}
```

## TypeScript & React Best Practices

### 1. Component Structure
```typescript
// 1. Imports
import React from 'react';
import { useAuth } from '@/hooks/useAuth';

// 2. Types/Interfaces
interface Props {
  userId: string;
  onSuccess?: () => void;
}

// 3. Component
export const UserProfile: React.FC<Props> = ({ userId, onSuccess }) => {
  // 4. Hooks
  const { user } = useAuth();

  // 5. State
  const [isLoading, setIsLoading] = useState(false);

  // 6. Effects
  useEffect(() => {
    // ...
  }, [userId]);

  // 7. Handlers
  const handleSubmit = async () => {
    // ...
  };

  // 8. Render
  return (
    // ...
  );
};
```

### 2. Type Safety
- Luôn sử dụng TypeScript strict mode
- Tránh sử dụng `any`
- Sử dụng type inference khi có thể
- Định nghĩa rõ ràng props types cho components

### 3. Error Handling
```typescript
try {
  const data = await fetchData();
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
- Sử dụng `useState` cho state đơn giản
- Sử dụng `useReducer` cho state phức tạp

### 2. Global State (Zustand)
```typescript
// stores/userStore.ts
interface UserState {
  user: User | null;
  setUser: (user: User) => void;
}

export const useUserStore = create<UserState>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
}));
```

## Xử lý API & Services

### 1. API Structure
```typescript
// services/api.ts
const api = {
  get: async <T>(url: string): Promise<T> => {
    // ...
  },
  post: async <T>(url: string, data: unknown): Promise<T> => {
    // ...
  },
};

// services/userService.ts
export const userService = {
  getUser: (id: string) => api.get<User>(`/users/${id}`),
  updateUser: (id: string, data: UpdateUserData) => 
    api.post<User>(`/users/${id}`, data),
};
```

### 2. Error Handling
```typescript
class ApiError extends Error {
  constructor(
    public status: number,
    public message: string,
    public data?: unknown
  ) {
    super(message);
  }
}
```

## Styling & UI Components

### 1. Tailwind CSS
- Sử dụng Tailwind utility classes
- Tạo custom components cho các patterns lặp lại
- Sử dụng `@apply` cho các styles phức tạp

### 2. Component Styling
```typescript
// components/Button.tsx
const Button: React.FC<ButtonProps> = ({ variant = 'primary', ...props }) => {
  const baseStyles = 'px-4 py-2 rounded-md';
  const variantStyles = {
    primary: 'bg-blue-500 text-white',
    secondary: 'bg-gray-200 text-gray-800',
  };

  return (
    <button
      className={`${baseStyles} ${variantStyles[variant]}`}
      {...props}
    />
  );
};
```

## Performance & Optimization

### 1. Code Splitting
- Sử dụng dynamic imports cho các components lớn
- Lazy load các routes không cần thiết ngay lập tức

### 2. Memoization
```typescript
// Sử dụng useMemo cho tính toán phức tạp
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// Sử dụng useCallback cho function props
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

## Testing & Quality Assurance

### 1. Unit Tests
```typescript
// __tests__/Button.test.tsx
describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
});
```

### 2. Code Quality
- Sử dụng ESLint và Prettier
- Chạy type checking trước khi commit
- Review code trước khi merge

## Linting & Formatting

### 1. ESLint Rules
```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "no-console": "warn",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "react-hooks/rules-of-hooks": "error"
  }
}
```

### 2. Prettier Config
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
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
};
```

### 2. README Updates
- Cập nhật README khi thêm dependencies mới
- Document các breaking changes
- Cung cấp setup instructions rõ ràng 

## Git Workflow

### 1. Branch Naming
Format: `{type}/{issue-number}-{short-description}`

Ví dụ:
- `feature/Backlog_123-add-user-authentication`
- `bugfix/Backlog_456-fix-login-form-validation`
- `hotfix/Backlog_789-resolve-api-timeout`

Trong đó:
- `type`: feature, bugfix, hotfix
- `issue-number`: Mã issue từ backlog (ví dụ: Backlog_123)
- `short-description`: Mô tả ngắn gọn về task, sử dụng kebab-case

### 2. Commit Messages
Format: `{type}({issue-number}): {description}`

Ví dụ:
```
feat(Backlog_123): add user authentication
fix(Backlog_456): resolve login form validation
docs(Backlog_789): update README with new setup instructions
```

Các loại commit type:
- `feat`: Thêm tính năng mới
- `fix`: Sửa lỗi
- `docs`: Thay đổi documentation
- `style`: Thay đổi format code (không ảnh hưởng đến code)
- `refactor`: Refactor code
- `test`: Thêm/sửa tests
- `chore`: Thay đổi build process hoặc tools

### 3. Quy trình làm việc với Git

#### 3.1 Tạo branch mới
```bash
# Luôn checkout từ develop
git checkout develop
git pull origin develop
git checkout -b feature/Backlog_123-add-user-authentication
```

#### 3.2 Quy trình commit và push
```bash
# Commit theo format đã định
git commit -m "feat(Backlog_123): add user authentication"

# Push branch lên remote
git push origin feature/Backlog_123-add-user-authentication
```

#### 3.3 Trước khi tạo Pull Request
1. Rebase với develop để đảm bảo code mới nhất:
```bash
git checkout develop
git pull origin develop
git checkout feature/Backlog_123-add-user-authentication
git rebase develop
```

2. Self-test checklist:
   - [ ] Code build thành công: `npm run build`
   - [ ] Không có lỗi lint: `npm run lint`
   - [ ] Tất cả test pass: `npm run test`
   - [ ] Code đã được format: `npm run format`
   - [ ] Đã test thủ công các chức năng đã thay đổi
   - [ ] Đã review lại code của mình

3. Push sau khi rebase:
```bash
git push origin feature/Backlog_123-add-user-authentication --force-with-lease
```

#### 3.4 Tạo Pull Request
- Base branch: `develop`
- Compare branch: `feature/Backlog_123-add-user-authentication`
- Điền đầy đủ thông tin theo template
- Ping reviewer sau khi đã hoàn thành self-test

#### 3.5 Sau khi được approve
1. Merge code vào develop
2. Xóa branch feature sau khi merge thành công
3. Pull develop về local để cập nhật code mới nhất

### 4. Pull Request Template
```markdown
## Description
[Describe changes made]

## Related Issue
Closes #Backlog_123

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
- [ ] Branch name follows convention: {type}/{issue-number}-{description}
- [ ] Commit messages follow convention: {type}({issue-number}): {description}
``` 