---
title: Criptografar declarações de volume persistentes com uma chave gerenciada pelo cliente (CMK) no Azure Red Hat OpenShift (ARO)
description: Instruções de implantação de BYOK (traga sua própria chave)/CMK (chave gerenciada pelo cliente) para o Red Hat OpenShift do Azure
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: criptografia, byok, aro, openshift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054447"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Criptografar declarações de volume persistentes com uma chave gerenciada pelo cliente (CMK) no Azure Red Hat OpenShift (toa) (versão prévia)

O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela plataforma Microsoft que incluem o sistema operacional e OS discos de dados. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografar dados em seus clusters do Azure Red Hat OpenShift.

> [!NOTE]
> Neste estágio, o suporte existe apenas para criptografar volumes persistentes de toa com chaves gerenciadas pelo cliente. Este recurso não está disponível atualmente para discos do sistema operacional.

> [!IMPORTANT]
> Os recursos de visualização de toa estão disponíveis em uma base de autoatendimento e aceitação. As visualizações são fornecidas "como estão" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações de ARO são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção.

## <a name="before-you-begin"></a>Antes de começar
Este artigo supõe que:

* Você tem um cluster ARO já existente na versão OpenShift 4,4 (ou superior).

* Você tem a ferramenta de linha de comando OpenShift ' OC ', Base64 (parte dos principais utils) e o CLI do Azure ' az ' instalado.

* Você está conectado ao seu cluster toa usando o *OC* como um usuário administrador global do cluster (kubeadmin).

* Você está conectado ao CLI do Azure usando *AZ* com uma conta autorizada para conceder acesso "colaborador" na mesma assinatura que o cluster toa.

## <a name="limitations"></a>Limitações

* A disponibilidade para criptografia de chave gerenciada pelo cliente é específica da região. Para ver o status de uma região específica do Azure, verifique [regiões do Azure][supported-regions].
* Se você estiver usando ultra discos, habilite ultra discos em sua assinatura antes de começar.

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância de Azure Key Vault
Uma instância de Azure Key Vault deve ser usada para armazenar suas chaves. Crie um novo Key Vault com a proteção de limpeza e a exclusão reversível habilitada. Em seguida, crie uma nova chave dentro do cofre para armazenar sua própria chave personalizada:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Criar um conjunto de criptografia de disco do Azure

O conjunto de criptografia de disco do Azure é usado como o ponto de referência para discos na toa. Ele está conectado à Azure Key Vault que criamos na etapa anterior e efetuará pull de chaves gerenciadas pelo cliente desse local.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Conceder acesso ao conjunto de criptografia de disco para Key Vault
Use o conjunto de criptografia de disco que criamos nas etapas anteriores e conceda ao conjunto de criptografia de disco acesso ao Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Obter outras IDs necessárias para atribuições de função
Precisamos permitir que o cluster toa use o conjunto de criptografia de disco para criptografar as declarações de volume persistentes (PVCs) no cluster toa. Para fazer isso, criaremos um novo Identidade de Serviço Gerenciada (MSI). Também vamos definir outras permissões para o MSI da toa e para o conjunto de criptografia de disco.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementar outras atribuições de função necessárias para a criptografia BYOK/CMK
Aplique as atribuições de função necessárias usando as variáveis obtidas na etapa anterior:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Criar uma classe de armazenamento K8S para ultra discos de & Premium criptografados (opcional)
Gere classes de armazenamento a serem usadas para BYOK/CMK para os discos Premium_LRS e UltraSSD_LRS:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Configurar sua configuração de classe de armazenamento
Substitua as variáveis que são exclusivas do cluster de toa nos dois arquivos de configuração de classe de armazenamento:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Em seguida, execute esta implantação em seu cluster toa para aplicar a configuração de classe de armazenamento:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Testar a criptografia com chaves gerenciadas pelo cliente
Para verificar se o cluster está usando uma chave gerenciada pelo cliente para a criptografia de PVC, criaremos uma declaração de volume persistente usando a classe de armazenamento apropriada. O trecho de código abaixo cria um pod e monta uma declaração de volume persistente usando discos padrão
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Aplicar o arquivo de configuração do pod de teste
Execute os comandos abaixo para aplicar a configuração de pod de teste e retornar a UID da nova declaração de volume persistente. A UID será usada para verificar se o disco está criptografado usando BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Verifique se o disco do PVC está configurado com "EncryptionAtRestWithCustomerKey" 
O Pod deve criar uma declaração de volume persistente que faça referência à classe de armazenamento BYOK/CMK. A execução do comando a seguir validará que o PVC foi implantado conforme o esperado:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Próximas etapas

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

