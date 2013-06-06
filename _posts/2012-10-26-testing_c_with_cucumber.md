---
layout: post
title: Testing C with Cucumber
category:
tag: blog
---

I had to write a small c program for a course at university. The program is quite simple, it takes arguments from the command line and output a result.

Naturally I wanted to automate the process of compiling and testing different result from the command line.
So I decided to use Cucumber (with a few rspec helper) to do it.

I will show an example with a simple hello world C program.

###Getting Started
First, we need to install the cucumber and rspec gem

    gem install cucumber && gem install rspec

I began by creating a hello.feature file inside a features directory.

Here is what I wrote inside.

{% highlight ruby %}

    Feature: hello world
     In order to know it work
     As a User
     I want to be greeted

     Scenario: Name as argument
      Given I have compiled the program
      When I execute it with "alex"
      Then the output should be "Hello alex"
     
     Scenario: No argument
      Given I have compiled the program
      When I execute it with ""
      Then the output should be "Hello"

{% endhighlight %}

The two scenario are pretty straightforward, I want to compile and execute the program with and without an argument.

We can run our test by simply entering cucumber in the command line.
If you do so now, you should get an output with pending test.

To implement our test, we need to create a new directory inside our feature directory called step_definitions.
We then create a file named hello_steps.rb
Here is the content of this file:

{% highlight ruby %}

    # execute a command and return the result in a string.
    def command_output_to_s(system_command)
      IO.popen(system_command,:err=>[:child,:out]).read.gsub(/\n/,' ').rstrip
    end

    # compile the hello.c program with gcc
    Given /^I have compiled the program$/ do
      command_output_to_s('gcc -W -Wall -o hello hello.c').should be_empty
      command_output_to_s('ls| grep hello').should eq("hello hello.c")
    end

    # run the program with the arguments
    When /^I execute it with "(.*?)"$/ do |arguments|
      @result = command_output_to_s('./hello ' + arguments)
    end

    # compare the previous result with the text
    Then /^the output should be "(.*?)"$/ do |text|
        @result.should eq(text)
    end
    {% endhighlight %}

We can run our test and watch it fails.
Now all we need to do is to write the implementation in c

Here is my c code

    {% highlight c %}
    #include<stdio.h>

    int main(int argc, char *argv[]){
      printf("Hello ");
      if(argc>0){
        printf(argv[1]);
      }
      return 0;
    }
{% endhighlight %}

Of course this is an extremly basic example but it should be a good foundation to build upon.
