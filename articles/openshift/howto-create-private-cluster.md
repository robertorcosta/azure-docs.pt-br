---
title: Criar um cluster privado com o Azure Red Hat OpenShift 3,11 | Microsoft Docs
description: Criar um cluster privado com o Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: toa, openshift, cluster privado, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399413"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Criar um cluster privado com o Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Os clusters privados do Azure Red Hat OpenShift (toa) atualmente só estão disponíveis em versão prévia privada no leste dos EUA 2. A aceitação da visualização privada é apenas por convite. Certifique-se de registrar sua assinatura antes de tentar habilitar esse recurso.

Os clusters privados oferecem os seguintes benefícios:

* Os clusters privados não expõem componentes de plano de controle de cluster (como os servidores de API) em um endereço IP público.
* A rede virtual de um cluster privado é configurável por clientes, permitindo que você configure a rede para permitir o emparelhamento com outras redes virtuais, incluindo ambientes de ExpressRoute. Você também pode configurar o DNS personalizado na rede virtual para integração com os serviços internos.

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este recurso requer a versão 2019-10-27-preview da API HTTP ARO. Ainda não há suporte no CLI do Azure.

Os campos no trecho de configuração a seguir são novos e devem ser incluídos na configuração do cluster. `managementSubnetCidr` deve estar dentro da rede virtual de cluster e é usada pelo Azure para gerenciar o cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Um cluster privado pode ser implantado usando os scripts de exemplo fornecidos abaixo. Depois que o cluster for implantado, execute o comando `cluster get` e exiba a propriedade `properties.FQDN` para determinar o endereço IP privado do servidor de API OpenShift.

A rede virtual do cluster terá sido criada com permissões para que você possa modificá-la. Em seguida, você pode configurar a rede para acessar a rede virtual (ExpressRoute, VPN, emparelhamento de rede virtual) conforme necessário para suas necessidades.

Se você alterar os nameservers DNS na rede virtual do cluster, será necessário emitir uma atualização no cluster com a propriedade `properties.RefreshCluster` definida como `true` para que as VMs possam ter a imagem recriada. Essa atualização permitirá que eles escolham os novos nameservers.

## <a name="sample-configuration-scripts"></a>Scripts de configuração de exemplo

Use os scripts de exemplo nesta seção para configurar e implantar seu cluster privado.

### <a name="environment"></a>Ambiente

Preencha as variáveis de ambiente abaixo usando seus próprios valores.

> [!NOTE]
> O local deve ser definido como `eastus2`, pois este atualmente é o único local com suporte para clusters particulares.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>Private-cluster. JSON

Usando as variáveis de ambiente definidas acima, aqui está um exemplo de configuração de cluster com cluster privado habilitado.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Implantar um cluster privado

Depois de configurar seu cluster privado com os scripts de exemplo acima, execute o comando a seguir para implantar o cluster privado.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para saber mais sobre como acessar o console do OpenShift, consulte o [Guia do console Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
