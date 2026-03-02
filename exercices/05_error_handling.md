# Exercice 5 - Error Handling et exceptions

> Documentation : https://rubyapi.org/ - cherche `Exception`, `StandardError`, `raise`, `begin/rescue/ensure`

Reprends la classe `Task` de l'exercice 2. Ajoute de la validation :

## 5.1 Création d'une tâche avec validation

Modifie `initialize` pour que la création échoue avec un message clair si le title est vide ou nil.

```ruby
task = Task.new("")        # => raise "Title cannot be empty"
task = Task.new(nil)       # => raise "Title cannot be empty"
task = Task.new("OK")      # OK
```

## 5.2 Validation sur `due_in_days`

La méthode `due_in_days=` doit :
- Vérifier que la valeur est un Integer (ou nil)
- Lever une `TypeError` avec un message clair si ce n'est pas un nombre

```ruby
task = Task.new("Ranger le garage")
task.due_in_days = 5       # OK
task.due_in_days = "5"     # => TypeError: due_in_days must be an Integer or nil
task.due_in_days = nil     # OK
```

## 5.3 Validation sur `priority`

Ajoute une méthode setter pour `priority` qui valide la valeur. Lève une `InvalidPriorityError` (classe que tu créeras) si la priorité n'est pas dans `[:low, :medium, :high]`.

```ruby
task.priority = :high      # OK
task.priority = :urgent    # => InvalidPriorityError: priority must be :low, :medium, or :high
```
