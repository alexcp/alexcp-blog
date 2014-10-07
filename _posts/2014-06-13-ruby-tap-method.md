---
layout: post
title: "Ruby #tap method"
category: ruby
tag: blog
---

Ruby Object#tap method is a pretty cool method that can improve the readability of your code. 

`tap` was introduced in Ruby 1.9, what it does is pretty simple: it yields a value to a block and then returns it.

It's  especially useful when you need to populate an hash or a variable and return the value, it allows you to cut those intermediate variables.

For example you can turn this: 

{% highlight ruby %}

        def test
          hash = {}
          [:one, :two, :three].each_with_index do |num, i|
            hash[num] = i+1
          end
          hash
        end

{% endhighlight %}

into this:

{% highlight ruby %}

      def test
        {}.tap do |hash|
          [:one, :two, :three].each_with_index do |num, i|
            hash[num] = i+1
          end
        end
      end

{% endhighlight %}

In my opinion, the second example is much better, it is easier to read and parse rapidly.
