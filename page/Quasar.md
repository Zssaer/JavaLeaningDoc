# Quasar-Vue.js的第三方UI框架

## 前言

![](../picture/logo-vertical.svg)

[Quasar](https://github.com/quasarframework)是一款专门为Vue.js设计的一款的UI框架，该项目在GitHub上目前是Vue.js Start数量最多的UI框架。

在国内目前还未有大名气，但是Quasar是在国外非常火的Vue框架。

官方介绍中这么描述Quasar特点：

- 它基于Vue.js
- 拥有开箱即用的提供给**网站**和**应用程序**的最先进的UI（遵循《Material指南》）
- 开箱即用的对桌面和**移动浏览器**（包括iOS Safari！）的最佳支持
- 通过与我们自己的CLI紧密集成，对每种构建模式（SPA、SSR、PWA、移动应用程序、桌面应用程序和浏览器扩展）提供了一流的支持，并提供了最佳的开发人员体验
- 易于自定义（CSS）和可扩展（JS）
- 这是最注重性能的框架
- 自动tree shaking
- 在我们的论坛和Discord聊天基础上的很棒的社区
- 具有包括新功能在内的定期发布周期
- 获得快速修复并听取社区的要求
- 处理整个开发经验（甚至包括创建应用程序的图标和启动画面）

Quasar配备了40多种开箱即用的语言包，其中包括了中文。 最重要的是，如果缺少您的语言包，则只需5分钟即可添加。

Quasar官方文档为：https://quasar.dev/，但是目前官方尚未拥有中文文档，需要结合翻译工具和一定英语阅读水平。



## 安装

目前Quasar共支持4种安装模式：Quasar CLI脚手架安装、独立安装、Vite插件安装、Vue CLI安装

### **Quasar CLI 脚手架安装**

Quasar CLI脚手架是Quasar框架的独特产品，它拥有自动安装Quasar功能，只需要在NPM或者Yarn安装Quasar CLI即可。**一切都将开箱即用**。

**前提：确保Node环境版本 >=12.22.1 并且 NPM > 6.14，注意不要使用不稳定的Node 13或者15。**

```bash
$ yarn global add @quasar/cli
# or
$ npm install -g @quasar/cli
```

安装完毕后，启动脚手架安装即可。

```bash
$ quasar create 项目名称
```

生成项目后，使用NPM、Yarn安装依赖包即可。



### **独立模式安装**

如果您想将Quasar嵌入到您现有的网站项目中，并以渐进的方式进行整合，那么请选择独立模块安装。

这里这种方式并不推荐，因为这会变得很麻烦，可以自行查看：https://quasar.dev/start/umd



### **基于Vite插件安装**

作为Vue作者Evan You最新开发的打包工具Vite，作者称其实未来的Vue官方打包工具，自然Quasar在V2版本推出了相关的Vite插件。

但是目前主要是由社区来进行开发的，所以可能会出现相关未知问题，官方并不推荐这种方法用作实际开发中。此外官方声明了，未来将会发布基于Quasar CLI + Vite的方法。

首先插件一个Vite项目，这里关于Vite如何进行安装，自行查看[Vite官方文档](https://vitejs.dev/guide/#scaffolding-your-first-vite-project)

```bash
$ yarn create vite my-vue-app --template vue

# 或者使用 NPM
$ npm init vite@latest my-vue-app --template vue
```

安装成功后，再安装Quasar的Vite插件。

```bash
$ yarn add quasar @quasar/extras
$ yarn add -D @quasar/vite-plugin sass@1.32.0
```

这里的`@quasar/extras`组件不是必须的，包含一些MD图标内容。

随后使用NPM、Yarn安装项目依赖包即可。

配置方面，我们需要修改项目的main.js文件:

```js
// FILE: main.js
import { createApp } from 'vue'
import { Quasar } from 'quasar'
import quasarLang from 'quasar/lang/zh-CN'

// Import icon libraries
import '@quasar/extras/material-icons/material-icons.css'

// Import Quasar css
import 'quasar/src/css/index.sass'

// Assumes your root component is App.vue
// and placed in same folder as main.js
import App from './App.vue'

const myApp = createApp(App)

myApp.use(Quasar, {
  plugins: {}, // import Quasar plugins and add here
  lang: quasarLang,
})

// Assumes you have a <div id="app"></div> in your index.html
myApp.mount('#app')
```

还需要修改Vite.config.js文件:

```js
// FILE: vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { quasar, transformAssetUrls } from '@quasar/vite-plugin'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue({
      template: { transformAssetUrls }
    }),

    quasar({
      sassVariables: 'src/quasar-variables.sass'
    })
  ]
})
```

在src文件夹下新建一个`quasar-variables.sass`文件，用作存储UI框架的配饰，以及还需爱在项目根目录下还需要创建一个`quasar.conf.js`文件，用作导入使用的相关组件。

```css
// FILE (create it): src/quasar-variables.sass
$primary   : #1976D2
$secondary : #26A69A
$accent    : #9C27B0

$dark      : #1D1D1D

$positive  : #21BA45
$negative  : #C10015
$info      : #31CCEC
$warning   : #F2C037
```

```js
// FILE quasar.conf.js
framework: {
  plugins: [ 'QLayout', 'QHeader','QDrawer',' QToolbar','QToolbarTitle','QAvatar',
  'QTabs','QRouteTab','QPageContainer','QFooter', ...
 ]
}
```



### Vue CLI插件安装

使用VueCli创建安装，基于WebPack打包。

在创建项目之前需要先查看当前本地全局安装的Vue-CLI版本。

``` bash
vue --version
```

如果已安装过Vue CLI 2.x.x，你需要使用以下命令卸载它：

```bash
$ npm uninstall -g vue-cli
# 或（取决于您安装的方式）
$ yarn global remove vue-cli
```

需要安装Vue-CLI (v4+)以上的版本才可以:

```bash
$ npm install -g @vue/cli 
```

在使用Vue-CLI创建项目后，使用命名安装Quasar即可:

```bash
$ vue add quasar
```



