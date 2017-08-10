## Input format and conversion

Input is eigenstrat format. If you need to go from plink to eignestrat (file.ped file.map):
`i=file`

`cut -f1-6 -d" " "$i".ped > "$i".pedind`

`cp "$i".map "$i".pedsnp`

`echo -e "genotypename: $i.ped\nsnpname: $i.pedsnp\nindivname: $i.pedind\noutputformat: EIGENSTRAT\ngenooutfilename: $i.eigenstratgeno\nsnpoutfilename: $i.snp\nindoutfilename: $i.ind" > "$i".par`

`convertf -p "$i".par > conversion.log`

`sed 's/:/ /' "$i".ind  |  awk '{print $2" "$3" "$1}' > tmp; mv tmp "$i".ind `
