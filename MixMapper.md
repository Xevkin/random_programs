## Input format and plink>eigenstrat conversion

Input is eigenstrat format. 

If you need to go from plink to eignestrat (dataset.ped dataset.map):

`i=dataset`

`cut -f1-6 -d" " "$i".ped > "$i".pedind`

`cp "$i".map "$i".pedsnp`

`echo -e "genotypename: $i.ped\nsnpname: $i.pedsnp\nindivname: $i.pedind\noutputformat: EIGENSTRAT\ngenooutfilename: $i.eigenstratgeno\nsnpoutfilename: $i.snp\nindoutfilename: $i.ind" > "$i".par`

`convertf -p "$i".par > conversion.log`

`sed 's/:/ /' "$i".ind  |  awk '{print $2" "$3" "$1}' > tmp; mv tmp "$i".ind `

Edit the dataset.ind so the populations (third column) are correct/what you want to use.

## Preliminary steps - moment stat calculation

The first step does f3s to determine which pops are admixed.

`<path to MixMapper folder>/compute_moment_stats "$i".ind "$i".snp "$i".eigenstratgeno "$i" n 20 50 > "$i"_stdout.txt 2> "$i"_stderr.txt`

Remove the “number of snps“ lines from the output. This shows which pops did not give negative f3s. 

`grep -v "number" "$i"_stdout.txt > tmp; mv tmp "$i"_stdout.txt; more "$i"_stdout.txt`

f3s may miss admixture, so you can manually remove from stdout.txt that you know/believe to be admixed.

## Compute most additive trees

We create NJ trees using subsets of the unadmixed pops IDed above.

We also specify in required_scaffold_pops.txt the pops we want to include in each subset i.e. Auroch pops, Neolithic pops.

`<path to MixMapper folder>/compute_most_additive_trees "$i".f2.tab 10000 "$i"_stdout.txt  required_scaffold_pops.txt > most_additive_trees.txt`

Ideally we chose the top output of this step (smallest number of pops to use for the scaffold/unadmixed tree, minimum error.

However the example/readme in the MixMapper folder encourages some decisions on the user's part at this stage.

Using more pops in the scaffold increases error, but gives better geographical coverage of the three.

It's possible to get different scaffold trees with very similar error i.e. both close to 0.

They recommend you try a few different scaffolds, to see if results are sensitive to scaffold choice.

## run MixMapper using MixMapper MCR distribution

We edit the <MixMapper MCR folder path>/cmd.txt to define input files and analyses. Modify the following lines to something similar:

`pop_data_reps = read_MixMapper_input('/home/kdaly/mixmapper/high-coverage');`

`scaffold_pop_names = {'EPI_TURK' 'NEO_IRAN' 'NEO_SERB'};`

`[trees,fits] = MixMapper(pop_data_reps(1:4),scaffold_pop_names,'BRNZ_TURK','',options);`

`[trees,fits] = MixMapper(pop_data_reps(1:4),scaffold_pop_names,'MOROCCN_MOD','',options);`



`<path to MixMapper MCR folder>/run_MixMapper_wrapper.sh <MATLAB MCR path>/v717/ <path to MixMapper MCR folder>/cmd.txt > out.txt`

`grep -v "In\|running\|in\|trees" out.txt`



