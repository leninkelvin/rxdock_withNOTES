Environment

export RBT_ROOT='/home/kelvin/apps/rxdock'
PATH=$PATH":$RBT_ROOT/bin"


$ cd 1sj0/

#first create the cavity using rbcavity
$ rbcavity -r 1sj0_rdock.prm -W > 1sj0_cavity.log

#then use rbdock to run docking
$ rbdock -r 1sj0_rdock.prm -p dock.prm -n 100 -i 1sj0_ligand.sd \
-o 1sj0_docking_out > 1sj0_docking_out.log

#sdsort for sorting the results according to their score
$ sdsort -n -f'SCORE' 1sj0_docking_out.sd > 1sj0_docking_out_sorted.sd

#calculate rmsd from the output comparing with the crystal structure of the ligand
$ sdrmsd 1sj0_ligand.sd 1sj0_docking_out_sorted.sd

Multi ligand example

$ cd hivpr/

#first create the cavity using rbcavity
$ rbcavity -r hivpr_rdock.prm -W > hivpr_cavity.log

#uncompress ligand file
$ gunzip hivpr_ligprep.sdf.gz

sdsplit -20 -osplit input.sdf

#use rbdock to run docking
$ rbdock -r hivpr_rdock.prm -p dock.prm -n 100 -i hivpr_ligprep.sdf \
-o hivpr_docking_out > hivpr_docking_out.log

#sdsort with -n and -s flags will sort internally each ligand by increasing
#score and sdfilter will get only the first entry of each ligand
$ sdsort -n -s -fSCORE hivpr_docking_out.sd | sdfilter \
-f'$_COUNT == 1' > hivpr_1poseperlig.sd

#sdreport will print all the scores of the output in a tabular format and,
#with command awk, we will format the results
$ sdreport -t hivpr_1poseperlig.sd | awk '{print $2,$3,$4,$5,$6,$7}' > dataforR_uq.txt