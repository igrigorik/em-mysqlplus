# EM-MySQL (Plus)

**[mysql2 gem](https://github.com/brianmario/mysql2) now ships with an EM driver - I recommend you switch & use it! And if you want synchronous API but an async driver, then take a look at [em-synchrony](https://github.com/igrigorik/em-synchrony)**

EventMachine wrapper for the C-based MySQL / MySQLPlus Ruby gems, which provides callbacks, errbacks and all other niceties of EventMachine while keeping the API of the original C-based MySQL gem.

Features:

 * Maintains C-based mysql gem API
 * Deferrables for every query with callback & errback
 * Connection query queue - pile 'em up!
 * Auto-reconnect on disconnects
 * Auto-retry on deadlocks

## Example usage:

```ruby
# gem install em-mysqlplus
# irb -r em-mysqlplus

EventMachine.run do
  conn = EventMachine::MySQL.new(:host => 'localhost')
  query = conn.query("select 1+1")
  query.callback { |res| p res.all_hashes }
  query.errback  { |res| p res.all_hashes }
end
```
## Query queueing:

```ruby
EventMachine.run do
  conn = EventMachine::MySQL.new(:host => 'localhost')

  results = []
  conn.query("select 1") {|res| results.push res.fetch_row.first.to_i}
  conn.query("select 2") {|res| results.push res.fetch_row.first.to_i}
  conn.query("select 3") {|res| results.push res.fetch_row.first.to_i}

  EventMachine.add_timer(0.05) do
    p results # => [1,2,3]
  end
end
```

# Credits

 * Original Async MySQL driver for Ruby/EventMachine - (c) 2008 Aman Gupta (tmm1)
 * ActiveRecord fiber patches - Mike Perham (mperham)