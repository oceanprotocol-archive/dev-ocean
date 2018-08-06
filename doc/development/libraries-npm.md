# How to create NPM Javascript libraries


The purpose of this page is to provide a guide to follow when you want to publish a npm package from an existing JavaScript project.
In this case we are using the [keeper-contracts project](https://github.com/oceanprotocol/keeper-contracts) project as a reference.

Ocean Protocol has a [NPM organization](https://www.npmjs.com/settings/oceanprotocol/packages). All the official Ocean Protocol must be published in the scope of the [@oceanprotocol](https://www.npmjs.com/settings/oceanprotocol/packages) organization.
The default DevOps user allowing to publish packages in that organization is called [leucothia](https://www.npmjs.com/~leucothia). This user is the one integrated in the CI environment (Travis).
So to publishing packages in the @oceanprotocol you need to have access the NPM **oceanprotocol** organization. If you don't, please request this sending an email to devops@oceanprotocol.com.



# Login into npmjs.com

Before publishing a package, you need to login in the npm registry using the @oceanprotocol devops user:

```bash

aitor@tijuana:~/Projects/Ocean/keeper-contracts (master)$ npm login
Username: leucothia
Password:
Email: (this IS public) devops@oceanprotocol.com
Logged in as leucothia on https://registry.npmjs.org/.
aitor@tijuana:~/Projects/Ocean/keeper-contracts (master)$
```


# Preparing the package.json

The package.json file is the one including the main details of your npm package. Main attributes to consider are:

* name - package name including the organization
* version - you can't overwrite previously published versions, so any time you publish a npm package you need to change the version number
* scripts - including the commands to install, compile and publish the package
* directories & files - listing the directories and files to be included in the package

Here you have a fragment of the [keeper-contracts/package.json](https://github.com/oceanprotocol/keeper-contracts/blob/master/package.json):
```json
{
  "name": "@oceanprotocol/keeper-contracts",
  "version": "0.0.8",
  "description": "Integration of TCRs, CPM and Ocean Tokens in Solidity",
  "bugs": {
    "url": "https://github.com/oceanprotocol/keeper-contracts/issues"
  },
  "homepage": "https://github.com/oceanprotocol/keeper-contracts#readme",
  "main": "truffle.js",
  "scripts": {
    "install": "truffle install",
    "compile": "truffle compile",
    "publish": "truffle publish",
    "coverage": "solidity-coverage",
    "deploy-rinkeby": "truffle migrate --network rinkeby",
    "deploy-mainnet": "truffle migrate --network mainnet",
    "lint-fix": "eslint --fix ./**/*.js && ./node_modules/solium/bin/solium.js -d ./contracts/ --fix",
    "lint": "eslint ./**/*.js && ./node_modules/solium/bin/solium.js -d ./contracts/",
    "test": "npm run lint && truffle test"
  },
  "directories": {
    "doc": "doc",
    "test": "test"
  },
  "files": [
    "build/contracts/*",
    "doc/*",
    "README.md"
  ],
}
```

For a complete attributes reference you can review the [package.json official documentation](https://docs.npmjs.com/getting-started/using-a-package.json).

# Publishing the library

After to have everything defined in the package.json only it's pending to publish the package:

```bash
npm publish --scope=@oceanprotocol
```

After a few seconds, the new version of the package should be publicly available as part of the [@oceanprotcol npm organization](https://www.npmjs.com/settings/oceanprotocol/packages).

## Updating a package

If you want to update a previously published package, you have to update the version number in package.json and then publish it:

```
"version": "0.0.9",
```

```bash
npm publish --scope=@oceanprotocol --otp xxxxx
```bash

