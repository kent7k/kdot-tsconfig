# @kent7k/tsconfig (kdot-tsconfig)

TypeScript 共通 tsconfig (Node 22+/24 / TS 5.9+ 前提)。

## プリセット

| 名前 | 用途 | キーポイント |
|---|---|---|
| `base.json` | 全プリセットの土台 | strict + safe strictness (`noUncheckedIndexedAccess`, `noImplicitOverride`, `noFallthroughCasesInSwitch`), `verbatimModuleSyntax`, `moduleDetection: force`. `lib`/`module`/`target` は持たない |
| `node-base.json` | Node 用の土台 | `target/lib: ES2023`, `module/moduleResolution: NodeNext`, `types: [node]` |
| `node-esm.json` | Node ESM ライブラリ (publish 用) | `composite` + `declaration` + `sourceMap` |
| `node-cjs.json` | Node CJS ライブラリ | `module: CommonJS`, `moduleResolution: Node10`, `verbatimModuleSyntax: off` |
| `scripts.json` | tsx 実行スクリプト | `noEmit` のまま |
| `web-base.json` | Web/Bundler の土台 | `module: Preserve`, `moduleResolution: Bundler`, `DOM` 含む |
| `web.json` | Web ライブラリ (publish 用) | `composite` + `declaration` + `sourceMap` |
| `app.json` | Next.js アプリ | `jsx: preserve`, `plugins: [{name: "next"}]` |
| `figma.json` | Figma プラグイン | DOM を入れず `@figma/plugin-typings` のみ |

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
