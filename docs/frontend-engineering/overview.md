# 前端工程化 - 总览

## 概述

前端工程化是现代Web开发的核心，通过标准化的工具链、流程和规范，提升开发效率、代码质量和项目维护性。本章节涵盖构建工具、代码规范、自动化测试、性能优化等前端工程化的各个方面。

## 📚 章节内容

### 1. [构建工具链](./build-tools.md)
- **打包工具**: Webpack、Vite、Rollup、Parcel配置和优化
- **编译转换**: Babel、TypeScript、PostCSS、SASS处理
- **开发服务器**: HMR热更新、代理配置、环境变量
- **代码分割**: 动态导入、路由分割、按需加载
- **资源优化**: 图片压缩、文件哈希、CDN集成

### 2. [代码质量保证](./code-quality.md)
- **代码规范**: ESLint、Prettier、StyleLint配置
- **类型检查**: TypeScript、Flow静态类型系统
- **Git钩子**: Husky、lint-staged、commitlint
- **代码审查**: Pull Request流程、Review清单
- **质量度量**: 代码覆盖率、复杂度分析、技术债务

### 3. [自动化测试](./automated-testing.md)
- **单元测试**: Jest、Vitest、Mocha测试框架
- **组件测试**: React Testing Library、Vue Test Utils
- **E2E测试**: Cypress、Playwright、Puppeteer
- **视觉回归**: Percy、Chromatic、BackstopJS
- **性能测试**: Lighthouse CI、Web Vitals监控

### 4. [CI/CD流水线](./cicd-pipeline.md)
- **持续集成**: GitHub Actions、GitLab CI、Jenkins
- **自动化部署**: Netlify、Vercel、AWS S3+CloudFront
- **环境管理**: 开发、测试、预生产、生产环境
- **发布策略**: 蓝绿部署、灰度发布、回滚机制
- **监控告警**: 错误监控、性能监控、用户行为分析

### 5. [性能优化](./performance-optimization.md)
- **加载优化**: 资源压缩、缓存策略、CDN加速
- **渲染优化**: 虚拟DOM、组件懒加载、服务端渲染
- **用户体验**: Core Web Vitals、交互优化、可访问性
- **监控分析**: RUM监控、性能预算、优化建议
- **工具使用**: Lighthouse、WebPageTest、Chrome DevTools

## 🎯 现代前端工程化架构

### 工具链生态系统

#### 核心工具对比
| 工具类型 | 主流选择 | 特点 | 适用场景 |
|----------|----------|------|----------|
| **构建工具** | Vite | 快速、ESM原生 | 现代项目 |
| | Webpack | 成熟、插件丰富 | 复杂项目 |
| **包管理** | pnpm | 快速、节省空间 | 单体仓库 |
| | npm/yarn | 生态成熟 | 通用项目 |
| **测试框架** | Vitest | 快速、Vite集成 | Vite项目 |
| | Jest | 功能完整 | React项目 |

### Vite现代构建配置

#### 完整的vite.config.ts
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { resolve } from 'path';

