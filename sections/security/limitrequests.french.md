# Limitez les requêtes simultanées en utilisant un équilibreur ou un intergiciel

### Un paragraphe d'explication

La limitation de la cadence devrait être implémentée dans votre application pour protéger une application Node.js d'être submergée par trop de requêtes simultanées.
La limitation de la cadence est une tâche plus performante avec un service conçu pour celle-ci, comme nginx, cependant c'est aussi possible avec la librairie [rate-limiter-flexible](https://www.npmjs.com/package/rate-limiter-flexible) ou l'intergiciel (NdT middleware) comme [express-rate-limiter](https://www.npmjs.com/package/express-rate-limit) pour une application Express.js.

### Exemple de code : application Node.js avec [rate-limiter-flexible](https://www.npmjs.com/package/rate-limiter-flexible)
 
  ```javascript
 const http = require('http');
 const redis = require('redis');
 const { RateLimiterRedis } = require('rate-limiter-flexible');
 
 const redisClient = redis.createClient({
   enable_offline_queue: false,
 });
 
 // maximum 20 requêtes par seconde
 const rateLimiter = new RateLimiterRedis({
   storeClient: redisClient,
   points: 20,
   duration: 1,
   blockDuration: 2, // bloque pour deux secondes si plus de 20 points par seconde sont consommés
 });

 http.createServer(async (req, res) => {
    try {
    const rateLimiterRes = await rateLimiter.consume(req.socket.remoteAddress);
    // logique de l'application ici

    res.writeHead(200);
    res.end();
    } catch {
    res.writeHead(429);
    res.end('Too Many Requests');
    }
 })
   .listen(3000);
 ```

Vous pouvez trouver [plus d'exemples dans la documentation](https://github.com/animir/node-rate-limiter-flexible/wiki/Overall-example).

### Exemple de code: l'intergiciel d'Express de limitation de cadence pour certaines routes

En utilisant la librairie npm [express-rate-limiter](https://www.npmjs.com/package/express-rate-limit)

```javascript
const RateLimit = require('express-rate-limit');
// si derrière un proxy, il est important de s'assurer que l'IP du client est passée à req.ip
app.enable('trust proxy'); 
 
const apiLimiter = new RateLimit({
  windowMs: 15*60*1000, // 15 minutes
  max: 100,
});
 
// s'applique uniquement aux requêtes qui commencent par /user/
app.use('/user/', apiLimiter);
```

### Ce que disent les autres blogueurs

Extrait du [blog NGINX](https://www.nginx.com/blog/rate-limiting-nginx/):
> La limitation de la cadence peut être utilisée pour des raisons de sécurité, par exemple pour ralentir les attaques par force brute pour deviner un mot de passe. Cela peut aider à se protéger contre les attaques par déni de service en limitant le nombre de requêtes entrantes à une valeur typique pour les utilisateur réels, et (avec journalisation) identifie les URL ciblées. Plus généralement, c'est utilisé pour protéger en amont les serveurs de l'application d'être submergés par trop de requêtes d'utilisateurs en même temps.