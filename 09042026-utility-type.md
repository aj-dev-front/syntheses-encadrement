# Utility Types
Les utility type sont des types mis à disposition par typescript pour déclarer et surtout dériver des types d'autres type.

Il sont utiles pour conserver la cohérences des types et facilité la maintenance de l'application dans le temps.

Les principaux sont :

## Record<KeyType, TypeValue>
Permet de définir un objet de structure `{ [key: KeyType]: TypeValue}`, avec KeyType contraint à être string | number | symbol.
Très utile pour définir un objet qui n'a pas de structure précise, ou dont on ne connait pas la structure exact.

## Partial<Type>
Permet de définir un type dont les champs seront tous optionels.
Il évite ainsi de mettre des champs optionels dans le type de base, ou de redéclarer un type qu'il faudra maintenir.

## Required<Type>
Fait l'exact opposé de Partial.

## Pick<Type, fields>
Permet de créer un type à partir de Type dont on aura les champs fields sous la forme `"field1" | "field2"`.

## Omit<Type, fields>
Permet de créer un type à partir de Type dont on aura exclu les champs fields, sous la même forme que Pick

Ces types sont combinable pour gérer des cas complexes, comme demadé dans les exercices.

# Exercices
```
type User = {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
  updatedAt: Date;
}
```
Il faut dériver ce type afin de remplir les contraintes de chaque exercice.

## CreateUser
Contraintes:
* pas de dates
* pas d'id
* password obligatoire

## UpdateUser
Contraintes :
* id obligatoire
* le reste optionel

## UserDTO
Contraintes :
* id immutable
* dates obligatoires
* pas de password
* le reste optionel

# Ressources
- [Doc Typescript](https://www.typescriptlang.org/docs/handbook/utility-types.html#nonnullabletype)