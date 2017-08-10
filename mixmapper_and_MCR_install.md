http://cb.csail.mit.edu/cb/mixmapper/

Download both mixmapper and the mixmapper MCR distribution (we use this as we don't have MATLAB).

## Download and install MATLAB Composite Runtime
As per  the readme in the MCR distribution:

`wget www.mathworks.com/supportfiles/MCR_Runtime/R2012a/MCR_R2012a_glnxa64_installer.zip`

uncomment lines of installer_input.txt:

destinationFolder=[INSTALL_PATH eg first mkdir /usr/local/MCR; then set destinationFolder=/usr/local/MCR]

agreeToLicense=yes

mode=silent
 
`./install -inputFile installer_input.txt`

This has installed MCR. We will refer to the it when we run mixmapper.

## Install mixmapper
Mixmapper now needs to be install. The mixmapper MCR will be used to run the final step and does not need installation.

`tar -xzf MixMapper_v2.0.tar.gz

cd MixMapper_v2.0/

g++ -O2 compute_moment_stats.cpp -o compute_moment_stats

g++ -O2 compute_most_additive_trees.cpp -o compute_most_additive_trees`

