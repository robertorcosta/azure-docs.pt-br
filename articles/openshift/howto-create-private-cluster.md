---
title: Crie um cluster privado com o Azure Red Hat OpenShift 3.11 | Microsoft Docs
description: Crie um cluster privado com o Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, cluster privado, chapéu vermelho
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399413"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Crie um cluster privado com o Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Azure Red Hat OpenShift (ARO) clusters privados estão atualmente disponíveis apenas em pré-visualização privada no Leste dos EUA 2. A aceitação de pré-visualização privada é apenas por convite. Certifique-se de registrar sua assinatura antes de tentar habilitar esse recurso.

Os clusters privados oferecem os seguintes benefícios:

* Clusters privados não expõem componentes do plano de controle de cluster (como os servidores de API) em um endereço IP público.
* A rede virtual de um cluster privado é configurável pelos clientes, permitindo que você configure rede para permitir peering com outras redes virtuais, incluindo ambientes ExpressRoute. Você também pode configurar DNS personalizado na rede virtual para se integrar com serviços internos.

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este recurso requer a versão 2019-10-27-preview da API ARO HTTP. Ainda não é suportado na CLI azure.

Os campos no seguinte trecho de configuração são novos e devem ser incluídos na configuração do cluster. `managementSubnetCidr`deve estar dentro da rede virtual de cluster e é usado pelo Azure para gerenciar o cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Um cluster privado pode ser implantado usando os scripts de amostra fornecidos abaixo. Assim que o cluster `cluster get` for implantado, `properties.FQDN` execute o comando e visualize a propriedade para determinar o endereço IP privado do servidor API Do OpenShift.

A rede virtual de cluster será criada com permissões para que você possa modificá-la. Em seguida, você pode configurar a rede para acessar a rede virtual (ExpressRoute, VPN, peering de rede virtual) conforme necessário para suas necessidades.

Se você alterar os servidores de nomes DNS na rede virtual do cluster, `properties.RefreshCluster` então você `true` precisará emitir uma atualização no cluster com a propriedade definida para que as VMs possam ser reimagemdas. Esta atualização permitirá que eles peguem os novos servidores de nomes.

## <a name="sample-configuration-scripts"></a>Scripts de configuração de exemplo

Use os scripts de amostra nesta seção para configurar e implantar seu cluster privado.

### <a name="environment"></a>Ambiente

Preencha as variáveis de ambiente abaixo como usando seus próprios valores.

> [!NOTE]
> O local deve `eastus2` ser definido como este é atualmente o único local suportado para clusters privados.

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

### <a name="private-clusterjson"></a>private-cluster.json

Usando as variáveis de ambiente definidas acima, aqui está uma configuração de cluster de amostra com cluster privado ativado.

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

Depois de configurar seu cluster privado com os scripts de amostra acima, execute o seguinte comando para implantar seu cluster privado.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Próximas etapas

Para saber como acessar o console OpenShift, consulte [O Passo a Passo do Console da Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
