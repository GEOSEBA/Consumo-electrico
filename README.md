# Consumo-electrico
# Indicador Consumo electrico ciudad

#setwd("D:/ownCloud/Indicadores_de_Sustentabilidad_Expansion/Consumo Electrico")
setwd("D:/ownCloud/Indicadores_de_Sustentabilidad_Expansion/Consumo Electrico")

library(readxl); library(dplyr)
# Cargar codigos de ciudades en estudio
city_codes <- read.csv("Input/Ciudades_expansion.csv")
# Cargar cantidad de viviendas por comuna CENSO 2017
viviendas_2017 <- read.csv("Input/viviendas_censo_2017.csv", sep = ",")
# Seleccionar viviendas de comunas en estudio y agregar columna Ciudad
viviendas_2017 <- viviendas_2017[toupper(viviendas_2017$NOMBRE.COMUNA) %in% toupper(city_codes$Comuna),]
viviendas_2017$Ciudad <- city_codes[match(toupper(viviendas_2017[["NOMBRE.COMUNA"]]), toupper(city_codes[["Comuna"]]) ), "Ciudad"]
#Unir consumo de kwh con total de vivienda y ciudades en estudio
consumo <- read_excel(path = "Raw_Data/Consumo_electrico_comuna_2018.xlsx")
consumo <- consumo[tolower(consumo$Comuna) %in% tolower(city_codes$Comuna) & consumo$`Tipo de Cliente` == "Residencial",]
consumo$Comuna <- toupper(consumo$Comuna)
consumo <- merge(consumo, viviendas_2017, by.x = "Comuna", by.y = "NOMBRE.COMUNA")
#Divir consumo anual kmh por cantidad de viviendas y meses del año
conusmo_hogar <- consumo %>%
group_by(Ciudad) %>%
summarise("kWh/hogar" = round(sum(`Energía (kWh)`)/sum(Total_viviendas)/12,1))
#Guardar resultado
output_folder <- "D:/ownCloud/Indicadores_de_Sustentabilidad_Expansion/Consumo Electrico/Output"
write.csv(conusmo_hogar, paste(output_folder, "91._Consumo_Electrico_expansion_ciudades.csv", sep = "/"), row.names = F)
