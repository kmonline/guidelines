Ruby
====

## Indentation

* 2 spaces
* No tabs

### Whitespaces around

#### Hashes

```ruby
# wrong
visit = {distance: 10.0}

# correct
visit = { distance: 10.0 }
```

#### Blocks

```ruby
# wrong
coordinates.each {|coordinate| puts coordinate}

# correct
coordinates.each { |coordinate| puts coordinate }
```

#### Default values

```ruby
# wrong
def cluster(coordinates,distance=100.0)
  # ...
end

# correct
def cluster(coordinates, distance = 100.0)
  # ...
end
```

#### Arrays

```ruby
# wrong
accuracy_limits = [13.50,23.40,400.10]

# correct
accuracy_limits = [13.50, 23.40, 400.10]
```

#### Function calls

```ruby
# wrong
filter( values )
filter (values)

# correct
filter(values)
```

### Blank line

We use blank lines to separate contexts and in conditions described bellow:

#### Method definition

```ruby
# wrong
def show_message
  puts 'Hello, world'
end
def sum(a, b)
  a + b
end

# correct
def show_message
  puts 'Hello, world'
end

def sum(a, b)
  a + b
end
```

#### Class definition

```ruby
# wrong
class Expense

  def initialize(data)
    # ...
  end

end

# correct
class Expense
  def initialize(data)
    # ...
  end
end
```

#### Methods visibility

```ruby
# wrong
class Expense
  def initialize(data)
    # ...
  end
  private

  def categorize!
    # ...
  end
end

# correct
class Expense
  def initialize(data)
    # ...
  end

  private

  def categorize!
    # ...
  end
end
```

#### Private class method

* Use pragma mark comment when instance private methods are absent

```ruby
# wrong
class Expense
  def self.last_modified
    # ...
    check_version(version)
  end

  def self.check_version(version)
    # ...
  end
  private_class_method :check_version
end
```

```ruby
# correct
class Expense
  def self.last_modified
    # ...
    check_version(version)
  end

  # private

  def self.check_version(version)
    # ...
  end
  private_class_method :check_version
end
```

When there are instance private methods you can use `private` modifier as usually (do not forgot about `private_class_method` definition).

#### Switch case

* Indent `when` and `else` at same level of `case`

```ruby
# correct
case speed
when 1..20 then 'Low gear'
when 21..40 then 'Low speed'
when 41..60 then 'Medium speed'
else 'High speed'
end

# alternative correct
case
when speed < 80
  register_speed(speed)
when speed > 80
  send_speed_danger_alert(speed)
end
```

#### Passing hashes as argument

* When there are multiple arguments

```ruby
# wrong
register_coordinate({accuracy: 12.10, speed: 89.29})

# correct
register_coordinate(
  accuracy: 12.10,
  speed: 89.29
)
```

#### Method chaining

* When there are multiple methods being chained

```ruby
# wrong
Visit.coordinates
  .where(:accuracy.lte => 50.0)
  .order(created_at: :asc)
  .limit(10)

# correct
Visit.coordinates.where(:accuracy.lte => 50.0).
  order(created_at: :asc).
  limit(10)
```

## Idioms

### Parenthesis

Only use parenthesis when there are params in method definition

```ruby
# wrong
def save()
  # ...
end

# correct
def save
  # ...
end

# also correct
def save(validate = true)
end
```

### Thousands

Use `underscore` to improve Fixnum thousands legibility:

```ruby
# wrong
9123
10250
333769
200780650

# correct
9_123
10_250
333_769
200_780_650
```

## Naming

### Classes

* Use `CamelCase`
* Use `UPCASE_SNAKE_CASE` for constants

### Methods

* Use `snake_case`
* Boolean return should end with `?`: Ex.: `valid?`

* Avoid types in names

```ruby
# wrong
visits_array = Visit.all

# correct
visits = Visit.all
```

## Syntax

* Prefer to use `&&` and `||` for boolean expressions. Do not use `and` and `or` to avoid precedence issues

```ruby
# wrong
success! if valid? and persisted?

# correct
success! if valid? && persisted?
```

```ruby
# wrong
success! if sent? or registered?

# correct
success! if sent? || registered?
```

