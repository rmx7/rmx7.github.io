---
layout : post
tech : true
---

Test Driven Development has become a crucial part on today development process. PHPUnit is the widely used unit testing framework for PHP programmer but can it play nicely with HHVM?
Since version 2.4, HHVM is fully compatible with PHPUnit so let's try to install it.

I choose to install it from debian repository just because i'm too lazy to download it manually.

```
$ sudo apt-get install phpunit
$ phpunit --version
PHPUnit 4.2.6 by Sebastian Bergmann.
```
What i am trying to do is to test a simple Hello World hack class.

{% highlight php %}
<?hh
// Hello.hh

class Hello {
    public function sayHello(): string {
        return "Hello World";
    }
}
{% endhighlight %}

{% highlight php %}
<?hh
// HelloTest.hh

require 'Hello.hh';

class HelloTest extends PHPUnit_Framework_TestCase {
    public function testHello() {
        $hello = new Hello();

        $this->assertEquals("Hello World", $hello->sayHello());
    }
}

{% endhighlight %}

Then let's try invoking PHPUnit command running on the top of HHVM.

```
$ hhvm phpunit HelloTest.hh
Notice: File could not be loaded: phpunit
```

You'll get an error like the above example if you're not providing a full installation path of the PHPUnit package. On linux you can find it out with the following command.

```
$ whereis phpunit
phpunit: /usr/bin/phpunit /usr/share/man/man1/phpunit.1.gz
```

Then try to run the test again.

```
$ hhvm /usr/bin/phpunit HelloTest.hh
Fatal error: File not found: PHPUnit/Autoload.php in /usr/bin/phpunit on line 40
```
PHPUnit depends on PEAR libraries and it seems like HHVM must be told where to find it like this.

```
$ hhvm -v Server.IncludeSearchPaths.share=/usr/share/php /usr/bin/phpunit HelloTest.hh
PHPUnit 4.2.6 by Sebastian Bergmann.

.

Time: 617 ms, Memory: 5.34Mb

OK (1 test, 1 assertion)

```

The test run successfully but let's make the command shorter by creating an alias to it. Insert the following into your .profile file.

```
alias hhunit='hhvm -v Server.IncludeSearchPaths.share=/usr/share/php /usr/bin/phpunit' 
```
Then you can run the test like this.

```
$ hhunit HelloTest.hh
```

Nice! Let's practice a real TDD in the next post.
