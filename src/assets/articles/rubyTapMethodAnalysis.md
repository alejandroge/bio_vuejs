# Ruby "tap" method analysis
###### Published on July 12, 2020

In this article, you will read an analysis of Ruby’s tap method, some examples of its use and it’s code.

`tap` is one of those methods that is not used that often. In the documentation for it, it gets mentioned that its main objective is to obtain access to an object during method chaining, making it very useful for debugging said chainings.

### Use examples
#### Debugging
Now, I’ll show you a couple of use examples for it. These are the ones that I’ve used, and the reason why I really like this method. The first of them, is a common problem when you need to make a few transformations to a data collection.

```ruby
(1..9).
  select(&:odd?).
  reject { |n| n > 5 }.
  sum
# => 9
```

So, let’s say we are not obtaining the desired value at the end of the method chaining, tap could help us find where in the chain we have the bug, simply by adding tap calls in the place where we would like to take a look.

```ruby
(1..9).
  tap { |x| p x.to_a }. # => [1 , 2, 3, 4, 5, 6, 7, 8, 9]
  select(&:odd?).
  tap { |x| p x }.      # => [1, 3, 5, 7, 9]
  reject { |n| n > 5 }.
  tap { |x| p x }.      # => [1, 3, 5]
  sum
# => 9
```

In this we can tap into the chain and see how the data is changing after each method, without having to change the structure of the code. If we would like to do something similar, without using tap we would have to:

1. Save each step independently in a variable
1. Puts the variable
1. Finally, call the same method off the variable to continue the chaining

Even though it doesn’t sound that bad right now, we still have to consider putting the code back to its original state after finding the bug. This is where tap shines.

But, tap is not only useful for debugging. It is also use possible to use it as part of the features of the code we write.

#### Object initialization

The second example is exactly about this. When we create new objects, it is common that we do some steps for its initialization. tap is an excellent alternative for these scenarios, which we can group perfectly in a block.

```ruby
person = People.tap do |p|
  p.name = 'Alex'
  p.last_name = 'Guevara'
  p.greet #=> 'Hello I'm Alex'
end
```

#### tap code
For better understanding the observed behaviour, we can take a look at the method’s code itself. From Rails docs, we know that:

```ruby
def tap
  yield self
  self
end
```

We can see that in fact, is a pretty simple method, but still useful nonetheless. Another interesting bit of information we can get from the documentation, is the methods history. Originally, it was found in Rails `2.3.2` version, but it got included into Ruby code in `v1_8_7_72` version.
