# Exercice 6 - Synthèse (bonus)

Cet exercice combine tout ce qu'on a vu dans la journée. Reprends tes classes et modules des exercices précédents.

## 6.1 La classe TaskManager

Crée une classe `TaskManager` qui sert de point d'entrée :

- `@projects` initialisé à un array vide
- `add_project(project)` → ajoute un projet
- `all_tasks` → retourne toutes les tâches de tous les projets dans un seul array
- `stats` → retourne un hash :

```ruby
{
  total: 10,
  done: 2,
  urgent: 3,
  overdue: 1
}
```

## 6.2 Recherche et filtrage

Ajoute les méthodes suivantes à `TaskManager` :

- `search(keyword)` → retourne les tâches dont le titre contient le keyword (insensible à la casse)
- `by_tag(tag)` → retourne les tâches qui ont ce tag
- `by_priority(level)` → retourne les tâches avec cette priorité

## 6.3 Le programme principal

Crée un scénario complet :

1. Crée un `TaskManager`
2. Crée 2-3 projets
3. Ajoute 5-10 tâches aux projets (dont des `RecurringTask`) avec des priorités, statuts et tags variés
4. Fais avancer certaines tâches (`start`, `complete`)
5. Affiche les stats
6. Recherche des tâches par mot-clé
7. Filtre par tag
8. Filtre par priorité

## 6.4 Bonus : Validation et error handling

Si tu veux (et tu as le temps), ajoute de la validation à `TaskManager` :

- `add_project(project)` doit lever une `ArgumentError` si le projet est `nil` ou si le nom est vide
- `search(keyword)` doit lever une `TypeError` si le keyword n'est pas une string
- Gère les cas limites (recherche avec un mot-clé vide, filtre par un tag inexistant, etc.)

Exemple :

```ruby
begin
  manager.add_project(nil)
rescue ArgumentError => e
  puts "Impossible d'ajouter le projet : #{e.message}"
end
```

## 6.5 Bonus : Chaîner les filtres

Crée une méthode `filter_by` qui accepte un block :

```ruby
high_urgent = manager.filter_by { |task| task.priority == :high && task.urgent? }
```

Ou mieux, utilise les Procs qu'on a vus en 4.8 :

```ruby
is_high = Proc.new { |t| t.priority == :high }
manager.filter_by(&is_high)
```

Ça marche grâce à `&` et `to_proc` !
