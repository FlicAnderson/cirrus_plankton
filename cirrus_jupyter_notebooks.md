# Notes on Running Jupyter Notebooks on Cirrus  

This is how I got the PyTorch.ipynb notebook running on cirrus: 

### Jupyter Notebooks on Cirrus (login node): 

#### Log into cirrus  

**Step 1:** log in to cirrus from local terminal: `ssh flic-epcc-laptop-cirrus-m23ss@login.cirrus.ac.uk`, enter password.  

#### Create .bashrc file  

**Step 2:** created .bashrc in home directory (`/home/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss`) with nano, added following lines:   
```
# .bashrc file contents at /home/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss:

export WORK_DIR=/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/
export HOME=$WORK_DIR
export JUPYTER_RUNTIME_DIR=$(pwd)
```

**Step 3:** run .bashrc file using `source .bashrc`.  

#### Get miniconda (instead of anaconda)  

**Step 4:** change directory to work directory using `cd $WORK_DIR` (this helps confirm that the .bashrc file works).  

**Step 5:** download `miniconda` installer script to my folder in the work directory. Probably don't need to do this if you're using `module load anaconda/python3`, but Ananya recommended it so I went with it :D  
```
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
$ bash Miniconda3-latest-Linux-x86_64.sh
```
This then runs an installer, where you press enter to scroll through, then type 'yes' to agree after reading a EULA, and then it asks: 
```
Miniconda3 will now be installed into this location:
/home/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/home/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3] >>> /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3     
```
Specify your $WORK_DIR location, with `/miniconda3` on the end of it. I don't think it would work if I just typed $WORK_DIR into the miniconda3 installer, so I typed the whole thing out. It then went through a bunch of downloading and installing, and showed that it had modified my .bashrc file.   

**Step 6:** use nano (or whatever text editor) to check the .bashrc file contents contain a block starting `# >>> conda initialize >>>`. It should look like this now:   
```console
export WORK_DIR=/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/
export HOME=$WORK_DIR
export JUPYTER_RUNTIME_DIR=$(pwd)

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

```
(At this point, I logged out and logged back in to check that everything was still good. It was probably unnescessary.)   

#### Copy dataset files to my work directory ($WORK_DIR location)  

**Step 7:** ensuring I was in $WORK_DIR location, I copied over the contents of the `shared/plankton_project` folder: `cp -r ../shared/plankton_project/ .` I got quite a few permissions errors from this, and the `plankton_project/images/` folder didn't seem to have copied across at all, but that's the one that contains a folder called `Libraries_dismembered3`, and I figured it might not be immediately neccessary? I'm not sure but it might say in the README I haven't read yet...  
```console
(base) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ cp -r ../shared/plankton_project/ .
cp: cannot open '../shared/plankton_project/jupyter_cookie_secret' for reading: Permission denied
cp: cannot open '../shared/plankton_project/.ipynb_checkpoints/jpserver-643631-checkpoint.json' for reading: Permission denied
cp: cannot open '../shared/plankton_project/.ipynb_checkpoints/jupyter_cookie_secret-checkpoint' for reading: Permission denied
cp: cannot open '../shared/plankton_project/.ipynb_checkpoints/kernel-0a77b523-640e-47c3-9699-06acd3338206-checkpoint.json' for reading: Permission denied
cp: cannot open '../shared/plankton_project/CPRNet/code/notebook_cookie_secret' for reading: Permission denied
```

#### Create & activate conda environment from the pytnite.yaml file

**Step 8:** I created a conda environment from the `pytnite.yaml` file now in my $WORK_DIR:  
```console
# check location of pytnite.yaml file:
(base) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ ls
condaenvs  miniconda3  Miniconda3-latest-Linux-x86_64.sh  notebook_cookie_secret  pytnite.yaml

# check contents of pytnite.yaml file:
(base) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ cat pytnite.yaml 
name: pytnite
channels:
  - pytorch
  - anaconda
dependencies:
  - ignite
  - pytorch
  - torchvision
  - matplotlib
  - pandas
  - jupyter(base)

# create conda environment from yaml file (specifying file name using -f flag)
[flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ conda env create -f pytnite.yaml 
Collecting package metadata (repodata.json): done
Solving environment: done

Downloading and Extracting Packages
                                                                                                                                                        
Preparing transaction: done                                                                                                                             
Verifying transaction: done                                                                                                                             
Executing transaction: done                                                                                                                             
#                                                                                                                                                       
# To activate this environment, use                                                                                                                     
#                                                                                                                                                       
#     $ conda activate pytnite                                                                                                                          
#                                                                                                                                                       
# To deactivate an active environment, use                                                                                                              
#                                                                                                                                                       
#     $ conda deactivate                                                                                                                                

# activate new pytnite environment:                                                    
(base) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ conda activate pytnite

# check python version in newly activated conda environment:                                          
(pytnite) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ python --version                                                 
Python 3.10.9                                                                                               
```

