---
name: eslint-setup
description: Use when setting up or applying ESLint + Prettier in a TypeScript/JavaScript project (keywords - "eslint 적용", "eslint 구성", "prettier 설정", "lint 셋업", "ESLint setup"). Configures the integrated eslint-plugin-prettier style so a single `eslint --fix` (or WebStorm "save → eslint --fix") handles both quality and formatting. Mirrors the user's dews-oneai reference setup.
user-invocable: true
---

# ESLint + Prettier Setup (사용자 선호 구성)

오빠가 선호하는 lint/format 구성은 **eslint-plugin-prettier 통합형**이다.
Prettier를 ESLint 규칙으로 돌려서 `eslint --fix` **하나로 포맷 + 품질 검사**가 같이 적용된다.
WebStorm "저장 시 eslint --fix 실행" 토글 하나로 끝나게 하는 게 목표.

> ❌ "ESLint=품질 / Prettier=포맷" 역할 분리(separate Prettier on-save)형은 제안하지 말 것.
> 기준 프로젝트: `~/WebstormProjects/dews-oneai` (.eslintrc.cjs + .prettierrc.cjs).

## 0. 환경 먼저 확인

```bash
node -e "console.log(require('./package.json').type)"   # "module"이면 ESM
npx eslint --version 2>/dev/null                          # 설치/버전 확인
```

- **ESLint 9/10 (flat config) + typescript-eslint v8** → 아래 flat config 사용 (권장, 신규 프로젝트).
- **ESLint 8 (legacy)** → dews-oneai의 `.eslintrc.cjs`를 거의 그대로 복사.

## 1. 설치

```bash
npm install -D eslint @eslint/js typescript-eslint prettier eslint-plugin-prettier eslint-config-prettier
```

(`eslint-plugin-prettier/recommended`가 `eslint-config-prettier`를 포함하므로 config는 plugin recommended만 써도 됨.)

## 2. eslint.config.js (ESLint 9/10 flat + typescript-eslint v8)

```js
import js from "@eslint/js";
import tseslint from "typescript-eslint";
import prettierRecommended from "eslint-plugin-prettier/recommended";

export default tseslint.config(
  { ignores: ["dist/**", "node_modules/**"] },
  js.configs.recommended,
  ...tseslint.configs.recommended,
  prettierRecommended, // = eslint-plugin-prettier + eslint-config-prettier
  {
    languageOptions: { ecmaVersion: "latest", sourceType: "module" },
    rules: {
      "@typescript-eslint/no-unused-vars": "warn",
      "@typescript-eslint/no-empty-function": "warn",
      "@typescript-eslint/no-explicit-any": "off",
      "@typescript-eslint/no-empty-object-type": "warn", // v8: ← no-empty-interface
      "@typescript-eslint/no-this-alias": "off",
      "@typescript-eslint/no-require-imports": "warn",    // v8: ← no-var-requires
      "@typescript-eslint/ban-ts-comment": "warn",
      "no-empty": "warn",
      "no-useless-escape": "off",
      "no-async-promise-executor": "off",
      "prefer-const": "off",
      "no-prototype-builtins": "warn",
      "no-redeclare": "warn",
      "no-constant-condition": "warn",
      "no-dupe-class-members": "warn",
      "no-unsafe-optional-chaining": "warn",
      "linebreak-style": "off",
    },
  },
);
```

### ⚠️ typescript-eslint v8에서 사라진 룰명 (dews 룰셋 미러링 시 교체 필수)
unknown rule을 그대로 두면 ESLint가 "Definition for rule ... was not found"로 깨진다.
- `@typescript-eslint/no-empty-interface` → `@typescript-eslint/no-empty-object-type`
- `@typescript-eslint/no-var-requires` → `@typescript-eslint/no-require-imports`
- `no-undef` → TS가 처리하므로 생략(또는 off). TS 프로젝트에선 켜지 말 것.

## 3. ESLint 8 (legacy) 일 때 — .eslintrc.cjs

dews-oneai 원본 그대로:

```js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  extends: ['eslint:recommended', 'plugin:@typescript-eslint/recommended', 'plugin:prettier/recommended'],
  plugins: ['@typescript-eslint', 'prettier'],
  parserOptions: { ecmaVersion: 'latest', sourceType: 'module' },
  rules: {
    '@typescript-eslint/no-unused-vars': 'warn',
    '@typescript-eslint/no-empty-function': 'warn',
    '@typescript-eslint/no-explicit-any': 'off',
    '@typescript-eslint/no-empty-interface': 'warn',
    '@typescript-eslint/no-this-alias': 'off',
    '@typescript-eslint/no-var-requires': 'warn',
    '@typescript-eslint/ban-ts-comment': 'warn',
    'no-empty': 'warn',
    'no-useless-escape': 'off',
    'no-async-promise-executor': 'off',
    'prefer-const': 'off',
    'no-prototype-builtins': 'warn',
    'no-undef': 'warn',
    'no-redeclare': 'warn',
    'no-constant-condition': 'warn',
    'no-dupe-class-members': 'warn',
    'no-unsafe-optional-chaining': 'warn',
    'linebreak-style': 0,
  },
  env: { browser: true, node: true },
};
```

## 4. .prettierrc.json (dews-oneai 값)

```json
{
  "singleQuote": true,
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "jsxSingleQuote": true,
  "trailingComma": "none",
  "arrowParens": "always",
  "bracketSpacing": true,
  "bracketSameLine": false,
  "endOfLine": "lf"
}
```

### ⚠️ Prettier 3 호환
- `jsxBracketSameLine` (dews는 Prettier 2)는 Prettier 3에서 제거됨 → `bracketSameLine: false`로 교체.

`.prettierignore`:
```
dist
node_modules
package-lock.json
```

## 5. npm scripts

오빠는 보통 WebStorm 저장 시 자동 적용을 쓰므로 **lint/format npm script는 넣지 않는다**(요청 시에만). CI가 필요하면:
```json
"lint": "eslint \"src/**/*.ts\"",
"format:check": "prettier --check \"src/**/*.ts\""
```

## 6. WebStorm 설정 (사용자 환경)

`Settings → 언어 및 프레임워크 → JavaScript → 코드 품질 도구 → ESLint`
- ☑ 자동 ESLint 구성, glob에 `ts/tsx` 포함, ☑ **저장 시 eslint --fix 실행**

통합형이라 **Prettier on-save는 따로 안 켜도 됨** (eslint --fix가 포맷까지 함).
flat config + ESLint 9/10은 WebStorm 2024.1+ 필요. 안 잡히면 WebStorm 재시작/업데이트.

## 7. 검증 (완료 주장 전 반드시 실행)

```bash
npx eslint "src/**/*.ts" --fix   # 적용
npx eslint "src/**/*.ts"; echo "eslint: $?"      # 0 기대
npx tsc --noEmit; echo "tsc: $?"                 # 0 기대 (빌드 깨짐 방지)
npx prettier --check "src/**/*.ts"; echo "prettier: $?"  # 0 기대
```

빠른 동작 확인: 아무 `.ts`에서 `const  x =1` 처럼 망가뜨리고 저장 → `const x = 1`로 정리되면 성공.
