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

`pop_data_reps = read_MixMapper_input('/home/kdaly/mixmapper/dataset');`

This defines the input data. "Dataset" refers to what you used for $i above.

`scaffold_pop_names = {'EPI_TURK' 'NEO_IRAN' 'NEO_SERB'};`

This defines what scaffold you end up using for this run.

Delete the % in front of matlabpool open 4 to run bootstrap replicates in parallel.

`[trees,fits] = MixMapper(pop_data_reps(1:20),scaffold_pop_names,'BRNZ_TURK','',options);`

We define some of fitting we want to do. The above line will fit BRNZ_TURK as the product of admixture between two scaffold pops.

pop_data_reps(1:20) defines the number of bootstrap replicates.

You can also fit pops as admixtures between an admixed pop and a third pop:

`[trees,fits] = MixMapper(pop_data_reps(1:20),scaffold_pop_names,'BRNZ_TURK','MOROCCN_MOD',options);`

This would fit BRNZ_TURK as a mixture of two scaffold pops, then MOROCCN_MOD as mixture of BRNZ_TURK and a third scaffold pop.

You can also test if a two way admixture or three way admixture of a particular pop fits best by putting the following line before a three way fit e.g.

`options.test_2v3 = 1;`

`[trees,fits] = MixMapper(pop_data_reps(1:20),scaffold_pop_names,'BRNZ_TURK','MOROCCN_MOD',options);`

Now run MixMapper:

`<path to MixMapper MCR folder>/run_MixMapper_wrapper.sh <MATLAB MCR path>/v717/ <path to MixMapper MCR folder>/cmd.txt > out.txt`

`grep -v "In\|running\|in\|trees\|class\|option\|matl\|scaff" out.txt`

## MixMapper output
The output file will describe the results of fittings. A tree file of the scaffold will also be created which you can visualize using seaview. 

Not 100% about this but the output should be:

#: Number of replicates supporting admixture event.

Resnorm: residual error (smaller = better).

alpha: mixing proportion of Branch1 pop.

Branch1Loc: think this is where along Branch1 admixture occurs (see tree of scaffold in seaview).

MixedDrift: amount of drift in admixed pop following admixture.





