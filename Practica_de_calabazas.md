#  Hacemos llamados a las librerias necesarias
*       library(adegenet) 
        library(hierfstat)
        library(rgdal)
        library(vcfR)
        library(ape)
        library(pegas)
        library(ade4)
        library(ggplot2)
#Empezamos a correr ya con las librerias instaladas
*     calabazas.vcf<-read.vcfR("moschata_minDP5_geno_50_mind_50_MAF0.05_HWE_LD_free.plink.vcf")
*     base <- read.table("popmap-calabazas.csv", header = T,sep = ",")
**Convertimos el vcf en objeto genind, cambiando el pop**
*     calabazas_genind_pop <- vcfR2genind(calabazas.vcf, pop=base$POP)
**revisemos los nombres** 
*     popNames(calabazas_genind_pop)
**Revisamos nuestros datos para verificar que todo este bien**
*     calabazas_genind_pop@ploidy
*     calabazas_genind_pop@pop
*     calabazas_genind_pop@type
*     calabazas_genind_pop@pop
*     calabazas_genind_pop@loc.n.all
*     calabazas_genind_pop@all.names
## Por si acaso todo está impreso en consola :3
**Vamos a preaparar los objetos que usaremos**
*     vcf.fstat.pop <- genind2hierfstat(calabazas_genind_pop)
*     vcf.genpop.pop <-genind2genpop(calabazas_genind_pop)
**Estimamos diversidad**
*     BasicSS_pop <- basic.stats(calabazas_genind_pop)
*     BasicSS_pop$overall
  ## Preparando los datos para gráficos, aquí no despliega nada, ya que solo estamos dando instrucciones :)
  **Heterocigosis esperada promedio**
*     Div_Raw.Hs.pop <-as.data.frame(BasicSS_pop$Hs, scale=T)
  **Heterocigosis observada**
*     Div_Raw.Ho.pop <-as.data.frame(BasicSS_pop$Ho)
**indices de endogamia**
*     Fis_Raw.pop <-as.data.frame(BasicSS_pop$Fis)
# plot de diversidddes
*     colores<- c("red","hotpink","black","darkorange","chartreuse4",
           "blue","greenyellow","coral1","darkorchid1",
           "saddlebrown")
*     Ho.pop<-barplot(colMeans(Div_Raw.Ho.pop,na.rm=T),las=2,main="Ho", 
                col = colores, ylim=c(-0,0.20))

*     barplot(colMeans(Div_Raw.Hs.pop,na.rm=T),las=2,main="HE", 
        col = colores, ylim=c(-0,0.20))

*     barplot(colMeans(Fis_Raw.pop,na.rm=T),las=2,main="FIS", 
        col = colores, ylim=c(-0.1,0.05))
## Distancia Geneica entre grupos de individuos
**Distancia de Edwars**
*     pop.dist <-dist.genpop(vcf.genpop.pop,method="2")
  **Lo ploteamos**
  *     plot(hclust(pop.dist,method="ward.D2"), hang=-1)
    # FST pareada para ver estructura
    **esto tarda mucho**
*     pop_fst.mat <- pairwise.neifst(vcf.fstat.pop)

## Haciendo un heatmap con los valores
*     heatmap(pop_fst.mat,col=rev(heat.colors(100)),symm=TRUE, keep.dendro = T)
*       X.pop <- scaleGen(calabazas_genind_pop, NA.method="mean")pca_pop.ALL <- dudi.pca(X.pop,scannf=FALSE,scale=FALSE)  summary(pca_pop.ALL)
## Realizar plot de PCA 
*       plot(pca_pop.ALL$li,col=transp(colores_calabazas[pop(calabazas_genind_pop)],0.8),pch=20,cex=2,xlab="Axis.1 2.059%",ylab="Axis.2 1.316%",bty="n",cex.axis=1.25,cex.lab=1.5) abline(h=0,lty=3,col="gray") abline(v=0,lty=3,col="gray") legend("topright",legend=levels(pop(calabazas.ind)),xpd=TRUE,pt.cex=2,text.font=1,cex=1.25,col=Colores.cucu,pch=20) dev.off()
*      abline(h=0,lty=3,col="gray")
       abline(v=0,lty=3,col="gray") legend("topright",legend=levels(pop(calabazas.ind)),xpd=TRUE,pt.cex=2,text.font=1,cex=1.25,col=Colores.cucu,pch=20)
   #DPAC####
  **muy usado para establecer grupos genéticos con SNPs**
   **es algo instable con los resultados, usar con precaución**
*Nota los argumentos n.da=2 y n.pca=8,*
*si cambias sus valores, cambian los resultados*
# consulta los manuales.
## Veamos con la asignación original
*     pop.dpac <-dapc(calabazas_genind_pop,n.da=2,n.pca=8)
      scatter (pop.dpac, col =colores,cex=2,bg="white", solid=0.8,
         scree.da=FALSE,scree.pca=FALSE,lwd = 2, lty = 1, 
         segcol = "black",legend = FALSE, 
         posi.leg = "topright",cstar=1,cellipse=1,
         clabel =0.75,grid =TRUE)
  #   Plot de la probabilidad de asignación de individuos
*     compoplot(pop.dpac)
