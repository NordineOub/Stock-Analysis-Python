# Data Lakes and Data Integration with Azure

## Objectif

Concaténer et exporter un ensemble de données, tout en préservant leur structure d'origine, vers un stockage Azure Data Lake et faire de l'analyse de données

## Différentes étapes du travail

### Instanciation des ressources


#### Account Storage
1- Nous commençons par la création d'un groupe de ressource qui stockera l'ensemble de nos ressources (par manque de crédit, nous avons utilisé l'abonnement Hackathon).

Ensuite, nous crééons un compte de stockage visant à stocker les différents fichiers de valeurs boursières

Une fois le comptede stockage créé, nous crééons un conteneur qui sera intégré au compte de stockage et nous y instancions un azure blob storage où il est possible de simplement "Drag & drop" l'ensemble du dossier où sont stockés les fichiers csv.

#### SQL Server

Nous crééons aussi un serveur SQL Azure qui servira de zone de stockage pour notre sortie.

Une fois celui-ci créé et instancié, nous crééons une base de donnée SQL qui pourra stocker des tables et des vues

Nous passons aussi par l'éditeur de requête pour créer en amont la table de sortie de nos fichiers CSV en reprenant la même structure :

````sql
create table Stock (    
date_ VARCHAR(255),    
high_ double precision,    
low_ double precision,    
open_ double precision,    
close_ double precision,    
volume_ double precision,    
adj_close double precision,    
stock_name VARCHAR(255)
)
````

Une fois cet ensemble créé, nous instancions le Azure Data Factory et nous y crééons notre première pipeline.

### ETL Sur Data Factory

Une fois la pipeline créé, on créer un dataset qui sera relié à notre storage account.

De retour dans le pipeline, nous utilisons l'action "Copy Data". Celui-ci permettra de concaténer l'ensemble des fichiers CSV. 

On spécifie en paramètre un pattern comme chemin de fichier (*.csv pour spécifier l'ensemble des fichiers dont l'extension est en CSV).

On donne en sortie la base de données SQL créée précédemment, on spécifie la table en sortie et on fait "importer le schéma" sur l'onglet de mappage pour qu'Azure retrouve les colonnes similaires entre la BDD et les CSV.

On lance un premier débogage pour voir le fonctionnement de notre pipeline, si tout est bon on passe à la suite.

P.S : Il est aussi possible de vérifier en allant requêter dans la base de donnée la présence de la table vide ou non.

### Databricks

On ajoute l'action Bloc note de Databricks dans le pipeline et on créer un cluster Databricks.

Une fois le cluster créé, on génère un token qu'on utilisera dans les paramètres du "Bloc note" sur le Data Factory.

Dès que le Data factory est relié, on importe le notebook suivant dans le cluster databricks : ***`Analysis_Databricks_spark.ipynb`***

On complete l'URL avec celle de la base de données (trouvable dans paramètres => Chaine de caractère

On lance le tout et on vérifie la bonne création d'une table de sortie pour le moving average
