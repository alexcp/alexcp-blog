---
layout: post
title: Ruby #tap
category: ruby
tag: blog
---

Ruby Object#tap method is a pretty cool method that can improve the readability of your code. 

`tap` was introduced in Ruby 1.9, what it does is pretty simple: it yields a value to a block and then returns it.

It's  especially useful when you need to populate an hash or a variable and return the value, it allows you to cut those intermediate variables.

For example you can turn this: 

        def test
          hash = {}
          [:one, :two, :three].each_with_index do |num, i|
            hash[num] = i+1
          end
          hash
        end

into this:

      def test
        {}.tap do |hash|
          [:one, :two, :three].each_with_index do |num, i|
            hash[num] = i+1
          end
        end
      end

In my opinion, the second example is much better, it is easier to read and parse rapidly.
