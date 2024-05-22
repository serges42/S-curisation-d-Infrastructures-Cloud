# Projet de Sécurisation d'Infrastructures Cloud : Configuration Sécurisée des Services Azure

## Objectif du Projet
L’objectif de ce projet est de démontrer comment sécuriser une infrastructure cloud hébergée sur Microsoft Azure. Nous allons configurer plusieurs services Azure de manière sécurisée en suivant les meilleures pratiques de sécurité.

## Problématique
La sécurisation des infrastructures cloud est cruciale pour protéger les données sensibles et prévenir les accès non autorisés. Les configurations par défaut peuvent souvent être insuffisantes, et il est nécessaire de mettre en place des mesures de sécurité supplémentaires.

## Solution


# 1. Guide de Création d'un VNet avec des Sous-Réseaux Publics et Privés

Configuration d'un Réseau Virtuel (VNet) Sécurisé
[Utiliser le Portail Azure pour créer un réseau virtuel](https://learn.microsoft.com/fr-fr/azure/virtual-network/quick-create-portal)
- **Description:** Création d'un VNet avec sous-réseaux publics et privés.
- **Commandes Azure CLI:**
    ```plaintext
    az network vnet create --name myVnet --resource-group myResourceGroup --address-prefix 10.0.0.0/16
    az network vnet subnet create --address-prefix 10.0.1.0/24 --name mySubnet --vnet-name myVnet --resource-group myResourceGroup
    ```

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


# 2. Guide de Configuration des Groupes de Sécurité Réseau (NSG) pour Restreindre l'Accès aux Machines Virtuelles

 Configuration des Groupes de Sécurité Réseau (NSG)
- **Description:** Définition des règles NSG pour restreindre l'accès aux machines virtuelles.
- **Commandes Azure CLI:**
    ```plaintext
    az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
    az network nsg rule create --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --name AllowSSH --protocol tcp --priority 1000 --destination-port-range 22 --access Allow
    ```

## Étape 1 : Créer un Groupe de Sécurité Réseau (NSG)

### 1. Se connecter au portail Azure
- Allez sur le [portail Azure](https://portal.azure.com) et connectez-vous avec vos informations d'identification.

### 2. Accéder à la création d'un NSG
- Dans le menu de gauche, cliquez sur `Créer une ressource`.
- Recherchez `Groupe de Sécurité Réseau` et sélectionnez `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-create.png)

### 3. Configurer les paramètres du NSG
- Remplissez les champs nécessaires :
  - Nom : `MonNSG`
  - Groupe de ressources : `MonGroupeDeRessources`
  - Région : Choisissez votre région
- Cliquez sur `Revoir + créer`, puis `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-create-settings.png)

## Étape 2 : Configurer les règles de sécurité

### 1. Accéder à votre NSG
- Dans le menu de gauche, cliquez sur `Groupes de sécurité réseau`, puis sélectionnez `MonNSG`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-select.png)

### 2. Ajouter une règle de sécurité entrante pour SSH (port 22)
- Cliquez sur `Règles de sécurité entrantes`, puis sur `+ Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule.png)

- Configurez les paramètres de la règle :
  - Nom : `AutoriserSSH`
  - Priorité : `1000`
  - Source : `Any`
  - Plage de ports source : `*`
  - Destination : `Any`
  - Plage de ports de destination : `22`
  - Protocole : `TCP`
  - Action : `Allow`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule-settings.png)

### 3. Ajouter une règle de sécurité entrante pour HTTP (port 80)
- Cliquez sur `Règles de sécurité entrantes`, puis sur `+ Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule.png)

- Configurez les paramètres de la règle :
  - Nom : `AutoriserHTTP`
  - Priorité : `2000`
  - Source : `Any`
  - Plage de ports source : `*`
  - Destination : `Any`
  - Plage de ports de destination : `80`
  - Protocole : `TCP`
  - Action : `Allow`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule-settings.png)

### 4. Ajouter une règle de sécurité entrante pour RDP (port 3389)
- Cliquez sur `Règles de sécurité entrantes`, puis sur `+ Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule.png)

- Configurez les paramètres de la règle :
  - Nom : `AutoriserRDP`
  - Priorité : `3000`
  - Source : `Any`
  - Plage de ports source : `*`
  - Destination : `Any`
  - Plage de ports de destination : `3389`
  - Protocole : `TCP`
  - Action : `Allow`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule-settings.png)

### 5. Ajouter une règle de sécurité entrante pour interdire tout autre trafic
- Cliquez sur `Règles de sécurité entrantes`, puis sur `+ Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule.png)