export default defineConfig({
  plugins: [
    react(),
    // 其他插件配置
  ],
  
  // 路径别名
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      '@components': resolve(__dirname, 'src/components'),
      '@utils': resolve(__dirname, 'src/utils'),
    },
  },
  
  // 开发服务器配置
  server: {
    port: 3000,
    open: true,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
    },
  },
  
  // 构建配置
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          ui: ['@mui/material', '@emotion/react'],
        },
      },
    },
  },
  
  // 环境变量
  define: {
    __APP_VERSION__: JSON.stringify(process.env.npm_package_version),
  },
});
```

### 代码质量配置

#### ESLint + Prettier + TypeScript
```json
// .eslintrc.json
{
  "extends": [
    "@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "prettier"
  ],
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint", "react", "react-hooks"],
  "rules": {
    "react/react-in-jsx-scope": "off",
    "@typescript-eslint/no-unused-vars": "error",
    "react-hooks/exhaustive-deps": "warn"
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

```json
// prettier.config.js
module.exports = {
  semi: true,
  singleQuote: true,
  tabWidth: 2,
  trailingComma: 'es5',
  printWidth: 80,
  bracketSpacing: true,
  arrowParens: 'avoid',
};
```

## 💡 自动化测试策略

### 测试金字塔实践

#### 单元测试 (70%)
```typescript
// 组件单元测试示例
import { render, screen, fireEvent } from '@testing-library/react';
import { Counter } from './Counter';

describe('Counter Component', () => {
  test('renders initial count', () => {
    render(<Counter initialCount={0} />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });

  test('increments count when button clicked', () => {
    render(<Counter initialCount={0} />);
    
    const button = screen.getByRole('button', { name: /increment/i });
    fireEvent.click(button);
    
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });

  test('calls onCountChange when count updates', () => {
    const mockOnCountChange = jest.fn();
    render(<Counter initialCount={0} onCountChange={mockOnCountChange} />);
    
    const button = screen.getByRole('button', { name: /increment/i });
    fireEvent.click(button);
    
    expect(mockOnCountChange).toHaveBeenCalledWith(1);
  });
});
```

#### 集成测试 (20%)
```typescript
// API集成测试
import { renderHook, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from 'react-query';
import { useUserProfile } from './useUserProfile';

const createWrapper = () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
      mutations: { retry: false },
    },
  });
  
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
};

describe('useUserProfile Hook', () => {
  test('fetches user profile successfully', async () => {
    const { result } = renderHook(() => useUserProfile('123'), {
      wrapper: createWrapper(),
    });

    await waitFor(() => {
      expect(result.current.isSuccess).toBe(true);
    });

    expect(result.current.data).toEqual({
      id: '123',
      name: 'John Doe',
      email: 'john@example.com',
    });
  });
});
```

#### E2E测试 (10%)
```typescript
// Cypress E2E测试
describe('Todo App E2E', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('should add and complete a todo item', () => {
    // 添加待办事项
    cy.get('[data-testid=todo-input]')
      .type('Learn Cypress testing');
    
    cy.get('[data-testid=add-button]')
      .click();

    // 验证添加成功
    cy.get('[data-testid=todo-list]')
      .should('contain', 'Learn Cypress testing');

    // 完成待办事项
    cy.get('[data-testid=todo-item]')
      .first()
      .find('[data-testid=checkbox]')
      .click();

    // 验证完成状态
    cy.get('[data-testid=todo-item]')
      .first()
      .should('have.class', 'completed');
  });

  it('should filter todos by status', () => {
    // 添加多个待办事项
    cy.get('[data-testid=todo-input]').type('Task 1{enter}');
    cy.get('[data-testid=todo-input]').type('Task 2{enter}');
    
    // 完成第一个任务
    cy.get('[data-testid=todo-item]')
      .first()
      .find('[data-testid=checkbox]')
      .click();

    // 过滤显示已完成
    cy.get('[data-testid=filter-completed]').click();
    cy.get('[data-testid=todo-list]')
      .should('contain', 'Task 1')
      .should('not.contain', 'Task 2');

    // 过滤显示未完成
    cy.get('[data-testid=filter-active]').click();
    cy.get('[data-testid=todo-list]')
      .should('contain', 'Task 2')
      .should('not.contain', 'Task 1');
  });
});
```

## 🛠️ CI/CD自动化流程

### GitHub Actions工作流

#### 完整的CI/CD配置
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [16.x, 18.x]
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linting
        run: npm run lint
      
      - name: Run type checking
        run: npm run type-check
      
      - name: Run unit tests
        run: npm run test:unit -- --coverage
      
      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
      
      - name: Run E2E tests
        run: npm run test:e2e
        env:
          CI: true

  build:
    needs: test
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18.x'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-files
          path: dist/
      
      - name: Deploy to S3
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: 'us-east-1'
        run: |
          aws s3 sync dist/ s3://${{ secrets.S3_BUCKET_NAME }} --delete
          
      - name: Invalidate CloudFront
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: 'us-east-1'
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} \
            --paths "/*"

  lighthouse:
    needs: deploy
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.12.x
          lhci autorun
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
```

## 📊 性能优化实践

### Web性能指标监控

#### Core Web Vitals监控
```typescript
// 性能监控工具
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

function sendToAnalytics(metric: any) {
  // 发送到分析服务
  gtag('event', metric.name, {
    event_category: 'Web Vitals',
    event_label: metric.id,
    value: Math.round(metric.name === 'CLS' ? metric.value * 1000 : metric.value),
    non_interaction: true,
  });
}

// 监控所有核心指标
getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
```

### 构建优化策略

#### Webpack Bundle分析
```javascript
// webpack-bundle-analyzer配置
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  // ... 其他配置
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
      reportFilename: 'bundle-report.html',
    }),
  ],
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
        common: {
          name: 'common',
          minChunks: 2,
          chunks: 'all',
          enforce: true,
        },
      },
    },
  },
};
```

## 🚀 最佳实践建议

### 项目结构规范
```
src/
├── components/          # 可复用组件
│   ├── ui/             # 基础UI组件
│   └── feature/        # 业务功能组件
├── pages/              # 页面组件
├── hooks/              # 自定义Hook
├── services/           # API服务
├── utils/              # 工具函数
├── stores/             # 状态管理
├── types/              # TypeScript类型定义
├── styles/             # 样式文件
└── tests/              # 测试文件
    ├── __mocks__/      # Mock文件
    ├── fixtures/       # 测试数据
    └── utils/          # 测试工具
```

### 开发工作流
```
1. 需求分析和设计
2. 创建功能分支
3. 编写测试用例
4. 实现功能代码
5. 本地测试验证
6. 代码质量检查
7. 提交PR审查
8. 合并主分支
9. 自动部署发布
10. 监控和反馈
``` 