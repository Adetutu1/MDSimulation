# MDSimulation
# The commandline for the extension of MDS runs, analysis, and generation of plots and pdf files on the CHPC cluster

# Commandline to extend simulation run on Desmond

module purge
module add chpc/schrodinger/2022-1
module add chpc/cuda/10.1
env SCHRODINGER_CUDA_VISIBLE_DEVICES="1"

$SCHRODINGER/desmond -JOBNAME desmond_md_job_ZINC000670449238_run3 -HOST 'gpu_1' -PROCS 1 -restore /mnt/lustre/users/aakinnuwesi/mdsimulation2/desmond_md_job_ZINC000670449238_run2/desmond_md_job_ZINC000670449238_run2.cpt -in /mnt/lustre/users/aakinnuwesi/mdsimulation2/desmond_md_job_ZINC000670449238/desmond_md_job_ZINC000670449238-in.cms -cfg mdsim.last_time=225000.0 -QARG "-P CBBI1154 -l walltime=12:00:00 -l select=1:ncpus=1:ngpus=1"

# Commandline to merge all the generated trajectories

$SCHRODINGER/run trj_merge.py desmond_md_job_ZINC000003798074-out.cms desmond_md_job_ZINC000003798074*_trj -o merged

# Commandline for protein analysis

$SCHRODINGER/run event_analysis.py analyze -prot "(protein)" -lig "auto" -out merged_7nln merged-out.cms

# Commandline to generate .eaf files
$SCHRODINGER/run analyze_simulation.py merged-out.cms merged_trj merged_ZINC000670449238-out.eaf merged_ZINC000670449238-in.eaf -HOST "gpu_1" -TMPLAUNCHDIR -QARG "-P CBBI1154 -l walltime=8:00:00 -l select=1:ncpus=1:ngpus=1" -JOBNAME "merge_analysis"

# Commandline	to generate plots and pdf files from the result analysis

mkdir data_run1
module purge
module load chpc/schrodinger/2022-1
$SCHRODINGER/run event_analysis.py report -pdf /mnt/lustre/users/aakinnuwesi/mdsimulation2/merge_ZINC000670449238/data_run1/merge_ZINC000670449238.pdf -data -plots -data_dir /mnt/lustre/users/aakinnuwesi/mdsimulation2/merge_ZINC000670449238/data_run1/ desmond_md_job_ZINC000670449238-out.eaf

