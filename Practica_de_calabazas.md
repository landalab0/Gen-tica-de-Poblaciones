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
