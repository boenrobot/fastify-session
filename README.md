# fastify-session

![Build Status](https://github.com/SerayaEryn/fastify-session/workflows/ci/badge.svg)
[![Coverage Status](https://coveralls.io/repos/github/SerayaEryn/fastify-session/badge.svg?branch=master)](https://coveralls.io/github/SerayaEryn/fastify-session?branch=master)
[![NPM version](https://img.shields.io/npm/v/fastify-session.svg?style=flat)](https://www.npmjs.com/package/fastify-session)
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)
[![Greenkeeper badge](https://badges.greenkeeper.io/SerayaEryn/fastify-session.svg)](https://greenkeeper.io/)

A session plugin for [fastify](http://fastify.io/). 
Requires the [fastify-cookie](https://github.com/fastify/fastify-cookie) plugin.

## Install

```
npm install fastify-session
```

## Usage

```js
const fastify = require('fastify');
const fastifySession = require('fastify-session');
const fastifyCookie = require('fastify-cookie');

const app = fastify();
app.register(fastifyCookie);
app.register(fastifySession, {secret: 'a secret with minimum length of 32 characters'});
```
Store data in the session by adding it to the `session` decorator at the `request`:
```js
app.register(fastifySession, {secret: 'a secret with minimum length of 32 characters'});
app.addHook('preHandler', (request, reply, next) => {
  request.session.user = {name: 'max'};
  next();
})
```
**NOTE**: For all unencrypted (HTTP) connections, you need to set the `secure` cookie option to `false`. Look below for all cookie options and their details.  
The `sessionStore` decorator of the `request` allows to get, save and delete sessions.
```js
app.register(fastifySession, {secret: 'a secret with minimum length of 32 characters'});
app.addHook('preHandler', (request, reply, next) => {
  const session = request.session;
  request.sessionStore.destroy(session.sessionId, next);
})
```

## Examples

* [Authentication](https://github.com/fastify/example/tree/master/fastify-session-authentication)

## API
### session(fastify, options, next)
The session plugin accepts the following options. It decorates the request with the `sessionStore` and a `session` object. The session data is stored server side using the configured session store. 
#### options
##### secret (required) 
The secret used to sign the cookie. Must have length 32 or greater.
##### cookieName (optional) 
The name of the session cookie. Defaults to `sessionId`.
##### cookie
The options object used to generate the `Set-Cookie` header of the session cookie. May have the following properties:
* `path` - The `Path` attribute. Defaults to `/` (the root path). 
* `maxAge` - A `number` in milliseconds that specifies the `Expires` attribute by adding the specified milliseconds to the current date. If both `expires` and `maxAge` are set, then `expires` is used.
* `httpOnly` - The `boolean` value of the `HttpOnly` attribute. Defaults to true.
* `secure` - The `boolean` value of the `Secure` attribute. Set this option to false when communicating over an unencrypted (HTTP) connection. Defaults to true.
* `expires` - The expiration `date` used for the `Expires` attribute. If both `expires` and `maxAge` are set, then `expires` is used.
* `sameSite`- The `boolean` or `string` of the `SameSite` attribute. 
* `domain` - The `Domain` attribute.
##### store
A session store. Needs the following methods: 
* set(sessionId, session, callback)
* get(sessionId, callback)
* destroy(sessionId, callback)

Compatible to stores from [express-session](https://github.com/expressjs/session).

Defaults to a simple in memory store.</br>
**Note**: The default store should not be used in a production environment because it will leak memory.

##### saveUninitialized (optional) 
Save sessions to the store, even when they are new and not modified. Defaults to `true`.
Setting this to `false` can be useful to save storage space and to comply with the EU cookie law.

#### request.session

Allows to access or modify the session data.

#### request.destroySession(callback)

Allows to destroy the session in the store

#### Session#touch()

Updates the `expires` property of the session. 

#### Session#regenerate()

Regenerates the session by generating a new `sessionId`.

## License

[MIT](./LICENSE)
