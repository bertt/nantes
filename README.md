# nantes

## Demo

https://bertt.github.io/nantes/index.html

## Processing data

Source data format: https://geoservices.ign.fr/sites/default/files/2021-07/DL_RGEALTI_2-0.pdf

Process creating terrain tiles for Cesium:

1] Download asc files

Source: https://geoservices.ign.fr/ressource/209211 

RGE ALTI® 1M - D044 Loire-Atlantique - Décembre 2022

Part 1: RGEALTI_2-0_1M_ASC_LAMB93-IGN69_D044_2022-12-20.7z.001 (4 GB)

Part 2: RGEALTI_2-0_1M_ASC_LAMB93-IGN69_D044_2022-12-20.7z.002 (235 MB)

```
$ wget https://geoservices.ign.fr/download/files/209211/d2dd036772939af1a1e9e462486609cd/0/1060/0/2 --no-check-certificate
$ wget https://geoservices.ign.fr/download/files/209211/881bc6e79958baea3f1dbf741c63af2e/0/1060/1/2 --no-check-certificate
```

2] Unzip the files

```
$ mkdir nantes
$ 7z x RGEALTI_2-0_1M_ASC_LAMB93-IGN69_D044_2022-12-20.7z.001 -o./nantes
```

7401 asc files are in 'RGEALTI_2-0_1M_ASC_LAMB93-IGN69_D044_2022-12-20/RGEALTI/1_DONNEES_LIVRAISON_2023-01-00125/RGEALTI_MNT_1M_ASC_LAMB93_IGN69_D044_20230113'

3] Make a selection of asc files for processing

There are 3 folders:

- RGEALTI_DST_1M_ASC_LAMB93_IGN69_D044_20230113      

- RGEALTI_MNT_1M_ASC_LAMB93_IGN69_D044_20230113

- RGEALTI_SRC_1M_ASC_LAMB93_IGN69_D044_20230113

DST -> Surface?

MNT -> DTM?

SRC -> ?

In our case: RGEALTI_FXX_0351_6696_MNT_LAMB93_IGN69.asc

```
$ cp RGEALTI_FXX_0351_6696_MNT_LAMB93_IGN69.asc ~/nantes
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


