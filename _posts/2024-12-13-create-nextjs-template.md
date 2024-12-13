---
title: Next.js 개발 환경 구축
date: 2024-12-13 20:00:00 +0900
categories: [Frontend]
tags: [next]     # TAG names should always be lowercase
description: Next.js + TypeScript + ESLint + Prettier
---

> [Smang09/nextjs-ts-template](https://github.com/Smang09/nextjs-ts-template)
{: .prompt-info }

## 목표
- [x] create-next-app CLI 없이 개발 환경을 구축해 보면서 프로젝트 내부 구조 이해하기
- [x] 의존성과 버전 관리를 이해하고 충돌 해결하기
- [x] GitHub Template Repository 생성하기

---

## 1. package.json
- [npm docs on package.json](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)
- [Next.js Manual Installation Guide](https://nextjs.org/docs/app/getting-started/installation#manual-installation)

```bash
npm init
npm i next@latest react@latest react-dom@latest
npm i -D typescript @types/node @types/react @types/react-dom
```

## 2. tsconfig.json
- [TypeScript tsconfig.json Handbook](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```
{: file='tsconfig.json'}

## 3. next.config.js
next.config.js는 Next.js 서버와 빌드 단계에서 사용되며, 브라우저 빌드에는 포함되지 않는다. (브라우저에서 접근 불가)

- [Next.js next.config.js API Reference](https://nextjs.org/docs/app/api-reference/config/next-config-js)

- [X-Powered-By](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Powered-By)

```ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  poweredByHeader: false,
};

export default nextConfig;
```
{: file='next.config.js'}

## 4. ESLint & Prettier

### 4-1. [next built-in ESLint](https://nextjs.org/docs/app/getting-started/installation#set-up-eslint)

```bash
> next lint

Installing devDependencies (npm):
- eslint@^9
- eslint-config-next
```

### 4-2. [@titicaca/eslint-config-triple](https://github.com/titicacadev/triple-config-kit/tree/main/packages/eslint-config-triple)
예전에 채용 과제 전형에서 사용한 경험이 있으며, 설치와 설정이 간편했던 기억이 있어 적용해보려고 한다.

- ESLint 버전 다운그레이드
  
  작성일 기준 [ESLint 최신 버전](https://github.com/eslint/eslint/releases)은 `9.16.0`이고, @titicaca/eslint-config-triple의 ESLint 의존성은 아래와 같다. 따라서, 호환되는 ESLint 버전은 `8.57.1`부터 `9.0.0` 미만의 모든 버전이다.

  [Semantic Versioning](https://semver.org/)

  ```json
  {
    "devDependencies": {
      "eslint": "^8.57.1"
    },
    "peerDependencies": {
      "eslint": "^8.0.0"
    }
  }
  ```
  {: file='triple-config-kit/packages/eslint-config-triple/package.json'}


```bash
npm install -D eslint@8.57.1 prettier
npm install -D @titicaca/eslint-config-triple
```

```json
{
  "extends": [
    "next/typescript",
    "@titicaca/eslint-config-triple",
    "@titicaca/eslint-config-triple/requiring-type-checking",
    "@titicaca/eslint-config-triple/frontend",
    "@titicaca/eslint-config-triple/prettier",
    "next/core-web-vitals"
  ]
}
```
{: file='.eslintrc.json'}

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "always",
  "printWidth": 80,
  "endOfLine": "lf"
}
```
{: file='.prettierrc'}

```json
{
  "scripts": {
    "lint:es": "eslint .",
    "lint:es:fix": "eslint . --fix",
    "format": "prettier . --check",
    "format:fix": "prettier . --write"
  }
}
```
{: file='package.json'}

---

## 마무리
  - 새로운 기술을 적용할 때 마주치는 문제의 80%는 개발 문서에 해결책이 있다. 만약 문서에서 답을 찾지 못했다면, 관련 커뮤니티나 해당 라이브러리의 GitHub Issue를 참고해 보자.
  - 버전 충돌 등의 오류가 발생하면 오류 메시지를 정확히 이해하는 것이 중요하다. 문제 해결의 첫 번째 단계는 원인 파악이다.
  - 어떤 것을 설치할 때는 필요한 이유와 제대로 적용되었는지 확인하는 방법을 반드시 알아야 한다. "이렇게 사용하던데?"라는 접근은 위험한 생각이다.