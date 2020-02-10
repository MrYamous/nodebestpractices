# Cacher les détails des erreurs au client

### Un paragraphe d'explication

Exposer les details des erreurs de l'application au client en production doit être évité du fait du risque de l'exposition de détails sensibles de l'application comme le chemin vers des fichiers du serveur, les modules tiers utilisés, et d'autres processus internes de l'application qui pourraient être exploités par un attaquant.
Express vient avec un gestionnaire d'erreurs intégré, qui s'occupe de toutes les erreurs qui pourraient être recontrées dans l'application. Cette fonction de gestion des erreurs par défaut de l'intergiciel (NdT *middleware*) est ajouté à la fin de la pile de fonctions de l'intergiciel.  
Si vous passez une erreur à `next()` et que vous ne la traitez pas dans un gestionnaire d'erreur personnalisé, elle sera traitée par le gestionnaire d'erreurs intégré d'Express; l'erreur sera affichée au client avec la pile d'erreurs (NdT *stacktrace*). Ce comportement sera vrai quand `NODE_ENV` est fixé à `development`, toutefois quand `NODE_ENV` est fixé à `production`, la pile d'erreurs n'est pas écrite, seulement le code de la réponse HTTP.

### Exemple de code: Le gestionnaire d'erreurs d'Express

```javascript
// Traitement des erreurs en production
// Pas de fuite de pile d'erreurs vers l'utilisateur
app.use((err, req, res, next) => {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});
```

### Ressources additionnelles

🔗 [Documentation Express.js sur le traitement des erreurs](https://expressjs.com/en/guide/error-handling.html)
