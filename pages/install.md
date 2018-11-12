# Install projection system

Follow the following steps to install CIL projection system:

1. Access sacagawea

```
ssh -Y username@sacagawea.gspp.berkeley.edu
Are you sure you want to continue connecting (yes/no)? yes
username@sacagawea.gspp.berkeley.edu's password: password
```

2. Install python packages:

Make sure you have python 2.7 with `python --version`

```
pip install --user statsmodels
pip install --user metacsv
pip install --user scipy
```

3. Clone repositories

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

4. Create new file in `cil` with:

```
echo /shares/gcp >> server.yml
```

Data is stored at `/shares/gcp/social`
The folder should look like this:

```
username@sacagawea:~/cil$ ls
impact-calculations  impact-common  impactlab-tools  open-estimate  server.yml
```

5. Checkout to the right branches

```
cd impact-calculations/
git checkout release-1.0
cd ..
cd open-estimate/
git checkout release-2.0
```

6. Create your first job

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
./generate.sh configs/mortality-test.yml
```

Results are to be found at: `dummy-test`
