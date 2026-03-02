# Ruby, les fondations

## Ressources

- Documentation Ruby : https://rubyapi.org/ (cherche une classe pour voir toutes ses méthodes)
- Ruby en 20 minutes : https://www.ruby-lang.org/fr/documentation/quickstart/

---

## 1. Les bases - Tout est objet

### Exploration en IRB

Lance `irb` dans ton terminal. C'est ton terrain de jeu pour la journée.

Essaie ceci :

```ruby
1.class
"hello".class
nil.class
true.class
:draft.class
[1, 2, 3].class
{ name: "Ruby" }.class
```

Tout est un objet. Même `1`, même `nil`, même `true`. Et chaque objet a des méthodes :

```ruby
42.even?
-5.abs
3.times { puts "hello" }
"hello".upcase
"hello".reverse
"hello world".split(" ")
[3, 1, 2].sort
[1, 1, 2, 3, 3].uniq
```

Pour découvrir les méthodes disponibles sur un objet, le mieux c'est la doc : https://rubyapi.org/. Par exemple, cherche `String` pour voir tout ce qu'on peut faire avec une chaîne de caractères.

> Tu peux aussi taper `"hello".methods.sort` en IRB pour lister toutes les méthodes, mais c'est surtout utile quand tu sais déjà un peu ce que tu cherches.

### Symbols vs Strings

Tu vas croiser des symbols partout dans Rails. Essaie ça :

```ruby
"hello".object_id
"hello".object_id
# => deux valeurs différentes !

:hello.object_id
:hello.object_id
# => la même valeur !
```

Un symbol c'est un identifiant immuable et unique en mémoire. Un string c'est une valeur qui peut changer.

En règle générale : **string pour du contenu, symbol pour des clés et des noms**.

```ruby
# Hash avec des symbols comme clés (convention Ruby/Rails)
task = { title: "Apprendre Ruby", status: :todo, priority: :high }
task[:title]
task[:status]
```

### Conditions et méthodes

```ruby
def urgent?(priority)
  priority == :high
end

urgent?(:high)   # => true
urgent?(:low)    # => false
```

Note la convention : une méthode qui retourne un booléen se termine par `?`.

➡️ **Ouvre `exercices/01_bases.md`**

---

## 2. Programmation orientée objet - Les classes

### Explorer les classes en IRB

Avant de coder, comprends le mécanisme :

```ruby
class Animal
  def initialize(name)
    @name = name
  end

  def speak
    "..."
  end
end

a = Animal.new("Rex")
a.speak
# a.name => NoMethodError ! Pourquoi ?
```

La variable `@name` est une variable d'instance - elle vit à l'intérieur de l'objet. Depuis l'extérieur, on ne peut pas y accéder directement. Il faut écrire une méthode pour ça :

```ruby
class Animal
  def initialize(name)
    @name = name
  end

  # Méthode pour LIRE @name depuis l'extérieur
  def name
    @name
  end

  # Méthode pour MODIFIER @name depuis l'extérieur
  def name=(new_name)
    @name = new_name
  end
end

a = Animal.new("Rex")
a.name            # appelle la méthode name => "Rex"
a.name = "Médor"  # appelle la méthode name=
a.name            # => "Médor"
```

C'est tellement courant que Ruby fournit des raccourcis :

```ruby
class Animal
  attr_reader :name       # génère la méthode name (lecture seule)
  attr_writer :name       # génère la méthode name= (écriture seule)
  attr_accessor :status   # génère les deux (lecture + écriture)

  def initialize(name)
    @name = name
    @status = :alive
  end
end

a = Animal.new("Rex")
a.name          # => "Rex"
a.status        # => :alive
a.status = :sleeping
a.status        # => :sleeping
```

Ce ne sont pas des mots-clés magiques - ce sont des méthodes Ruby qui génèrent d'autres méthodes pour toi. Ce principe de "conventions et raccourcis" on le retrouvera partout dans Rails (meta-programming).

### `self` - le receveur implicite

En Ruby, chaque appel de méthode a un **receveur** - l'objet sur lequel on appelle la méthode :

```ruby
a = Animal.new("Rex")
a.name   # le receveur c'est `a`
```

