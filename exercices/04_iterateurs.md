# Exercice 4 - Blocks, itérateurs, Procs et Lambdas

Cet exercice reprend les classes et modules des exercices 2 et 3. Recopie-les dans `04_iterateurs.rb` avant de commencer.

## Données de travail

Crée ces tâches et projets pour travailler dessus (tu peux copier-coller) :

```ruby
project = Project.new("Mon app")

tasks = [
  Task.new("Modifier l'API"),
  Task.new("Corriger le bug panier"),
  Task.new("Composant sidebar"),
  Task.new("Écrire les tests"),
  Task.new("Mettre à jour les gems"),
  Task.new("Page d'accueil"),
  Task.new("Refacto auth"),
  RecurringTask.new("Code review", :daily),
]

tasks[0].priority = :high
tasks[0].due_in_days = 2
tasks[1].priority = :high
tasks[1].due_in_days = -3
tasks[1].start
tasks[2].priority = :medium
tasks[2].due_in_days = 5
tasks[3].priority = :medium
tasks[3].due_in_days = -1
tasks[4].priority = :low
tasks[4].complete
tasks[5].priority = :high
tasks[5].due_in_days = 0
tasks[6].priority = :low
tasks[6].due_in_days = 10

tasks[0].add_tag("feature")
tasks[1].add_tag("bug")
tasks[2].add_tag("feature")
tasks[2].add_tag("design")
tasks[3].add_tag("feature")
tasks[4].add_tag("refacto")
tasks[6].add_tag("refacto")
```

## 4.1 each et map

- Affiche chaque tâche avec `puts`
- Crée un array `titles` contenant uniquement les titres
- Crée un array `statuses` contenant le statut de chaque tâche en majuscules (ex: `"TODO"`, `"IN_PROGRESS"`)

## 4.2 Filtrer

- Crée un array contenant uniquement les tâches urgentes
- Crée un array contenant uniquement les tâches non terminées
- Crée un array contenant les tâches en retard

Affiche le nombre de résultats pour chaque array.

## 4.3 Chercher, compter, trier

- Trouve la première tâche qui a le tag `"bug"`
- Compte le nombre de tâches avec la priorité `:high`
- Trie les tâches par `due_in_days` croissant. Attention : certaines tâches ont `due_in_days` à `nil` - mets-les à la fin.

## 4.4 Chaîner les itérateurs

En une seule chaîne d'appels :

- Les titres des tâches urgentes non terminées, triées par `due_in_days`
- Tous les tags uniques de toutes les tâches (indice : `flat_map`)

## 4.5 Écrire une méthode avec yield

Ajoute une méthode `filter` à la classe `Project` qui :

- parcourt les tâches du projet
- passe chaque tâche au block
- retourne un array des tâches pour lesquelles le block a retourné `true`

C'est un `select` fait maison.

Teste :

```ruby
project.add_task(tasks[0])
project.add_task(tasks[1])
project.add_task(tasks[2])
project.add_task(tasks[3])

overdue = project.filter { |task| task.overdue? }
overdue.each { |t| puts t }
```

---

## Pour aller plus loin (démo)

Les sections suivantes sont des démonstrations à lire et tester en IRB. Pas d'exercice à coder, on les explore ensemble.

## 4.6 Procs et Lambdas - le concept

Un **Proc** c'est un block stocké dans une variable. Un **Lambda** c'est un Proc plus strict.

Essaie ceci :

```ruby
# Lambda avec la syntaxe stabby (flèche)
double = ->(n) { n * 2 }
puts double.call(5)      # => 10
puts double.call(3, 4)   # => ArgumentError ! arity mismatch

# Proc plus permissif
multiply = Proc.new { |a, b| (a || 1) * (b || 1) }
puts multiply.call(5)    # => 5 (ignore b)
puts multiply.call(3, 4) # => 12
```

Les différences clés :

| | Lambda | Proc |
|---|---|---|
| **Arity** | Strict : doit avoir exactement le bon nombre d'arguments | Permissif : arguments en trop ignorés, manquants → `nil` |
| **Return** | `return` sort juste la lambda | `return` sort la méthode qui l'entoure |
| **Syntaxe** | `->(args) { code }` ou `lambda { \|args\| code }` | `Proc.new { \|args\| code }` |

## 4.7 Symbol to_proc et la magie du `&:`

Tu as sûrement vu ça dans du vrai code :

```ruby
["hello", "world"].map(&:upcase)
# => ["HELLO", "WORLD"]

[1, 2, 3].map(&:to_s)
# => ["1", "2", "3"]
```

Ça marche comment ? Quand tu écris `&:upcase`, Ruby:

1. Prend le symbol `:upcase`
2. Appelle `.to_proc` dessus, qui retourne une Proc : `Proc.new { |x| x.upcase }`
3. Convertit cette Proc en block avec `&`

C'est équivalent à :

```ruby
["hello", "world"].map { |s| s.upcase }
```

Tu peux même écrire ton propre `to_proc` :

```ruby
class Symbol
  def to_proc
    Proc.new { |obj| obj.send(self) }
  end
end

# Maintenant ça marche :
[1, 2, 3].map(&:to_s)
```

`send` permet d'appeler une méthode à partir de son nom (un symbol). C'est du **meta-programming** — du code qui manipule du code. C'est ce qui permet à `&:upcase` de fonctionner sur n'importe quel objet. On retrouvera ce principe partout dans Rails.

## 4.8 Entraînement avec Procs et `&:`

Avec `tasks`:

- Récupère les titres en majuscules : `tasks.map { |t| t.title.upcase }`. Pourquoi on ne peut pas écrire `tasks.map(&:upcase)` ici ?
- Crée un Proc qui filtre les tâches urgentes : `is_urgent = Proc.new { |t| t.urgent? }`
- Utilise-le : `urgent_with_proc = tasks.select(&is_urgent)`
- Compare avec `tasks.select(&:urgent?)`. Pourquoi ça marche ?
