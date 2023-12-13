Linux distributions provide the commands `modprobe` , `insmod` and `depmod` within a package.

Ubuntu :

```bash
sudo apt-get install build-essential kmod
```

Discover what modules are already loaded within your current kernel use the command `lsmod`

```bash
sudo lsmod
```

Modules are stored within the file `/proc/modules`

```bash
sudo cat /proc/modules
```

This can be a long list, and you might prefer to search for something particular. To search for the fat module:

```bash
sudo lsmod | grep fat
```

A module compiled for one kernel will not load if a different kernel is booted, unless `CONFIG_MODVERSIONS` is enabled in the kernel

Before you can build anything you’ll need to install the header files for your kernel.

Ubuntu:

```bash
sudo apt-get update 
apt-cache search linux-headers-`uname -r`
```
This will tell you what kernel header files are available. Then for example:

```bash
sudo apt-get install kmod linux-headers-5.4.0-80-generic
```

Here is the simplest module possible.

```bash
mkdir -p ~/develop/kernel/hello-1 
cd ~/develop/kernel/hello-1
```

hello-1.c :

```bash
/* 
 * hello-1.c - The simplest kernel module. 
 */ 
#include <linux/module.h> /* Needed by all modules */ 
#include <linux/printk.h> /* Needed for pr_info() */ 
 
int init_module(void) 
{ 
    pr_info("Hello world 1.\n"); 
 
    /* A non 0 return means init_module failed; module can't be loaded. */ 
    return 0; 
} 
 
void cleanup_module(void) 
{ 
    pr_info("Goodbye world 1.\n"); 
} 
 
MODULE_LICENSE("GPL");
```

Now you will need a Makefile. If you copy and paste this, change the indentation to use tabs, not spaces.

Solve: Nano good for separate with tab


compile:

```bash
make
```

You can see the environment variable settings by:

```bash
$ sudo -s
# sudo -V
```

```bash
# should return nothing. You can try loading your shiny new module with:
sudo insmod hello-1.ko 

# The dash character will get converted to an underscore, so when you again try:
sudo lsmod | grep hello

# You should now see your loaded module. It can be removed again with:
sudo rmmod hello_1
```

new way to compile with `kbuild`

Additional details about Makefiles for kernel modules are available in Documentation/kbuild/makefiles.rst. Be sure to read this and the related files before starting to hack Makefiles. It will probably save you lots of work.

https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/Documentation/kbuild/makefiles.rst

these days you can name those anything you want by using the `module_init` and `module_exit` macros

https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/include/linux/module.h

The only requirement is that your init and cleanup functions must be defined before calling the those macros, otherwise you’ll get compilation errors. example of this technique:

Modules Spanning Multiple Files:

