# ensable_pacbio
##Index para PacBio versión 2.1

#PBS -N index
#PBS -l nodes=1:ppn=10,vmem=64G
#PBS -q ensam
#PBS -V

#Load modules
module load hisat2/2.0.4
#Change to working directory
hisat2-build -p 10 -f /LUSTRE/usuario/jmvilla/TrichoNet/genoma_pacbio/Trichoderm
a_atroviride_IMI_206040_v2.1.fasta /LUSTRE/usuario/jmvilla/TrichoNet/Index_pacbi
o/pacbiov2.1


# mapeo usando Hisat2

#qsub parameters
#rameters
ppn=8
qsubParams="-V -q default -l nodes=1:ppn=$ppn,vmem=30g -N hisat2"

#Working directory and change to there
RNAseqDir="/LUSTRE/usuario/jmvilla/RNAseq_total/scripts"
cd ${RNAseqDir}

#In/output folder
trimmDir="/LUSTRE/usuario/jmvilla/TrichoNet/RNAseq_todas/Fibo"
hisat2Out="/LUSTRE/usuario/jmvilla/TrichoNet/mapeo_pacbioV21/"

#HISAT2 params
hisatParams="-q -p $ppn --dta"

#Modules
loadMod="module load samtools/1.3.1; module load hisat2/2.0.4"

genome="/LUSTRE/usuario/jmvilla/TrichoNet/Index_pacbio/pacbiov2.1"

for file in $(ls ${trimmDir}/*fastq.gz); do
filename=$(basename $file)
sampName=${filename%.fastq.gz}

inputFiles="-U ${file}"
libType=""

cmd="hisat2 $libType $hisatParams \
-x $genome \
$inputFiles \
-S ${hisat2Out}/${sampName}.sam >& ${hisat2Out}/${sampName}.log;


samtools view -Su ${hisat2Out}/${sampName}.sam \
| samtools sort - -o ${hisat2Out}/${sampName}.sorted.bam;
rm ${hisat2Out}/${sampName}.sam"

echo "$loadMod; $cmd" | qsub $qsubParams

#break

done

# tanto para el genoma de referencia del JGI como para el de PacBio se usando los mismos parametros de mapeo.


## Resultado de comparación del mapeo usando ultimo ensamble de pacbio y JGI.


# Resultado en porcentaje de mapeo total:


![Diapositiva1](https://user-images.githubusercontent.com/22058504/78101390-9e8b9500-73a4-11ea-8606-fcdf4cd19f95.jpeg)


# Resultado en porcentaje de lecturas que mapean a una sola región:


![Diapositiva1](https://user-images.githubusercontent.com/22058504/78094747-8ad73300-7392-11ea-9a5f-9be6834bfa37.jpeg)