- Configurez les paramètres de la règle :
  - Nom : `DenyAllInbound`
  - Priorité : `4096`
  - Source : `Any`
  - Plage de ports source : `*`
  - Destination : `Any`
  - Plage de ports de destination : `*`
  - Protocole : `Any`
  - Action : `Deny`
- Cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-add-inbound-rule-settings.png)

## Étape 3 : Associer le NSG à un sous-réseau ou à une interface réseau

### 1. Associer à un sous-réseau
- Accédez à `MonVNet`, puis sélectionnez `Sous-réseaux`.
- Sélectionnez `SousReseauPublic` ou `SousReseauPrive`.
- Cliquez sur `Groupe de sécurité réseau` et sélectionnez `MonNSG`.
- Cliquez sur `Enregistrer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-attach-subnet.png)

### 2. Associer à une interface réseau
- Accédez à `Machines virtuelles`, sélectionnez votre VM, puis cliquez sur `Réseau`.
- Sélectionnez l'interface réseau associée à la VM.
- Cliquez sur `Groupes de sécurité réseau`, puis sélectionnez `MonNSG`.
- Cliquez sur `Enregistrer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-filter-network-traffic/nsg-attach-nic.png)

## Étape 4 : Vérification et tests

### 1. Vérifiez que les règles de sécurité sont correctement appliquées en testant la connectivité des ressources selon les règles définies dans le NSG.

## Conclusion

Ces étapes vous permettent de configurer un Groupe de Sécurité Réseau (NSG) dans Azure et de définir des règles pour restreindre l'accès aux machines virtuelles. Cela garantit une sécurité renforcée et un contrôle strict de l'accès au réseau.



# 3. Utilisation de Azure Active Directory (AAD)

 Utilisation de Azure Active Directory (AAD)
- **Description:** Création de rôles et d’utilisateurs avec des permissions minimales nécessaires. Mise en place de l'authentification multi-facteurs (MFA) pour les utilisateurs.
- **Commandes Azure CLI:**
    ```plaintext
    az ad user create --display-name "John Doe" --password Password123 --user-principal-name john.doe@mydomain.com
    az ad group create --display-name "Developers" --mail-nickname "developers"
    az ad group member add --group "Developers" --member-id $(az ad user show --id john.doe@mydomain.com --query objectId -o tsv)
    ```

## Étape 1 : Créer un utilisateur dans Azure Active Directory

### 1. Se connecter au portail Azure
- Allez sur le [portail Azure](https://portal.azure.com) et connectez-vous avec vos informations d'identification.

### 2. Accéder à Azure Active Directory
- Dans le menu de gauche, cliquez sur `Azure Active Directory`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-create-user/aad-users-1.png)

### 3. Créer un nouvel utilisateur
- Dans le menu de gauche d'AAD, cliquez sur `Utilisateurs`, puis sur `+ Nouveau utilisateur`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-create-user/aad-users-2.png)

- Remplissez les informations de l'utilisateur :
  - Nom : `NouvelUtilisateur`
  - Nom d'utilisateur : `nouvelutilisateur@votre_domaine.onmicrosoft.com`
  - Profil : Configurez les informations de profil si nécessaire.
  - Cliquez sur `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-create-user/aad-users-3.png)

## Étape 2 : Créer un rôle personnalisé avec des permissions minimales

### 1. Accéder à la gestion des rôles
- Dans le menu de gauche d'AAD, cliquez sur `Rôles et administrateurs`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-custom-role/role-management.png)

### 2. Créer un nouveau rôle
- Cliquez sur `+ Nouveau rôle personnalisé`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-custom-role/new-custom-role.png)

- Configurez les informations du rôle :
  - Nom : `RoleMinimal`
  - Description : `Rôle avec les permissions minimales nécessaires`
  - Cliquez sur `Suivant`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-custom-role/custom-role-creation.png)

### 3. Ajouter des permissions au rôle
- Cliquez sur `Ajouter des autorisations`.
- Recherchez et sélectionnez les autorisations minimales nécessaires pour ce rôle.
- Cliquez sur `Ajouter`, puis sur `Suivant`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-custom-role/add-permissions.png)

### 4. Attribuer le rôle à un utilisateur
- Dans le menu de gauche d'AAD, cliquez sur `Utilisateurs`.
- Sélectionnez `NouvelUtilisateur`.
- Cliquez sur `Affectations de rôles`, puis sur `+ Ajouter une affectation`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-assign-role/assign-role.png)

- Sélectionnez `RoleMinimal` et cliquez sur `Ajouter`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/media/tutorial-assign-role/assign-role-select.png)

## Étape 3 : Mise en place de l'authentification multi-facteurs (MFA)

### 1. Accéder aux paramètres MFA
- Dans le menu de gauche d'AAD, cliquez sur `Sécurité`, puis sur `Authentification multifacteur`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-mfa-getstarted/mfa-settings.png)

### 2. Activer MFA pour un utilisateur
- Cliquez sur `Utilisateurs`, puis sur `Activer MFA`.
- Recherchez `NouvelUtilisateur` et sélectionnez-le.
- Cliquez sur `Activer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-mfa-userstates/mfa-enable.png)

