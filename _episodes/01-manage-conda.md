---
title: "Managing Your Python Environment"
teaching: 45
exercises: 30
questions:
- "What Python packages are available to me?"
- "How do I control the packages that are available to me?"
objectives: 
- "Learn how to create, select and manage conda environments."
keypoints:
- "Conda environments allow you to install and manage Python packages to suit your needs." 
- "Conda environments let you keep a stable set of software versions for your projects, so that code remains functional and backwards-compatible."
---

When you run Python, either from the command line 
(whether from a terminal window from your laptop or within the Hopper Desktop on your browser), 
there will be certain functions and libraries installed, 
but there are many more functions and libraries that are available 
which are not part of the _default_ setup on Hopper.

If you run Python from a JupyerLab session, you may see a clue that there are options. 
On the upper right corner of your workspace, there will be a small circle, with a _string_ to its left.
The string is the name of the **kernel** that your notebook is running.
Jupyter _dynamically_ creates a kernel that meets the specifications of a particular **environment**.
Click on the string, and you will see some alternatives listed.
Different environments can include different versions of the same software,
or completely different functions and libraries. 
What do you do when there is a certain Python function or library you want to use, 
but the `import` command returns a message that the function or library is not available?

## Personalizing your environment

There are two main ways to maintain environments:

### Python virtual environments

On Hopper, the default system is to use Python Virtual Environments. In this approach, 
commands and libraries are packaged into separate **modules** that can be individually loaded.
This system of modules is much broader than just for Python - it is a general system
for making software available on Linux/Unix systems. Recall when we used the `ncks` command
to peer inside NetCDF files, we had to load a module called `nco`. That did not 
involve Python at all.

The module system requires system administrators to install software before you can use it. 
If the software you want is not already avaiable on the system, you will have to submit a 
request in the same way you would request any other assistance from ORC: 
by sending an email to [orchelp@gmu.edu](mailto:orchelp@gmu.edu).

### Conda environments

