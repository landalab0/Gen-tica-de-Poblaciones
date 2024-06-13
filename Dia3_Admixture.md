# Correr admixture

*Correr admixture puede representar un ligero problema con la codificación del nombre de cada cromosoma cuando la identificación de SNPs se realiza con el genoma de referencia. Por ello es importante. Para evitar ese problema hay múltiples maneras:*

* 1. La primera que se me ocurre es que editen el fasta del genoma de referencia antes de hacer el llamado de SNPs. De esta manera el vcf de datos crudos ya tendrá asignados los nombres de cada cromosoma con número. Ojo, recuerda que muchos programas solo aceptan la numeración hasta 23, por ello mi recomendación es que el cromosoma 24 lo llames 124 y asi sucesivamente.

*  2.- Si tienes ya un vcf como el que te dieron en este curso, podemos cambiar el nombre de los cromosomas usando awk. Este comando  es una herramienta de procesamiento de texto poderosa y versátil que se utiliza en sistemas Unix y Linux. Se usa principalmente para procesar patrones en archivos de texto o en la salida de comandos.

### En nuestro ejercicio en clase se tiene que hacer lo siguiente:

**1.Cambiar los nombres de los cromosomas del archivo**
*      moschata_minDP5_geno_50_mind_50_MAF0.05_HWE_LD_free.plink.vcf ejecutando el script 
*     $ ./Chr-rename.sh

**2. llamar plink**
*     $ conda activate plink

**3. ejecutar plink para crear un bed** 
*     $ plink --vcf calabazas_admixture.vcf --make-bed --out calabazas_admixture --allow-extra-chr

**4. Ejecutar el script admixture**
*     $ ./admixture.sh

