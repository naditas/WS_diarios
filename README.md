---
title: "Titulares"
author: ""
date: "`r paste('', format(Sys.Date(), '%d-%B- %Y'))`"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
library(rvest)
library(knitr)
library(kableExtra)
```
 
 
## Diario Financiero
 
```{r, echo=FALSE}

url= "https://www.df.cl/"

pagina <- read_html(url) |> html_elements('h2') 

tabla1 <- tibble(
  Titular = pagina |> html_elements('a') 
                  |> html_text2(),
  Link = paste0("https://www.df.cl",
                str_squish(pagina |> html_elements('a') 
                               |> html_attr('href')))
    )   

tabla1 <- tabla1 |> 
filter(!str_detect(Titular, "Dólar en Chile cierra" )) |> distinct()

kable(tabla1, booktabs=TRUE) |> kable_styling(full_width = F,
                latex_options = "HOLD_position")

```
 
## La Tercera
 
```{r, echo=FALSE}

url= "https://www.latercera.com/"

paginaLT <- read_html(url) |> html_elements('h2') 

tabla1LT <- tibble(
  Titular = paginaLT |> html_elements('a') 
  |> html_text2(),
  Link = paste0("https://www.latercera.com",
                str_squish(paginaLT |> html_elements('a') 
                           |> html_attr('href')))
)   

kable(tabla1LT, booktabs=TRUE) |> kable_styling(full_width = F,
                latex_options = "HOLD_position")

```
  

## emol 
 
```{r, echo=FALSE}

url= "https://www.emol.com/"

paginaE <- read_html(url) |> html_elements('h3') 

tabla1E <- tibble(
  Titular = paginaE |> 
    html_elements('a') |> 
    html_text2() |> 
    str_squish(),
  Link = paste0("https://www.emol.com",
                str_squish(paginaE |> 
                             html_elements('a') |> 
                             html_attr('href')))
  ) |>  filter(Titular!="")  


repar1 <- tabla1E |> mutate(Link = str_replace_all(Link, "https://www.emol.comhttps://"," " )) |> 
  filter(!str_detect(Link,"www.emol.com")) |> 
  mutate(Link = paste0("htpps://www.emol.com",Link))
  
repar2 <- tabla1E |> mutate(Link = str_replace_all(Link, "https://www.emol.comhttps://"," " )) |> 
  filter(str_detect(Link,"https://www.emol.com"))

tabla1E_f <- bind_rows(repar1, repar2)


kable(tabla1E_f, booktabs=TRUE) |> kable_styling(full_width = F,
                latex_options = "HOLD_position")

```
    
    
    
```{r, echo=FALSE}

tabla1    <- tabla1 |> mutate(Medio="Diario_Financiero")
tabla1LT  <- tabla1LT |> mutate(Medio="La Tercera")
tabla1E_f <- tabla1E_f |> mutate(Medio="emol")

TF_medios <- bind_rows(tabla1, tabla1LT, tabla1E_f)


write_csv2(TF_medios, paste0("tabla_diarios","_",Sys.Date(),".csv"))

saveRDS(TF_medios, paste0("tabla_diarios","_",Sys.Date(),".rds"))
```
    
    