A more customizable and independent way to maintain environments 
specific to Python and a number of other programming languages
is to use [Conda](https://docs.conda.io/projects/conda/en/latest/index.html).
Conda is an open-source **package** management system and _environment_ management system that
ensures the versions of various _packages_ (a set of related software functions and/or libraries)
within an environment are all consistent with each other... 99% of the time. 
Occasionally it fails to ensure consistency - an occurrence that is usually the fault of the 
software engineers maintaining one or more of the packages. 
But such problems are rare, and outweighed by the benefits of customizability.

It is common to start a new environment for each project 
(e.g., each manuscript you submit to a peer-reviewed journal). 
This is done to maintain back-compatability. 
You do not want to find that the scripts you used to make the figures for a paper 
are broken when you come back to revise the manuscript, because a software update for one of
your packages changed the function names, arguments or parameters (this does happen, especially
when software versions change their first-digit numbers, e.g., from v0.5 to v1.0)
By keeping an environment _frozen_ with all the Python scripts and notebooks that are working correctly, 
you can ensure that they will still work correctly weeks, months, even years later.

## Setting up Conda on your Hopper account 

From a terminal session on Hopper, 
edit your `.bashrc` file and make sure you are not loading the Python module - we 
will be invoking versions of Python from our Conda environments from now on, not from modules.

~~~
#module load python
~~~
{: .language-bash}

The hash `#` at the start of the line will _comment out_ that line so it is not executed.  
Save your edited .bashrc file, and then reinitialize your shell:

~~~
$ source .bashrc
~~~
{: .language-bash}

Then we will need to _unload_ the Python module if is loaded.  To see your loaded modules, type:

~~~
$ module list
~~~
{: .language-bash}

If you see a module there starting with `python`, unload it:

~~~
$ module unload python
~~~
{: .language-bash}

Now we are ready to run Conda. From the terminal command line on Hopper, type two commands:

~~~
$ module load anaconda3
$ conda init bash
~~~
{: .language-bash}

Now, you should see something different in your command line prompt.  Before it looked something like this:

~~~
[yourname@hopper1 ~]$
~~~
{: .language-bash}

But now it looks like this:

~~~
(base) [yourname@hopper1 ~]$
~~~
{: .language-bash}

That extra word in parentheses is your _Conda environment_. By default it is `base`. 

Let's have a look at the environments available to you:

~~~
$ conda env list
~~~
{: .language-bash}

You will see a list of Conda environment names, along with their paths - all (probably) 
residing under the `/opt` first-level directory on the cluster.

If you look closely, there is one called `clim680`. We are _not_ going to use that one.
Why? Because it is owned by `opsadmin` and not us, which means we cannot modify it.
The whole point of Conda environments is that they are customizable. 
Instead, we will each create our own copy of that environment, 
and add some missing packages that we will need for this class.

## Making a new environment

There are several ways to make a new environment in Conda. 
One way is to create and share `.yaml` files (YAML stands for "yet another markup language") that 
document all the pakages and versions in an environment, so that they can be duplicated
by downloading and installing all the right files.

Since we have the `clim680` environment on our system already, we will **clone** it to a new name and
location under our home directory, and then update it to suit our needs.
Let's name the new environment for this class `clim_data`:

~~~
$ conda create --clone clim680 --name clim_data
~~~
{: .language-bash}

It will take some time to complete this command. Conda will verify that all of the
packages are compatable with one another before installing the copy in our home directory.

![A little longer than a few minutes later...](../fig/spongebob_later.jpg)

Once complete, you can see that your new environment is listed among the others:

~~~
$ conda env list
~~~
{: .language-bash}
Furthermore, you will see that your new environment's path is under your home directory, not a system directory. 
It belongs to you!


## Choosing and updating environments

From the command like in your `bash` shell, we can change the active environment with
the `activate` command in Conda:

~~~
$ conda activate clim_data
~~~
{: .language-bash}

Your new environment is the active environment. If we install new packages now, 
they will go into this environment as updates.

Let's install a few packages that we will need later in this class:
* `cftime` and `cfgrib` will expand the capabilities of `xarray` to read and interpret self-describing data files.
* `metpy` is a handy Python package of functions for Atmospheric Science, which includes the tracking and conversion of units.
* `esmpy` is a Python package for functions used in the Earth System Modeling Framework (ESMF) - also very useful for Climate Science.

~~~
$ conda install -c conda-forge cftime cfgrib metpy esmpy
~~~
{: .language-bash}

Here, `conda-forge` is the name of the main **channel** for software in the Conda universe - it is the most
complete and most likely to have all the things we need. 
You can set up a prioritized list of channels to search for packages. 
The [Conda documentation](https://docs.conda.io/projects/conda/en/latest/commands.html) shows you how.
Some specialty packages are only available on specific sites, and may not be on the most popular sites. 

You will notice that Conda will do more than simply locate and install these specific packages. 
It also determines other necessary packages, called **dependencies**, and flags them for downloading as well.
It may also determine that versions of packages already installed need to change - to either newer _or older_ versions.
This is all part of Conda's effort to maintain consistency so that everything will work smoothly.

Conda will also ask you if you want to proceed with the changes - you may notice that it is
proposing to make a change you do not want, e.g., it might propose to downgrade an
important package to an older version that is lacking important functionality. 

~~~
Proceed ([y]/n)?
~~~
{: .language-bash}

In this case, enter `y` and proceed. Again, you will have to wait several minutes for the verification and 
execution of the _transaction_.

Because you have installed additional packages, your `clim_data` environment is now different than the `clim680` environment you cloned.

## Making your new environment accessible to JupyterLab

The last setup step is making your new environment available as a choice when you are running JupyterLab. 
To do that, we need to make `ipython`, the interactive version of Python upon 
which Jupyter and other interactive interfaces such as Spyder are based, 
aware of your new environment. 

From your command line, be sure the `clim_data` environment is still the active one, and type:

~~~
$ ipython kernel install --user --name=clim_data
~~~
{: .language-bash}

You could give it a different name: this is the name of the _kernel_ you will see listed among the choices in JupyterLab. 
However, it is usually less confusing to keep the name of the kernel the same as the name of its associated environment. 



### A few other useful Conda commands

We can list the versions of all Python packages we have access to in the active environment with the command:

~~~
$ conda list
~~~
{: .language-bash}

This list is very long.  We can list specific packages, or use wildcards to find packages whose name contains a particular string.

~~~
$ conda list xarray
~~~
{: .language-bash}

~~~
# Name                    Version                   Build  Channel
xarray                    0.20.1                   pypi_0    pypi
~~~
{: .output}

You can also search the contents of a channel for packages, also using wildcards if you want:

~~~
$ conda search -c conda-forge metpy
~~~
{: .language-bash}

~~~
# Name                       Version           Build  Channel
metpy                          0.3.0          py27_0  conda-forge
metpy                          0.3.0          py27_1  conda-forge
.
.
.
metpy                          1.2.0    pyhd8ed1ab_0  conda-forge
metpy                          1.3.0    pyhd8ed1ab_0  conda-forge
metpy                          1.3.1    pyhd8ed1ab_0  conda-forge
~~~
{: .output}

Specific versions or ranges of versions of packages can be queried:

~~~
$ conda search -c conda-forge "metpy>=1.0"
~~~
{: .language-bash}

~~~
# Name                       Version           Build  Channel
metpy                            1.0    pyhd8ed1ab_0  conda-forge
metpy                          1.0.1    pyhd8ed1ab_0  conda-forge
metpy                          1.1.0    pyhd8ed1ab_0  conda-forge
metpy                          1.2.0    pyhd8ed1ab_0  conda-forge
metpy                          1.3.0    pyhd8ed1ab_0  conda-forge
metpy                          1.3.1    pyhd8ed1ab_0  conda-forge
~~~
{: .output}

You can create a catalog of the current state of this environment by writing it out to a .yaml file:

~~~
$ conda env export > clim_data.yaml
~~~
{: .language-bash}

Later, if you want to update it with changes you have made (e.g., after installing more packages),
you can update it:

~~~
$ conda env update -f clim_data.yaml
~~~
{: .language-bash}

You can share a `.yaml` file with colleagues so that they will have the same environment, 
and thus the same functionality, as you. This can be crucial if you are sharing a Python script or notebook.
Your colleague may find your notebook does not work properly in any of their environments. 
Sharing the environment that matches the script or notebook is a great way to ensure functionality.

If someone shares an environment file with you, you can make a Conda environment from it with the
`env create` command:

~~~
$ conda env create --file <colleague's_file>.yaml
~~~

The name of the new environment is not necessarily the same as the filename - the first line of the `.yaml`
file will contain a line that names the environment, e.g.:

~~~
name: hopper_test
~~~
{: .output}

Be sure that the name is different from any of your existing environments! If it is not, you can edit the `.yaml`
file and change the name before creating the new environment.

## Do I have to do this every time? 

Whether you are running Python from the command line, a Jupyter session or submitting a batch job using slurm, 
you will want to ensure the correct environment is active before you start running. 
But the initialization and installation steps described above do not need to be repeated.

We've given you just enough information to get a better environment setup for this class, but there's lots more if you are interested.  The [conda User's Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) can help.

Additionally, there is the [Conda cheat sheet](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html) - a handy two-page guide to common commands and syntax.
