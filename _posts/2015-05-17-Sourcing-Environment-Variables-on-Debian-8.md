---
layout : post
---

First, check the content of our ~/.bash_profile.
{% highlight bash %}
# ~/.bash_profile
[[ -s "$HOME/.profile" ]] && source "$HOME/.profile" # Load the default .profile
{% endhighlight %}

As we can see that .bash_profile is actually searching for a file named .profile inside our home directory. So let's declare our environment variable in .profile instead. In my case, i want to declare the JAVA_HOME env variable.

{% highlight bash %}
# ~/.profile
export JAVA_HOME=$HOME/Apps/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin
{% endhighlight %}

We can source the file manually by invoking.
{% highlight bash %}
source ~/.profile
{% endhighlight %}

But that's not how .profile get sourced normally. Logout and login again, open the terminal then type.
{% highlight bash %}
echo $JAVA_HOME
{% endhighlight %}

It should print our JDK installation path. Then check the version of our executable java binaries.
{% highlight bash %}
java -version
{% endhighlight %}

Next, try to run java program both from command line and GUI Menu then let's see what happen. I can succesfully run eclipse launcher from terminal. Great, but how about running it from GUI menu?

### JAVA_HOME cannot be detected by GUI Menu of cinnamon desktop !
Usually on linux, when we want to declare an environment variables then we only need to add it into one of the following files.

- User scope
    1. ~/.bash\_profile
    2. ~/.profile
    3. ~/.bashrc
- System wide
    1. /etc/profile
    2. /etc/profile.d/\*.sh
    3. /etc/environment 

After that, all of the variables should be available on X session or terminal session each on their own scope. But it turns out this is not the case on Debian 8. I am using cinnamon version of Debian 8 which is using lightDM as the desktop manager. LightDM does not source the listed file above when creating new X session whereas GDM or KDM are doing exactly the opposite of what LightDM did. So in a nutshell, lightDM separates rc files for login shell and the X session. That's mean any other attempt to declare the env var inside the rest of listed files above is useless. 

### .xsessionrc to the rescue
So what we need to do is to create ~/.xsessionrc inside our home directory then put our env var declaration in there.
{% highlight bash %}
# ~/.xsessionrc
export JAVA_HOME=$HOME/Apps/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin
{% endhighlight %}

In my case, i am successfully run eclipse through the cinnamon menu. But the downside is we need to edit both files everytime we want to modify something. So here is my solution. Instead of redeclare our variables inside .xsessionrc, why don't we just source the .profile again inside the .xsessionrc. By this way, we only need to modify .profile file if we want to add, delete, or update the environment variables.
{% highlight bash %}
# ~/.xsessionrc
. ~/.profile
{% endhighlight %}

I don't know why they're doing this. But for me this is not a bug , it's more like a design decision from lightDM developer.

Hope that help someone out there.  

See you later!
