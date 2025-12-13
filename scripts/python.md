#### 1-Nombre de commandes livrées et non livrées par mois et année 

import pandas as pd

import matplotlib.pyplot as plt



\# Vérification des colonnes

colonnes\_requises = \['id\_temps', 'nbr\_commande\_livrees', 'nbr\_commande\_non\_livrees', 'mois\_annee']

colonnes\_manquantes = \[col for col in colonnes\_requises if col not in dataset.columns]



if colonnes\_manquantes:

&nbsp;   fig, ax = plt.subplots(figsize=(10, 2))

&nbsp;   ax.text(0.5, 0.5, f"Glissez ces colonnes:\\n{', '.join(colonnes\_manquantes)}", 

&nbsp;           ha='center', va='center', fontsize=12, color='red',

&nbsp;           bbox=dict(boxstyle="round,pad=0.5", facecolor="yellow", alpha=0.7))

&nbsp;   ax.axis('off')

&nbsp;   plt.show()

&nbsp;   

else:

&nbsp;   # Agrégation par mois

&nbsp;   commandes\_par\_mois = dataset.groupby('mois\_annee').agg({

&nbsp;       'nbr\_commande\_livrees': 'sum',

&nbsp;       'nbr\_commande\_non\_livrees': 'sum'

&nbsp;   }).reset\_index()

&nbsp;   

&nbsp;   commandes\_par\_mois\['total\_commandes'] = (

&nbsp;       commandes\_par\_mois\['nbr\_commande\_livrees'] + 

&nbsp;       commandes\_par\_mois\['nbr\_commande\_non\_livrees']

&nbsp;   )

&nbsp;   

&nbsp;   # Trier par date

&nbsp;   try:

&nbsp;       commandes\_par\_mois\['date\_sort'] = pd.to\_datetime(

&nbsp;           commandes\_par\_mois\['mois\_annee'] + '/01', 

&nbsp;           format='%m/%Y/%d'

&nbsp;       )

&nbsp;       commandes\_par\_mois = commandes\_par\_mois.sort\_values('date\_sort')

&nbsp;   except:

&nbsp;       commandes\_par\_mois = commandes\_par\_mois.sort\_values('mois\_annee')

&nbsp;   

&nbsp;   # Création du graphique

&nbsp;   fig, ax = plt.subplots(figsize=(14, 7))

&nbsp;   

&nbsp;   x = range(len(commandes\_par\_mois))

&nbsp;   mois\_labels = commandes\_par\_mois\['mois\_annee'].tolist()

&nbsp;   

&nbsp;   # Barres empilées

&nbsp;   ax.bar(x, commandes\_par\_mois\['nbr\_commande\_livrees'], 

&nbsp;          label='Commandes Livrées', color='green', alpha=0.7)

&nbsp;   ax.bar(x, commandes\_par\_mois\['nbr\_commande\_non\_livrees'],

&nbsp;          bottom=commandes\_par\_mois\['nbr\_commande\_livrees'],

&nbsp;          label='Commandes Non Livrées', color='red', alpha=0.7)

&nbsp;   

&nbsp;   ax.set\_xlabel('Mois', fontsize=12)

&nbsp;   ax.set\_ylabel('Nombre de Commandes', fontsize=12)

&nbsp;   ax.set\_title(' VOLUME DES COMMANDES PAR MOIS', fontsize=14, fontweight='bold')

&nbsp;   ax.set\_xticks(x)

&nbsp;   ax.set\_xticklabels(mois\_labels, rotation=45, ha='right')

&nbsp;   ax.legend()

&nbsp;   ax.grid(True, alpha=0.3)

&nbsp;   

&nbsp;   # Ajouter les totaux au-dessus des barres

&nbsp;   for i, total in enumerate(commandes\_par\_mois\['total\_commandes']):

&nbsp;       ax.text(i, total + (total\*0.01), f'{total:,}', 

&nbsp;               ha='center', va='bottom', fontsize=9)

&nbsp;   

&nbsp;   # Statistiques

&nbsp;   total\_periode = commandes\_par\_mois\['total\_commandes'].sum()