To understand why, check this [Ruby Tapas video](http://www.virtuouscode.com/2014/08/26/how-to-use-rubys-english-andor-operators-without-going-nuts/).

* Don't use `else` together with `unless`

```ruby
# wrong
unless employee
  assign_employee!
else
  grant_bonus!
end

# correct
if employee
  grant_bonus!
else
  assign_employee!
end
```

* Don't use `NOT` with `unless`

```ruby
# wrong
register_event unless !turned_off?

# correct
register_event if turned_off?
```

* Don't use `then` for multi-line `if/unless`

```ruby
# wrong
if turned_on? then
  register_event
end

# correct
register_event if turned_on?

# also correct
if turned_on?
  register_event
end
```

* Avoid `return` when not required

```ruby
# wrong
def valid?
  if visit.valid? && expense.valid?
    return true
  else
    return false
  end
end

# correct
def valid?
  visit.valid? && expense.valid?
end
```

* Avoid use of nested conditionals for flow control

Prefer a guard clause when you can assert invalid data. A guard clause is a conditional statement at the top of a function that bails out as soon as it can.

```ruby
# bad
def calculate_distance(coordinate)
  if coordinate[:accuracy] <= 50.0
    update_traveled_distance!(coordinate[:latitude], coordinate[:longitude])

    if coordinate[:provider] == 'fused'
      register_visit_with_data(coordinate[:provider])
    else
      recalculate_distance_using_all_providers(coordinate)
    end
  end
end

# good
def calculate_distance(coordinate)
  return unless coordinate[:accuracy] > 50.0

  update_traveled_distance!(coordinate[:latitude], coordinate[:longitude])

  unless coordinate[:provider] == 'fused'
    return recalculate_distance_using_all_providers(coordinate)
  end

  register_visit_with_data(coordinate[:provider])
end
```

* Avoid `self` when not required

```ruby
# wrong
def full_name
  self.first_name + ' ' + self.last_name
end

# correct
def full_name
  [first_name, last_name].join(' ')
end
```

* Use `_` for unused block parameters

```ruby
# wrong
speeds.map { |key, speed| register_speed(speed) }

# correct
speeds.map { |_, speed| register_speed(speed) }
```

* Use `attr_*` to define trivial methods

```ruby
# wrong
class Employee
  def initialize(name, license_number)
    @name = name
    @license_number = license_number
  end

  def name
    @name
  end

  def license_number
    @license_number
  end
end

# correct
class Employee
  attr_reader :name, :license_number

  def initialize(name, license_number)
    @name = name
    @license_number = license_number
  end
end
```

* If you're defining an empty class, do it in a single-line

```ruby
# wrong
class Manager
end

class InvalidWorkflow < StandardError
end

# correct
class Manager; end

class InvalidWorkflow < StandardError; end
```

* Only use `{ ... }` for single-line blocks, otherwise use `do/end`

```ruby
# wrong
visits.each do |visit|
  visit.update_last_coordinate
end

# correct
visits.each { |visit| visit.update_last_coordinate }

# better yet!
visits.each(&:update_last_coordinate)
```

```ruby
# wrong
visits.each { |visit|
  coordinate = visit.update_last_coordinate
  visit.register_speed(coordinate.speed)
}

# correct
visits.each do |visit|
  coordinate = visit.update_last_coordinate

  visit.register_speed(coordinate.speed)
end
```

## Data Structure Syntax

* Always use new hash syntax when using Ruby >= 1.9x

```ruby
# wrong
{ :driver_name => 'Rubinho Barrichello' }

# correct
{ driver_name: 'Ayrton Senna' }
```

* Only use double-quoted string when there is interpolation

```ruby
# wrong
vehicle_model = "Ferrari 488 GTB"

# correct
vehicle_model = 'Ferrari 488 GTB'

# also correct
vehicle_model = "#{manufacturer_name} 488 GTB"
```

* Use `%w` to define array of strings

```ruby
# wrong
beaches = ['Campeche', 'Daniela', 'Solidão']

# correct
beaches = %w(Campeche Daniela Solidão)
```

## Standard library

* When using methods with `bang!` on strings, arrays and other enumerables, do not chain them

This is because many `bang!` methods return `nil` if no change happens ([for example, Array#reject!](http://ruby-doc.org/core-1.9.3/Array.html#method-i-reject-21)) and those mistakes are very hard to detect since it only happens in special circumstances.

```ruby
# wrong
vehicles = foreigner_vehicles.select { |v| v.is_a?(Ferrari) }.sort_by!(&:year)

# correct
vehicles = foreigner_vehicles.select { |v| v.is_a?(Ferrari) }
vehicles.sort_by!(&:year)
vehicles
```

* Use `tap` when memoizing instead of using `begin..end` block

```ruby
# wrong
def addresses
  @addresses ||= begin
    address = Services::Geolocate.locate(@data)

    raise 'Invalid coordinates for geofence' unless outside_geofence?(address)

    address
  end
end

# correct
def addresses
  @addresses ||= Services::Geolocate.locate(@data).tap do |address|
    raise 'Invalid coordinates for geofence' unless outside_geofence?(address)
  end
end
```

## Linting

For linting your Ruby code you can use our existing configs in all projects `.rubocop.yml`. To do that run:

```
$ gem install rubocop
$ rubocop
```
