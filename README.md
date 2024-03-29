# Entraînement du modèle yolov5 sur les images radiographiques

## 1) Création d'un environnement et l'installation des packages nécessaires

Pour l'utilisation de ce modèle, il faut avoir une version de Python entre 3.7 et 3.10.

### a) Création et activation de l'environnement 

Sur une invite de commandes on exécute les commandes suivantes pour créer et activer l'environnement (Je l'ai nommé Projet3A)


```python
py -m pip install --upgrade pip
```


```python
py -m pip install --user virtualenv
```


```python
py -m venv Projet3A
```


```python
.\Projet3A\Scripts\activate
```
ou sur Linux
```python
source Projet3A/bin/activate
```
```python
cd Projet3A
```

### b) Importation du modèle Yolov8 initial et installation des packages nécessaires



```python
pip install ultralytics
```
Si la commande ne marche pas vous pouvez exécuter celle ci :

```python
pip install -r requirements.txt
```
Cette commande doit installer Yolov8 automatiquement dans votre environnement virtuel.

### c) Structure des données

Vu qu'on utilise le modèle initial pour l'adapter à notre dataset, il faut inclure les fichiers de notre dataset dans le dossier yolov5.

```python
python lesions_data.py chemin/vers/fichier/xml  chemin/vers/images
```

Cette commande permet de:
1) Convertir les images DICOM en images png de dimensions (512,256) (modifiables en modifiant la fonction  convert_dicom_folder_to_png)

2) Convertir les fichiers xml en des fichier txt

3) Répartir la base de données en 80% train, 10% validation, 10% test (Pourcentages modifiables en modifiant les arguments de la fonction train_test_split)

### d) Entraînement

On lance l'entraînement en exécutant le fichier train.py avec les arguments suivant :

img: taille de l'image 

epochs: nombre d'epochs (itérations sur le dataset entier) 

data: chemin d'accés vers le fichier yaml du dataset

batch: taille du batch 

cfg: Configuration du modèle

weights: Pour montrer au modèle les poids donnés au paramétres au début (on peut lui passer finalbest.pt pour commencer l'entraînement depuis la configuration optimale trouvée)
    

La configuration optimale a été trouvée à l'aide de cette configuration (Entraînement du modèle yolov5s sur des images de (1200,1200) avec un batch de 32)

```python
mkdir ../datasets
```
```python
mv lesions ../datasets
```

```python
yolo detect train data=lesions.yaml model=yolov8m.pt epochs=200 imgsz=1024
```

Après l'entraînement, le modèle crée un dossier train dans runs contenant les résultats d'entraînemnt, les paramètres du modèle optimal trouvé sous forme de fichiet pt (best.pt), et la prédiction du modèle sur les images de validation

La commande ci-dessus a permis de trouver la configuration optimale que j'ai mis sur le github sous le nom de finalbest.pt, on peut utiliser cette configuration pour:

1) Prédire la position des lésions sur des images radiographiques en utilisant le fichier val.py ou detect.py

2) Refaire un entraînement du modèle à partir de cette configuration (dans le cas d'ajout de nouvelles images ou qu'on veut changer de nombre d'epochs ou dimensions d'images

Pour la détection sur des nouvelles images, il faut créer un fichier Python. Vous pouvez le nommez comme vous voulez. (Dans ce cas je vais le nommer detect.py) 

```python
from ultralytics import YOLO

# Load a pretrained YOLOv8n model
model = YOLO('best.pt')
images=["path/to/image","path/to/image"] #Vous pouvez mettre le nombre que vous voulez d'images dans cette liste
for elt in images
    model.predict(elt,imgsz=1024, save_conf=True, verbose=False,save_txt=True)
```


# Résultats finaux 


## Courbes de loss et précision moyenne
<table>
  <tr>
    <td align="center">
      <img src="results.png" alt="Image" style="width:100%;">
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong>Courbes de loss, précision moyenne et recall</strong>
    </td>
  </tr>
</table>

La métrique utilisée pour l'évaluation est basée sur l'IoU définie par le quotient de la surface de l'intersection entre la zone prédite et la zone réelle  sur la surface d'union de ces deux zones.
map@0.5 définit la précision moyenne avec un seuil de 0.5 pour l'IoU (i.e on ne considére qu'un résultat est vrai positif que si l'IoU dépasse le seuil de 0.5.
Les résultats peuvent être résumés dans ce tableau :
|                 | Yolov8          |
| --------------- | --------------- |
| Précision       | 0.999           |
| Recall          | 0.836           |
| F1-score        | 0.91            |

On peut aussi visualiser les résultats en faisant les prédictions sur l'ensemble de validation

## Prédictions 

<table>
  <tr>
    <td align="center">
      <img src="labels.jpg" alt="Image 1" style="width:100%;">
      <br>
      <strong>Labels</strong>
    </td>
    <td align="center">
      <img src="predictions.jpg" alt="Image 2" style="width:100%;">
      <br>
      <strong>Prédictions</strong>
    </td>
  </tr>
</table>


Ce projet est la continuation d'un autre projet. Voici le lien du projet d'origine : https://github.com/achraflouzali/Peri-apical-lesions-detection 
Pour plus de détails n'hésitez pas à me contacter par mail: yousseftrabelsi250@gmail.com
