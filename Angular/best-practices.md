## 最佳實踐

### 樹搖優化

Tree-shaking (樹搖優化) 的目的是要將未使用到的程式碼從打捆中移除。現在支援樹搖優化的工具有 Rollup 和 Webpack 2 等等。

如果專案是 Angular CLI 生產的 (Angular CLI 的模組整合工具是 Webpack 2)，可以透過 `ng build --prod` 來執行樹搖優化的動作。

底下範例為使用 Rollup 進行樹搖優化的程式碼：
```js
// package.json
{
  [...]
  "scripts": {
    "start": "rollup -c && lite-server"
  },
  [...]
  "dependencies": {
    "@angular/common": "~2.1.1",
    "@angular/compiler": "~2.1.1",
    "@angular/core": "~2.1.1",
    "@angular/forms": "~2.1.1",
    "@angular/http": "~2.1.1",
    "@angular/platform-browser": "~2.1.1",
    "@angular/platform-browser-dynamic": "~2.1.1",
    "@angular/router": "~3.1.1",
    "core-js": "^2.4.1",
    "rxjs": "5.0.0-beta.12",
    "zone.js": "^0.6.25"
  },
  "devDependencies": {
    "@types/core-js": "^0.9.34",
    "@types/node": "^6.0.45",
    "lite-server": "^2.2.2",
    "rimraf": "^2.5.4",
    "rollup": "^0.36.3",
    "rollup-plugin-commonjs": "^5.0.5",
    "rollup-plugin-node-resolve": "^2.0.0",
    "rollup-plugin-typescript": "^0.8.1",
    "rollup-plugin-uglify": "^1.0.1",
    "typescript": "^2.0.3"
  }
}

```
```js
// rollup.config.js
import { join } from 'path';

import typescript from 'rollup-plugin-typescript';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import uglify from 'rollup-plugin-uglify';

export default {
  entry: join(__dirname, 'src', 'main.ts'),
  dest: join(__dirname, 'app.js'),
  format: 'iife',
  treeshake: true,  // 選項: true, false，比較有無樹搖優化的前後差別
  plugins: [
    typescript(),
    resolve({ jsnext: true, browser: true }),
    commonjs(),
    uglify()
  ]
};
```
```js
// tsconfig.json
{
  "compilerOptions": {
    "target": "es5",
    "module": "es2015",
    "moduleResolution": "node",
    "sourceMap": false,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": true,
    "noImplicitAny": false
  },
  "exclude": [
    "node_modules"
  ],
  "compileOnSave": false
}
```
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Rollup</title>
  </head>
<body>
  <app>Loading...</app>
  <script src="app.js"></script>
</body>
</html>
```
```ts
// src/main.ts
// polyfills
import 'core-js/es6/symbol';
import 'core-js/es6/object';
import 'core-js/es6/function';
import 'core-js/es6/parse-int';
import 'core-js/es6/parse-float';
import 'core-js/es6/number';
import 'core-js/es6/math';
import 'core-js/es6/string';
import 'core-js/es6/date';
import 'core-js/es6/array';
import 'core-js/es6/regexp';
import 'core-js/es6/map';
import 'core-js/es6/set';
import 'core-js/es6/reflect';
import 'core-js/es7/reflect';
import 'zone.js/dist/zone';

// app
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';

enableProdMode();

platformBrowserDynamic().bootstrapModule(AppModule);
```
```ts
// src/app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';

@NgModule({
  imports: [BrowserModule],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
```ts
// src/app/app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app',
  template: `
    <h3>Hello World</h3>
  `
})
export class AppComponent { }
```
```bash
$ npm start
```
