# @kent7k/tsconfig (kdot-tsconfig)

TypeScript 共通 tsconfig。**Node 22 / TS 5.9+ 前提**、TS 6.0 の既定値変更を先取りしてある。

## プリセット

| 名前 | 用途 | キーポイント |
|---|---|---|
| `base.json` | 全プリセットの土台 | strict + safe strictness, `verbatimModuleSyntax`, `moduleDetection: force`, `types: []`。`target`/`lib`/`module` は持たない |
| `node-base.json` | Node 用の土台 | `target/lib: ES2024`, `module: node20`, `types: ["node"]` |
| `node-esm.json` | Node ライブラリ (publish 用) | `composite` + `sourceMap`。出力形式は package.json の `type` で決める |
| `scripts.json` | tsx / `node --experimental-strip-types` で走らせるスクリプト | `noEmit` + `erasableSyntaxOnly` |
| `web-base.json` | Web/Bundler の土台 | `target: ES2023`, `lib: [ES2024, DOM, DOM.Iterable]`, `module: preserve` |
| `react.json` | React ライブラリ/アプリの土台 | `jsx: react-jsx` |
| `web.json` | Web/React ライブラリ (publish 用) | `react.json` + `composite` + `sourceMap` |
| `app.json` | Next.js アプリ | `jsx: preserve`, `types: ["node"]`, `plugins: [{name: "next"}]` |
| `figma.json` | Figma プラグイン | DOM を入れず `@figma/plugin-typings` のみ。バンドラ前提なので `noEmit` |

## 使い方

```jsonc
{
  "extends": "@kent7k/tsconfig/node-esm.json",
  "compilerOptions": {
    "rootDir": "src",
    "outDir": "dist"
  },
  "include": ["src"]
}
```

`include`/`exclude` と `rootDir`/`outDir` は各パッケージ側で定義する。

## 設計方針

**TS 6.0 で既定値が総入れ替えになる** (`strict: true`, `module: esnext`, `target: es2025`,
`types: []`, `rootDir: "."`, `noUncheckedSideEffectImports: true`)。
このリポジトリは「6.0 の既定を 5.9 で先取りする層」として機能する。
つまり 6.0 に上げた時点で **何も壊れない**のが正しい状態。

- **`types: []` を base で固定** — 6.0 で強制されるいちばん影響の大きい変更。
  グローバル型が必要な package は `"types": ["node"]` / `["vitest/globals"]` を明示する。
- **`module: node20` (not `nodenext`)** — `nodenext` は TS のバージョン更新で意味が動く。
  共有 config が最も避けるべきものなので、engines (`node: 22.x`) に合わせて固定する。
- **`composite: true` だけ書く** — `declaration` と `incremental` は composite に含意される。
- **`baseUrl` は使わない** — 6.0 で解決ルートとして deprecated。`paths` を tsconfig 相対で書く。
- **CJS 強制の preset は持たない** — `moduleResolution: node10` が 7.0 で削除されるため。
  CJS 出力は package.json の `"type": "commonjs"` で決める。
- `noUnusedLocals` / `noUnusedParameters` は入れない（lintkit-eslint 側の担当）。
- `exactOptionalPropertyTypes` は React props と相性が悪いので入れない。

## TypeScript 7.0 について

7.0 (Go ネイティブ) は `tsc` は速いが **programmatic API がない**。
typescript-eslint / ts-morph / Next.js の型プラグイン / typed-scss-modules が動かないため、
**API が入る 7.1 まで上げない**。このリポジトリは 7.0 で削除される option を既に使っていない。
