# Router [![Build Status](https://www.travis-ci.com/coldbrewjs/router.svg?branch=master)](https://travis-ci.com/movillnet/movill-manager-client)

API Router for Readable Code.
## Table of Contents

  - [Features](#features)
  - [Browser Support](#browser-support)
  - [Installing](#installing)
  - [Example](#example)
  - [License](#license)

## Features

- mostly covered basic HTTP method
- provide method which make payload resource for HTTP client
- easily set and override HTTP client configuration with less code 
- modify partially basic routing configuration with builder pattern 

## Browser Support

![Chrome](https://raw.github.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.github.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![Safari](https://raw.github.com/alrra/browser-logos/master/src/safari/safari_48x48.png) | ![Opera](https://raw.github.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Edge](https://raw.github.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![IE](https://raw.github.com/alrra/browser-logos/master/src/archive/internet-explorer_9-11/internet-explorer_9-11_48x48.png) |
--- | --- | --- | --- | --- | --- |
Latest ✔ | Latest ✔ | Latest ✔ | Latest ✔ | Latest ✔ | 11 ✔ |

## Installing

Using npm:

```bash
$ npm install @coldbrewjs/router
```

Using yarn:

```bash
$ yarn add @coldbrewjs/router
```

## Example

Setting up a Routing environment using Configure

- Use Configure to set a global environment for api routing
- All instance of Router inherit routing environment from Configure
- Recommend you use Configure at the top of file directory such as app.tsx, index.ts

```typescript
import { Configure } from '@coldbrewjs/router';

Configure.getInstance.baseUri('http://api-test.com/v1');
Configure.getInstance.header({
  accessToken: '1234',
  transactionId: '5678'
});

```

Performing a `GET` request

```typescript
import { Router } from '@coldbrewjs/router';

const router = new Router();
/** 
 *  already have string value 'http://http://api-test.com/v1' as prefix uri
 *  it always routes with header { accessToken: '1234', transactionId: '5678' }
 */

// Make a request with callback pattern
router.uri('/user?id=12345')
  .get((err: RouterError, response: RouterResponse) => {
        if (err) {
          console.log('err:', err);
        }

        console.log(response);
  });

// Make a request with promise pattern
router.uri('/user?id=12345')
  .get()
  .then((response: RouterResponse) => {
          console.log(response);
  })
  .catch((err: RouterError) => {
          console.log(err);
  });

// Make a request with async/await pattern
async function getUser() {
  try {
    const results: RouterResponse = await router.uri('/user?id=12345').get();
  } catch (err) {
    console.log(err);
  }
}
```

> **NOTE:** `async/await` is part of ECMAScript 2017 and is not supported in Internet
> Explorer and older browsers.

Performing a `POST` request

```typescript
import { Router } from '@coldbrewjs/router';

const router = new Router();

// Make a request with callback pattern
router.uri('/user')
      .payload({
                id: 'abcdef',
                password: 'qwerty',
              })
      .post((err: RouterError, response: RouterResponse) => {
              if (err) {
                  console.log(err);
              }

              console.log(response);
      });

// Make a request with promise pattern
router.uri('/user')
      .payload({
                id: 'abcdef',
                password: 'qwerty',
              })
      .post()
      .then((response: RouterResponse) => {
            console.log(response);
      })
      .catch((err: RouterError) => {
            console.log(err);
      })
      .finally(() => {
            console.log('done...!');    
      });

// Make a request with async/await pattern
try {
  const response = 
      await router.uri('/user')
                  .payload({
                            id: 'abcdef',
                            password: 'qwerty',
                          })
                  .post();

  console.log(response);
} catch(err) {
  console.log(err);
}
```

Override routing environment dynamically 

```typescript
import { Configure, Router } from '@coldbrewjs/router';

Configure.getInstance.baseUri('https://dev.api.com/v1');
Configure.getInstance.header({
  accessToken: '1234',
  transactionId: '5678'
});

async function dynamicRouting(isDev: boolean) {
  const router = new Router():

  if (isDev) {
    /**
     * Url: https://dev.api.com/v1/ping
     * Header: { accessToken: '1234', transactionId: '5678' }
    */
    return await router.uri('/ping').get();
  } else {
     /**
     * Url: https://stage.api.com/v1/ping
     * Header: { accessToken: '1234', transactionId: '8765', customToken: '0000' }
    */
    return await router.overrideHeader({
      transactionId: '8765',
      customToken: '0000'
    }).overrideUrl('https://stage.api.com/v1/ping').get();
  }
}

try {
  const response = await dynamicRouting(process.env.NODE_ENV === 'development' ? true : false);
} catch(err) {
  console.log(err);
}
```

> **NOTE:** You must use overrideHeader method at every single router instance if you custom http header without Configure.

Performing multiple concurrent requests

```typescript
import { Router } from '@coldbrewjs/router';

const products = 'http://api.service.com/products';
const categories = 'http://api.service.com/categories';
const sellers = 'http://api.service.com/sellers';

const firstRouter = new Router().uri(products).get();
const secondRouter = new Router().uri(categories).get();
const thirdRouter = new Router().uri(sellers).get();

Promise.all([firstRouter, secondRouter, thirdRouter]).then(
    (response) => {
        console.log(response[0]);
        console.log(response[1]);
        console.log(response[2]);
    },
);
```

## License

[MIT](LICENSE)