#### Move into jupyter notebook location & reset JUPYTER_RUNTIME_DIR  

**Step 9:** I decided to move to the `code/` directory within `$WORK_DIR/plankton_project/CPRNet/` using the `cd` command, and export this as my JUPYTER_RUNTIME_DIR `export JUPYTER_RUNTIME_DIR=$(pwd)`, since this line is in my .bashrc file but I ran that from a different location previously and want to update my JUPYTER_RUNTIME_DIR location to the place that actually has the .ipynb files in it :)  

#### Run jupyter notebook command  

**Step 10:** I ran the jupyter notebook command: `jupyter notebook --ip=0.0.0.0 --no-browser` from the cirrus terminal, which gave the notebook URLs below: 
```console
(pytnite) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 code]$ jupyter notebook --ip=0.0.0.0 --no-browser
[I 16:38:07.273 NotebookApp] Writing notebook server cookie secret to /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/plankton_project/CPRNet/code/notebook_cookie_secret
[W 16:38:08.393 NotebookApp] Loading JupyterLab as a classic notebook (v6) extension.
[W 2023-07-13 16:38:08.399 LabApp] 'ip' has moved from NotebookApp to ServerApp. This config will be passed to ServerApp. Be sure to update your config before our next release.
[W 2023-07-13 16:38:08.399 LabApp] 'ip' has moved from NotebookApp to ServerApp. This config will be passed to ServerApp. Be sure to update your config before our next release.
[W 2023-07-13 16:38:08.399 LabApp] 'ip' has moved from NotebookApp to ServerApp. This config will be passed to ServerApp. Be sure to update your config before our next release.
[I 2023-07-13 16:38:08.404 LabApp] JupyterLab extension loaded from /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite/lib/python3.10/site-packages/jupyterlab
[I 2023-07-13 16:38:08.404 LabApp] JupyterLab application directory is /mnt/lustre/indy2lfs/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite/share/jupyter/lab
[I 16:38:08.409 NotebookApp] Serving notebooks from local directory: /mnt/lustre/indy2lfs/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/plankton_project/CPRNet/code
[I 16:38:08.409 NotebookApp] Jupyter Notebook 6.5.2 is running at:
[I 16:38:08.409 NotebookApp] http://cirrus-login1:8888/?token=d55a7ee6ac78a1cda5eae43d3b458b2ec3cdbfad5244dc5a
[I 16:38:08.409 NotebookApp]  or http://127.0.0.1:8888/?token=d55a7ee6ac78a1cda5eae43d3b458b2ec3cdbfad5244dc5a
[I 16:38:08.409 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 16:38:08.417 NotebookApp] 
    
    To access the notebook, open this file in a browser:
        file:///work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/plankton_project/CPRNet/code/nbserver-457643-open.html
    Or copy and paste one of these URLs:
        http://cirrus-login1:8888/?token=d55a7ee6ac78a1cda5eae43d3b458b2ec3cdbfad5244dc5a
```  
I then created another local terminal since I'm using Linux (Ubuntu 22.04 LTS) using `ssh flic-epcc-laptop-cirrus-m23ss@login.cirrus.ac.uk -L8888:cirrus-login1:8888`, which asked me for password etc.  

**Step 11:** I then went to the browser specified in the URL and opened the `PyTorch.ipynb` notebook.  

**Step 12:** I tried running the first code cell (`import torch` etc), and got an error: `ModuleNotFoundError: No module named 'torch'`  

#### Fixing `ModuleNotFoundError: No module named 'torch'` error  

