http://cb.csail.mit.edu/cb/mixmapper/

Download both mixmapper and the mixmapper MCR distribution (we use this as we don't have Mathlab).

As per  the readme in the MCR distribution:

`wget www.mathworks.com/supportfiles/MCR_Runtime/R2012a/MCR_R2012a_glnxa64_installer.zip`

uncomment lines of installer_input.txt:

destinationFolder=[INSTALL_PATH eg first mkdir /usr/local/MCR; then set destinationFolder=/usr/local/MCR]

agreeToLicense=yes

mode=silent
 
`./install -inputFile installer_input.txt`



