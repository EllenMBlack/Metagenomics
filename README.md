# Bioinformatics used for shotgun sequencing data analysis
# Ellen notes for contig matches using blastX with diamond
Notes for running diamond on uiowa HPC:
  1.	Open SecureCRT
    a.	Enter passcode
    b.	Connect to ssh node 22 (40 off campus); neon.hpc.uiowa.edu
    c.	Use hawk id and password
  2.	Transfer files to use on Neon
    a.	Ipswitch allows easy transfer between local computer and neon account
  3.	Compute node:
    a.	qlogin –q all.q
      i.	Or other node. Type "whichq" to view your accessible compute nodes 
      ii.	“qstat –g c” to view available nodes
      iii.	“qstat –u ‘username’” to see which compute nodes you’re logged into. i.e. qstat –u emblack
      iv.	If already logged into compute node, begin computing by typing “ssh ‘name of compute node’”. Ex ssh neon-compute-3-18.local
      v.	If running a script where you would lose data if kicked off, you must run on UI compute nodes.
  4.	Submit command line scripts using qsub in a Sun Grid Engine system Uiowa HPC wiki page is helpful for setting up jobs
    a.	Make job file in Jedit program and transfer to HPC
   # Example of  job file named “diamond_script1.job” saved in /Users/emblack/diamond directory:

          #!/bin/sh
          #script to run parallel blastx with diamond
          /Users/emblack/diamond/blastx_script1
          #$ -q FISH

  # Repeat for each fasta file. i.e.  Make file named “diamond_script2.job”:

          #!/bin/sh
          #script to run parallel blastx with diamond
          /Users/emblack/diamond/blastx_script2
          #$ -q FISH


   # Make script in Jedit and transfer to HPC
   # Example of script named “blastx_script1”  saved in path defined in part (a) (/Users/emblack/diamond):
          PATH=$PATH:$HOME/bin:$HOME/diamond
          diamond blastx -d /Users/emblack/diamond/nr -q /Users/emblack/Shotgun_metagenomics/Sample1/S1contigs.fasta -a /Users/emblack/diamond/matches1 -t /Users/emblack/diamond/temp

   # Repeat for each fasta file i.e. Make file named “blastx_script2”:
          PATH=$PATH:$HOME/bin:$HOME/diamond
          diamond blastx -d /Users/emblack/diamond/nr -q /Users/emblack/Shotgun_metagenomics/Sample2/S2contigs.fasta -a /Users/emblack/diamond/matches2 -t /Users/emblack/diamond/temp

   # Submit each job to the queue with the following command:	   
          qsub /Users/emblack/diamond/diamond_script1.job -cwd /Users/emblack/diamond -e /Users/emblack/diamond/error -o /Users/emblack/diamond/output -M ellen-black@uiowa.edu -m e –N S1 –S bash

   # when setting the shell via “-S” you can check to see which shell you are currently using by typing “echo $0”

   # Repeat for each fasta file. i.e. submit script2:

          qsub /Users/emblack/diamond/diamond_script2.job -cwd /Users/emblack/diamond -e /Users/emblack/diamond/error -o /Users/emblack/diamond/output -M ellen-black@uiowa.edu -m e –N S2 –S bash

   # Check on job status via HPC login node:
        qstat –u emblack

   # see HPC wiki for description of qsub options https://wiki.uiowa.edu/display/hpcdocs/Basic+Job+Submission
