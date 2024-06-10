# Genética de Poblaciones
*Curso intensivo de Genética de Poblaciones Verano 2024*
Durante nuestras prácticas, estaremos trabajando en el servidor del ICMyL (Chihuil) 
Ustedes estarán trabajando en el disco axolote del servidor, por lo que deben ser ajustadas las direcciones proporcionadas más adelante 

*Conectarse al servidor*
*     ssh popgen_20@132.248.15.30 -p 7915 -o ServerAliveInterval=60  

*Crear la carperta GP (carpeta que contendrá todos los archivos de trabajo del curso)*
*     mkdir GP
*     cd GP

*     scp -r /axolote/popgen_data/GP/00.rawdata .

*'Entrar a la carpeta 00.rawdata'*
*     cd 00.rawdata

Iniciamos con un total de 40 muestras, ubicadas en la carpeta nombrada 00.rawdata

Visualizar la calidad de los datos crudos: FastQC (solo lo haremos para la población Ccalk)
*     fastqc Ccalk*

En mi Escritorio: 
*Crear la carpeta GP*
Copiar los html del servidor a mi compu para poder visualizarlos (en la terminal de mi compu)
*     scp -r -P 7915 popgen_01@132.248.15.30:/axolote/popgen_20/GP/00.rawdata/*.html /home/Massi/Desktop/GP

Puntos claves a revisar en FastQC: Per base sequence content y Sequence Length Distribution


## 1- Limpieza: Trimmomatic

*Conociendo Trimmomatic*
http://www.usadellab.org/cms/?page=trimmomatic
 
**para cada secuencia por separado
*     java -jar /opt/Trimmomatic-0.39/trimmomatic-0.39.jar SE -phred33 -trimlog Ccalk_2triminfo.txt Ccalk_2.fq.gz Ccalk_2_trimm.fastq.gz ILLUMINACLIP:/opt/Trimmomatic-0.39/adapters/TruSeq3-SE:2:30:10 HEADCROP:7 CROP:170 LEADING:5 TRAILING:5 SLIDINGWINDOW:5:15 MINLEN:150

Correr fastQC para ir verificando los parámetros de limpieza utilizados
*     fastqc Ccalk_2_trimm.fastq.gz

Copiar el html del servidor a mi compu para poder visualizarlos (en la terminal de mi compu)
*     scp -r -P 7915 popgen_20@132.248.15.30:/axolote/popgen_20/GP/00.rawdata/Ccalk_2_trimm_fastqc.html /home/Massi/Desktop/GP
Se recuperan 130666 reads


## 3 equipos: probar distintos parámetros de Trimmomatic para la muestra **'Ccalk_2.fq.gz'**


Una vez seleccionados y verificados los parámetros en algunas muestras, la corrida se realiza para todo nuestro set de datos (NO LO HAREMOS AHORA):
Dentro de la carpeta GP, crear una nueva carpeta nombrada 02.trimmomatic. Esta carpeta va a contener los archivos fastq.gz ya limpios.
*     mkdir 02.trimmomatic

**todas las secuencias juntas (loop)
*     for i in GP/00.rawdata/*.gz; do
         filename=$(basename "$i")
         java -jar /opt/Trimmomatic-0.39/trimmomatic-0.39.jar SE -phred33 "$i" "GP/02.trimmomatic/trimm_$filename" HEADCROP:7 CROP:170 LEADING:5 TRAILING:5 SLIDINGWINDOW:5:15 MINLEN:150
      done
Ejemplo: $i=Ccalk_2.fastqc



## 2- Llamado de SNPs: ipyrad
Para correr **`ipyrad`** necesitamos activar el ambiente conda:
Posicionarnos en la carpeta de usuario /axolote/popgen_20
Con el comando **`ll -a`** veremos todos los archivos ocultos que están en nuestro usuario. En este momento el que nos interesa es el .bashrc
Abrimos el archivo de texto .bashrc
*     nano .bashrc
Y al final de ese archivo copiamos lo siguiente:

*     # >>> conda initialize >>>
      # !! Contents within this block are managed by 'conda init' !!
      __conda_setup="$('/opt/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
      if [ $? -eq 0 ]; then
      eval "$__conda_setup"
      else
          if [ -f "/opt/anaconda3/etc/profile.d/conda.sh" ]; then
              . "/opt/anaconda3/etc/profile.d/conda.sh"
          else
              export PATH="/opt/anaconda3/bin:$PATH"
          fi
      fi
      unset __conda_setup
      # <<< conda initialize <<<

**'Ctrl X, guardar'**

**Posteriormente, tecleamos lo siguiente en la terminal**
*      source .bashrc

y listo! Nuestro ambiente conda ya está habilitado y listo para correr **`ipyrad`**

*      conda activate ipyrad

 
Posicionarnos dentro de GP y crear la carpeta 04.ipyrad. Esta carpeta va a contener los resultados del llamado de SNPs con ipyrad.
*      mkdir 04.ipyrad
*      cd 04.ipyrad


Crear el archivo que empleará el programa: 
*      ipyrad -n U80M50
Visualizar el archivo creado: 
*      nano params-U80M50.txt
## Modificar el params:
 **[1] [project_dir]:** Project dir: Especificar directorio de los archivos de salida: /axolote/popgen_01/GP/04.ipyrad
 
 **[4] [sorted_fastq_path]:** Location of demultiplexed/sorted fastq files. /axolote/popgen_data/GP/02.trimmomatic/*fq.gz
 
 **[7] [datatype]:** Datatype (see docs): rad, gbs, ddrad, etc.: gbs
 
 **[8] [restriction_overhang]:** poner el sitio de corte de mis enzimas: AATTC, CTAGC, CTAGA (para EcoR1, Xba y NheI)
 
 **[14] [clust_threshold]:** Clustering threshold for de novo assembly: poner el umbral de similitud para la creación de clusters (en este ejemplo 0.80)
 
 **[21] [min_samples_locus]:** Min # samples per locus for output: depende del tamaño de muestra y cuanto missing data acaptaré 40----100
 
   x----50 (estoy aceptando un 50% de missing data)
   x=20 (mínimo número de muestras que debe tener un locus para que este sea retenido)
   
 **[27] [output_formats]:** Output formats (see docs): acá coloco * para que me genere todos los formatos posibles de outputs

Una vez modificador el params, procedemos a correr el programa en la carpeta donde está el archivo params.txt: 
*      ipyrad -p params-U80M50.txt -s 1234567 -c 4 -f --MPI  

Revisar los resultados de ipyrad 
*      U80M50_outfiles  U80M50_stats.txt
total de loci 1098
total de SNPs 4390


NOTA: Con umbral de similitud de 85% y con 30% de missing data obtuve 475 loci. Es muy poco por lo que probé parámetros más flexibles.
      Con umbral de similitud de 80% y con 50% de missing data obtuve 1098 loci.     
  

Nota adicional **(NO LO HAREMOS AHORA)**
*Si queremos descartar muestras de nuestro análisis (por ejemplo porque tengan pocos reads luego de los filtros aplicados), podemos crear un nuevo params y correr SOLAMENTE el último paso de ipyrad

*      ipyrad -p params-original.txt -b nuevo_params Ids_ipyrad.txt -c 24

Ids_ipyrad.txt: archivo.txt creado previamente que contiene solo el nombre de las muestras de interés para un determinado análisis

**Sobre el nuevo params podemos hacer modificaciones para otras pruebas, por ejemplo cambiar el % de missing data** 

Correr nuevamente el paso 7 de ipyrad, con el nuevo params:
*      ipyrad -p nuevo_params.txt -s 7 -c 4 --MPI
Revisar carpeta_outfiles  archivo_stats.txt para ver cómo se ha modificado el número de SNPs al quitar muestras

Desactivar ambiente conda
*      conda deactivate

##  3- Filtrados adicionales:    **`VCFtools`**   y  **`Plink`**
VCFtools: utiliza el archivo .vcf resultante de ipyrad
Posicionarnos en la carpeta donde está en .vcf (carpeta_outfiles generada por ipyrad)

Correr vcftools, definiendo el MAF 
*      vcftools --vcf U80M50.vcf --maf 0.02 --thin 1000 --min-alleles 2 --max-alleles 2 --recode --out U80M50_vcf0.02
   **`--maf minor allele frequency.`** El maf dependerá de mi tamaño de muestra. (en este ejemplo el alelo tiene que estar en una frecuencia mayor o igual al 2%)     
   **`--thin 1000`** puedo usarlo para eliminar loci ligados. Solo retiene un snp cada 1000 pb
   **`--min-alelos 2 --max-alelos 2`**: solo se quedan los sitios bialélicos 
   **`--recode`** genera un archivo.vcf 
   **`--plink`** genera un archivo.ped y un .map. (no puedo correr en una misma línea el --recode y el --plink)

   
*Repito la corrida pero cambiando la opción recode por plink*
*      vcftools --vcf U80.vcf --maf 0.02 --thin 1000 --min-alleles 2 --max-alleles 2 --plink --out U80M50_vcf0.02
-Visualizar el **`.log`** para ver cuantos sitios se mantienen, luego del filtrado
Después del filtrado en **'VCFtools'** , me quedé con 820 loci.  

Los 4 archivos los descargo del servidor a mi compu (.ped .map .log .vcf). Guardárlos en el Escritorio para ahí correr Plinkl.

*     scp -r -P 7915 malfonso@132.248.15.30:/botete/malfonso/GP/04.ipyrad/U80_outfiles/*.vcf /home/Massi/Desktop
      scp -r -P 7915 malfonso@132.248.15.30:/botete/malfonso/GP/04.ipyrad/U80_outfiles/*.log /home/Massi/Desktop
      scp -r -P 7915 malfonso@132.248.15.30:/botete/malfonso/GP/04.ipyrad/U80_outfiles/*.map /home/Massi/Desktop
      scp -r -P 7915 malfonso@132.248.15.30:/botete/malfonso/GP/04.ipyrad/U80_outfiles/*.ped /home/Massi/Desktop


**`Plink`**: utiliza el archivo 
-Correr Plink desde la terminal de mi compu (cmp, simbolo de sistema). Lo corro para evaluar loci en EHW y para obtener el archivo .bed que utiliza fastStructure
 Ubicarme en Desktop  
*     cd Desktop 
*     HWE plink2 --vcf U80M50_vcf0.02.recode.vcf --hardy --recode vcf --out U80M50_vcf0.02_HWE
 Esto crea un nuevo archivo .vcf conformado por los SNPs que pasaron el filtro de HW. 
 Subir el nuevo vcf a la carpeta U80M50_outfiles
*     scp -r -P 7915 /home/Massi/Desktop/U80M50_vcf0.02_HWE.vcf malfonso@132.248.15.30:/botete/malfonso/GP/04.ipyrad/U80M50_outfiles/

Ejecutar vcftools
Error: VCF version must be v4.0, v4.1 or v4.2:

El .vcf que se crea en Plink es versión 4.3, sin embargo, VCFtools trabaja con versiones inferiores. La versión 4.3 y 4.2 son muy similares por lo que solo debemos hacer un pequeño cambio en U80M50_vcf0.02.recode.vcf para que el programa lo lea.
Abrir el archivo  nano U80M50_vcf0.02.recode.vcf
Modificar la segunda línea ##fileformat=VCFv4.3  (cambiar 4.3 por 4.2) y listo! 

De esta forma, ya tenemos el vcf filtrado que será utilizado en los análisis posteriores