**Step 13:** I logged out of the ipynb in the browser using the logout button, and then closed the local terminal. I also ran Ctrl + C to stop the jupyter notebook command, and typed 'y' to end the process. Once that was done, I ran `conda list` to check whether the `pytorch` module had definitely been installed to the conda environment I created and had activated.  It was there, despite it being called `pytorch` instead of `torch` as the python notebook code imports.  This actually doesn't seem to matter tbh.
```console
(pytnite) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 flic-epcc-laptop-cirrus-m23ss]$ conda list
# packages in environment at /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                        main    anaconda
_openmp_mutex             5.1                       1_gnu    anaconda
anyio                     3.5.0           py310h06a4308_0    anaconda
argon2-cffi               21.3.0             pyhd3eb1b0_0    anaconda
argon2-cffi-bindings      21.2.0          py310h7f8727e_0    anaconda
asttokens                 2.0.5              pyhd3eb1b0_0    anaconda
attrs                     22.1.0          py310h06a4308_0    anaconda
babel                     2.11.0          py310h06a4308_0    anaconda
backcall                  0.2.0              pyhd3eb1b0_0    anaconda
beautifulsoup4            4.11.1          py310h06a4308_0    anaconda
blas                      1.0                         mkl    anaconda
bleach                    4.1.0              pyhd3eb1b0_0    anaconda
bottleneck                1.3.5           py310ha9d4c09_0    anaconda
brotli                    1.0.9                h5eee18b_7    anaconda
brotli-bin                1.0.9                h5eee18b_7    anaconda
brotlipy                  0.7.0           py310h7f8727e_1002    anaconda
bzip2                     1.0.8                h7b6447c_0    anaconda
ca-certificates           2023.01.10           h06a4308_0    anaconda
certifi                   2022.12.7       py310h06a4308_0    anaconda
cffi                      1.15.1          py310h5eee18b_3    anaconda
charset-normalizer        2.0.4              pyhd3eb1b0_0    anaconda
comm                      0.1.2           py310h06a4308_0    anaconda
contourpy                 1.0.5           py310hdb19cb5_0    anaconda
cryptography              38.0.4          py310h9ce1e76_0    anaconda
cycler                    0.11.0             pyhd3eb1b0_0    anaconda
dbus                      1.13.18              hb2f20db_0    anaconda
debugpy                   1.5.1           py310h295c915_0    anaconda
decorator                 5.1.1              pyhd3eb1b0_0    anaconda
defusedxml                0.7.1              pyhd3eb1b0_0    anaconda
entrypoints               0.4             py310h06a4308_0    anaconda
executing                 0.8.3              pyhd3eb1b0_0    anaconda
expat                     2.4.9                h6a678d5_0    anaconda
ffmpeg                    4.3                  hf484d3e_0    pytorch
filelock                  3.9.0           py310h06a4308_0    anaconda
flit-core                 3.6.0              pyhd3eb1b0_0    anaconda
fontconfig                2.14.1               h52c9d5c_1    anaconda
fonttools                 4.25.0             pyhd3eb1b0_0    anaconda
freetype                  2.12.1               h4a9f257_0    anaconda
giflib                    5.2.1                h5eee18b_1    anaconda
glib                      2.69.1               he621ea3_2    anaconda
gmp                       6.2.1                h295c915_3    anaconda
gmpy2                     2.1.2           py310heeb90bb_0    anaconda
gnutls                    3.6.15               he1e5248_0    anaconda
gst-plugins-base          1.14.0               h8213a91_2    anaconda
gstreamer                 1.14.0               h28cd5cc_2    anaconda
icu                       58.2                 he6710b0_3    anaconda
idna                      3.4             py310h06a4308_0    anaconda
ignite                    0.4.12                     py_0    pytorch
intel-openmp              2021.4.0          h06a4308_3561    anaconda
ipykernel                 6.19.2          py310h2f386ee_0    anaconda
ipython                   8.8.0           py310h06a4308_0    anaconda
ipython_genutils          0.2.0              pyhd3eb1b0_1    anaconda
ipywidgets                7.6.5              pyhd3eb1b0_1    anaconda
jedi                      0.18.1          py310h06a4308_1    anaconda
jinja2                    3.1.2           py310h06a4308_0    anaconda
jpeg                      9e                   h7f8727e_0    anaconda
json5                     0.9.6              pyhd3eb1b0_0    anaconda
jsonschema                4.16.0          py310h06a4308_0    anaconda
jupyter                   1.0.0           py310h06a4308_8    anaconda
jupyter_client            7.4.8           py310h06a4308_0    anaconda
jupyter_console           6.4.4           py310h06a4308_0    anaconda
jupyter_core              5.1.1           py310h06a4308_0    anaconda
jupyter_server            1.23.4          py310h06a4308_0    anaconda
jupyterlab                3.5.3           py310h06a4308_0    anaconda
jupyterlab_pygments       0.1.2                      py_0    anaconda
jupyterlab_server         2.16.5          py310h06a4308_0    anaconda
jupyterlab_widgets        1.0.0              pyhd3eb1b0_1    anaconda
kiwisolver                1.4.4           py310h6a678d5_0    anaconda
krb5                      1.19.4               h568e23c_0    anaconda
lame                      3.100                h7b6447c_0    anaconda
lcms2                     2.12                 h3be6417_0    anaconda
ld_impl_linux-64          2.38                 h1181459_1    anaconda
lerc                      3.0                  h295c915_0    anaconda
libbrotlicommon           1.0.9                h5eee18b_7    anaconda
libbrotlidec              1.0.9                h5eee18b_7    anaconda
libbrotlienc              1.0.9                h5eee18b_7    anaconda
libclang                  10.0.1          default_hb85057a_2    anaconda
libdeflate                1.8                  h7f8727e_5    anaconda
libedit                   3.1.20221030         h5eee18b_0    anaconda
libevent                  2.1.12               h8f2d780_0    anaconda
libffi                    3.4.2                h6a678d5_6    anaconda
libgcc-ng                 11.2.0               h1234567_1    anaconda
libgomp                   11.2.0               h1234567_1    anaconda
libiconv                  1.16                 h7f8727e_2    anaconda
libidn2                   2.3.2                h7f8727e_0    anaconda
libllvm10                 10.0.1               hbcb73fb_5    anaconda
libpng                    1.6.37               hbc83047_0    anaconda
libpq                     12.9                 h16c4e8d_3    anaconda
libsodium                 1.0.18               h7b6447c_0    anaconda
libstdcxx-ng              11.2.0               h1234567_1    anaconda
libtasn1                  4.16.0               h27cfd23_0    anaconda
libtiff                   4.5.0                h6a678d5_1    anaconda
libunistring              0.9.10               h27cfd23_0    anaconda
libuuid                   1.41.5               h5eee18b_0    anaconda
libwebp                   1.2.4                h11a3e52_0    anaconda
libwebp-base              1.2.4                h5eee18b_0    anaconda
libxcb                    1.15                 h7f8727e_0    anaconda
libxkbcommon              1.0.1                hfa300c1_0    anaconda
libxml2                   2.9.14               h74e7548_0    anaconda
libxslt                   1.1.35               h4e12654_0    anaconda
lxml                      4.9.1           py310h1edc446_0    anaconda
lz4-c                     1.9.4                h6a678d5_0    anaconda
markupsafe                2.1.1           py310h7f8727e_0    anaconda
matplotlib                3.6.2           py310h06a4308_0    anaconda
matplotlib-base           3.6.2           py310h945d387_0    anaconda
matplotlib-inline         0.1.6           py310h06a4308_0    anaconda
mistune                   0.8.4           py310h7f8727e_1000    anaconda
mkl                       2021.4.0           h06a4308_640    anaconda
mkl-service               2.4.0           py310h7f8727e_0    anaconda
mkl_fft                   1.3.1           py310hd6ae3a3_0    anaconda
mkl_random                1.2.2           py310h00e6091_0    anaconda
mpc                       1.1.0                h10f8cd9_1    anaconda
mpfr                      4.0.2                hb69a4c5_1    anaconda
mpmath                    1.2.1                    pypi_0    pypi
munkres                   1.1.4                      py_0    anaconda
nbclassic                 0.4.8           py310h06a4308_0    anaconda
nbclient                  0.5.13          py310h06a4308_0    anaconda
nbconvert                 6.5.4           py310h06a4308_0    anaconda
nbformat                  5.7.0           py310h06a4308_0    anaconda
ncurses                   6.4                  h6a678d5_0    anaconda
nest-asyncio              1.5.6           py310h06a4308_0    anaconda
nettle                    3.7.3                hbbd107a_1    anaconda
networkx                  2.8.4           py310h06a4308_0    anaconda
notebook                  6.5.2           py310h06a4308_0    anaconda
notebook-shim             0.2.2           py310h06a4308_0    anaconda
nspr                      4.33                 h295c915_0    anaconda
nss                       3.74                 h0370c37_0    anaconda
numexpr                   2.8.4           py310h8879344_0    anaconda
numpy                     1.23.5          py310hd5efca6_0    anaconda
numpy-base                1.23.5          py310h8e6c178_0    anaconda
openh264                  2.1.1                h4ff587b_0    anaconda
openssl                   1.1.1s               h7f8727e_0    anaconda
packaging                 22.0            py310h06a4308_0    anaconda
pandas                    1.5.2           py310h1128e8f_0    anaconda
pandocfilters             1.5.0              pyhd3eb1b0_0    anaconda
parso                     0.8.3              pyhd3eb1b0_0    anaconda
pcre                      8.45                 h295c915_0    anaconda
pexpect                   4.8.0              pyhd3eb1b0_3    anaconda
pickleshare               0.7.5           pyhd3eb1b0_1003    anaconda
pillow                    9.3.0           py310h6a678d5_2    anaconda
pip                       22.3.1          py310h06a4308_0    anaconda
platformdirs              2.5.2           py310h06a4308_0    anaconda
ply                       3.11            py310h06a4308_0    anaconda
prometheus_client         0.14.1          py310h06a4308_0    anaconda
prompt-toolkit            3.0.20             pyhd3eb1b0_0    anaconda
prompt_toolkit            3.0.20               hd3eb1b0_0    anaconda
psutil                    5.9.0           py310h5eee18b_0    anaconda
ptyprocess                0.7.0              pyhd3eb1b0_2    anaconda
pure_eval                 0.2.2              pyhd3eb1b0_0    anaconda
pycparser                 2.21               pyhd3eb1b0_0    anaconda
pygments                  2.11.2             pyhd3eb1b0_0    anaconda
pyopenssl                 22.0.0             pyhd3eb1b0_0    anaconda
pyparsing                 3.0.9           py310h06a4308_0    anaconda
pyqt                      5.15.7          py310h6a678d5_1    anaconda
pyqt5-sip                 12.11.0                  pypi_0    pypi
pyrsistent                0.18.0          py310h7f8727e_0    anaconda
pysocks                   1.7.1           py310h06a4308_0    anaconda
python                    3.10.9               h7a1cb2a_0    anaconda
python-dateutil           2.8.2              pyhd3eb1b0_0    anaconda
python-fastjsonschema     2.16.2          py310h06a4308_0    anaconda
pytorch                   2.0.1              py3.10_cpu_0    pytorch
pytorch-mutex             1.0                         cpu    pytorch
pytz                      2022.7          py310h06a4308_0    anaconda
pyzmq                     23.2.0          py310h6a678d5_0    anaconda
qt-main                   5.15.2               h327a75a_7    anaconda
qt-webengine              5.15.9               hd2b0992_4    anaconda
qtconsole                 5.4.0           py310h06a4308_0    anaconda
qtpy                      2.2.0           py310h06a4308_0    anaconda
qtwebkit                  5.212                h4eab89a_4    anaconda
readline                  8.2                  h5eee18b_0    anaconda
requests                  2.28.1          py310h06a4308_0    anaconda
send2trash                1.8.0              pyhd3eb1b0_1    anaconda
setuptools                65.6.3          py310h06a4308_0    anaconda
sip                       6.6.2           py310h6a678d5_0    anaconda
six                       1.16.0             pyhd3eb1b0_1    anaconda
sniffio                   1.2.0           py310h06a4308_1    anaconda
soupsieve                 2.3.2.post1     py310h06a4308_0    anaconda
sqlite                    3.40.1               h5082296_0    anaconda
stack_data                0.2.0              pyhd3eb1b0_0    anaconda
sympy                     1.11.1          py310h06a4308_0    anaconda
terminado                 0.17.1          py310h06a4308_0    anaconda
tinycss2                  1.2.1           py310h06a4308_0    anaconda
tk                        8.6.12               h1ccaba5_0    anaconda
toml                      0.10.2             pyhd3eb1b0_0    anaconda
tomli                     2.0.1           py310h06a4308_0    anaconda
torchvision               0.15.2                py310_cpu    pytorch
tornado                   6.2             py310h5eee18b_0    anaconda
traitlets                 5.7.1           py310h06a4308_0    anaconda
typing-extensions         4.4.0           py310h06a4308_0    anaconda
typing_extensions         4.4.0           py310h06a4308_0    anaconda
tzdata                    2022a                hda174b7_0    anaconda
urllib3                   1.26.14         py310h06a4308_0    anaconda
wcwidth                   0.2.5              pyhd3eb1b0_0    anaconda
webencodings              0.5.1           py310h06a4308_1    anaconda
websocket-client          0.58.0          py310h06a4308_4    anaconda
wheel                     0.37.1             pyhd3eb1b0_0    anaconda
widgetsnbextension        3.5.2           py310h06a4308_0    anaconda
xz                        5.2.10               h5eee18b_1    anaconda
zeromq                    4.3.4                h2531618_0    anaconda
zlib                      1.2.13               h5eee18b_0    anaconda
zstd                      1.5.2                ha4553b6_0    anaconda
```
After googling about a bit, I found this page: https://www.educative.io/answers/how-to-resolve-modulenotfounderror-no-module-named-torch-error and after looking through some of the options (install the pytorch package again with pip?!?), I figured I'd try the section 'Verify the Path': "If PyTorch is installed in a non-standard location, then add the path to your environment."  That sounded reasonable, so I figured I'd add that code to the ipynb as a new code cell above the import section. They had given the code as follows: 
```python
import sys
sys.path.append('/path/to/pytorch')
```
But obviously I needed to know which the path was, which I knew after running `conda list` - the first comment says: `# packages in environment at /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite:` 
So I edited the code in my new cell to the following: 
```python
import sys
sys.path.append('/work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite/pytorch')
```
After trying this, I got a different error, saying module `tdqm` not found. This was good news, since it means that the system found pytorch and moved on to a different part of the code.  

