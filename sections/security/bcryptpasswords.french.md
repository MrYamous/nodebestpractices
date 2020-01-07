# Éviter d'utiliser la librairie Crypto de Node.js pour les mots de passe, utiliser Bcrypt

### Un paragraphe d'explication

Quand on stocke les mots de passe des utilisateurs, utiliser un algorithme de hachage adaptatif comme bcrypt, offert par le [module npm bcrypt](https://www.npmjs.com/package/bcrypt), est recommandé plutôt que d'utiliser le module crypto natif de Node.js. `Math.random()` ne devrait aussi jamais être utilisée dans la génération de mot de passe ou de token du fait de sa prévisibilité.

Le module `bcrypt` ou autre similaire devrait être utilisé par opposition à l'implémentation JavaScript, quand on utilise `bcrypt`, un nombre de 'tours' peut être spécifié afin de fournir un hash sécurisé. Cela défini le facteur travail ou le nombre de 'tours' pour lesquels les données sont traitées, et plus de tours de hachage mène à un hash plus sécurisé (bien que cela coûte plus de temps du CPU). L'introduction des tours de hachage signifie que le facteur de force brute est significativement réduit, car les craqueurs de mot de passe sont réduits, ce qui augmente le temps requis pour une tentative.

### Exemple de code

```javascript
try {
// Générer de manière asynchrone un mot de passe sécurisé en utilisant 10 tours de hachage
  const hash = await bcrypt.hash('myPassword', 10);
  // Conserver le hash sécurité au niveau de l'utilisateur

  // Comparer le mot de passe entré avec le hash enregistré
  const match = await bcrypt.compare('somePassword', hash);
  if (match) {
   // Les mots de passe correspondent
  } else {
   // Les mots de passe ne correspondent pas
  } 
} catch {
  logger.error('could not hash password.')
}
```

### Ce que disent les autres blogueurs

Extrait du blog de [Max McCarty](https://dzone.com/articles/nodejs-and-password-storage-with-bcrypt):
> ... Ce n'est pas juste utiliser les bons algorithmes de hachage. J'ai beaucoup parlé de comment le bon outil inclus aussi l'ingrédient nécessaire de "temps" comme une partie de l'algorithme de hachage de mot de passe et de ce que cela signifie pour l'attaquant qui essaie de craquer les mots de passe par force brute.