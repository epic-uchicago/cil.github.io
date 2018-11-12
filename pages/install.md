# Install projection system

This tutorial is based on [these instructions](https://bitbucket.org/ClimateImpactLab/impact-calculations/src/release-1.0/docs/process.md). Follow these steps to install CIL projection system and run your first job on Sacagawea.

## 1. Access sacagawea

```
ssh -Y username@sacagawea.gspp.berkeley.edu
Are you sure you want to continue connecting (yes/no)? yes
username@sacagawea.gspp.berkeley.edu's password: password
```

## 2. Install python packages:

Make sure you have python 2.7 with `python --version`

```
pip install --user statsmodels
pip install --user metacsv
pip install --user scipy
```

## 3. Clone repositories and install

You can just copy and paste all of these commands. Make sure you have permission to access `bitbucket.org/ClimateImpactLab/impact-calculations.git`

```
mkdir cil
cd cil
git clone https://github.com/ClimateImpactLab/open-estimate.git
cd open-estimate/
python setup.py develop --user
cd ..
git clone https://github.com/ClimateImpactLab/impactlab-tools.git
cd impactlab-tools/
python setup.py develop --user
cd ..
git clone https://github.com/ClimateImpactLab/impact-common.git
cd impact-common/
python setup.py develop --user
cd ..
git clone https://username@bitbucket.org/ClimateImpactLab/impact-calculations.git
```

Your `cil` folder should look like this:

```
username@sacagawea:~/cil$ ls
impact-calculations  impact-common  impactlab-tools  open-estimate
```

## 4. Create new file in `cil` with:

> The `impact-calculations` code needs to know where to find the `$DATA` directory and is given this information by placing a file named `server.yml` in the directory that contains `impact-calculations`. 

Data is stored at `/shares/gcp/social`, hence we create the file with the following command:

```
username@sacagawea:~/cil$ echo /shares/gcp >> server.yml
```

The folder `cil` should now look like this:

```
username@sacagawea:~/cil$ ls
impact-calculations  impact-common  impactlab-tools  open-estimate  server.yml
```

## 5. Checkout to the right `git` branches

Copy and paste this:

```
cd impact-calculations/
git checkout release-1.0
cd ..
cd open-estimate/
git checkout release-2.0
cd ..
```

## 6. Create your first job

Navigate to `configs`

```
cd impact-calculations/
cd configs/
vim mortality-test.yml
```

Put the following inside `mortality-test.yml`

```
module: mortality
mode: single
outputdir: /shares/gcp/outputs/mortality/impacts-darwin
do_only: interpolation
do_farmers: false # do not include all three adaptation scenarious
do_historical: false # true means it includes contrafactual
csvvfile: /shares/gcp/social/parameters/mortality/mortality_nonFGLS_23082018/Agespec_interaction_GMFD_POLY-4_TINV_CYA_NW_w1.csvv
singledir: dummy-test
specification: polynomial
econcovar: # income
   class: bartlett
   length: 13 # take 13 previous years
climcovar: # long run average temperature
   class: bartlett
   length: 30 # take 30 previous years
```

Run the job:

```
username@sacagawea:~/cil/impact-calculations$ ./generate.sh configs/mortality-test.yml
rcp85 CCSM4
SSP3 high
/shares/gcp/outputs/mortality/impacts-darwin/dummy-test/rcp85/CCSM4/high/SSP3
interpolation
Agespec_interaction_GMFD_POLY-4_TINV_CYA_NW_w1
REDO: Cannot find /shares/gcp/outputs/mortality/impacts-darwin/dummy-test/rcp85/CCSM4/high/SSP3/Agespec_interaction_GMFD_POLY-4_TINV_CYA_NW_w1-young.nc4
Smart Farmer
Collecting baseline information...
1981
1982
1983
1984
1985
...
```

Results are to be found at: `dummy-test`

## Troubleshooting 

Error:

```
Traceback (most recent call last):
  File "/usr/lib/python2.7/runpy.py", line 174, in _run_module_as_main
    "__main__", fname, loader, pkg_name)
  File "/usr/lib/python2.7/runpy.py", line 72, in _run_code
    exec code in run_globals
...
  File "/usr/lib/python2.7/contextlib.py", line 17, in __enter__
    return self.gen.next()
  File "/usr/lib/python2.7/dist-packages/matplotlib/__init__.py", line 1002, in _open_file_or_url
    encoding = locale.getdefaultlocale()[1]
  File "/usr/lib/python2.7/locale.py", line 545, in getdefaultlocale
    return _parse_localename(localename)
  File "/usr/lib/python2.7/locale.py", line 477, in _parse_localename
    raise ValueError, 'unknown locale: %s' % localename
ValueError: unknown locale: UTF-8
```

try this:

```
username@sacagawea:~/cil/impact-calculations$ locale
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE=UTF-8
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
username@sacagawea:~/cil/impact-calculations$ export LC_ALL=en_US.UTF-8
username@sacagawea:~/cil/impact-calculations$ export LANG=en_US.UTF-8
```
