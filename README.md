# Projet de Sécurisation d'Infrastructures Cloud : Configuration Sécurisée des Services Azure

## Objectif du Projet
L’objectif de ce projet est de démontrer comment sécuriser une infrastructure cloud hébergée sur Microsoft Azure. Nous allons configurer plusieurs services Azure de manière sécurisée en suivant les meilleures pratiques de sécurité.

## Problématique
La sécurisation des infrastructures cloud est cruciale pour protéger les données sensibles et prévenir les accès non autorisés. Les configurations par défaut peuvent souvent être insuffisantes, et il est nécessaire de mettre en place des mesures de sécurité supplémentaires.

## Solution

### 1. Configuration d'un Réseau Virtuel (VNet) Sécurisé
[Utiliser le Portail Azure pour créer un réseau virtuel](https://learn.microsoft.com/fr-fr/azure/virtual-network/quick-create-portal)
- **Description:** Création d'un VNet avec sous-réseaux publics et privés.
- **Commandes Azure CLI:**
    ```plaintext
    az network vnet create --name myVnet --resource-group myResourceGroup --address-prefix 10.0.0.0/16
    az network vnet subnet create --address-prefix 10.0.1.0/24 --name mySubnet --vnet-name myVnet --resource-group myResourceGroup
    ```
# Guide de Création d'un VNet avec des Sous-Réseaux Publics et Privés

## Étape 1 : Créer un VNet

### 1. Se connecter au portail Azure
- Allez sur le [portail Azure](https://portal.azure.com) et connectez-vous avec vos informations d'identification.

### 2. Accéder à la création d'un VNet
- Dans le menu de gauche, cliquez sur `Créer une ressource`.
- Recherchez `Réseau Virtuel` et sélectionnez `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create.png)

### 3. Configurer les paramètres du VNet
- Remplissez les champs nécessaires :
  - Nom : `MonVNet`
  - Espace d'adressage : `10.0.0.0/16`
  - Groupe de ressources : `MonGroupeDeRessources`
  - Région : Choisissez votre région

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create-basic.png)

## Étape 2 : Ajouter des sous-réseaux

### 1. Configurer les sous-réseaux lors de la création du VNet
- Sous l'onglet `Sous-réseaux`, cliquez sur `+ Ajouter un sous-réseau`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create-subnet.png)

### 2. Créer un sous-réseau public
- Nom : `SousReseauPublic`
- Plage d'adresses : `10.0.1.0/24`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create-subnet-settings.png)

### 3. Créer un sous-réseau privé
- Cliquez à nouveau sur `+ Ajouter un sous-réseau`.
- Nom : `SousReseauPrive`
- Plage d'adresses : `10.0.2.0/24`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create-subnet-settings-private.png)

### 4. Finaliser la création du VNet
- Vérifiez vos paramètres et cliquez sur `Revoir + créer`, puis `Créer`.

## Étape 3 : Configurer les tables de routage

### 1. Accéder aux tables de routage
- Dans le menu de gauche, recherchez `Table de routage` et sélectionnez `Créer une table de routage`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-route-table/route-table-create.png)

### 2. Créer une table de routage pour le sous-réseau public
- Nom : `TableDeRoutagePublic`
- Groupe de ressources : `MonGroupeDeRessources`
- Région : Choisissez votre région
- Cliquez sur `Revoir + créer`, puis `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-route-table/route-table-create-settings.png)

### 3. Créer une table de routage pour le sous-réseau privé
- Suivez les mêmes étapes pour créer une deuxième table de routage nommée `TableDeRoutagePrive`.

### 4. Ajouter des routes aux tables de routage
- Sélectionnez `TableDeRoutagePublic` et cliquez sur `Routes`, puis `Ajouter`.
- Route vers internet :
  - Nom : `RouteVersInternet`
  - Préfixe d'adresse : `0.0.0.0/0`
  - Next hop : `Passerelle Internet`
- Cliquez sur `OK`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-route-table/route-table-create-route.png)

### 5. Associer les tables de routage aux sous-réseaux
- Accédez à la table de routage publique, cliquez sur `Sous-réseaux`, puis `Associer`.
- Sélectionnez `MonVNet` et `SousReseauPublic`.
- Répétez l'opération pour `TableDeRoutagePrive` en associant `SousReseauPrive`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-route-table/route-table-attach.png)

## Étape 4 : Vérification et tests

### 1. Vérifiez que les sous-réseaux sont correctement isolés et que le routage fonctionne comme prévu en testant la connectivité des ressources déployées dans chaque sous-réseau.

