# Auth Based Token

Pour gérer les authorisations d'accès à vos endpoint, la solutions commune est de mettre au point un système de token qui sera utilisé pour identifier les users.

Le workflow est comme suit :

- un user envoie (POST) ses crendentials (username, password) au endpoint /authentication
- Le serveur vérifie si les credentials sont bons.
- Si oui, il génère un token et le renvoie au user.
- Si non, une erreur est envoyée, ici une 401 Unauthorized.

## Création du endpoint

On ajoute un endpoint "/authentication" qui sera chargé d'orchestrer l'authent des users.

## Ajout de la méthode POST

Ensuite, on ajoute une fonction annoté POST, pour récupérer les demande d'authent via un POST sur /autentication.
La requête permettant l'appel à cette fonction consomme dans notre cas les données d'un formulaire, donc MediaType.APPLICATION_FORM_URLENCODED.
Elle produira le token d'authefication, en JSON (MediaType.APPLICATION_JSON).
La fonction en elle même retourne un objet Response et prend le username et le password en paramètres, annoté du @FormParam(<name de l'input du formulaire>) afin de relier le paramètre de la fonction avec le champs de formulaire.

La méthode doit alors authenticate le user, générer le token et le renvoyer si tout se poasse bien.
Dans le cas contraire elle doit retourner une erreur.

## Recherche du User dans la DB

Afin de vérifier les credentials du user, il faut demander à la base de données si elle détient bien un user avec pour identifiants les credentials envoyés.
On fait une requète SQL demandant de trouver un user avec le username et le password tels qu'envoyés.
Si la requète SQL ne renvoie pas de resultat, alors on lève une exception, remontant l'information que l'authentification à échoué.
Dans le cas contraire on poursuit comme prévue avec le token.

# Comment tester

On lance un .html contenant un formulaire et les champs correspondant au paramètres attendus de la fonction d'authentification.
Si l'authent est valide, on devrait voir le token retourné, sinon une 401.

# Pour continuer

- Implémenter la génération d'un JWT
- Sécuriser un endpoint en demandant le token pour exécuter la fonction du endpoint

# Ressources

- [Tuto CodeLessGenie](https://www.codelessgenie.com/blog/how-to-implement-rest-token-based-authentication-with-jax-rs-and-jersey/)
- [Tuto Stackoverflow](https://stackoverflow.com/questions/26777083/how-to-implement-rest-token-based-authentication-with-jax-rs-and-jersey)
- [Builder Pattern](https://refactoring.guru/design-patterns/builder)
- [JWT Java](https://github.com/auth0/java-jwt)
- [Codes erreurs Http](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)

# Code complet

AuthenticationResource.java

```
@Path("/authentication")
public class AuthenticationResource {
    Database database = new Database();

    @POST
    @Consumes(MediaType.APPLICATION_FORM_URLENCODED)
    @Produces(MediaType.APPLICATION_JSON)
    public Response authenticateUser(@FormParam("username") String username,
                                     @FormParam("password") String password) {
        try {
            authenticate(username, password);
            String token = issueToken(username);
            return Response.ok(token).build();
        } catch (Exception e) {
            e.printStackTrace();
            return Response.status(Response.Status.UNAUTHORIZED).build();
        }
    }

    private void authenticate(String username,
                              String password) throws Exception {
        database.authenticateUser(username, password);
    }

    private String issueToken(String username) {
        return username.toUpperCase() + UUID.randomUUID();
    }
}
```

Database.java

```
public class Database {
        public void authenticateUser(String username, String password)
            throws SQLException {
        String selectUser
                = String.format(
                "SELECT * FROM users WHERE name='%s' AND password='%s'"
                , username
                , password);
        ResultSet resultSet = statement.executeQuery(selectUser);
        if (!resultSet.next()) {
            throw new SQLException("Invalid username or password");
        }
    }
}
```

index.html

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <form action="http://localhost:8080/api/authentication" method="POST">
      <input type="text" name="username" id="username" />
      <input type="password" name="password" id="password" />
      <input type="submit" value="Soumettre" />
    </form>
  </body>
</html>

```
