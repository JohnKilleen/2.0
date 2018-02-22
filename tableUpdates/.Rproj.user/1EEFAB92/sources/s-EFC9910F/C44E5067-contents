library(tidyverse)
library(magrittr)

## RESIDENTIAL BUILDING PERMITS
rRPMTS <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/rRPMTS.csv",header=TRUE)
dRPMTS <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/dRPMTS.csv",header=TRUE) %>% 
  mutate(.,y_2017=y_2016)
write.csv(dRPMTS,"dRPMTS.csv",row.names = FALSE)
rRPMTS17 <- read.csv("/Users/johnk/Downloads/newCompassTables/rPERMITS17.csv",header=TRUE) %>% 
  merge(rRPMTS,.,by='id',all=TRUE) 
write.csv(rRPMTS17,"rRPMTS.csv",row.names = FALSE)

## COMMERCIAL BUILDING PERMITS
rCPMTS <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/rCPMTS.csv",header=TRUE)
dCPMTS <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/dCPMTS.csv",header=TRUE) %>% 
  mutate(.,y_2017=y_2016)
write.csv(dCPMTS,"dCPMTS.csv",row.names = FALSE)
rCPMTS17 <- read.csv("/Users/johnk/Downloads/newCompassTables/cPERMITS17.csv",header=TRUE) %>% 
  merge(rCPMTS,.,by='id', all=TRUE) 
write.csv(rCPMTS17,"rCPMTS.csv",row.names = FALSE)

## RESIDENTIAL COs
rCOR <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/rCOR.csv",header=TRUE)
rCOR16 <- read.csv("/Users/johnk/Downloads/newCompassTables/rCOR16.csv",header=TRUE)
rCOR17 <- read.csv("/Users/johnk/Downloads/newCompassTables/rCOR17.csv",header=TRUE) 
rCORs <- merge(rCOR,rCOR16,by='id',all=TRUE) %>% 
          merge(.,rCOR17,by='id',all=TRUE) %>% 
  select(.,1:7)
write.csv(rCORs,"rCOR.csv",row.names = FALSE)

dCOR <- read.csv("/Users/johnk/Documents/2.0/src/data/metric/census/dCOR.csv",header=TRUE) %>% 
  mutate(.,y_2016=y_2015, y_2017=y_2016)
write.csv(dCOR,"dCOR.csv",row.names = FALSE)
