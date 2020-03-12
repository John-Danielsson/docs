# Ruby

As usual, "groupname" is your group name and "username" is your userid.

Below are the steps to install ruby from source:
1) ```srun -p build --time=2:00:00 --mem=100G --pty /bin/bash```

2) ```mkdir /gscratch/groupname/username/rubystuff```

3) ```mkdir /gscratch/groupname/username/rubyinstall```

4) ```cd /gscratch/groupname/username/rubystuff```

_Below wget command will get source code for Ruby 2.5.1. If you want a different version of Ruby, go to [this link](https://www.ruby-lang.org/en/downloads/) and right click on the links under stable releases and copy link location to choose a different version of Ruby._

5) ```wget https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.1.tar.gz```

6) ```tar -xvf ruby-2.5.1.tar.gz```

7) ```cd ruby-2.5.1```

8) ```./configure --prefix=/gscratch/groupname/username/rubyinstall```

9) ```make```

10) ```make install```

11) After this you can put ```/gscratch/groupname/username/rubyinstall/bin``` in your PATH environment variable like so:

```export PATH=/gscratch/groupname/username/rubyinstall/bin:$PATH```

12) Run this command to verify that you have got Ruby 2.5.1:
```ruby -v```
 
**For RVM users:**
If you have installed RVM and want to use RVM then first run the below command and then use RVM. This command will ensure that RVM does not try to install dependencies to system locations.
```RVM autolibs disable```