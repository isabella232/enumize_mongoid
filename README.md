# EnumizeMongoid

EnumizeMongoid is a gem aimed for Mongoid users, that lets you create your own classes/types representing/encapsuluating an enum. There are other great gems out there dealing with enums for Mongoid (like [simple_enum](https://github.com/lwe/simple_enum)) but this one is approaching the problem from another angle.

Using this gem you will are required to create your own classes for your enum types, but on the other hand you will NOT face inconsistencies like this (using simple_enum):

```ruby
doc = Document.first
p Document.where(status: doc.status).count
# 0 - why? because doc.status is a symbol
```

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'enumize_mongoid'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install enumize_mongoid

## Usage

Include `Enumize::Field` into your class representing the enum, and call `enumize` with the appropriate values:

```ruby
class Status
  include EnumizeMongoid::Field

  enumize([:bouncing, :still], create_constants: true)
end
```

Set it as the field's type you want to use an enum for:

```ruby
class RedBall
  include Mongoid::Document

  field :status, type: Status
end
```

After that you can use it in various flexible ways:

### Document creation
```ruby
RedBall.create(status: :bouncing)
RedBall.create(status: Status::BOUNCING)
RedBall.create(status: 0)
RedBall.create(status: Status.new(:bouncing))
```

### Document querying
```ruby
RedBall.where(status: :bouncing)
RedBall.where(status: Status::BOUNCING)
RedBall.where(status: 0)
RedBall.where(status: Status.new(:bouncing))
```

### Comparing values
```ruby
rb = RedBall.where(status: :bouncing).first

rb.status == :bouncing # true
rb.status == Status::BOUNCING # true
rb.status == 0 # true
rb.status == Status.new(:bouncing)
```


#### enumize(values, create_constants: false)

`values`:
  * array of symbols: `[:bouncing, :still]` - In this case the order matters, as the index of the value will be the value saved in the database: `:bouncing` -> 0, `:still` -> 1
  * hash: `{ bouncing: 2, still: 4 }` - Order does not matter, you decide what the values will be

`create_constants: true` will create:
  * a `VALUES` constant: `Status::VALUES` -> `{ bouncing: 0, still: 1 }`
  * a constant for each value:
    * `Status::BOUNCING` -> `0`
    * `Status::STILL` -> `0`



## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release` to create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

1. Fork it ( https://github.com/InnovativeTravel/enumize_mongoid/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