#### Fixing `ModuleNotFoundError: No module named 'tqdm'` error  

**Step 14:** I closed the notebook safely again, and re-ran `conda list` to check whether `tqdm` package was installed. It wasn't, so I checked google for the best conda install command for it (`conda install -c conda-forge tqdm`) and ran that: 
```
(pytnite) [flic-epcc-laptop-cirrus-m23ss@cirrus-login1 code]$ conda install -c conda-forge tqdm
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /work/m23ss/m23ss/flic-epcc-laptop-cirrus-m23ss/miniconda3/envs/pytnite

  added / updated specs:
    - tqdm


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    ca-certificates-2023.5.7   |       hbcca054_0         145 KB  conda-forge
    certifi-2023.5.7           |     pyhd8ed1ab_0         149 KB  conda-forge
    colorama-0.4.6             |     pyhd8ed1ab_0          25 KB  conda-forge
    openssl-1.1.1u             |       h7f8727e_0         3.7 MB
    tqdm-4.65.0                |     pyhd8ed1ab_1          86 KB  conda-forge
    ------------------------------------------------------------
                                           Total:         4.1 MB

The following NEW packages will be INSTALLED:

  colorama           conda-forge/noarch::colorama-0.4.6-pyhd8ed1ab_0 
  tqdm               conda-forge/noarch::tqdm-4.65.0-pyhd8ed1ab_1 

The following packages will be UPDATED:

  ca-certificates    anaconda::ca-certificates-2023.01.10-~ --> conda-forge::ca-certificates-2023.5.7-hbcca054_0 
  certifi            anaconda/linux-64::certifi-2022.12.7-~ --> conda-forge/noarch::certifi-2023.5.7-pyhd8ed1ab_0 
  openssl               anaconda::openssl-1.1.1s-h7f8727e_0 --> pkgs/main::openssl-1.1.1u-h7f8727e_0 


Proceed ([y]/n)? y


Downloading and Extracting Packages
                                                                                                                                                        
Preparing transaction: done                                                                                                                             
Verifying transaction: done                                                                                                                             
Executing transaction: done                    
```

#### Everything works!

**Step 15:** I then re-ran the jupyter notebook command again, and I'm now able to run the full import block without errors. Yay!!



#### **IMPORTANT CAVEATS!:**

💀💀💀 EVERY TIME I LOG INTO CIRRUS, I NEED TO RUN `source .bashrc`!!!! THIS IS REALLY IMPORTANT. Apparently I will need to also run it again if I shift to a compute node, and I'm not certain whether I'd need to re-run it if I shift to an interactive node or not. But it wouldn't do any harm ;D  

💀💀: EVERY TIME I WANT TO RUN JUPYTER NOTEBOOKS, I need to run `conda activate pytnite` to activate the correct environment so all the right python modules are loaded, and I need to move to my `$WORK_DIR/plankton_project/CPRNet/code` folder and run `export JUPYTER_RUNTIME_DIR=$(pwd)` BEFORE I run the `jupyter notebook --ip=0.0.0.0 --no-browser` command order to be able to see and run the jupyter notebooks in that folder.  

💀 this is run via a login node, so I haven't tested it from an interactive or compute node yet. 
💀 I'm running linux ubuntu locally, your keyboard commands and cirrus commands may vary if your system is different  
