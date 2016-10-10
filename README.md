# JSON::Schematized

Object builder based on JSON-Schema.

Code badges:  
[![Build Status](https://travis-ci.org/kawamanza/json-schematized.svg)](https://travis-ci.org/kawamanza/json-schematized)
[![Test Coverage](https://codeclimate.com/github/kawamanza/json-schematized/badges/coverage.svg)](https://codeclimate.com/github/kawamanza/json-schematized/coverage)
[![Dependency Status](https://gemnasium.com/kawamanza/json-schematized.svg)](https://gemnasium.com/kawamanza/json-schematized)

Project badges:  
[![Gem Version](https://badge.fury.io/rb/json-schematized.svg)](https://badge.fury.io/rb/json-schematized)
[![Code Climate](https://codeclimate.com/github/kawamanza/json-schematized/badges/gpa.svg)](https://codeclimate.com/github/kawamanza/json-schematized)
[![security](https://hakiri.io/github/kawamanza/json-schematized/master.svg)](https://hakiri.io/github/kawamanza/json-schematized/master)

## Sample usage

Consider the following JSON-Schema (escaped as YAML, for better viewing):

```yaml
# person.yml
person:
  type: object
  properties:
    name:
      type: string
    birth:
      type: object
      properties:
        name:
          type: string
    children:
      type: array
      required: true
      items:
        type: object
        properties:
          name:
            type: string
```

### Basic Wrapper Usage

```ruby
require "json-schematized"

class Person < JSON::Schematized::Base
  json_schema do  # block called for each new instance
    YAML.load(File.read(File.expand_path("../person.yml", __FILE__)))["person"]
  end
end

person = Person.new name: "John", children: [{name: "William"}]
person.name                     # => "John"
person.children                 # => [{"name" => "William"}]
person.children.class           # => Person::ChildrenCollection
person.children.first.class     # => Person::Child
person.children.first.name      # => "William"
```

Another way to use Basic Wrapper is as follows:

```ruby
class Person < Hash
  include JSON::Schematized
  json_schema wrapper: :basic do  # block called for each new instance
    YAML.load(File.read(File.expand_path("../person.yml", __FILE__)))["person"]
  end
end
```

### Virtus Wrapper Usage

```ruby
require "json-schematized"

class Person
  include JSON::Schematized
  json_schema wrapper: :virtus do  # block called only once
    YAML.load(File.read(File.expand_path("../person.yml", __FILE__)))["person"]
  end
end

person = Person.new name: "John", children: [{name: "William"}]
person.name                     # => "John"
person.children                 # => [#<Person::Child:0x007fc990906fd0 @name="William">]
person.children.class           # => Person::ChildrenCollection
person.children.first.class     # => Person::Child
person.children.first.name      # => "William"
```

### Object with Basic Wrapper usage

```ruby
person = Hash.new
json_schema = YAML.load(File.read(File.expand_path("../person.yml", __FILE__)))["person"]
person.extend JSON::Schematized::BasicWrapper.modularize(json_schema)

person.children << {}
# ...
```
