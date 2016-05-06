# wait-promise

[![npm status](https://img.shields.io/npm/v/wait-promise.svg)](https://www.npmjs.org/package/wait-promise)
[![build status](https://api.travis-ci.org/akira-cn/wait-promise.svg?branch=master)](https://travis-ci.org/akira-cn/wait-promise) 
[![dependency status](https://david-dm.org/akira-cn/wait-promise.svg)](https://david-dm.org/akira-cn/wait-promise) 
[![coverage status](https://img.shields.io/coveralls/akira-cn/wait-promise.svg)](https://coveralls.io/github/akira-cn/wait-promise)

Syntactic sugar for setTimeout and setInterval. Based on [ES6 Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise).

## Use it in nodeJS

A version compiled to ES5 in CJS format is published to npm as wait-promise.

```bash
npm install wait-promise
```

## Use it on browser

**wait-promise CDN**

```html
<script src="https://s5.ssl.qhimg.com/!0451677a/wait-promise.min.js"></script>
```

You can use it with any AMD loader or **standalone**

```js
var promise = wait.before(5000).until(function(){
  return typeof Babel !== 'undefined';
});

promise.then(function(){
  code = Babel.transform(code, { presets: ['es2015-loose', 'react', 'stage-0'] }).code;
}).catch(function(){
  console.error('Cannot load babeljs.');
});
```

## API Doc

* [check](#check)
* [until](#until)
* [till](#till)
* [before](#before)
* [after](#after)
* [limit](#limit)
* [every](#every)
* [sleep](#sleep)

### check

**wait.check(condition)** always returns a promise. If **condition** throws error or returns `false` value explicitly, the promise will be rejected.

```js
let i = 1;
let promise = wait.check(function(){
  return i < 1;
});
promise.catch(function(err){
  console.log(err.message); //will be check failed
});
```

### until

**wait.until(condition)**

Check condition async and re-check every 100ms until it neighter throws error nor returns `false`.

```js
let i = 0;
let promise = wait.until(function(){
	return ++i >= 10;
});
promise.then(function(){
	console.log(i); //i will be 10
});
```

**With async/wait**

```js
let until = wait.until;

async function foo(){
	let i = 0;
	
	await until(() => ++i > 10);
	bar();
}
```

### till

**wait.till(condition)**

Check condition async and re-check every 100ms till it **explicitly** returns `true`. If condition throws error, the promise will be rejected.

```js
let i = 0;
let promise = wait.till(function(){
  return ++i >= 10;
});
promise.then(function(){
  console.log(i); //i will be 10
});
```

### before

**wait.before(millisec).until(condition)**

Check condition in `millisec`. If time out, the promise will be rejected.

```js
let i = 0;
let promise = wait.before(200).until(function(){
	return ++i >= 10;
});
promise.catch(function(err){
	console.log(i, err.message); //2 check failed
});
```

### after

**wait.after(millisec).check(condition)**

Check condition after `millisec`.

```js
let i = 1;
setTimeout(function(){
	++i;
}, 0); 
let promise = wait.after(1000).check(function(){
	return i > 200;
});
```

### limit

Check condition with limit times. If exceed the limit, the promise will be rejected.

```js
let i = 0;
let p = wait.limit(10).until(function(){
  i++;
  return false;
});
return p.catch(function(){
  console.log(i); //i will be 10
});
```

### every

**wait.every(millisec[,limit]).until(condition)**

Change time interval from 100ms to `millisec`。

```js
let i = 0;
let promise = wait.every(10).before(200).until(function(){
	return ++i >= 10;
});
promise.then(function(){
	console.log(i) // i will be 10
});
```

**every with limit**

`wait.every(1, 10)` is equal to `wait.every(1).limit(10)` 

```js
let i = 0;
let p = wait.every(1, 10).till(function(){
  return ++i >= 10;
});
p.catch(function(){
  console.log(i); //i will be 10
});
```

### sleep

**wait.sleep(millisec)**

Do nothing but sleep `millisec`

```js
var promise = wait.sleep(200);
promise.then(function(){
	//do sth.
});
```

**With async/await**

```js
let sleep = wait.sleep;

async function foo(){
	await sleep(500);
	bar();
} 
```

## LICENSE

[MIT](LICENSE)
