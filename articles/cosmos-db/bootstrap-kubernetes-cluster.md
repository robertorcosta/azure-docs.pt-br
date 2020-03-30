---
title: Como usar o Kubernetes do Azure com o Azure Cosmos DB
description: Saiba como inicializar um cluster Kubernetes no Azure que usa o Azure Cosmos DB (versão prévia)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093720"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Como usar o Kubernetes do Azure com o Azure Cosmos DB (versão prévia)

A API do etcd no Azure Cosmos DB permite que você use o Azure Cosmos DB como armazenamento de back-end para Kubernetes do Azure. O Azure Cosmos DB implementa o protocolo de transmissão etcd, que permite que os servidores de API do nó mestre usem o Azure Cosmos DB exatamente como ele acessaria um etcd instalado localmente. A API do etcd no Azure Cosmos DB está atualmente em versão prévia. Quando você usa a API do etcd do Azure Cosmos como repositório de backup para o Kubernetes, obtém os seguintes benefícios: 

* Não há necessidade de configurar e gerenciar etcd manualmente.
* Alta disponibilidade de etcd, garantida pelo Cosmos (99,99% em única região, 99,999% em várias regiões).
* Escalabilidade elástica do etcd.
* Seguro por padrão e pronto para empresas.
* SLAs abrangentes líderes do setor.

Para saber mais sobre a API do etcd no Azure Cosmos DB, veja o artigo de [visão geral](etcd-api-introduction.md). Esse artigo mostra como usar o [Mecanismo de Kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) para inicializar um cluster Kubernetes no Azure que usa o [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), em vez de um etcd instalado e configurado localmente. 

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Você pode baixar a CLI do Azure específica para seu sistema operacional e instalar.

1. Instale a [última versão](https://github.com/Azure/aks-engine/releases) do Mecanismo de Kubernetes do Azure. As instruções de instalação para diferentes sistemas operacionais estão disponíveis na página [Mecanismo de Kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine). Você só precisa das etapas da **seção Instalar o Motor AKS** do doc vinculado. Após o download, extraia o arquivo zip.

   O Mecanismo do Kubernetes do Azure (**aks-engine**) gera os modelos do Azure Resource Manager para clusters do Kubernetes no Azure. A entrada para o aks-engine é um arquivo de definição de cluster que descreve o cluster desejado, incluindo orquestrador, recursos e agentes. A estrutura dos arquivos de entrada é semelhante à API pública do Serviço de Kubernetes do Azure.

1. A API do etcd no Azure Cosmos DB está atualmente em versão prévia. Inscreva-se para usar a versão prévia em: https://aka.ms/cosmosetcdapi-signup. Depois de enviar o formulário, sua assinatura será colocada na lista de permissões para usar a API do etcd do Azure Cosmos. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Implantar o cluster com o Azure Cosmos DB

1. Abra uma janela de prompt de comando e entre no Azure com o seguinte comando:

   ```azurecli-interactive
   az login 
   ```

1. Se você tiver mais de uma assinatura, alterne para a assinatura que foi colocada na lista de permissões da API do etcd do Azure Cosmos DB. Você pode alternar para a assinatura necessária usando o seguinte comando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Em seguida, crie um novo grupo de recursos no qual você implantará os recursos exigidos pelo cluster do Kubernetes do Azure. Crie o grupo de recursos na região "centralus". Não é obrigatório que o grupo de recursos esteja na região "centralus", no entanto, a API do etcd do Azure Cosmos atualmente está disponível apenas para implantação na região de "centralus". Portanto, é melhor que um cluster Kubernetes seja colocado com a instância etcd do Cosmos:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Em seguida, crie uma entidade de serviço para o cluster Kubernetes do Azure para que ele possa se comunicar com os recursos que fazem parte do mesmo grupo de recursos. Você pode criar uma entidade de serviço usando a CLI do Azure, o PowerShell ou o portal do Azure; neste exemplo, você usará a CLI para criá-lo.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Esse comando gera os detalhes de uma entidade de serviço, por exemplo:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Tome nota dos campos **appId** e **senha**, pois você usará esses parâmetros nas próximas etapas. 

1. No prompt de comando, vá até a pasta na qual se encontra o executável do Mecanismo de Kubernetes do Azure. Por exemplo, no prompt de comando, você pode navegar até a pasta como:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Abra um editor de texto de sua escolha e defina um modelo do Resource Manager que implante o cluster Kubernetes do Azure com a API do etcd do Azure Cosmos DB. Copie a seguinte definição do JSON para o seu editor de texto e salve o arquivo como `apiModel.json`:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   No arquivo de definição JSON/cluster, o parâmetro chave a observar é **"cosmosEtcd": true**. Esse parâmetro está nas propriedades de "masterProfile" e indica para implantação usar a API do etcd do Azure Cosmos, em vez do etcd normal. 

1. Implante o cluster Kubernetes do Azure que usa o Azure Cosmos DB com o seguinte comando:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   O Mecanismo de Kubernetes do Azure consome uma definição de cluster que descreve a forma, o tamanho e a configuração desejados do Kubernetes do Azure. Vários recursos podem ser habilitados por meio da definição do cluster. Neste exemplo, você usará os seguintes parâmetros:

   * **id de assinatura:** ID de assinatura do Azure que tem Azure Cosmos DB etcd API ativado.
   * **id do cliente:** O diretor do serviço está aplique. O `appId` foi retornado como saída na etapa 4.
   * **Cliente-segredo:** A senha do diretor do serviço ou uma senha gerada aleatoriamente. Esse valor foi retornado como saída no parâmetro 'senha' na etapa 4. 
   * **dnsPrefix:** Um nome DNS único na região. Esse valor fará parte do nome do host (valores de exemplo são -myprod1, staging).
   * **localização:**  Local para onde o cluster deve ser implantado, atualmente apenas "centralus" é suportado.

   > [!Note]
   > A API do etcd do Azure Cosmos está disponível atualmente para implantação na região de "centralus" apenas. 
 
   * **modelo api:** Caminho totalmente qualificado para o arquivo de modelo.
   * **sobrepor a força:** Esta opção é usada para substituir automaticamente os arquivos existentes no diretório de saída.
 
   O comando a seguir mostra um exemplo de implantação:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Verificar a implantação

A implantação de modelo leva vários minutos para ser concluída. Depois que a implantação for concluída com êxito, você vê a saída a seguir no prompt de comandos:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

O grupo de recursos agora contém recursos, como máquina virtual, conta do Azure Cosmos (API do etcd), rede virtual, conjunto de disponibilidade e outros recursos exigidos pelo cluster do Kubernetes. 

O nome da conta do Cosmos do Azure será compatível com seu prefixo de DNS especificado acrescentado com k8s. Sua conta do Azure Cosmos será provisionada automaticamente com um banco de dados denominado **EtcdDB** e um contêiner denominado **EtcdData**. O contêiner armazenará todos os dados relacionados a etcd. O contêiner é provisionado com um determinado número de unidades de solicitação e você pode [dimensionar (aumentar/diminuir) a taxa de transferência](scaling-throughput.md) com base em sua carga de trabalho. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [trabalhar com banco de dados, contêineres e itens do Azure Cosmos](databases-containers-items.md)
* Aprenda a [Otimizar o custo da taxa de transferência provisionada](optimize-cost-throughput.md)

