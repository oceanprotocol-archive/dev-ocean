# Code Style Guides

Table of Contents
=================

  * [Python](#python)
  * [JavaScript](#javascript)
  * [CSS](#css)
  * [Solidity](#solidity)
  * [TypeScript](#typescript)

## Python

See here: [Python Style Guide](python-style-guide.md) and [Python Developer Guide](python-developer-guide.md)

## JavaScript

We lint all JavaScript code with [ESLint](https://eslint.org) following the [JavaScript Standard Style](https://standardjs.com) with some modifications collected in our own ESLint config:

* [eslint-config-oceanprotocol](https://github.com/oceanprotocol/eslint-config-oceanprotocol)

## CSS

All CSS, CSS-preprocessors, and CSS-in-JS is linted with [stylelint](https://stylelint.io) and our own config from BigchainDB:

* [stylelint-config-bigchaindb](https://github.com/bigchaindb/stylelint-config-bigchaindb)

## Solidity

Solidity code is linted with [Solium](https://www.getsolium.com) and its recommended rules, the only customization being single quotes and indentation of 4 spaces. An example `.soliumrc.json` file would be:

```json
{
  "extends": "solium:recommended",
  "plugins": [
    "security"
  ],
  "rules": {
    "quotes": ["error", "single"],
    "indentation": ["error", 4],
    "security/no-tx-origin": ["warning"]
  }
}
```

Solidity security checks are done with [Mythril](https://github.com/ConsenSys/mythril). It is a static code analyzer to check for the most common vulnerabilities and bad practices.

```bash
pip3 install mythril
truffle compile
myth --truffle
```

Solidity documentation could be created with [Solgraph](https://github.com/raineorshine/solgraph).

```bash
npm install -g solgraph
solgraph Market.sol > Market.dot
brew install graphviz
dot -Tpng Market.dot -o Market.png
```

## TypeScript

TypeScript code is linted with [tslint](https://palantir.github.io/tslint/) and its recommended rules. A sample `tslint.json` file would be:

```json
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended"
  ],
  "jsRules": {},
  "rules": {
    "object-literal-sort-keys": false,
    "semicolon": [
      true,
      "never"
    ]
  },
  "rulesDirectory": []
}
```

TypeScript compilation is done with the TypeScript Compiler `tsc`. A sample `tsconfig.json` file would be:

```json
{
  "compilerOptions": {
    "resolveJsonModule": true,
    "lib": [
      "es6",
      "es7"
    ],
    "declaration": true,
    "noUnusedLocals": true,
    "module": "commonjs",
    "noImplicitAny": false,
    "removeComments": true,
    "preserveConstEnums": true,
    "outDir": "./dist/",
    "rootDir": "./src/",
    "sourceMap": true
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "**/*.test.ts"
  ]
}
```

We use [typedoc](https://github.com/TypeStrong/typedoc) to generate documentation in TypeScript.

```bash
npm i -g typedoc
typedoc --mode modules --out ./doc/ src/
```