### 3. Configurer MFA pour l'utilisateur
- L'utilisateur doit maintenant se connecter et suivre les instructions pour configurer MFA (par exemple, via une application d'authentification ou un SMS).

![Capture d'écran](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-mfa-userstates/mfa-user-setup.png)

## Conclusion

Ces étapes vous permettent de créer des utilisateurs et des rôles avec des permissions minimales dans Azure Active Directory, ainsi que de configurer l'authentification multi-facteurs (MFA) pour renforcer la sécurité des comptes utilisateurs.




# 4. Configuration de Blob Storage avec des Politiques de Sécurité

 Configuration de Blob Storage avec des Politiques de Sécurité
- **Description:** Mise en place de politiques de conteneur pour restreindre l'accès. Activation du chiffrement côté serveur pour les objets stockés.
- **Commandes Azure CLI:**
    ```plaintext
    az storage account create --name mystorageaccount --resource-group myResourceGroup --location eastus --sku Standard_LRS
    az storage container create --name mycontainer --account-name mystorageaccount --public-access off
    az storage account update --name mystorageaccount --resource-group myResourceGroup --default-action Deny
    ```

## Étape 1 : Créer un compte de stockage

### 1. Se connecter au portail Azure
- Allez sur le [portail Azure](https://portal.azure.com) et connectez-vous avec vos informations d'identification.

### 2. Accéder à la création d'un compte de stockage
- Dans le menu de gauche, cliquez sur `Créer une ressource`.
- Recherchez `Compte de stockage` et sélectionnez `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/media/common/storage-account-create.png)

### 3. Configurer les paramètres du compte de stockage
- Remplissez les champs nécessaires :
  - Nom : `MonCompteDeStockage`
  - Groupe de ressources : `MonGroupeDeRessources`
  - Région : Choisissez votre région
  - Performances : `Standard`
  - Réplication : `LRS (Locally-redundant storage)`
- Cliquez sur `Revoir + créer`, puis `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/media/common/storage-account-create-settings.png)

## Étape 2 : Créer un conteneur dans le compte de stockage

### 1. Accéder au compte de stockage
- Dans le menu de gauche, cliquez sur `Comptes de stockage`, puis sélectionnez `MonCompteDeStockage`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/media/common/storage-account-overview.png)

### 2. Créer un conteneur
- Dans le menu de gauche du compte de stockage, cliquez sur `Conteneurs`, puis sur `+ Conteneur`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/blobs/media/blob-storage-quickstart/blobs-portal-quickstart-container.png)

- Remplissez les informations du conteneur :
  - Nom : `MonConteneur`
  - Niveau d'accès public : `Privé (aucun accès anonyme)`
- Cliquez sur `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/blobs/media/blob-storage-quickstart/blobs-portal-create-container.png)

## Étape 3 : Mettre en place des politiques de conteneur pour restreindre l'accès

### 1. Configurer les politiques d'accès
- Sélectionnez `MonConteneur` dans la liste des conteneurs.
- Cliquez sur `Politiques d'accès`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/blobs/media/storage-blob-container-policy/choose-access-policy.png)

### 2. Ajouter une politique d'accès
- Cliquez sur `Ajouter une politique`.
- Configurez les paramètres :
  - Nom de la politique : `PolitiqueRestreinte`
  - Heure de début et heure de fin : Définissez les dates et heures appropriées pour la validité de la politique.
  - Permissions : Sélectionnez les permissions nécessaires (ex. Lecture, Écriture, Liste).
