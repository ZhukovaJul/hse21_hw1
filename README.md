# hse21_hw1
<h3> 1. Подготовительный шаг. Создание папки для дз </h3> 
mkdir hm_1
cd hm_1

<h3> 2. Выбор случайных 5 млн. paired-end чтений и 1.5 млн. mate-pairs чтений </h3> 
seqtk sample -s1227 oil_R1.fastq 5000000 > R1_pe.fastq <br>
seqtk sample -s1227 oil_R2.fastq 5000000 > R2_pe.fastq  <br>
seqtk sample -s1227 oilMP_S4_L001_R1_001.fastq 1500000 > R1_mp.fastq  <br>
seqtk sample -s1227 oilMP_S4_L001_R2_001.fastq 1500000 > R2_mp.fastq  <br>

<h3> 3. Оценка качества исходных чтений с помощью fastQC и multiQC</h3> 
mkdir fastqc  <br>
ls R* | xargs -P 4 -tI{} fastqc -o fastqc {}

multiqc -o multiqc fastqc

<h3> 4. Подрезка чтения по качеству и удаление праймеров </h3>
platanus_trim R1_pe.fastq R2_pe.fastq <br>
platanus_internal_trim R1_mp.fastq R2_mp.fastq

<h3> 5. Оценка качества подрезанных чтений с помощью fastQC и multiQC </h3>
mkdir fastqc1  <br>
ls *.trimmed | xargs -P 4 -tI{} fastqc1 -o fastq1c {}  <br>
multiqc -o multiqc1 fastqc1 <br>
ls *.int_trimmed | xargs -P 4 -tI{} fastqc -o fastqc1 {} <br>
multiqc -o multiqc1 fastqc1

<h3> 6. Континги </h3>
time platanus assemble -o Poil -t 1 -m 8 -f *.trimmed 2>assemble

<h3> 7. Скаффолды </h3>
time platanus scaffold -o Poil -t 1 -c Poil_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed 2>scaffold

<h3> 8. Уменьшение кол-во гэпов с помощью подрезанных чтений </h3>
platanus gap_close -o Poil -t 1 -c Poil_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed 2>gapclose.log

https://colab.research.google.com/drive/1bZFLrpRKEhWo1c6NP7zVPDrXHAhCMil3?usp=sharing
