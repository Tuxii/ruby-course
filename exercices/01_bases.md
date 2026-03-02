# Exercice 1 - Les bases de Ruby

> Documentation : https://rubyapi.org/ - cherche une classe (String, Array, Hash…) pour voir ses méthodes.

## 1.1 Variables et types

Crée les variables suivantes :

- `title` - `"Apprendre Ruby"` (String)
- `priority` - `:high` (Symbol)
- `done` - `false` (Boolean)
- `due_in_days` - `3` (Integer)
- `tags` - `["pro", "urgent"]` (Array)

Affiche chaque variable et sa classe avec `puts`.

```ruby
puts "#{title} est un #{title.class}"
```

## 1.2 Manipulations de strings

À partir de `title = "Apprendre Ruby"` :

- Affiche le titre en majuscules
- Affiche le nombre de caractères
- Vérifie si le titre contient le mot `"Ruby"` (cherche une méthode qui finit par `?`)

## 1.3 Hash

Crée un hash `task` avec les clés symbols `:title`, `:priority`, `:done`, `:due_in_days` et `:tags`, en utilisant des valeurs cohérentes.

- Affiche le titre de la tâche
- Affiche le nombre de tags
- Ajoute une clé `:description` avec une valeur de ton choix
- Affiche le hash complet avec `puts`

## 1.4 Une première méthode

Écris une méthode `format_task(task)` qui prend un hash en paramètre et retourne une string formatée :

```
[HIGH] Apprendre Ruby (3 jours restants)
```

- La priorité doit être en majuscules
- Si `done` est `true`, affiche `(terminée)` au lieu des jours restants

Teste avec une tâche terminée et une non terminée.

## 1.5 Méthodes prédicats

En Ruby, une méthode qui retourne un booléen se termine par `?` - c'est une convention.

Écris une méthode `done?(task)` qui retourne `true` si la tâche est terminée.

Écris une méthode `urgent?(task)` qui retourne `true` si :
- la priorité est `:high`, **et**
- la tâche n'est pas terminée

Teste avec plusieurs hashes différents.

## 1.6 Plusieurs tâches

Crée un array `tasks` contenant 4 hashes de tâches :

| Titre | Priorité | Terminée ? | Jours restants | Tags |
|---|---|---|---|---|
| Apprendre Ruby | `:high` | non | 2 | `["pro", "urgent"]` |
| Ranger le garage | `:low` | oui | 0 | `["perso"]` |
| Faire les courses | `:high` | non | 1 | `["perso"]` |
| Acheter un nouveau clavier | `:medium` | non | 7 | `["perso", "matos"]` |

Ensuite :

- Affiche chaque tâche avec `format_task`
- Affiche uniquement les tâches urgentes (utilise `urgent?`)
- Affiche le nombre total de tâches
- Affiche le titre de la première et de la dernière tâche
- Crée un nouveau hash et ajoute-le à `tasks` avec `<<`