À l'intérieur d'une méthode, quand tu appelles une autre méthode sans préciser de receveur, c'est `self` (l'objet courant) qui est utilisé :

```ruby
class Animal
  attr_reader :name
  attr_accessor :status

  def initialize(name)
    @name = name
    @status = :alive
  end

  def sleep!
    self.status = :sleeping  # self nécessaire pour appeler le setter status=
    puts "#{name} dort"      # ici name c'est self.name (implicite)
  end
end
```

`name` tout seul fonctionne (Ruby comprend que c'est `self.name`), mais pour les setters il faut écrire `self.status =` sinon Ruby pense qu'on crée une variable locale.

### Méthodes de classe vs méthodes d'instance

```ruby
class Animal
  def self.categories
    [:mammal, :bird, :fish]
  end

  def alive?
    @status != :dead
  end
end

Animal.categories   # méthode de classe
a.alive?            # méthode d'instance
```

➡️ **Ouvre `exercices/02_classes.md`**

---

## 3. Modules et héritage

### Héritage

```ruby
class Vehicle
  attr_reader :name, :wheels

  def initialize(name, wheels)
    @name = name
    @wheels = wheels
  end

  def to_s
    "#{name} (#{wheels} roues)"
  end
end

class Car < Vehicle
  def initialize(name)
    super(name, 4)
  end
end

class Bike < Vehicle
  def initialize(name)
    super(name, 2)
  end
end
```

```ruby
car = Car.new("Peugeot 208")
bike = Bike.new("VTT")

puts car         # => Peugeot 208 (4 roues)
puts bike        # => VTT (2 roues)
puts car.wheels  # => 4  (hérité)
```

`Car` et `Bike` héritent de `to_s`, `name`, `wheels` - et utilisent `super` pour appeler le `initialize` du parent avec un nombre de roues fixe.

Le lien avec Rails : quand tu écris `class Task < ApplicationRecord`, ta classe hérite de tout le comportement d'ActiveRecord.

### Modules - les mixins

Un module c'est un ensemble de méthodes qu'on peut inclure dans plusieurs classes. C'est la réponse de Ruby à la question "comment partager du comportement sans héritage multiple ?"

```ruby
module Printable
  def print_summary
    puts to_s
  end
end

class Car < Vehicle
  include Printable
end

class Bike < Vehicle
  include Printable
end
```

Le lien avec Rails : les `concerns` que tu vois dans `app/models/concerns/` sont des modules.

> **Note** : dans un vrai projet, on sépare les classes dans des fichiers différents et on les charge avec `require_relative`. Par exemple : `require_relative 'task'`. On garde la recopie pour aujourd'hui, mais on verra ça en pratique demain avec Rails.

➡️ **Ouvre `exercices/03_heritage_modules.md`**

---

## 4. Blocks, itérateurs, Procs et Lambdas

### Blocks

Un block c'est un morceau de code qu'on passe à une méthode. Tu en utilises déjà :

```ruby
3.times { puts "hello" }
[1, 2, 3].each { |n| puts n }
```

Le `{ ... }` (ou `do...end`) c'est le block. La méthode l'exécute quand elle veut.

### Écrire une méthode qui accepte un block

```ruby
def with_logging
  puts "--- Début ---"
  yield
  puts "--- Fin ---"
end

with_logging { puts "Je fais un truc" }
```

`yield` exécute le block passé à la méthode. On peut aussi lui passer des arguments :

```ruby
def for_each_status
  [:todo, :in_progress, :done].each { |s| yield(s) }
end

for_each_status { |status| puts "Statut : #{status}" }
```

### Les itérateurs essentiels

```ruby
animals = [
  { name: "Rex", species: :dog, age: 5 },
  { name: "Felix", species: :cat, age: 12 },
  { name: "Bulle", species: :fish, age: 2 },
  { name: "Médor", species: :dog, age: 8 }
]

# each - parcourir (ne retourne rien d'utile)
animals.each { |a| puts a[:name] }

# map - transformer (retourne un nouveau tableau)
names = animals.map { |a| a[:name] }

# select - filtrer (garde ceux qui matchent)
dogs = animals.select { |a| a[:species] == :dog }

# reject - filtrer inversé (retire ceux qui matchent)
not_fish = animals.reject { |a| a[:species] == :fish }

# find - trouver le premier qui matche
first_cat = animals.find { |a| a[:species] == :cat }

# sort_by - trier
by_age = animals.sort_by { |a| a[:age] }

# count - compter avec condition
dog_count = animals.count { |a| a[:species] == :dog }
```

**La différence clé : `each` vs `map`**
- `each` exécute le block pour chaque élément et retourne le tableau original
- `map` exécute le block et retourne un nouveau tableau avec les résultats

```ruby
# each retourne le tableau original
result = [1, 2, 3].each { |n| n * 2 }
# result => [1, 2, 3]  (le résultat du block est ignoré)

# map retourne les résultats du block
result = [1, 2, 3].map { |n| n * 2 }
# result => [2, 4, 6]
```

### Chaîner les itérateurs

```ruby
# Les noms des chiens de plus de 3 ans, triés par nom
animals
  .select { |a| a[:species] == :dog }
  .select { |a| a[:age] > 3 }
  .sort_by { |a| a[:name] }
  .map { |a| a[:name] }
```

### Procs et Lambdas

Un **Proc** c'est un block stocké dans une variable. Un **Lambda** c'est un Proc plus strict.

```ruby
double = ->(n) { n * 2 }
double.call(5)  # => 10
```

Les différences clés :

| | Lambda | Proc |
|---|---|---|
| **Arity** | Strict : doit avoir le bon nombre d'arguments | Permissif : arguments ignorés ou `nil` |
| **Return** | `return` sort juste la lambda | `return` sort la méthode parente |
| **Syntaxe** | `->(args) { }` ou `lambda { \|args\| }` | `Proc.new { \|args\| }` |

Tu n'en as pas besoin au quotidien dans Rails, mais tu les croiseras dans les scopes ActiveRecord :

```ruby
scope :urgent, -> { where(priority: :high) }
```

### Symbol to_proc et la magie du `&:`

Tu as sûrement vu ça dans du vrai code :

```ruby
["hello", "world"].map(&:upcase)
# => ["HELLO", "WORLD"]

[1, 2, 3].map(&:to_s)
# => ["1", "2", "3"]
```

Quand tu écris `&:upcase`, Ruby appelle `.to_proc` sur le symbol, qui retourne une Proc, puis la convertit en block.

➡️ **Ouvre `exercices/04_iterateurs.md`**

---

## 5. Error Handling et exceptions

En Ruby, les erreurs s'appellent des **exceptions**. Tu les lèves avec `raise` et tu les attrapes avec `begin/rescue/ensure`.

### Lever une exception

```ruby
def set_priority(priority)
  unless [:low, :medium, :high].include?(priority)
    raise "Priorité invalide : #{priority}"
  end
  @priority = priority
end
```

### Attraper les exceptions

```ruby
begin
  set_priority(:urgent)
rescue => e
  puts "Erreur attrapée : #{e.message}"
  # continue l'exécution
end
```

### Types d'exceptions

Ruby a plusieurs types : `StandardError`, `ArgumentError`, `TypeError`, `ZeroDivisionError`, `NoMethodError`, etc.

Tu peux attraper un type spécifique :

```ruby
begin
  1 / 0
rescue ZeroDivisionError => e
  puts "Impossible de diviser par zéro"
rescue ArgumentError => e
  puts "Argument invalide"
rescue => e
  puts "Autre erreur : #{e.message}"
end
```

### Créer tes propres exceptions

```ruby
class InvalidPriorityError < StandardError
end

begin
  raise InvalidPriorityError, "Priorité invalide"
rescue InvalidPriorityError => e
  puts "Erreur de priorité : #{e.message}"
end
```

### `ensure` - code qui s'exécute toujours

```ruby
begin
  file = File.open("data.txt", "w")
  file.write("Données")
rescue IOError => e
  puts "Erreur d'écriture"
ensure
  file.close if file  # toujours exécuté
end
```

`ensure` s'exécute **toujours**, même en cas d'erreur. Idéal pour le nettoyage.

➡️ **Ouvre `exercices/05_error_handling.md`**

---

## 6. Synthèse (bonus)

Si on a le temps, on combine tout ce qu'on a vu dans un mini-programme.

➡️ **Ouvre `exercices/06_synthese.md`**
