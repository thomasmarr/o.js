# THIS IS A FORK OF THE o.js PROJECT
The o.js project states that it runs on node. I tried unsuccessfully to use it on a node (12.22.1) project. This fork is a fix which enables it to run in my project environment. It may be unnecessary in your environment. Please try o.js before using this.

# o.js

o.js is a isomorphic Odata Javascript library to simplify the request of data. The main goal is to build a **standalone, lightweight and easy** to understand Odata lib.

## Install

```
npm install odata-node
```

## Usage in node
```javascript
const o = require('odata-node').o;

// promise example
o('http://my.url')
  .get('resource')
  .then((data) => console.log(data));
```

## CRUD examples
> The following examples using async/await but for simplicity we removed the async deceleration. To make that work this example must be wrapped in an async function or use promise.

### **C**reate (POST):
```javascript
const data = {
  FirstName: "Bar",
  LastName: "Foo",
  UserName: "foobar",
}

const response = await o('http://my.url')
  .post('User', data)
  .query(); 

console.log(response); // E.g. the user 
```

### **R**ead (GET):
```javascript
const response = await o('http://my.url')
  .get('User')
  .query({$filter: `UserName eq 'foobar'`}); 

console.log(response); // If one -> the exact user, otherwise an array of users
```

### **U**pdate (Patch):
```javascript
const data = {
  FirstName: 'John'
}

const response = await o('http://my.url')
  .patch(`User('foobar')`, data)
  .query(); 

console.log(response); // The result of the patch, e.g. the status code
```

### **D**elete:
```javascript
const response = await o('http://my.url')
  .delete(`User('foobar')`)
  .query(); 

console.log(response); // The status code
```


## Options
You can pass as a second option into the `o` constructor options. The signature is:
```typescript
function o(rootUrl: string | URL, config?: OdataConfig | any)
```
Basic configuration is based on [RequestInit](https://developer.mozilla.org/en-US/docs/Web/API/Request/Request) and additional [odata config](src/OdataConfig.ts). By default o.js sets the following values:
```
{
    batch: {
      boundaryPrefix: "batch_",
      changsetBoundaryPrefix: "changset_",
      endpoint: "$batch",
      headers: new Headers({
        "Content-Type": "multipart/mixed",
      }),
      useChangset: false,
      useRelativeURLs: false,
    },
    credentials: "omit",
    fragment: "value",
    headers: new Headers({
      "Content-Type": "application/json",
    }),
    mode: "cors",
    redirect: "follow",
    referrer: "client",
    onStart: () => null,
    onFinish: () => null,
    onError: () => null,
  }
```

## Query
The following query options are supported by `query()`, `fetch()` and `batch()` by simply adding them as object:

```typescript
$filter?: string;
$orderby?: string;
$expand?: string;
$select?: string;

$skip?: number;
$top?: number;
$count?: boolean;
$search?: string;
$format?: string;
$compute?: string;
$index?: number;
[key: string]: any; // allows to add anything that is missing
```
> The $count flag will add an inline count property as metadata to a query response. In order to just retrieve the count, you'll have query the $count resource, such as

```typescript
oHandler.get('People/$count').query().then((count) => {})
```
   
The queries are always attached as the [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams).

## Just fetching
The lib tries to parse the data on each request. Sometimes that is not wanted (e.g. when you need a status-code or need to access odata meta data), therefore you can use the `.fetch` method that acts like the default [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API).

## Batching
By default o.js chains request in sequent. You can batch them together by using `batch()`. They are then send to the defined batch endpoint in the config. Changsets are at the moment in a experimental phase and needs to be enabled in the config.

## Polyfills
Polyfills are automatically added for node.js. If you like polyfills to support IE11 please include the `dist/umd/o.polyfill.js` file.