- Cliquez sur `OK`, puis `Enregistrer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/blobs/media/storage-blob-container-policy/add-policy.png)

## Étape 4 : Activer le chiffrement côté serveur

### 1. Accéder aux paramètres du compte de stockage
- Dans le menu de gauche du compte de stockage, cliquez sur `Chiffrement`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/media/storage-encryption/storage-encryption.png)

### 2. Activer le chiffrement côté serveur
- Vérifiez que le chiffrement côté serveur avec des clés gérées par Microsoft est activé par défaut.
- Si vous souhaitez utiliser des clés gérées par le client, configurez cette option et téléchargez ou créez une clé dans Azure Key Vault.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/storage/media/storage-encryption/storage-encryption-customer-key.png)

## Conclusion

Ces étapes vous permettent de configurer un Blob Storage sécurisé dans Azure, en définissant des politiques d'accès pour restreindre l'accès aux conteneurs et en activant le chiffrement côté serveur pour les objets stockés, assurant ainsi une sécurité renforcée des données.





# 5. Mise en Place d'Azure Monitor pour la Surveillance

 Mise en Place d'Azure Monitor pour la Surveillance
- **Description:** Configuration des alertes et des logs pour surveiller l'activité des services.
- **Commandes Azure CLI:**
    ```plaintext
    az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myWorkspace
    az monitor diagnostic-settings create --resource-id $(az storage account show --name mystorageaccount --query id -o tsv) --workspace myWorkspace --logs '[{"category": "StorageRead", "enabled": true, "retentionPolicy": {"enabled": false}}, {"category": "StorageWrite", "enabled": true, "retentionPolicy": {"enabled": false}}]'
    ```

## Étape 1 : Configurer Azure Monitor

### 1. Se connecter au portail Azure
- Allez sur le [portail Azure](https://portal.azure.com) et connectez-vous avec vos informations d'identification.

### 2. Accéder à Azure Monitor
- Dans le menu de gauche, cliquez sur `Monitor`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/media/azure-monitor/monitor-overview.png)

## Étape 2 : Créer un espace de travail Log Analytics

### 1. Accéder à la création d'un espace de travail
- Dans le menu de gauche d'Azure Monitor, cliquez sur `Log Analytics workspaces`, puis sur `+ Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/log-analytics-create.png)

### 2. Configurer les paramètres de l'espace de travail
- Remplissez les champs nécessaires :
  - Nom : `MonEspaceDeTravail`
  - Groupe de ressources : `MonGroupeDeRessources`
  - Région : Choisissez votre région
- Cliquez sur `Revoir + créer`, puis `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/log-analytics-create-settings.png)

## Étape 3 : Configurer les logs pour les services

### 1. Activer le diagnostic pour un service
- Allez dans le menu de gauche, cliquez sur `Comptes de stockage` ou un autre service à surveiller.
- Sélectionnez le service souhaité, puis cliquez sur `Diagnostics settings` dans le menu de gauche.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/diagnostic-settings.png)

### 2. Ajouter un paramètre de diagnostic
- Cliquez sur `+ Add diagnostic setting`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/add-diagnostic-settings.png)

- Configurez les paramètres :
  - Nom : `MonDiagnostic`
  - Logs : Sélectionnez les types de logs à collecter (par exemple, `Read`, `Write`, `Delete`).
  - Destination : Sélectionnez `Send to Log Analytics workspace`, puis choisissez `MonEspaceDeTravail`.
- Cliquez sur `Enregistrer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/diagnostic-settings-create.png)

## Étape 4 : Créer des alertes

### 1. Accéder aux alertes
- Dans le menu de gauche d'Azure Monitor, cliquez sur `Alerts`, puis sur `+ New alert rule`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/media/platform/alert-rules-create/alerts-management.png)

### 2. Configurer une règle d'alerte
- Configurez les paramètres :
  - Scope : Sélectionnez le service ou la ressource à surveiller.
  - Condition : Cliquez sur `Add condition` et définissez la condition de l'alerte (par exemple, `Metric signal`, `Log signal`).
  - Actions : Cliquez sur `Add action group` pour définir l'action à entreprendre lorsque l'alerte se déclenche (par exemple, envoyer un email, un SMS, ou appeler une fonction Azure).
  - Details : Donnez un nom à la règle d'alerte et une description si nécessaire.
- Cliquez sur `Créer l'alerte`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/media/platform/alert-rules-create/alerts-rule-create.png)

## Étape 5 : Vérification et tests

### 1. Vérifiez que les logs sont correctement collectés
- Allez dans `Log Analytics workspaces`, sélectionnez `MonEspaceDeTravail`, puis cliquez sur `Logs`.
- Exécutez des requêtes pour vérifier la collecte des logs.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/media/log-analytics-tutorial/log-query.png)

### 2. Testez les alertes
- Provoquez des conditions qui devraient déclencher les alertes et vérifiez que les actions définies sont correctement exécutées.

## Conclusion

Ces étapes vous permettent de configurer Azure Monitor pour surveiller l'activité des services, en collectant des logs et en définissant des alertes pour assurer une surveillance proactive et réactive de votre environnement Azure.





 
# 6. Configuration de Azure SQL Database Sécurisée

