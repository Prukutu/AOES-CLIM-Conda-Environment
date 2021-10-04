---
title: "Managing Your Python Environment"
teaching: 0
exercises: 0
questions:
- "What Python packages are available to me?"
- "How do I control the packages that are available to me?"
objectives:
- ""
keypoints:
- ""
---

Typically, we run Python on COLA by typing:

~~~
$ module load anaconda/3
~~~
{: .language-bash}

Then we can run a Python interface like Jupyter, Spyder, or the standard text-based Python interface (these are called interpreters).
The available Python packages (what we can import) and what version of those packages is available is controlled by the COLA system adminstration.

### Why do I care?

Suppose I searched the internet for how to do something in Python and found cool package or feature I want to use, but its not available to me.  What if I shared a program with you that used a cool new feature that was not available to you? 

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

The latest `xarray` documentation is for v0.16.1 (Sep 20, 2020).  The `xarray.cov` function and the `xarray.corr` functions were added in v0.16.0 (Jul 11, 2020).  How do I know this?  Click on the `What's new` in the `xarray` documentation and take a look.   How can I get this version of `xarray`?

### What version of `xarray` are we running?

We can list the versions of all Python packages we have access to with the command:
~~~
$ conda list
~~~
{: .language-bash}

This list is very long.  But, we can use our Unix skills to help us -- the `grep` command:
~~~
$ conda list | grep xarray
~~~
{: .language-bash}

~~~
xarray                    0.13.0                     py_0    conda-forge
~~~
{: .output}

We only have `xarray` version 0.13.0!  How can we change that?  Since we are not the system administrator, we cannot change the system Python packages. We could email the system administrator, but we may have to wait and someone else may want a different version because their program uses an old feature.  Instead what we can do is to manage our own Python environment. The Python environment manager is called `conda`, so we refer to this as our `conda` environment.

We can install lots of packages by hand, but what we really want to do is make a list of Python packages we will commonly use and install all of those.  I have provided you a file called `environment.yml`.  It contains a collection of Python packages that are common to climate.  We will install those packages into an environment called clim680.  

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

When we install the new environment, it will get the latest available versions of each package we listed in the environment file unless we specify a certain version. If we want to update to the latest version of our packages, we can run:

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

Now let's check if we have an updated version of `xarray`
~~~
$ conda list | grep xarray
~~~
{: .language-bash}

~~~
 xarray                    0.16.1                     py_0    conda-forge
~~~
{: .output}

There is one more command we need to run in order to tell Jupyter about our new environment:

~~~
$ python -m ipykernel install --user --name clim680 --display-name "Python (clim680)"
~~~
{: .language-bash}

*This should probably be removed* Now we will need to close our Jupyter notebook and get our COLA window back.  At the COLA prompt, type:
~~~
$ conda activate clim680
~~~
{: .language-bash}

Re-launch Jupyter. Open your notebook and click in the upper right corner where is says Python 3 and switch it to Python(clim680)
What do you see that is different?

### Do I have to do this everytime? 

Theses are 1-time steps that you only have to do to setup a new environment. 
From now on, you only have to select the environment you want for your notebook in Jupyter. 

If you run in a different Python interpreter, you will need to do the following before running Python:
~~~
$ conda activate clim680
~~~
{: .language-bash}

In the future, you may want to create different environments for different projects. If you provied someone with an `environment.yml` file for your project, it will guarantee they can reproduce the environment you ran it in and be able to run your codes.

I've given you just enough information to get a better environment setup for this class, but there's lots more if you are interested.  The conda User's Guide is [here](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).
