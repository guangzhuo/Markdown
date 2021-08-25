### 浏览器兼容


```javascript
// Opera 8.0+
export const isOpera = (!!window.opr && !!window.opr.addons) || !!window.opera || navigator.userAgent.indexOf(' OPR/') >= 0;

// Firefox 1.0+
export const isFirefox = typeof InstallTrigger !== 'undefined';

// Safari 3.0+ "[object HTMLElementConstructor]"
// eslint-disable-next-line no-undef
export const isSafari = /constructor/i.test(window.HTMLElement) || (function (p) { return p.toString() === '[object SafariRemoteNotification]'; })(!window['safari'] || (typeof safari !== 'undefined' && safari.pushNotification));

// Internet Explorer 6-11
export const isIE = /*@cc_on!@*/false || !!document.documentMode;

// Edge 20+
export const isEdge = !isIE && !!window.StyleMedia;

// Chrome
export const isChrome = (!!window.chrome && (!!window.chrome.webstore || !!window.chrome.runtime)) || (navigator.userAgent.indexOf('Chrome') !== -1);

// Edge (based on chromium) detection
export const isEdgeChromium = isChrome && (navigator.userAgent.indexOf('Edg') !== -1);


// Blink engine detection
export const isBlink = (isChrome || isOpera) && !!window.CSS;
```
### 环境变量


```javascript
// 判断当前是否开发环境
export const isDev = process.env.REACT_APP_ENV === 'development'

// 判断当前是否测试环境
export const isTest = process.env.REACT_APP_ENV === 'test'

// 判断当前是否预发环境
export const isPre = process.env.REACT_APP_ENV === 'preissue'

// 判断当前是否线上环境
export const isProd = process.env.REACT_APP_ENV === 'production'

// 获取当前环境
export const getEnv = process.env.REACT_APP_ENV
```

### scroll

```javscript
 window.addEventListener('scroll', (e) => {
       clearTimeout(this.scrollTime)
       this.scrollTime = setTimeout(() => {
         this.bindHandleScroll(e)
       }, 60)
    })
  }
  
  bindHandleScroll = (e) => {
    // 滚动的高度(兼容多种浏览器)
    const scrollTop = (e.srcElement ? e.srcElement.documentElement.scrollTop : false)  || window.pageYOffset || (e.srcElement ? e.srcElement.body.scrollTop : 0);
    const browserHeight = window.innerHeight;

    // 判断用户当前是否进行了横向滚动，如果用户发生了横向滚动，则设置元素为static
    const scrollLeft = (e.srcElement ? e.srcElement.documentElement.scrollLeft : false) || window.pageXOffset  || (e.srcElement ? e.srcElement.body.scrollLeft : 0);
 }
```
