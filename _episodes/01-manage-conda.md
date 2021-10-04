---
title: "Managing Your Python Environment"
teaching: 45
exercises: 0
questions:
- "What Python packages are available to me?"
- "How do I control the packages that are available to me?"
objectives: 
- "Learn how to create, select and manage conda environments."
keypoints:
- "Conda environments allow you to install and manage Python packages to suit your needs." 
- "Conda environments let you keep a stable set of software versions for your projects, so that code remains functional and backwards-compatible."
---

Typically, we run Python on COLA by typing:

~~~
$ module load anaconda/3
~~~
{: .language-bash}

Then we can run a Python interface like Jupyter, Spyder, or the standard text-based Python interface (these are called interpreters).
The available Python packages (what we can import) and what version of those packages is available is controlled by the COLA system adminstration.

### Why do I care?

Suppose we searched the internet for how to do something in Python and found cool package or feature to use, but its not available on our system.  What if I shared a program with you that used a cool new feature that was not available to you? 

Here's an example: Search for `calculate correlation xarray dataset`. The top hit is: http://xarray.pydata.org/en/stable/generated/xarray.corr.html.

This page has an example. We will try it.

The first part creates a fake `DataArray`:

~~~
da_a = xr.DataArray(
    np.array([[1, 2, 3], [0.1, 0.2, 0.3], [3.2, 0.6, 1.8]]),
    dims=("space", "time"),
    coords=[
        ("space", ["IA", "IL", "IN"]),
        ("time", pd.date_range("2000-01-01", freq="1D", periods=3)),
    ],
)
da_a
~~~
{: .language-python}

The second part creates a second fake `DataArray`:

~~~
da_b = xr.DataArray(
    np.array([[0.2, 0.4, 0.6], [15, 10, 5], [3.2, 0.6, 1.8]]),
    dims=("space", "time"),
    coords=[
        ("space", ["IA", "IL", "IN"]),
        ("time", pd.date_range("2000-01-01", freq="1D", periods=3)),
    ],
)
da_b
~~~
{: .language-python}

Use the `xarray.corr` function to correlate them:
~~~
xr.corr(da_a, da_b)
~~~
{: .language-python}

~~~
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-9-bcca402ca42a> in <module>
----> 1 xr.corr(da_a, da_b)

AttributeError: module 'xarray' has no attribute 'corr'
~~~
{: .output}

## Why doesn't this work?  I followed the example?

The latest `xarray` documentation is for v0.19.0 (23 July, 2021).  The `xarray.cov` function and the `xarray.corr` functions were added in v0.16.0 (Jul 11, 2020).  How do we know this?  Click on the ["What's new"](http://xarray.pydata.org/en/stable/whats-new.html#v0-16-0-2020-07-11) in the `xarray` documentation and take a look.  How can we get a more up-to-date version of `xarray`?

### What version of `xarray` are we running?

We can list the versions of all Python packages we have access to with the command:
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
xarray                    0.13.0                     py_0    conda-forge
~~~
{: .output}

We only have `xarray` version 0.13.0 (that was an update from 2019)!  How can we change that?  Since we are not the system administrator, we cannot change the system Python packages. We could email the system administrator, but we may have to wait and someone else may want a different version because their program uses an old feature.  Instead what we can do is to manage our own Python environment. The Python environment manager is called `conda`, so we refer to this as our `conda` environment.

We can install lots of packages by hand, but what we really want to do is make a list of Python packages we will commonly use and install all of those.  We have provided you a file called `environment.yml`.  It contains a collection of Python packages that are common to climate.  We will install those packages into an environment called clim680.  

Copy the file to your directory.
~~~
$ cp /homes/nburls/CLIM680-env/environment.yml .
~~~
{: .language-bash}

Install the environment:
~~~
$ conda env create -f environment.yml
~~~
{: .language-bash}

Now we will have to wait a bit while the environment installs. At some point it will ask us to answer yes or no if we wish to proceed. Type `y`.

While its installing, let's take a look at what is in this file using another window:
~~~
$ more environment.yml
~~~
{: .language-bash}

Note that the file includes a `name` set to `clim680`. 
When we install the new environment, it will get the latest available versions of each package we listed in the environment file unless we specify a certain version (e.g., as we have done for `python=3.6`) and give the new environment the name `clim680`. 
If we later want to update to the latest version of our packages, we can run:

~~~
$ conda env update -f environment.yml
~~~
{: .language-bash}

Anytime we want to add a new Python package to our environment, we can add it to our `environment.yml` file and run the same update command.

Once the environemnt is done installing, `conda` tells us what to do:
~~~
$ conda activate clim680
~~~
{: .language-bash}

Do that, and let's check if we have an updated version of `xarray`
~~~
$ conda list xarray
~~~
{: .language-bash}

~~~
 xarray                    0.18.2                     pyhd8ed1ab_0    conda-forge
~~~
{: .output}

Note we do not necessarily receive the very latest version. 
One of the features of `conda` is that it checks for compatabilities among all installed software packages and tries to ensure that every version of each 
will work correctly with all the other packages. 
When you install a new Python package, `conda` may also download _dependencies_ - other packages that are required to run your new package.
Also, sometimes `conda` will _downgrade_ the versions of packages already installed to maximize compatability.

There is one more command we need to run in order to tell Jupyter about our new environment:

~~~
$ python -m ipykernel install --user --name clim680 --display-name "Python (clim680)"
~~~
{: .language-bash}

This adds the new `clim680` environment to your list of available environments. To list all available environments:

~~~
$ conda env list
~~~
{: .language-bash}

A "cheat sheet" of common conda commands is [available for download](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html).


Now we will need to close our Jupyter notebook for changes to be recognized, and get our COLA terminal window back.  At the COLA prompt, type:
~~~
$ conda deactivate
~~~
{: .language-bash}

Re-launch Jupyter. Open your notebook, click in the upper right corner where is says Python 3 and switch it to Python(clim680).
Verify that you now have the new version of `xarray` available to you in your Jupyter session:
~~~
import xarray
!conda list xarray
~~~
{: .language-python}
What do you see that is different?

### Do I have to do this everytime? 

These are 1-time steps that you only have to do to setup a new environment. 
From now on, you _should_ only have to select the environment you want for your notebook in Jupyter by changing the kernel. 

If you run in a different Python interpreter (e.g., Spyder or from the command line), you will need to do the following to access this environment before running Python:
~~~
$ conda activate clim680
~~~
{: .language-bash}

In the future, you may want to create different environments for different projects. If you provied someone with a `*.yml` file for your project, it will guarantee they can reproduce the environment you ran it in and be able to run your codes.

We've given you just enough information to get a better environment setup for this class, but there's lots more if you are interested.  The [conda User's Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) can help.