Configuration de Azure SQL Database Sécurisée
- **Description:** Création d'une base de données SQL dans un sous-réseau privé. Activation de la sécurité au niveau des connexions avec SSL/TLS.
- **Commandes Azure CLI:**
    ```plaintext
    az sql server create --name mydemoserver --resource-group myResourceGroup --location eastus --admin-user myadmin --admin-password mypassword123
    az sql db create --resource-group myResourceGroup --server mydemoserver --name mySampleDatabase --service-objective S0
    az sql server firewall-rule create --resource-group myResourceGroup --server mydemoserver --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

## Étape 1 : Créer un VNet et un sous-réseau privé

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

### 4. Créer un sous-réseau privé
- Sous l'onglet `Sous-réseaux`, cliquez sur `+ Ajouter un sous-réseau`.
  - Nom : `SousReseauPrive`
  - Plage d'adresses : `10.0.1.0/24`
- Cliquez sur `Ajouter` puis sur `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/tutorial-create-vnet/vnet-create-subnet-settings.png)

## Étape 2 : Créer une Azure SQL Database dans un sous-réseau privé

### 1. Accéder à la création d'une base de données SQL
- Dans le menu de gauche, cliquez sur `Créer une ressource`.
- Recherchez `Base de données SQL` et sélectionnez `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/sql-database/media/sql-database-get-started/sql-database-create-database.png)

### 2. Configurer les paramètres de la base de données
- Remplissez les champs nécessaires :
  - Nom : `MaBaseDeDonnees`
  - Serveur : Créez un nouveau serveur ou utilisez un serveur existant.
  - Groupe de ressources : `MonGroupeDeRessources`
  - Tarification : Sélectionnez le niveau de service souhaité.
- Cliquez sur `Revoir + créer`, puis `Créer`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/sql-database/media/sql-database-get-started/sql-database-create-database-settings.png)

### 3. Configurer le point de terminaison privé
- Après la création, allez dans le menu de gauche et cliquez sur `Réseaux privés virtuels` (VNet).
- Cliquez sur `+ Ajouter un point de terminaison privé`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/virtual-network/media/virtual-network-service-endpoints/virtual-network-add-service-endpoint.png)

- Configurez les paramètres :
  - Nom : `MonPointDeTerminaisonPrivé`
  - Groupe de ressources : `MonGroupeDeRessources`
  - VNet : Sélectionnez `MonVNet`
  - Sous-réseau : Sélectionnez `SousReseauPrive`
- Cliquez sur `OK`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/sql-database/media/sql-database-private-endpoint/private-endpoint-create.png)

## Étape 3 : Activer la sécurité au niveau des connexions avec SSL/TLS

### 1. Accéder aux paramètres de la base de données SQL
- Dans le menu de gauche, cliquez sur `SQL Databases`, puis sélectionnez `MaBaseDeDonnees`.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/sql-database/media/sql-database-manage/connect-db-portal.png)

### 2. Configurer SSL/TLS
- Cliquez sur `Sécurité`, puis sur `Connexion SSL/TLS`.
- Activez l'option `Exiger SSL` pour obliger toutes les connexions à utiliser SSL.

![Capture d'écran](https://docs.microsoft.com/en-us/azure/sql-database/media/sql-database-security/sql-database-enforce-ssl.png)

- Cliquez sur `Enregistrer`.

## Étape 4 : Vérification et tests

### 1. Vérifiez que la base de données est accessible uniquement via le sous-réseau privé
- Déployez une machine virtuelle dans `SousReseauPrive` et essayez de vous connecter à la base de données.
- Vérifiez que les connexions à la base de données à partir d'autres réseaux sont refusées.

### 2. Testez les connexions sécurisées
- Utilisez un client SQL pour se connecter à la base de données en utilisant SSL/TLS et vérifiez que les connexions non sécurisées sont refusées.

## Conclusion

Ces étapes vous permettent de configurer une base de données SQL sécurisée dans Azure, en la créant dans un sous-réseau privé et en activant la sécurité au niveau des connexions avec SSL/TLS, garantissant ainsi une sécurité renforcée des données.


    

## Réflexion
Ce projet m’a permis de comprendre l'importance de la sécurisation des différentes couches d'une infrastructure cloud. J'ai appris à configurer des services Azure de manière sécurisée en suivant les meilleures pratiques. Les défis incluaient la gestion des permissions Azure Active Directory et la configuration des politiques de sécurité de Blob Storage, qui nécessitaient une attention particulière pour éviter les accès non autorisés.

---

