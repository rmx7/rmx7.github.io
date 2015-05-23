---
layout : post
tech   : true
---

I have deep interest on virtual machine. The public release of HHVM is tickling my curiosity. So let's just take a look at it. First thing first, we need to download it. Fortunately there are pre built packages for debian jessie that officially supported by facebook themself. You can check all available pre-built packages [here](https://github.com/facebook/hhvm/wiki/Prebuilt-Packages-for-HHVM). There is no windows version at this time and also mac user need to build it themself. 

We need to import the autentication key first since the package is not in the official debian respository.

```
$ sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0x5a16e7281be7a449
```

Next, add the repository url to our aptitude sources list file

```
$ echo deb http://dl.hhvm.com/debian jessie main | sudo tee /etc/apt/sources.list.d/hhvm.list
```

Next, update our local debian package index

```
$ sudo apt-get update
```

Then install it with fire !

```
$ sudo apt-get install hhvm
```

Check which version we got

```
$ hhvm --version
HipHop VM 3.7.0 (rel)
Compiler: tags/HHVM-3.7.0-0-gc8baf9cd3cb603e030969bfe24634d5e85549915
Repo schema: 1988ce75e6a571ce9c79c60fd7dc3ca8a5a5d403
```

So i got the 3.7.0 release version. Here is the first taste of a hack program.

{% highlight php %}
<?hh
class MyClass {
    const int MyConst = 0;
    private string $x = '';
    public function increment(int $x): int {
        $y = $x + 1;
        return $y;
    }
}

$test = new MyClass();
print $test->increment(1) . "\n";
{% endhighlight %}
Then run it.

```
$ hhvm test.hh
Fatal error: /home/anathink/Project/hhvm/test.hh appears to be a Hack file, but you do not appear to be running the Hack typechecker. See the documentation at http://docs.hhvm.com/manual/en/install.hack.bootstrapping.php for information on getting it running. You can also set Hack.Lang.LookForTypechecker=0 to disable this check (not recommended).
```
Woops, something went wrong. It turns out i need to activate the type checker by creating empty .hhconfig file inside the project root.

```
$ touch .hhconfig
```

```
$ hhvm test.hh
2
```

Okay that's it, the installation process is pretty normal. Well, if you're using linux though and i recommend you to stick with linux as the easiest way of installing HHVM then start digging it rather than compiling it from scratch. You could, however, setting up a vagrant linux workspace then install HHVM on the top of it if you are using Windows or Mac.

Cheers.
