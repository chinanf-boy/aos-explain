# aos [![explain](http://llever.com/explain.svg)](https://github.com/chinanf-boy/Source-Explain)

「 滚动 动画 库 」

    
Explanation

> "version": "1.0.0"

[github source](https://github.com/michalsnik/aos)

> [中文](./readme.md) | ~~[english](./readme.en.md)~~

---

作者曰:

👉 为了更好理解这是如何工作的, 我建议你查看我[my post on CSS-tricks:english](https://css-tricks.com/aos-css-driven-scroll-animation-library/).

### 🚀 [Demo](http://michalsnik.github.io/aos/)


###  AOS 如何 工作

AOS背后的想法很简单：

根据您提供的设置观察所有 `element` 及其位置。然后添加/删除类 `aos-animate`

当然，在实践中，并不总是那么容易，但AOS背后的想法就这么简单。动画的每个方面都由CSS处理。

---

本目录

---

## package.json

``` json
  "main": "dist/aos.js",
```

既然是 `dist`, 自然用了构建工具

## webpack.config.js

<details>

``` js
var webpack = require('webpack');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var autoprefixer = require('autoprefixer');

module.exports = {
  entry: './src/js/aos.js', // 出口
  output: { // 输入
    path: './dist',
    publicPath: 'dist/',
    filename: 'aos.js',
    library: 'AOS',
    libraryTarget: 'umd',
  },
  devServer: {
    contentBase: 'demo/' // 开发服务器, 基础目录
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel-loader' // 变通用js
      },
      {
        test: /\.scss$/, // css预处理
        loader: ExtractTextPlugin.extract("style-loader", "css-loader?sourceMap!sass-loader!postcss-loader")
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin('aos.css'), // css 复制
    new webpack.optimize.UglifyJsPlugin() // 缩小
  ]
}

```


</details>

在接触主code前, 我们从 `aos` 的使用方法认清方向

### Use aos

https://github.com/michalsnik/aos#-setup

1. 安装

2. 启动

``` html
  <script>
    AOS.init();
  </script>
```

3. 设置

``` html
  <div data-aos="animation_name">
```

> 重点 `AOS.init();`

---

### aos.js

`src/js/aos.js`

#### import

``` js
import styles from './../sass/aos.scss';

// Modules & helpers
import throttle from 'lodash.throttle'; // 一段时间能用一次
import debounce from 'lodash.debounce'; // 中间空一段时间才被用一次

import observe from './libs/observer';

import detect from './helpers/detector';
import handleScroll from './helpers/handleScroll';
import prepare from './helpers/prepare';
import elements from './helpers/elements';
```

#### options

``` js
/**
 * 默认配置
 */
let options = {
  offset: 120,
  delay: 0,
  easing: 'ease',
  duration: 400,
  disable: false,
  once: false,
  startEvent: 'DOMContentLoaded',
  throttleDelay: 99,
  debounceDelay: 50,
  disableMutationObserver: false,
};

```

#### init

- [elements](./help.md#elements)


``` js
/**
 * 初始化 AOS, 其实作者的注释很清楚的
 */
const init = function init(settings) {
  options = Object.assign(options, settings); // 创建选项，将默认值与用户定义的选项合并

  // 用元素创建初始数组 - > 稍后用prepare（）完成
  $aosElements = elements();

  /**
   * 如果设置了选项`disable`，则不要初始化插件
   * 或者当浏览器不支持
   */
  if (isDisabled(options.disable) || browserNotSupported) {
    return disable();
  }

  // 将<body>的属性设置为全局设置--css依靠它
  document.querySelector('body').setAttribute('data-aos-easing', options.easing);
  document.querySelector('body').setAttribute('data-aos-duration', options.duration);
  document.querySelector('body').setAttribute('data-aos-delay', options.delay);

  // 将准备元素附加到options.startEvent，
  if (options.startEvent === 'DOMContentLoaded' &&
    ['complete', 'interactive'].indexOf(document.readyState) > -1) {
    // 如果默认的startEvent已经被触发，则初始化AOS
    refresh(true);
  } else if (options.startEvent === 'load') {
    // 如果启动事件是'加载' - 将侦听器附加到窗口
    window.addEventListener(options.startEvent, function() {
      refresh(true);
    });
  } else {
    // 收听options.startEvent并初始化AOS
    document.addEventListener(options.startEvent, function() {
      refresh(true);
    });
  }

  /**
   * 刷新窗口上的插件大小或方向更改
   */
  window.addEventListener('resize', debounce(refresh, options.debounceDelay, true));
  window.addEventListener('orientationchange', debounce(refresh, options.debounceDelay, true));

  /**
   * 处理滚动事件以在滚动上动画元素
   */
  window.addEventListener('scroll', throttle(() => {
    handleScroll($aosElements, options.once);
  }, options.throttleDelay));

  /**
   * 观察[aos]元素
   * 如果有东西被AJAX加载
   * 它会自动刷新插件
   */
  if (!options.disableMutationObserver) {
    observe('[data-aos]', refreshHard);
  }

  return $aosElements;
};
```

- [handleScroll](./help.md#handlescroll)
- [observe](./lib.md#observe)

---

- [options](#options)
- [refresh](#refresh)
- [disable](#disable)
- [isDisabled](#isdisabled)
- [export](#export)
- [others](#others)

---

### others

``` js
/**
 * Private variables
 */
let $aosElements = [];
let initialized = false;

// 选出 不支持的浏览器 (<=IE9)
// http://browserhacks.com/#hack-e71d8692f65334173fee715c222cb805
const browserNotSupported = document.all && !window.atob;

```



#### refresh

``` js
/**
 * 重载 AOS
 */
const refresh = function refresh(initialize = false) {
  // Allow refresh only when it was first initialized on startEvent
  if (initialize) initialized = true;

  if (initialized) {
    // Extend elements objects in $aosElements with their positions
    $aosElements = prepare($aosElements, options);
    // Perform scroll event, to refresh view and show/hide elements
    handleScroll($aosElements, options.once);

    return $aosElements;
  }
};


/**
 * 硬刷新
  *用新元素创建数组并刷新触发器
 */
const refreshHard = function refreshHard() {
  $aosElements = elements();
  refresh();
};

```

- [prepare](./help.md#prepare)


---

#### disable

``` js
/**
 * 禁用AOS
 * 删除所有属性以重置应用样式
 */
const disable = function() {
  $aosElements.forEach(function(el, i) {
    el.node.removeAttribute('data-aos');
    el.node.removeAttribute('data-aos-easing');
    el.node.removeAttribute('data-aos-duration');
    el.node.removeAttribute('data-aos-delay');
  });
};

```

#### isDisabled

``` js
/**
 * 根据提供的设置检查是否应禁用AOS
 */
const isDisabled = function(optionDisable) {
  return optionDisable === true ||
  (optionDisable === 'mobile' && detect.mobile()) ||
  (optionDisable === 'phone' && detect.phone()) ||
  (optionDisable === 'tablet' && detect.tablet()) ||
  (typeof optionDisable === 'function' && optionDisable() === true);
};

```

- [detect](./help.md#detect)



#### export
``` js
/**
 * Export Public API
 */

module.exports = {
  init,
  refresh,
  refreshHard
};

```