## Conclusion

Ces étapes vous permettent de créer un VNet avec des sous-réseaux publics et privés dans Azure et de configurer les tables de routage pour isoler les sous-réseaux. Cela garantit une segmentation du réseau et un meilleur contrôle du trafic.

- **Capture d'écran:**
    ![Création du VNet](URL_DE_VOTRE_IMAGE_VNET)

### 2. Configuration des Groupes de Sécurité Réseau (NSG)
- **Description:** Définition des règles NSG pour restreindre l'accès aux machines virtuelles.
- **Commandes Azure CLI:**
    ```plaintext
    az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
    az network nsg rule create --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --name AllowSSH --protocol tcp --priority 1000 --destination-port-range 22 --access Allow
    ```
- **Capture d'écran:**
    ![Configuration des NSG](URL_DE_VOTRE_IMAGE_NSG)

### 3. Utilisation de Azure Active Directory (AAD)
- **Description:** Création de rôles et d’utilisateurs avec des permissions minimales nécessaires. Mise en place de l'authentification multi-facteurs (MFA) pour les utilisateurs.
- **Commandes Azure CLI:**
    ```plaintext
    az ad user create --display-name "John Doe" --password Password123 --user-principal-name john.doe@mydomain.com
    az ad group create --display-name "Developers" --mail-nickname "developers"
    az ad group member add --group "Developers" --member-id $(az ad user show --id john.doe@mydomain.com --query objectId -o tsv)
    ```
- **Capture d'écran:**
    ![Configuration AAD](URL_DE_VOTRE_IMAGE_AAD)
    ![Configuration MFA](URL_DE_VOTRE_IMAGE_MFA)

### 4. Configuration de Blob Storage avec des Politiques de Sécurité
- **Description:** Mise en place de politiques de conteneur pour restreindre l'accès. Activation du chiffrement côté serveur pour les objets stockés.
- **Commandes Azure CLI:**
    ```plaintext
    az storage account create --name mystorageaccount --resource-group myResourceGroup --location eastus --sku Standard_LRS
    az storage container create --name mycontainer --account-name mystorageaccount --public-access off
    az storage account update --name mystorageaccount --resource-group myResourceGroup --default-action Deny
    ```
- **Capture d'écran:**
    ![Configuration Blob Storage](URL_DE_VOTRE_IMAGE_BLOB_STORAGE)

### 5. Mise en Place d'Azure Monitor pour la Surveillance
- **Description:** Configuration des alertes et des logs pour surveiller l'activité des services.
- **Commandes Azure CLI:**
    ```plaintext
    az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myWorkspace
    az monitor diagnostic-settings create --resource-id $(az storage account show --name mystorageaccount --query id -o tsv) --workspace myWorkspace --logs '[{"category": "StorageRead", "enabled": true, "retentionPolicy": {"enabled": false}}, {"category": "StorageWrite", "enabled": true, "retentionPolicy": {"enabled": false}}]'
    ```
- **Capture d'écran:**
    ![Configuration Azure Monitor](URL_DE_VOTRE_IMAGE_AZURE_MONITOR)

### 6. Configuration de Azure SQL Database Sécurisée
- **Description:** Création d'une base de données SQL dans un sous-réseau privé. Activation de la sécurité au niveau des connexions avec SSL/TLS.
- **Commandes Azure CLI:**
    ```plaintext
    az sql server create --name mydemoserver --resource-group myResourceGroup --location eastus --admin-user myadmin --admin-password mypassword123
    az sql db create --resource-group myResourceGroup --server mydemoserver --name mySampleDatabase --service-objective S0
    az sql server firewall-rule create --resource-group myResourceGroup --server mydemoserver --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```
- **Capture d'écran:**
    ![Configuration Azure SQL Database](URL_DE_VOTRE_IMAGE_SQL_DATABASE)

## Réflexion
Ce projet m’a permis de comprendre l'importance de la sécurisation des différentes couches d'une infrastructure cloud. J'ai appris à configurer des services Azure de manière sécurisée en suivant les meilleures pratiques. Les défis incluaient la gestion des permissions Azure Active Directory et la configuration des politiques de sécurité de Blob Storage, qui nécessitaient une attention particulière pour éviter les accès non autorisés.

---

Pour compléter ce projet, ajoutez des captures d'écran de chaque étape mentionnée ci-dessus en utilisant Azure Portal ou CLI, et documentez vos configurations et résultats obtenus.
