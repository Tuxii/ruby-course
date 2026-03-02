# Exercice 2 - Les classes

## 2.1 La classe Task (à la main)

Crée une classe `Task` avec :

- un `initialize` qui accepte un `title`
- les variables d'instance : `@title`, `@status`, `@priority`, `@due_in_days`
- à l'initialisation : `@status = :todo`, `@priority = :medium`, `@due_in_days = nil`

Pour l'instant, écris les méthodes de lecture et d'écriture **à la main**, sans utiliser `attr_reader` / `attr_accessor` :

- `title` : lecture seule
- `status` : lecture + écriture
- `priority` : lecture + écriture
- `due_in_days` : lecture + écriture

Teste :

```ruby
task = Task.new("Apprendre Ruby")
puts task.title
puts task.status
task.priority = :high
puts task.priority
task.title = "Autre"  # => doit provoquer une NoMethodError
```

## 2.2 Refacto avec attr_reader / attr_accessor

Tu viens d'écrire beaucoup de méthodes qui se ressemblent. Remplace-les par les raccourcis Ruby :

- `attr_reader :title` (lecture seule)
- `attr_accessor :status, :priority, :due_in_days` (lecture + écriture)

Vérifie que tes tests du 2.1 passent toujours.

## 2.3 Méthodes prédicats

Ajoute les méthodes suivantes à `Task` :

- `done?` → `true` si le statut est `:done`
- `urgent?` → `true` si la priorité est `:high` et le statut n'est pas `:done`
- `overdue?` → `true` si `due_in_days` n'est pas `nil` et est négatif

Teste :

```ruby
task = Task.new("Faire les courses")
task.priority = :high
task.due_in_days = -2
puts task.done?      # => false
puts task.urgent?    # => true
puts task.overdue?   # => true
```

## 2.4 Méthodes d'action

Ajoute les méthodes suivantes :

- `complete` → passe le statut à `:done`
- `start` → passe le statut à `:in_progress`
- `to_s` → retourne une string lisible, par exemple : `"[HIGH] Apprendre Ruby (todo)"`

Teste :

```ruby
task = Task.new("Apprendre Ruby")
task.priority = :high
puts task
task.start
puts task
task.complete
puts task
puts task.urgent?   # => false (car done)
```

## 2.5 Méthodes de classe

Ajoute deux méthodes de classe :

- `Task.priorities` → retourne `[:low, :medium, :high]`
- `Task.statuses` → retourne `[:todo, :in_progress, :done]`

## 2.6 La classe Project

Crée une classe `Project` avec :

- un `initialize` qui accepte un `name`
- `@name` en lecture seule, `@archived` (default: `false`) en lecture + écriture
- `archived?` → `true` si archivé
- `archive` → passe `@archived` à `true`
- `to_s` → retourne `"Project: Refonte du site"`

Teste :

```ruby
project = Project.new("Refonte du site")
puts project
puts project.archived?   # => false
project.archive
puts project.archived?   # => true
```

## 2.7 Ajouter des tâches à un projet

Modifie `Project` pour avoir :

- un array `@tasks` vide à l'initialisation
- une méthode `add_task(task)` qui ajoute une Task à `@tasks`
- une méthode `tasks` qui retourne le tableau
- une méthode `tasks_count` qui retourne le nombre de tâches

Teste :

```ruby
project = Project.new("Refonte du site")
project.add_task(Task.new("Maquettes"))
project.add_task(Task.new("Intégration"))
project.add_task(Task.new("Tests"))
puts project.tasks_count   # => 3
project.tasks.each { |t| puts t }
```
