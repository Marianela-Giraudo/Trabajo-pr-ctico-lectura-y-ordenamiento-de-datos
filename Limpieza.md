# TP GRUPAL: lectura y ordenamiento de datos
Soledad Ciancio \_ Marianela Giraudo

# Carga de paquetes——————————————————–

``` r
suppressPackageStartupMessages({
  library(readxl)
  library(haven)
  library(readr)
  library(dplyr)
  library(purrr)
  library(data.table)
  library(stringr)
})
```

# Función para leer TSV sin mostrar mensajes ———————————-

``` r
leer_tsv_silencioso <- function(ruta){
  read_tsv(ruta, show_col_types = FALSE)
}
```

# Importación de tablas—————————————————–

``` r
serugiran <- read_excel("datos_crudos/serugiran.xlsx")
albumns <- read_excel("datos_crudos/albums.xlsx")
suigeneris <- read.csv("datos_crudos/suigeneris.csv")
porsuigieco <- read_delim("datos_crudos/porsuigieco.txt", delim = "|", show_col_types = FALSE)
bbatj <- read_sas("datos_crudos/bbatj.sas7bdat")
```

# Álbumes solista ———————————————————–

``` r
Clicsmodernos <- leer_tsv_silencioso("datos_crudos/solista/Clics Modernos.txt")
Como_conseguir_chicas <- leer_tsv_silencioso("datos_crudos/solista/Como Conseguir Chicas.txt")
Cuarenta_Obras_Fund_Vol1 <- leer_tsv_silencioso("datos_crudos/solista/Cuarenta Obras Fundamentales (Volumen 1).txt")
Cuarenta_Obras_Fund_Vol2 <- leer_tsv_silencioso("datos_crudos/solista/Cuarenta Obras Fundamentales (Volumen 2).txt")
El_Aguante <- leer_tsv_silencioso("datos_crudos/solista/El Aguante.txt")
El_Album <- leer_tsv_silencioso("datos_crudos/solista/El Álbum.txt")
Filosofia_Barata <- leer_tsv_silencioso("datos_crudos/solista/Filosofía Barata y Zapatos de Goma.txt")
Garcia_87 <- leer_tsv_silencioso("datos_crudos/solista/García 87-93.txt")
Garcia_el_mas_grande <- leer_tsv_silencioso("datos_crudos/solista/García, el Más grande.txt")
Influencia <- leer_tsv_silencioso("datos_crudos/solista/Influencia.txt")
La_Hija_de_la_lagrima <- leer_tsv_silencioso("datos_crudos/solista/La Hija De La Lagrima.txt")
Musica_del_alma <- leer_tsv_silencioso("datos_crudos/solista/Música del Alma (En vivo).txt")
Parte_de_la_religion <- leer_tsv_silencioso("datos_crudos/solista/Parte De La Religion.txt")
Piano_Bar <- leer_tsv_silencioso("datos_crudos/solista/Piano Bar.txt")
Random <- leer_tsv_silencioso("datos_crudos/solista/Random.txt")
Rock_and_Roll <- leer_tsv_silencioso("datos_crudos/solista/Rock And Roll Yo.txt")
Say_no_more <- leer_tsv_silencioso("datos_crudos/solista/Say No More.txt")
Tango <- leer_tsv_silencioso("datos_crudos/solista/Tango 4.txt")
Terapia_intensiva <- leer_tsv_silencioso("datos_crudos/solista/Terapia Intensiva.txt")
Unplugged <- leer_tsv_silencioso("datos_crudos/solista/Unplugged.txt")
Yendo_de_la_cama_al_living <- leer_tsv_silencioso("datos_crudos/solista/Yendo De La Cama Al Living.txt")
```

# Unión del álbum “La máquina de hacer pájaros” ——————————

``` r
ruta_lmdhp <- "datos_crudos/lmdhp/album_la_maquina_de_hacer_pajaros/"
archivos_lmdhp <- list.files(path = ruta_lmdhp, pattern = "\\.txt$", full.names = TRUE)

leer_cancion <- function(archivo) {
  lineas <- readLines(archivo)
  campos <- str_split_fixed(lineas, ":\\s+", 2)
  datos <- as.data.frame(t(campos[, 2]), stringsAsFactors = FALSE)
  names(datos) <- campos[, 1]
  datos
}

album_la_maquina <- map_dfr(archivos_lmdhp, leer_cancion)

write_tsv(album_la_maquina, "lmdhp_album_completo.txt")
```

# Unión del álbum “Películas” ———————————————–

``` r
ruta_peliculas <- "datos_crudos/lmdhp/album_peliculas/"
archivos_peliculas <- list.files(path = ruta_peliculas, pattern = "\\.txt$", full.names = TRUE)

album_peliculas <- map_dfr(archivos_peliculas, leer_cancion)

write_tsv(album_peliculas, "lmdhp_album_peliculas.txt")
```

# Cambio de nombres de variables ——————————————–

``` r
serugiran <- rename(serugiran, 
                    name = cancion, 
                    track_number = nro_track, 
                    disc_number = nro_disco, 
                    album_name= album_nombre, 
                    album_artist = album_artista, 
                    danceability = bailabilidad,
                    energy = energia, 
                    key= tonalidad , 
                    loudness = volumen, 
                    mode = modo,
                    speechiness = habladicidad, 
                    acousticness = acusticidad, 
                    instrumentalness = instrumentalidad,
                    liveness = envivocidad, 
                    valence = positividad, 
                    duration_ms = duracion_ms,
                    time_signature = compas)
```

# Unión de todas las tablas————————————————–

``` r
resultado <- rbindlist(
  list(
   album_peliculas,
    album_la_maquina,
    bbatj,
    Clicsmodernos,
    Como_conseguir_chicas,
    Cuarenta_Obras_Fund_Vol1,
    Cuarenta_Obras_Fund_Vol2,
    El_Aguante,
    El_Album, Filosofia_Barata,
    Garcia_87,
    Garcia_el_mas_grande,
    Influencia,
    La_Hija_de_la_lagrima,
    Musica_del_alma, 
    Parte_de_la_religion,
    Piano_Bar,
    porsuigieco,
    Random,
    Rock_and_Roll,
    Say_no_more, 
    serugiran,
    suigeneris,
    Tango,
    Terapia_intensiva,
    Unplugged,
    Yendo_de_la_cama_al_living
   )    ,
  fill = TRUE
)
```

# Guardar dataset final —————————————————–

``` r
suppressMessages({
  write_tsv(resultado, "Resultado_.txt")
})
```
