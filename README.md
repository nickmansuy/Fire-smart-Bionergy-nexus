Project Title
Fire-Smart Bioenergy nexus
Description
Here we provide the code for the figure.3 in  Mansuy et al. ( in press) Integrating fire-smart fuels management with bioenergy benefits remote and Indigenous communities in Canada. Nature Communications Earth & Environment 
To determine which communities would benefit the most from using biomass from fuel treatments as feedstock for bioenergy, we performed a hierarchical clustering analysis of the communities based on the BAFT, the theoretical percentage of FTA needed to meet AED, and current and future projected annual area burned (AAB). We used the heatmaply R package104 to perform the hierarchical clustering method and the data was scaled by subtracting the minimum and dividing by the maximum of all observations. This transformation brings the data for all variables to a 0 to 1 scale, while preserving the shape of each variable’s distribution. The clustering method, centroid, was chosen using the dendextend R package105 and the optimal number of clusters, five, was determined based on a consensus of many methods using the parameters R package106. Further details on the cluster analysis, including the R code, is available in the Supplementary information section. For each of the 33 communities, the current and future AAB was estimated from the HFRZ’s49 according to the community’s centroid. Estimates for current (1981-2010) and future (2011-2040) AAB were projected based on a refined version of the multivariate adaptive spline regression models developed by Boulanger et al. (2014)49 for each HFRZ, which estimates monthly AAB from current and future fire-weather and meteorological parameters. End-of-century climate conditions as simulated under the RCP 4.5 were used for future AAB projections.
Getting Started
Dependencies
R Project for Statistical Computing
heatmaply R package
dendextend R package
Installing
R: The R Project for Statistical Computing
Executing program
#Figure 3 clustering and heatmap R codelibrary(ggplot2)
library(rlang)
library(plotly)
library(heatmaply)
library(dendextend)
library(parameters)
library(NbClust)
library(mclust)
library(see)
library(factoextra)
library(dplyr)
library(gridExtra)

bio <- read.csv("heatmap_jan_25.csv",encoding="UTF-8")

bio_matrix <- as.data.frame(bio[,3:6]) %>% 
  dplyr::rename("FTA"=Percent.Harvest,
                "Current AAB"=Current.FR,
                "Future AAB"=Future.FR)

rownames(bio_matrix) <- paste(bio$Community,bio$Prov,sep=" ")
# calculates the cophenetic correlation between the distance matrix and the 
# cophenetic distance of the hclust obj 
# highest optim = best clustering model for a distance matrix
bio_d <- dist(bio_matrix,method="euclidean")
dend_expend(bio_d) # centroid

# finds optimal numbers of clusters based on consensus of many methods
n_clust <- n_clusters(bio_matrix,package=c("easystats","NbClust","mclust",standardize=TRUE,  # standardizes the dataset before clustering
                                           distance_method="euclidean",
                                           h_clust_method="centroid", # selected based on previous step
                                           method="hclust")) # same results for pam, hclust, kmeans etc
n_clust # 7 methods out of 29 recommend 5 clusters/groups

figheatmap$x$layout$xaxis$showticklabels <- FALSE ## remove tick labels for dendrogram
figheatmap$x$layout$xaxis2$showticklabels <- TRUE ## keep tick labels for columns

figheatmap <- heatmaply(normalize(bio_matrix),dist_method="euclidean",hclust_method="centroid",k_row=5, 
          colors=cool_warm(3), ylab="Communities",grid_gap=0.2,row_dend_left=TRUE,dendrogram='row',
          plot_method="plotly",fontsize_row=16,fontsize_col=16,colorbar_xpos=0,colorbar_ypos=0.35,
          subplot_widths=c(0.5,0.5)) %>% 
  colorbar(tickfont = list(size = 16)) 

figheatmap


Help
Any advise for common problems or issues.
command to run if program contains helper info
Authors
Contributors names and contact info
•	nicolas.mansuy@nrcan-rncan.gc.ca
•	jennifer.buss@nrcan-rncan.gc.ca 

Version History
v.1
License
Acknowledgments
