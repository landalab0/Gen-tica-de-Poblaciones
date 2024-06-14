## llamamos librerias para trabajar
*     library(adegenet) 
      library(hierfstat)
      library(rgdal)
      library(vcfR)
      library(ape)
      library(pegas)
      library(ade4)
      library(ggplot2)
**Con nuestro vcf corremos admixture, dentro de la carpeta creada para trabajar esta parte**
*     calabazas.vcf <- read.vcfR("../../05.Admixture/moschata_minDP5_geno_50_mind_50_MAF0.05_HWE_LD_free.plink.vcf") base <- read.table("../admixture/popmap-calabazas.csv", header = T,sep = ",")

## Se crea un objeto genind 
Este es un formato de archivo especifico para almacenar datos geneticos en software R, comunmente se almacenan SNPs
Comunmente incluyen loci y genotipos
**cambiamos el "pop" de acuerdo al analisis por hacer**
*     calabazas_genind_pop <- vcfR2genind(calabazas.vcf, pop=base$POP)

###   revisemos nuestros datos
**Observa los datos dentro cada "slot" del objeto**
*     calabazas_genind_pop@ploidy
*     calabazas_genind_pop@pop
*     calabazas_genind_pop@type
*     calabazas_genind_pop@loc.n.all
*     calabazas_genind_pop@all.names

### Vamos a preaparar los objetos que usaremos
*     vcf.fstat.pop <- genind2hierfstat(calabazas_genind_pop)
*     vcf.genpop.pop <-genind2genpop(calabazas_genind_pop)

## Estimemos diversidad
*     BasicSS_pop <- basic.stats(calabazas_genind_pop)
*     BasicSS_pop$overall


## Preparemos los datos para gráficos
> Aquí no despliega nada, ya que solo estamos dando instrucciones :)
### Heterocigosis esperada promedio
*     Div_Raw.Hs.pop <-as.data.frame(BasicSS_pop$Hs, scale=T)
### Heterocigosis observada
*     Div_Raw.Ho.pop <-as.data.frame(BasicSS_pop$Ho)
### indices de endogamia
*     Fis_Raw.pop <-as.data.frame(BasicSS_pop$Fis)

#plot de diversidddes
'colores<-c("red","hotpink","black","darkorange","chartreuse4",
           "blue","greenyellow","coral1","darkorchid1",
           "saddlebrown")' 

## las siguientes instrucciones son para plotear
`Ho.pop<-barplot(colMeans(Div_Raw.Ho.pop,na.rm=T),las=2,main="Ho", 
                col = colores, ylim=c(-0,0.30))`

`barplot(colMeans(Div_Raw.Hs.pop,na.rm=T),las=2,main="HE", 
        col = colores, ylim=c(-0,0.30))`

`barplot(colMeans(Fis_Raw.pop,na.rm=T),las=2,main="FIS", 
        col = colores, ylim=c(-0.2,0.25))`

## Distancias genéticas entre grupos de individuos
*     pop.dist <-dist.genpop(vcf.genpop.pop,method="2")
*     plot(hclust(pop.dist,method="ward.D2"), hang=-1)


##  hagamos un PCA y escalemos los datos faltantes

`X.pop <- scaleGen(calabazas_genind_pop, NA.method="mean")
pca_pop.ALL <- dudi.pca(X.pop,scannf=FALSE,scale=FALSE)
summary(pca_pop.ALL)`


### Plot del PCA
*     colores2<-c(1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24)

`plot(pca_pop.ALL$li,col=transp(colores2[pop(calabazas_genind_pop)],0.8),
     pch=20,cex=2,xlab="Axis.1 2.059%",ylab="Axis.2 1.316%",
     bty="n",cex.axis=1.25,cex.lab=1.5)
abline(h=0,lty=3,col="gray")
abline(v=0,lty=3,col="gray")
legend("topright",legend=levels(pop(calabazas.ind)),xpd=TRUE,pt.cex=2,text.font=1,cex=1.25,col=Colores.cucu,pch=20)`

#### DPAC
Muy usado para establecer grupos genéticos con SNPs **es algo instable con los resultados, usar con precaución**
**Nota** los argumentos n.da=2 y n.pca=8,
> si cambias sus valores, cambian los resultados
**consulta los manuales**
## Veamos con la asignación original
`pop.dpac <-dapc(calabazas_genind_pop,n.da=20,n.pca=80)
scatter (pop.dpac, col =colores,cex=2,bg="white", solid=0.8,
         scree.da=FALSE,scree.pca=FALSE,lwd = 2, lty = 1, 
         segcol = "black",legend = FALSE, 
         posi.leg = "topright",cstar=1,cellipse=1,
         clabel =0.75,grid =TRUE)` 

### Plot de la probabilidad de asignación de individuos
*     compoplot(pop.dpac)

