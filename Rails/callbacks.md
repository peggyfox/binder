# Callbacks

In Rails, callbacks are methods that are run before, after, or around specific events in an object's "life cycle"  - when created, saved, validated, loaded, updated, or deleted (CRUD actions).


## Types

```before_```: Runs before an event

```after_```: Runs after an event

```around_```: Less commonly used - involves a method that can include before and/or after callbacks that ```yield ``` to the original event.


## Common use cases
```before_create```: Runs before initializing and saving a new record.

```after_create```: Runs after initializing and saving a new record.

```before_save```: Runs before saving any record (first time or on update)

```after_save```: Runs after saving any record (first time or on update)


## Implementation
In Rails, callbacks are defined in the model.  The time at which the callback should occur is followed by the callback method name and is specified toward the top of the model.  The callback method can be defined inline when the callback is specified or within a method defined below that or further down in the model.

```ruby
class Hours < ActiveRecord::Base
  after_save :reset_salon_caches

  def reset_salon_caches
    salon.touch_app_cache!
    SalonSweeper.new(salon).expire_all
  end
end
```

## Skipping Callbacks in Migrations and Tests
In model tests, it is best to test the callback in an individual test and not in the other model tests.  While it is possible to stub and unstub the callback method, [Andrey Koleshko suggests](http://railsguides.net/skip-callbacks-in-tests/) skipping callbacks instead of stubbing them.

Another case in which you might want to skip callbacks are in migrations.  When batch updating a records, it may not be necessary to perform the callback after each database entry or update, but better to perform the callback at the end of the migration.

Example:
```ruby
Hours.skip_callback :save, :after, :reset_salon_caches
```
You can also passing a block to the skip method.


## Debugging Callbacks
[From the Rails API Guides](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html), running the following code will show all ```after_save``` callbacks on ```Hours```, but the output can be quite long depending on how many fields are on the model.

```ruby
Hours._save_callbacks.select { |cb| cb.kind.eql?(:after) }
```
To see a specific callback, use filter on the query.

```ruby
Hours._save_callbacks.select { |cb| cb.kind.eql?(:after) }.collect(&:filter).include?(:rest_salon_caches)
```

## Issues
While Rails provides tools for skipping and debugging callbacks, these methods raise the greater question of whether a callback should be used at all in many instances or if the method is best called explicitly elsewhere, such as in a service object.  This [DevMynd Post](https://www.devmynd.com/blog/2013-2-effective-rails-part-1-activerecord-callbacks) explains that callbacks' "inherent value is to couple multiple behaviors together. While they can be extremely alluring as a way to ensure certain behaviors occur together, it is very easy to make a mess with them".  Common causes of mess:

1. Keeping track of chains of events and maintaining single responsibility
Callbacks can make it difficult to keep track of what's happening.  If they affect other models that also have callbacks, that can set off a diffult to trace chain of events and can affect performance.  It is better to use service objects, outside of the model and the controller, that are testable and that handle the propper chain of events.

2. Bad to use in cases unless an event should ALWAYS occur.
It is recommended to only use callbacks when you ALWAYS want an event to occur, like incrementing a record count, for example.  It is bad practice to use them to send notifications, as there may be instances when the notifications should not be sent.

3. Dependency on order
Rails callbacks include 19 methods (such as ```before_save``` and ```after_create ```) as well as other events set in various places in the code (```dependent: destroy```, for example, is a callback that destroys dependents of an object).  When one callback is dependent on data that will be changed in another callback, the order in which they are called must be managed.  In cases where the order of one of the 19 callback methods is important it is best to use one callback that executes each of the updates within it.  In the case of a callback method that must know something about an object's dependents, it is possible to pass ```prepend: true ``` to the callback so that it is run first in the sequence of all callbacks.


## Resources
[Odin Project Active Record Callbacks] (http://www.theodinproject.com/ruby-on-rails/active-record-callbacks)
