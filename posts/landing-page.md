---
title: '落地页实现'
date: 2020-03-11 17:03:19
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
## 通过scheme唤起App

### 方式一
```javascript
window.location.href = `scheme://xxx`;
```

### 方式二(iframe)
```javascript
const _iframe = document.createElement('iframe');
_iframe.style.display = 'none';
document.body.appendChild(_iframe);
_iframe.src = `scheme://xxx`;
window.setTimeout(function () {
    document.body.removeChild(_iframe);
}, 2000);
```


## 判断用户是否安装应用，未安装自动下载(workaround)
打开落地页时，浏览器会弹出是否打开应用的弹框，同时触发`blur`事件，未安装应用则不会弹出。可通过时间差来判断用户是否安装应用。

```javascript
const launchAppByIframe = (scheme: string, autoDownload: string | false = false) => {
  let time = 0;
  let shouldDownload = true;
  if (autoDownload) {
    window.addEventListener('blur', () => {
      if (Date.now() - time < 500) {
        shouldDownload = false;
      }
    }, { once: true });
  }
  const _iframe = document.createElement('iframe');
  _iframe.style.display = 'none';
  _iframe.addEventListener('load', () => {
    time = Date.now();
    setTimeout(() => {
      const triggered = localStorage.getItem('triggered');
      if (autoDownload && !triggered && shouldDownload) {
        window.location.href = autoDownload;
        localStorage.setItem('triggered', '1');
      }
    }, 500);
  });
  document.body.appendChild(_iframe);
  _iframe.src = scheme;
  window.setTimeout(function () {
    document.body.removeChild(_iframe);
  }, 2000);
}
```


## 判断用户设备
简易实现，更多请参考[ua-parser-js](https://www.npmjs.com/package/ua-parser-js)
```javascript
type IPlatform = 'Windows' | 'MacOS' | 'Android' | 'iOS';
export const getPlatform = (defaultPlatform: IPlatform = 'Android'): IPlatform => {
  const ua = window.navigator.userAgent.toLocaleLowerCase();
  const matchMap: { [key in IPlatform]: RegExp } = {
    Android: /x11|android|linux/,
    iOS: /iphone|ipad|ipod/,
    Windows: /windows/,
    MacOS: /mac/,
  };
  const platforms = Object.keys(matchMap);
  for (let p of platforms as IPlatform[]) {
    if (matchMap[p].test(ua)) {
      return p;
    }
  }
  return defaultPlatform;
}
```


## 解析query
```javascript
const getQuery = (): Record<string, string> => {
  const query = window.location.search.slice(1);
  if (!query) return {};
  let data: Record<string, string> = {};
  query.split('&').forEach(kv => {
    const keypair = kv.split('=');
    data[keypair[0]] = keypair[1] || '';
  });
  return data;
}
```


## 判断是否在微信中打开
以此显示popup，提示用户点击右上角，在Safari中打开
```javascript
const isInWechat = (): boolean => {
  const ua = window.navigator.userAgent.toLocaleLowerCase();
  return /micromessenger/.test(ua);
};
```


## Tips

### 页面高度
页面高度不宜设置为100vh，在手机Safari等浏览器中依然会出现滚动条。
解决办法
```css
html, body, #app {
    height: 100%;
}
```