&nbsp;   stats\_text = f"Total période: {total\_periode:,} commandes"

&nbsp;   

&nbsp;   plt.figtext(0.02, 0.02, stats\_text, fontsize=10,

&nbsp;               bbox=dict(boxstyle="round,pad=0.5", facecolor="lightgray", alpha=0.8))

&nbsp;   

&nbsp;   plt.tight\_layout()

&nbsp;   plt.show()



#### 2-Nombre de commandes livrées pour le top 10 des clients par company name

import pandas as pd

import matplotlib.pyplot as plt



\# Grouper par client et sommer les commandes livrées

df = dataset.groupby('CompanyName')\['nbr\_commande\_livrees'].sum().nlargest(10)



plt.figure(figsize=(8,6))

df.sort\_values().plot(kind='barh', color='skyblue')

plt.title("Top 10 Clients par commandes livrées")

plt.xlabel("Nombre de commandes livrées")

plt.tight\_layout()

plt.show()





#### 3-Nombre de commandes livrées pour le top 5 des territoires 

import pandas as pd

import matplotlib.pyplot as plt



df = dataset.groupby('TerritoryDescri')\['nbr\_commande\_livrees'].sum().nlargest(5)

plt.figure(figsize=(8,6))

df.sort\_values().plot(kind='barh', color='purple')

plt.title("Top 5 Territoires par commandes livrées")

plt.xlabel("Nombre de commandes")

plt.tight\_layout()

plt.show()





#### 4-Heatmap pour le nombre de commandes livrées pour le top 20 des clients et le top 9 des employés  

import pandas as pd

import matplotlib.pyplot as plt

import numpy as np



\# Créer nom complet de l'employé

dataset\['Employe'] = dataset\['Nom'] + " " + dataset\['Prenom']



\# Calcul total commandes par client

total\_client = dataset.groupby('CompanyName')\['nbr\_commande\_livrees'].sum()



\# Top 20 clients

top\_clients = total\_client.nlargest(20).index

df\_top = dataset\[dataset\['CompanyName'].isin(top\_clients)]



\# Pivot table : clients en lignes, employés en colonnes

df\_pivot = df\_top.pivot\_table(index='CompanyName', columns='Employe', values='nbr\_commande\_livrees', aggfunc='sum', fill\_value=0)



plt.figure(figsize=(12,6))

plt.imshow(df\_pivot, cmap='YlGnBu', aspect='auto')

plt.colorbar(label='Commandes livrées')

plt.xticks(range(len(df\_pivot.columns)), df\_pivot.columns, rotation=90)

plt.yticks(range(len(df\_pivot.index)), df\_pivot.index)

plt.title("Heatmap : Top 20 Clients x top 9 Employés")

plt.tight\_layout()

plt.show()





#### 5-Nombre de commandes livrées par region 

import pandas as pd

import matplotlib.pyplot as plt



df = dataset.groupby('Region')\['nbr\_commande\_livrees'].sum()



plt.figure(figsize=(7,7))

plt.pie(df.values, labels=df.index, autopct='%1.1f%%', colors=plt.cm.Paired.colors)

plt.title("Répartition des commandes livrées par region")

plt.tight\_layout()

plt.show()





#### 6-L'evolution du nombre de commandes livrées et non livrées dans le temps(par mois et année)

import pandas as pd

import matplotlib.pyplot as plt



\# Calculer le total des commandes par mois

df = dataset.groupby('mois\_annee')\[\['nbr\_commande\_livrees','nbr\_commande\_non\_livrees']].sum()

df = df.sort\_index()  # trier par mois



plt.figure(figsize=(10,6))

plt.plot(df.index, df\['nbr\_commande\_livrees'], marker='o', linestyle='-', color='green', label='Livrées')

plt.plot(df.index, df\['nbr\_commande\_non\_livrees'], marker='o', linestyle='--', color='red', label='Non-livrées')

plt.title("Évolution mensuelle des commandes livrées et non-livrées")

plt.xlabel("Mois")

plt.ylabel("Nombre de commandes")

plt.xticks(rotation=45)

plt.legend()

plt.tight\_layout()

plt.show()

&nbsp;

