
## Steps to create a minimal repro

### Init a yarn workspace ith a nestjs app
* `yarn init`
* `yarn install -W --dev @nestjs/cli`
* `yarn run nest new nestjs`
* Update package.json to define `nestjs` as a package

## Introduce nestjs-pino

* `yarn add workspace nestjs nestjs-pino`
* `rm -rf node_modules`
* `rm -rf nestjs/node_modules`
* `yarn install`
* Update `app.module.ts`:

```typescript
imports: [LoggerModule],
```

## Start the app

`yarn workspace nestjs start`

Observe the failure: 

```text
$ nest start
Error: Cannot find module '@nestjs/common'
Require stack:
- <redacted>/nestjs-pino-peer-deps/node_modules/nestjs-pino/LoggerModule.js
- <redacted>/nestjs-pino-peer-deps/node_modules/nestjs-pino/index.js
- <redacted>/nestjs-pino-peer-deps/nestjs/dist/app.module.js
- <redacted>/nestjs-pino-peer-deps/nestjs/dist/main.js
    at Function.Module._resolveFilename (node:internal/modules/cjs/loader:933:15)
    at Function.Module._load (node:internal/modules/cjs/loader:778:27)
    at Module.require (node:internal/modules/cjs/loader:1005:19)
    at require (node:internal/modules/cjs/helpers:102:18)
    at Object.<anonymous> (<redacted>/node_modules/nestjs-pino/LoggerModule.js:17:18)
    at Module._compile (node:internal/modules/cjs/loader:1101:14)
    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1153:10)
    at Module.load (node:internal/modules/cjs/loader:981:32)
    at Function.Module._load (node:internal/modules/cjs/loader:822:12)
    at Module.require (node:internal/modules/cjs/loader:1005:19)

```

Observe the relative locations of `nestjs-pino` and `@nestjs/common`.

`nestjs-pino` has been hoisted to the root node_modules, `@nestjs/common` has not. 
This is because yarn doesn't know of the dependency between the two - a declared peerDependency should
resolve this.

 