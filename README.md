R WUR setup
================

This documents outlines a simple way to reliably setup R on a WUR
windows machine. I took the time to write it because of the release of R
4.0.x. That way WUR folks can have a set of guidelines to efficiently
update their machine.

# Why update to R 4.0 ?

R 4.0 is a major release, that marks the 20th birthday of R. Aside from
that useless trivia, it also implies that a lot of stuff have changed
internally and you may soon encounter warnings when installing/loading
packages that might sooner or later turn into errors and seriously
hamper your R experience…

<img src="README_files/figure-gfm/unnamed-chunk-1-1.png" width="300" style="display: block; margin: auto;" />

So better getting that over with.

# R set-up good practices on windows WUR Sytems

On WUR windows system, I believe it is good practice to avoid installing
software in `C:/Program Files/`. For the simple reason that has a lambda
user you don’t have writing permission on that folder. Thus, you won’t
be able to modify the configuration files of your different programs. A
good work-around is to create a folder `C:/MyPrograms/` where you will
manually install software upon which you need to have finer control on.

A snapshot of my own `C:/MyPrograms/` directory:

``` r
fs::dir_tree("C:/MyPrograms/", recurse = FALSE)
```

    ## C:/MyPrograms/
    ## +-- Miniconda3
    ## +-- Pandoc
    ## +-- PyCharm Community Edition 2020.2.3
    ## +-- Python37
    ## +-- R
    ## +-- R-libraries
    ## +-- Rstudio
    ## +-- rtools40
    ## +-- Sublime Text 3
    ## +-- texlive
    ## +-- tinytex
    ## \-- Typora

**IMPORTANT**: To be able to install software yourself you need to have
**power-user** rights. This enables you to run program installers with
administrative rights simply by right-clicking on the .exe installer and
selecting **WUR - Run with administrative rights**. If you have a WUR
laptop it should be the default option, if not you will have to call IT,
so that they will grant you that privilege.

# Updating your WUR R setup

## Update R

### Download R for windows

[Download R 4.0.3](https://cran.r-project.org/bin/windows/base/)

### Install R

Execute the installer with the **WUR - Run with administrative rights**
option and make sure to install R in `C:/MyPrograms/R/`.

## Update Rstudio

For Rstudio to work smoothly with R 4.0.3, you need to install Rstudio
version 1.4.

### Uninstall your previous version of Rstudio:

Just to be sure. Type `WUR - uninstall applications` in the start-up
menu and click on the corresponding item. Then navigate to Rstudio in
the applications list, right click on it and uninstall it.

### Download Rstudio for windows

[Download Rstudio
1.4](https://rstudio.com/products/rstudio/download/#download)

### Install Rstudio

Execute the installer with the **WUR - Run with administrative rights**
option and make sure to install R in `C:/MyPrograms/Rstudio/`.

## Update Rtools

Rtools is the set of command line tools that is used to build R and R
packages from source, something you have to do from time to time to get
the latest version of a package. It is easy to install, you never have
to use it directly and it completes your setup. So no worries. You’d
better do that now as you are taking some time to upgrade your setup
rather than later when you will be trying to quickly run an analysis to
meet some important deadlines…

### Uninstall your previous version of Rtools:

If you already have Rtools installed, It should be version 3.5 or so.
Type `WUR - install applications` in the start-up menu and click on the
corresponding item. Then navigate to Rtools in the applications list,
right click on it and uninstall it.

### Download Rtools

[Downlad Rtools 4.0.3](https://cran.r-project.org/bin/windows/Rtools/)

Download the version suited for your system (32 or 64-bit). By now, you
should now have a 64-bit computer. If you have no clue about that go to
`Control Panel\System and Security\System` or simply type
`sessionInfo()` in the R console.

### Install Rtools

Execute the installer with the **WUR - Run with administrative rights**
option and make sure to install Rtools in `C:/MyPrograms/Rtools/`.

### Putting Rtools on the PATH

After installation is complete, you need to perform one more step: you
need to put the location Rtools on the your computer PATH which is
basically a system wide address book that allows programs to know where
to find each other. To do so we are going to edit a R start up file
called `Renviron.site`. Don’t worry it is REALLY simple.

At the R console in Rstudio type the following command:

``` r
file.edit(R.home("etc/Renviron.site"))
```

The `Renviron.site` will pop up as an editable script. Add the following
lines, save and close the `Renviron.site` file.

``` r
# Set Rtools path
PATH="${RTOOLS40_HOME}\\usr\\bin;${PATH}"
```

Now restart R, type the following command in the R console. That should
return something along those lines:

``` r
Sys.which("make")
```

    ##                                         make 
    ## "C:\\MYPROG~1\\rtools40\\usr\\bin\\make.exe"

If this works, you can try to install an R package from source:

``` r
install.packages("jsonlite", type = "source")
```

If this succeeds, you’re good to go!

## Reinstall your packages

### General advice

Your R packages are gathered in a special folder called a **library**.
By default on WUR windows system, that library is created in
`M:/Documents/R/win-library/` which means it goes dangling somewhere on
the WUR server. This is clearly sub-optimal as you have your packages
stored on a different network then R itself. It is a bit like having
your workshop in your basement and all your tools your the attic, not
really efficient. Moreover, if you don’t fix that you will get an error
each time you execute a command (something like :`\\\WURNET\\...`). So
for thing to run as smoothly as possible, I usually have my libraries in
`C:/MyPrograms/R-libraries/`.

#### Setting up R libraries in the proper locaton:

First, create a folder called: `C:/MyPrograms/R-libraries/`.

Then you will have to tell R upon start-up where your packages actually
are. This is fairly simple, just like we did with Rtools, we are gonna
edit the Renviron.site file once again:

At the R console type the following command:

``` r
file.edit(R.home("etc/Renviron.site"))
```

Add the following lines, save and close the `Renviron.site` file.

``` r
## Set location of user-installed packages
R_LIBS_USER=${R_LIBS_USER-'C:/MyPrograms/R-libraries/4.0.3'}
```

### Updating your packages

R 4.0 is a major release so it comes with brand you internals. In
non-jargon it means that R and R packages are built in a slightly
different way, which unfortunately forces the users to re-install all
their packages. To do that:

-   Make sure your CRAN mirror (the server where you download your
    packages) is set to the closet location: in RStudio, Tools &gt;
    Global Options &gt; Packages &gt; primary CRAN repository. For us at
    WUR the closest is
    `Belgium (Ghent) [https] - Ghent University Library`. If you are
    working from a different place, please pick the mirror closest to
    where you are.

-   Find your current R library, in the R console type:

``` r
.libPaths()
```

Something along those lines should appear:
`"C:/Users/Someone/Documents/win-library/4.0.3"`.

-   Close to that library should be your former R library with all the
    packages previously installed, something like:
    `"C:/Users/Someone/Documents/win-library/3.6"`

-   Still in the console now type:

**MAKE SURE YOU REPLACE THE PATH WITH YOURS !**

``` r
install.packages(list.files ("C:/Users/Someone/Documents/win-library/3.6")) 
```

This little hack should save you time. Just wait for everything to be
installed.

And *eh voilà* ! You should be ready with a sound and safe R 4.0.3 setup
!

## Acknowledgements

A lot of the ideas in this document came from the sweat and tears of
Maikel Verouden while battling with the WUR default R setup.
