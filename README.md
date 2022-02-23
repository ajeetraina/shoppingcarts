# Redis Shopping Cart demo

This shopping cart is using Redis and RedisJson module functionalities, allowing you to save JSON as keys using methods like json_get and json_set

![alt text](https://github.com/redis-developer/basic-redis-shopping-chart-nodejs/blob/main/preview.png?raw=true)

## How it works

### How the data is stored:
* The products data is stored in external json file. After first request this data is saved in a JSON data type in Redis like: `JSON.SET product:{productId} . {product data in json format}`.
* The cart data is stored in a hash like: `HSET cart:{cartId} product:{productId} {productQuantity}`, where cartId is random generated value and stored in user session.

### How the data is modified:
* The product data is modified like `JSON.SET product:{productId} . {new product data in json format}`.
* The cart data is modified like `HSET cart:{cartId} product:{productId} {newProductQuantity}` or `HINCRBY cart:{cartId} product:{productId} {incrementBy}`.
* Product can be removed from cart like `HDEL cart:{cartId} product:{productId}`
* Cart can be cleared using `HGETALL cart:{cartId}` and then `HDEL cart:{cartId} {productKey}`.
* All carts can be deleted when reset data is requested like: `DEL cart:{cartId}`.

### How the data is accessed:
* Products: `SCAN {cursor} MATCH product:*` to get all product keys and then `JSON.GET {productKey}`.
* Cart: `HGETALL cart:{cartId}`to get quantity of products and `JSON.GET product:{productId}` to get products data.

## Hot to run it locally?

### Prerequisites

- Node - v12.19.0

```
% brew install node
```

```
node 15.5.0 is already installed
```

- NPM - v6.14.8

```
 % npm version
{
  npm: '7.3.0',
```

- Docker - v19.03.13 (optional)

### Local installation

Go to server folder and then:

```
# Environmental variables

Copy `.env.example` to `.env` file and fill environmental variables

REDIS_PORT: Redis port (default: 6379)
REDIS_HOST: Redis host (default: 127.0.0.1)
REDIS_PASSWORD: Redis password (default: demo)

# Run docker compose or install redis with RedisJson module manually. You can also go to https://redislabs.com/try-free/ and obtain necessary environmental variables

docker network create global
docker-compose up -d --build
```

The output should display:

```
docker-compose ps
             Name                           Command               State             Ports          
---------------------------------------------------------------------------------------------------
redis.redisshoppingcart.docker   docker-entrypoint.sh redis ...   Up      127.0.0.1:55000->6379/tcp
```

```
% docker-compose logs -f
Attaching to redis.redisshoppingcart.docker
redis.redisshoppingcart.docker | 1:C 14 Feb 2021 15:39:02.178 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis.redisshoppingcart.docker | 1:C 14 Feb 2021 15:39:02.178 # Redis version=6.0.9, bits=64, commit=00000000, modified=0, pid=1, just started
redis.redisshoppingcart.docker | 1:C 14 Feb 2021 15:39:02.178 # Configuration loaded
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.179 * Running mode=standalone, port=6379.
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.179 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.179 # Server initialized
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.179 # <ReJSON> JSON data type for Redis v1.0.7 [encver 0]
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.179 * Module 'ReJSON' loaded from /usr/lib/redis/modules/rejson.so
redis.redisshoppingcart.docker | 1:M 14 Feb 2021 15:39:02.180 * Ready to accept connections
```



# Install dependencies

```
npm cache clean && npm install
```

# Run dev server

```
npm run dev
```

```
redis-cli
127.0.0.1:6379> info modules
NOAUTH Authentication required.
127.0.0.1:6379> auth demo
OK
127.0.0.1:6379> info modules
# Modules
module:name=ReJSON,ver=10007,api=1,filters=0,usedby=[],using=[],options=[]
127.0.0.1:6379> exit
ajeetraina@Ajeets-MacBook-Pro server % npm run dev

> redis-shopping-cart-backend@1.0.0 dev
> nodemon src/index.js

[nodemon] 2.0.7
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node src/index.js`
App listening on port 3000
```


Go to client folder and then:

```
# Environmental variables

Copy `.env.example` to `.env` file

# Install dependencies

npm cache clean && npm install

# Serve locally

npm run serve
```

Output:

```
npm run serve

> redis-shopping-cart-client@1.0.0 serve
> vue-cli-service serve

 INFO  Starting development server...
40% building 19/28 modules 9 active .../cl

DONE  Compiled successfully in 12956ms                                                                                                                             9:57:52 PM


  App running at:
  - Local:   http://localhost:8081/ 
  - Network: http://192.168.1.5:8081/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
  
```


## Deployment

To make deploys work, you need to create free account in https://redislabs.com/try-free/, create Redis instance with `RedisJson` module and get informations - REDIS_ENDPOINT_URI and REDIS_PASSWORD. You must pass them as environmental variables.

### Google Cloud Run

[![Run on Google
Cloud](https://deploy.cloud.run/button.svg)](https://deploy.cloud.run/?git_repo=https://github.com/redis-developer/basic-redis-shopping-chart-nodejs.git)

### Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

### Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/git/external?repository-url=https://github.com/redis-developer/basic-redis-shopping-chart-nodejs&env=REDIS_ENDPOINT_URI,REDIS_PASSWORD)

# Troubleshooting

If you encounter the below issue while running ```npm run serve```

```
npm ERR! As of npm@5, the npm cache self-heals from corruption issues
npm ERR! by treating integrity mismatches as cache misses.  As a result,
npm ERR! data extracted from the cache is guaranteed to be valid.  If you
npm ERR! want to make sure everything is consistent, use `npm cache verify`
npm ERR! instead.  Deleting the cache can only make npm go slower, and is
npm ERR! not likely to correct any problems you may be encountering!
npm ERR! 
npm ERR! On the other hand, if you're debugging an issue with the installer,
npm ERR! or race conditions that depend on the timing of writing to an empty
npm ERR! cache, you can use `npm install --cache /tmp/empty-cache` to use a
npm ERR! temporary cache instead of nuking the actual one.
npm ERR! 
npm ERR! If you're sure you want to delete the entire cache, rerun this command
npm ERR! with --force.
```

Here's a fix:



```
npm cache clean --force && npm install --legacy-peer-deps
npm WARN using --force Recommended protections disabled.
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated @hapi/topo@3.1.6: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/bourne@1.3.2: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/address@2.1.4: Moved to 'npm install @sideway/address'
npm WARN deprecated @hapi/hoek@8.5.1: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated @hapi/joi@15.1.1: Switch to 'npm install joi'
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated eslint-loader@2.2.1: This loader has been deprecated. Please use eslint-webpack-plugin
npm WARN deprecated babel-eslint@10.1.0: babel-eslint is now @babel/eslint-parser. This package will no longer receive updates.

added 1383 packages, and audited 1384 packages in 1m

76 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```
