# Exercice 3 - Héritage et modules

Cet exercice reprend les classes Task et Project de l'exercice 2. Recopie-les dans `03_heritage_modules.rb` avant de commencer.

## 3.1 Héritage - RecurringTask

Crée une classe `RecurringTask` qui hérite de `Task` :

- un attribut `@frequency` (`:daily`, `:weekly`, `:monthly`) donné à l'initialisation
- `frequency` en lecture seule
- `complete` remet le statut à `:todo` au lieu de passer à `:done` (la tâche revient toujours)
- `to_s` ajoute la fréquence : `"[MEDIUM] Daily standup (todo) - récurrence: daily"`

Teste :

```ruby
standup = RecurringTask.new("Daily standup", :daily)
puts standup
standup.start
puts standup.status     # => in_progress
standup.complete
puts standup.status     # => todo (et non :done !)
puts standup.urgent?    # fonctionne car hérité de Task
```

## 3.2 Module Archivable

Regarde tes classes Task et Project. Seul Project a `archived?` et `archive`. On veut que Task puisse aussi être archivée - sans copier-coller le code.

Crée un module `Archivable` qui contient :

- `@archived` initialisé à `false` (via une méthode `init_archivable`)
- `archived?` → `true` si archivé
- `archive` → passe `@archived` à `true`
- `unarchive` → passe `@archived` à `false`

Inclus ce module dans `Task` et dans `Project`. Appelle `init_archivable` dans leurs `initialize`.

Teste :

```ruby
task = Task.new("Vieille tâche")
task.archive
puts task.archived?    # => true
task.unarchive
puts task.archived?    # => false

project = Project.new("Vieux projet")
project.archive
puts project.archived? # => true
```

## 3.3 Module Taggable

On veut pouvoir ajouter des tags sur les tâches et les projets.

Crée un module `Taggable` qui contient :

- `@tags` initialisé à un array vide (via `init_taggable`)
- `tags` qui retourne `@tags`
- `add_tag(tag)` qui ajoute un tag seulement s'il n'est pas déjà présent
- `remove_tag(tag)` qui retire un tag
- `tagged?(tag)` qui retourne `true` si le tag est présent

Inclus ce module dans `Task` et `Project`.

Teste :

```ruby
task = Task.new("Rapport")
task.add_tag("urgent")
task.add_tag("dev")
task.add_tag("urgent")           # doublon, ne doit pas être ajouté
puts task.tags.inspect           # => ["urgent", "dev"]
puts task.tagged?("urgent")      # => true
task.remove_tag("urgent")
puts task.tags.inspect           # => ["dev"]
```

## 3.4 Vérifier la chaîne d'héritage

```ruby
standup = RecurringTask.new("Daily standup", :daily)
standup.add_tag("routine")
standup.archive
puts standup.tagged?("routine")  # => true
puts standup.archived?           # => true

puts RecurringTask.ancestors.inspect
# Que vois-tu dans la liste ? Où apparaissent les modules ?
```
