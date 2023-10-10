# nantes

## Demo

https://bertt.github.io/nantes/index.html

## Processing data

Source data format: https://geoservices.ign.fr/sites/default/files/2021-07/DL_RGEALTI_2-0.pdf

Process creating terrain tiles for Cesium:

1] Download asc files

Source: https://geoservices.ign.fr/ressource/210000 

RGE ALTI® 5M - D044 Loire-Atlantique - Décembre 2022

RGEALTI_2-0_5M_ASC_LAMB93-IGN69_D044_2022-12-20.7z (300 MB)

2] Unzip the files

```
$ mkdir nantes
$ 7z x RGEALTI_2-0_5M_ASC_LAMB93-IGN69_D044_2022-12-20.7z 
```

```
$ cd RGEALTI_2-0_5M_ASC_LAMB93-IGN69_D044_2022-12-20\RGEALTI\1_DONNEES_LIVRAISON_2023-01-00223\RGEALTI_MNT_5M_ASC_LAMB93_IGN69_D044
```

4] Convert to tif and set projection

```
$ find *.asc | parallel --bar 'gdalwarp -s_srs EPSG:2154 {} {=s:asc:tif:=}'
```

5] Warp

Warp to epsg:5698 (is EPSG:2154 + EPSG:5720), directory tmp is created

```
$ docker run -it -v $(pwd):/data geodan/terrainwarp -c EPSG:5698 -m 1
```
6] Tile

Tile from level 20 to 0, directory tiles is created

```
$ docker run -it -v $(pwd):/data geodan/terraintiler -s 20 -b 15
```

7] Create client


