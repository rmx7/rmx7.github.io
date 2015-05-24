---
layout : post
tech : true
---

In this post, i want to try practicing TDD with my previous completed setup of HHVM with PHPUnit. There are several variation of how to perform TDD but on this occasion i am going with the RED->GREEN->CLEAN pattern. The algorithm that i want to test is the infamous algorithm interview question, the FizzBuzz.

I'll create a FizzBuzz generate method which will return "Fizz" string if the given number is a factor of 3, return "Buzz" if the given number is a factor of 5, return "FizzBuzz" if the given number is a factor of 3 and 5, finally return the number as it is if the given number is others.

####STEP 1 - Blind Test
{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    
}
{% endhighlight php %}

```
$ hhunit FizzBuzzTest.hh
Fatal error: File not found: FizzBuzz.hh in /home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh on line 2
```

Let's create the file then run the test again.

```
$ touch FizzBuzz.hh
$ hhunit FizzBuzzTest.hh
...
There was 1 failure:

1) Warning
No tests found in class "FizzBuzzTest".

FAILURES!
Tests: 1, Assertions: 0, Failures: 1.
```

So let's add a test method to our test class. Name the method with prefix test\*. 

{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    public function testGenerate() {
        $fizzbuzz = new FizzBuzz();
    }
}
{% endhighlight php %}

```
$ hhunit FizzBuzzTest.hh
Fatal error: Class undefined: FizzBuzz in /home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh on line 6
```

The FizzBuzz.hh doesn't contain the definition of FizzBuzz class. So let's define one.

{% highlight php %}
<?hh
class FizzBuzz {

}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh
OK (1 test, 0 assertions)
```
These are the pragmatic ways of doing unit testing. Step by step and detailed.

####STEP 2 - The Fizz
Now try to test the target method,

{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    public function testGenerate() {
        $fizzbuzz = new FizzBuzz();

        $this->assertEquals("Fizz", $fizzbuzz->generate(3));
    }
}
{% endhighlight php %}

```
$ hhunit FizzBuzzTest.hh

Fatal error: Call to undefined method FizzBuzz::generate() in /home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh on line 8
```
So let's create the method.

{% highlight php %}
<?hh
class FizzBuzz {
    public function generate() {

    }
}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh

There was 1 failure:

1) FizzBuzzTest::testGenerate
Failed asserting that null matches expected 'Fizz'.

/home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh:8

FAILURES!
Tests: 1, Assertions: 1, Failures: 1.
```

Let's be more specific about the definition of our generate method. Hack language allows us to define the type of passed argument and the return type of our method.

{% highlight php %}
<?hh
class FizzBuzz {
    public function generate(int $num) : string {
       return ""; 
    }
}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh
There was 1 failure:

1) FizzBuzzTest::testGenerate
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'Fizz'
+''

/home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh:8

FAILURES!
Tests: 1, Assertions: 1, Failures: 1.
```
Now it's time to insert the logic.

{% highlight php %}
<?hh
class FizzBuzz {
    public function generate(int $num) : string {
       if($num % 3 == 0) {
           return "Fizz";
       }
       return "$num"; 
    }
}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh
OK (1 test, 1 assertion)
```

####STEP 3 - The Buzz
*RED*

{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    public function testGenerate() {
        $fizzbuzz = new FizzBuzz();

        $this->assertEquals("Fizz", $fizzbuzz->generate(3));
        $this->assertEquals("Buzz", $fizzbuzz->generate(5));
    }
}
{% endhighlight php %}

```
$ hhunit FizzBuzzTest.hh
There was 1 failure:

1) FizzBuzzTest::testGenerate
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'Buzz'
+'5'

/home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh:9

FAILURES!
Tests: 1, Assertions: 2, Failures: 1.
```
*GREEN*
{% highlight php %}
<?hh
class FizzBuzz {
    public function generate(int $num) : string {
       if($num % 3 == 0) {
           return "Fizz";
       }
       else if($num % 5 == 0) {
           return "Buzz";
       }
       return "$num"; 
    }
}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh
OK (1 test, 2 assertions)
```

####STEP 4 - The FizzBuzz
*RED*

{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    public function testGenerate() {
        $fizzbuzz = new FizzBuzz();

        $this->assertEquals("Fizz", $fizzbuzz->generate(3));
        $this->assertEquals("Buzz", $fizzbuzz->generate(5));
        $this->assertEquals("FizzBuzz", $fizzbuzz->generate(15));
    }
}
{% endhighlight php %}

```
$ hhunit FizzBuzzTest.hh
There was 1 failure:

1) FizzBuzzTest::testGenerate
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'FizzBuzz'
+'Fizz'

/home/anathink/Project/hhvm/TDD/ongoing/FizzBuzzTest.hh:10

FAILURES!
Tests: 1, Assertions: 3, Failures: 1.
```
*GREEN*
{% highlight php %}
<?hh
class FizzBuzz {
    public function generate(int $num) : string {
        if ($num % (3 * 5) == 0) {
            return "FizzBuzz";
        }
        else if ($num % 3 == 0) {
            return "Fizz";
        }
        else if ($num % 5 == 0) {
            return "Buzz";
        }
        return "$num";
    }
}
{% endhighlight %}

```
$ hhunit FizzBuzzTest.hh
OK (1 test, 3 assertions)
```

####STEP 5 - Clean things up

Finally, we need a special handler for 0 because if we don't then the first condition will be validated to true if given the number 0. So here they are, the final code.
{% highlight php %}
<?hh
class FizzBuzz {
    public function generate(int $num) : string {
        if ($num == 0) {
            return "$num";
        }
        else if ($num % (3 * 5) == 0) {
            return "FizzBuzz";
        }
        else if ($num % 3 == 0) {
            return "Fizz";
        }
        else if ($num % 5 == 0) {
            return "Buzz";
        }
        else {
            return "$num";
        }
    }
}
{% endhighlight %}

{% highlight php %}
<?hh
require 'FizzBuzz.hh'

class FizzBuzzTest extends PHPUnit_Framework_TestCase {
    public function testGenerate() {
        $fizzbuzz = new FizzBuzz();

        $this->assertEquals("Fizz", $fizzbuzz->generate(3));
        $this->assertEquals("Buzz", $fizzbuzz->generate(5));
        $this->assertEquals("FizzBuzz", $fizzbuzz->generate(15));
        $this->assertEquals("0", $fizzbuzz->generate(0));
    }
}
{% endhighlight php %}

By going RED->GREEN->CLEAN route, i run the test class even if the FizzBuzz class and it's method doesn't exist.
There will be errors but that's what it means by RED. Code as fast you can, run the test, get the errors, fix it, then run it again until it's GREEN which mean there are no errors. After that, look at your code then refactor repetitive code to get a more CLEANer code.

Thanks for reading, see you next time.
