

```javascript
import i18next from 'i18next'

i18next
  .use(initReactI18next)
  .use(LanguageDetector)
  .use(Backend)
  .init();

console.log(i18next.t('key'));

// Another example:

import i18next from 'i18next';
import Backend from 'i18next-http-backend';
import Cache from 'i18next-localstorage-cache';
import postProcessor from 'i18next-sprintf-postprocessor';
import LanguageDetector from 'i18next-browser-languagedetector';

i18next
  .use(Backend)
  .use(Cache)
  .use(LanguageDetector)
  .use(postProcessor)
  .init(options, callback);
```





```shell
save missing: true
npm install i18next-locize-backend //replace i18next-http-backend
```



Try to use i18next-http-backend, here comes the directory of an example when using Node.js as backend server: https://github.com/i18next/i18next-http-backend/tree/master/example/node





下拉菜单切换语言的时候，可以通过安装npm install flag-icon-css，来生成国家图标。

```html
import 'flag-icon-css/css/flag-icon.min.css'

<span className={`flag-icon flag-icon-${country_code} mx-2`}></span>
```



在Key前面加一个减号来避免escape，或者使用下面的设置，缺省情况下就是false。

```javascript
interpolation: {
      escapeValue: false, // not needed for react as it escapes by default
    },
```



![image-20230403230833729](/Users/xuhi/Library/Application Support/typora-user-images/image-20230403230833729.png)

