# umi-plugin-electron-builder

<a href="https://www.npmjs.com/package/umi-plugin-electron-builder"><img src="https://img.shields.io/npm/v/umi-plugin-electron-builder.svg?sanitize=true" alt="Version"></a>

## 更新日志

[更新日志](https://github.com/BySlin/umi-plugin-electron-builder/blob/master/CHANGELOG.md)

## Installation

仅支持umi3，2.0.0基于Vite

```
$ npm i umi-plugin-electron-builder@next --save-dev
```

or

```
$ yarn add umi-plugin-electron-builder@next --dev
```

安装之后会自动生成相关文件

默认安装最新版本的electron

自动生成主进程文件src/main/index.ts

自动在package.json增加

```json5
{
  "scripts": {
    "rebuild-deps": "electron-builder install-app-deps",
    "electron:dev": "umi dev electron",
    "electron:build:win": "umi build electron --win",
    "electron:build:mac": "umi build electron --mac",
    "electron:build:linux": "umi build electron --linux"
  },
  //这里需要修改成你自己的应用名称
  "name": "electron_builder_app",
  "version": "0.0.1"
}

```

### Electron 版本降级

你可以手动将package.json中的electron修改至低版本，插件与electron版本无关

## Usage
### 从1.x升级
1、去掉electron-webpack，electron-webpack-ts依赖

2、主进程文件src/main/main.ts变更为src/main/index.ts

3、删除mainWebpackConfig，增加viteConfig，配置参考 https://vitejs.dev/config/

4、src/main/tsconfig.json变为可选

### 开发

```
$ umi dev electron
```

### 打包

如报错请在对应系统上打包，路径不能有中文

```
//windows
$ umi build electron --win
//mac
$ umi build electron --mac
//linux
$ umi build electron --linux
//按平台打包
$ umi build electron --win --ia32    //32位
$ umi build electron --win --x64     //64位
$ umi build electron --win --armv7l  //arm32位
$ umi build electron --win --arm64   //arm64位
```

### 使用node环境下运行的模块

例：使用serialport插件

```
$ npm i serialport @types/serialport -S
```

.umirc.ts

```javascript
import { defineConfig } from 'umi';

export default defineConfig({
  electronBuilder: {            //可选参数
    mainSrc: 'src/main',        //默认主进程目录
    preloadSrc: 'src/preload',  //默认preload目录，可选，不需要可删除
    routerMode: 'hash',         //路由 只能是hash或memory
    outputDir: 'dist_electron', //默认打包目录
    externals: ['serialport'],  //不配置的无法使用
    rendererTarget: 'electron-renderer', //构建目标electron-renderer或web，使用上下文隔离时，必须设置为web
    viteConfig(config: InlineConfig, type: ConfigType) { //主进程Vite配置
      //配置参考 https://vitejs.dev/config/
      //ViteConfigType分为main和preload可分别配置
    },
    builderOptions: { //配置参考 https://www.electron.build/configuration/configuration
      appId: 'com.test.test',
      productName: '测试',
      publish: [
        {
          provider: 'generic',
          url: 'http://localhost/test',
        },
      ],
    }//electronBuilder参数
  },
  routes: [
    { path: '/', component: '@/pages/index' },
  ],
});
```
在Electron10以上使用[contextIsolation](https://www.electronjs.org/docs/tutorial/context-isolation)时rendererTarget需要设置成web

builderOptions[参考Electron Builder](https://www.electron.build/configuration/configuration)

### 已知问题
esbuild 暂不支持 typescript decorator metadata

Vite与typeorm冲突，typeorm在主进程无法使用 

相关Issue https://github.com/evanw/esbuild/issues/257
