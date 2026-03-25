# Podman Setup

POdman est une alternative open-source et lightweight à Docker.

## Installation Podman

Selon OS, voir la [doc](https://podman.io/docs/installation).
Pour Windows, il est nécessaire d'activer les WSL.
Pour vérifier s'ils sont installés, lancer dans un powershell `wsl --version`.
Si une erreur survient, lancer `wsl --install` et redémarrer.
L'installation de Podman Desktop permet d'installer Podman et fournit des aides pour Windows si nécessaire.

## Installation Podman Desktop

Podman Desktop est un gestionnaire graphique des images, pods et containers, qu'ils soient dans Podman ou Docker.

Selon l'OS, voir la [doc](https://podman-desktop.io/downloads).
L'installation de Podmanest la seule obligatoire, kubectl et compose ne sont pas nécessaires.

## Création d'un pod

On créer ensuite un pod, un groupement de plusieurs images, nommé database, qui nous servira à lancer facilement notre base postgres ainsi que pgadmin, une administrateur graphique de notre postgres.

`podman pod create --name database -p 9876:80 -p 5432:5432`

On remarque ici la gestion des port ave cle `-p` : le port de gauche est celui qui sera exposé sur le réseau externe et celui de droite est celui auquel il est branché dans le réseau interneau pod.
Exemple: `-p 1234:0987` nous dis qu'une image du pod qui s'expose sur le port 0987 sera accessible sur le port 1234 pour tous les services en dehors du pod.
Ici, le port 80 est la port par défaut de pgadmin, et 5432 celui de postgres.
Ils seront accessibles respectivement sur les ports 9876 et 5432 pour toutes les application ou services en dehors du pod.

## Ajout des images de postgres et pgadmin dans le pod

On ajoute ensuite nos 2 applications au pod.

```
podman run -d --name pgadmin --pod database -e PGADMIN_DEFAULT_EMAIL=julien@andre.com -e PGADMIN_DEFAULT_PASSWORD=pgpass dpage/pgadmin4

podman run -d --name postgres --pod database -e POSTGRES_PASSWORD=postgrespass postgres:latest
```

Il suffit ensuite de lancer et de stopper notre pod database pour lancer et stopper nos 2 applications.

PgAdmin est accessible dans n'importe quel browser sur l'adresse `http://localhost:9876`

# Ressources

- [Podman](https://podman.io/)
- [Podman Desktop](https://podman-desktop.io/)
