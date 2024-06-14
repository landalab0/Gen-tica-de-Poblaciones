# 06.Seleccion
Primero debemos copiar el archivo "moschata_minDP5_geno_50_mind_50_MAF0.05.plink.vcf" a tu nueva carpeta

**Activamos plink a través de Conda**
*     conda activate plink
**Convertimos moschata en un conjunto de datos BED**
*     plink --vcf moschata_minDP5_geno_50_mind_50_MAF0.05.plink.vcf --make-bed --out Cmos_selectivos.vcf --allow-extra-chr
**Extraemos informacion especifica de los archivos BIM**
*     cut  -f1,2,4,5,6 *bim > loci_info.txt

**En Consola  llamamos a la liberia pcadapt**
Esta libreria nos ayuda a detectar outliers en datos genomicos, basado en un analisis de componentes principales

*     library(pcadapt)

## leer un archivo con informción de SNP y su loc en cada chr o su pos
 CHR | SNP | BP | AlleleA | AlleleB
**El archivo.bim tiene la informacion** 

*     loci_info <- read.table("loci_info.txt", sep="\t", header = F)
*     colnames(loci_info) <- c("CHR", "SNP", "BP","AlleleA", "AlleleB")

##   En terminal copiar el popmap que usamos con los analisis exploratorios

*     cp ../05.Admixture/popmap-calabazas.csv .

**En Consola**

*     popmap <- read.table("popmap-calabazas.csv", header = T,sep = ",")
*     popmap <- popmap$POP

### Leer archivo de entrada, pcadapt acepta de todo (vcf, ped, bed)

*     calabazas <- read.pcadapt("n",type = "vcf")


##  hacemos un PCA

*     calabazas.pca <- pcadapt(calabazas.bed, K=10, method = "mahalanobis", ploidy = 2, min.maf = 0.05)
*     summary(calabazas.pca)

### hacer plot y elegir para elegir la K del PCA

*     plot(calabazas.pca, option = "screeplot", K=10)

### En nuestro caso el valor "correcto" es K=2

*     plot(calabazas.pca, option = "screeplot", K=2)

## PCA con la agrupacion de POP (la tendencia deberia sr similar a los datos neutros pero no obligatorio)

*     plot(calabazas.pca,option="scores", pop=popmap)
*     plot(calabazas.pca,option="scores",i = 1, j = 2,pop=popmap)


##  Plot del pcadapt y la K correspodiente

`plot(calabazas.pca, option = "manhattan", K = 2)
 plot(calabazas.pca, option = "qqplot")
 hist(calabazas.pca$pvalues, xlab="p-values", main = NULL, breaks = 50, col = "red")
 plot(calabazas.pca, option = "stat.distribution")`


# Como saber cuales son los SNPs candidatos?
### metodo q-values

*     library(qvalue)

`qval <- qvalue(calabazas.pca$pvalues)$qvalues
alpha1 <- 0.001
outliers1 <- which(qval < alpha1)
length(outliers1) 
met1 <- loci_info[outliers1,]
met1
candidatos1<- as.data.frame(met1)
candidatos1`

### metodo Bonferroni correction

`padj2 <- p.adjust(calabazas.pca$pvalues,method="bonferroni")
alpha2 <- 0.05
outliers2 <- which(padj2 < alpha2)
length(outliers2) 
met2 <- loci_info[outliers2,]
met2`

### hagamos un plot y señalesmos los SNPs candidatos


`plot (-log10(calabazas.pca$pvalues), type="p", col="grey20",
      cex=1.5, pch=20, xlab="SNPs", ylab="-log10(p-value)")
points(outliers2, -log10(calabazas.pca$pvalues[outliers2]), col = "red", pch=20, cex=2)`


*     install.packages("qqman")
*     library("qqman")
*     library("qqman")

## Dejar en numerico los nombres de los cromosomas
Esto es porque luego no corre si ve elementos alfabeticos

`loci_info$CHR <- gsub("Cmo_Chr", "", loci_info$CHR)
loci_info$CHR <- as.integer(loci_info$CHR)`

##  introducir P a loci info

`qval
P<-qval
P<-as.data.frame(P)
loci_info$P <- qval
colnames(loci_info)
snpsCan<-candidatos1$SNP`



`manhattan(na.omit(loci_info), chr="CHR", bp="BP",
          snp="SNP", p="P", highlight = snpsCan,
          annotatePval = 0.05, col=c("blue","pink"), 
          annotateTop = T)`

