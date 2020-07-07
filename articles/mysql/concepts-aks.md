---
title: Conectar-se ao serviço kubernetes do Azure-banco de dados do Azure para MySQL
description: Saiba como conectar o Serviço de Kubernetes do Azure ao Banco de Dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537271"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Conexão do Serviço de Kubernetes do Azure e Banco de Dados do Azure para MySQL

O Serviço do Kubernetes do Azure (AKS) fornece um cluster Kubernetes gerenciado que você pode usar no Azure. Abaixo estão algumas opções a serem consideradas ao usar o AKS e o Banco de Dados do Azure para MySQL juntos para criar um aplicativo.


## <a name="accelerated-networking"></a>Redes aceleradas
Use VMs subjacentes habilitadas para rede acelerada em seu cluster AKS. Quando a rede acelerada está habilitada em uma VM, há menor latência, menor oscilação e menor utilização da CPU na VM. Saiba mais sobre como funciona a rede acelerada, as versões de sistema operacional compatíveis e as instâncias de VM compatíveis para [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de novembro de 2018, o AKS oferece suporte à rede acelerada nessas instâncias de VM compatíveis. A rede acelerada é ativada por padrão nos novos clusters do AKS que usam essas VMs.

Você pode confirmar se o seu cluster AKS acelerou a rede:
1. Vá para o portal do Azure e selecione seu cluster AKS.
2. Selecione a guia Propriedades.
3. Copie o nome do **Grupo de Recursos de Infraestrutura**.
4. Use a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione uma VM nesse grupo de recursos.
6. Vá para a guia VM **Rede**.
7. Confirme se a **Rede acelerada** está "Ativada".

Ou por meio da CLI do Azure usando os dois comandos a seguir:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A saída será o grupo de recursos gerado que o AKS cria contendo o adaptador de rede. Pegue o nome "nodeResourceGroup" e use-o no próximo comando. **EnableAcceleratedNetworking** será true ou false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Instalar o Open Service Broker para o Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) permite provisionar serviços do Azure diretamente do Kubernetes ou do Cloud Foundry. É uma implementação do [Open Service Broker API](https://www.openservicebrokerapi.org/) para o Azure.

Com o OSBA, você pode criar um Banco de Dados do Azure para MySQL e vinculá-lo ao cluster do AKS usando o idioma nativo do Kubernetes. Saiba mais sobre como usar o OSBA e o banco de dados do Azure para MySQL juntos na [página do GitHub do OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Próximas etapas
- [Criar um cluster do Serviço de Kubernetes do Azure](../aks/kubernetes-walkthrough.md)
- Aprenda como [Instalar o WordPress a partir de um gráfico do Helm usando o OSBA e o Banco de Dados do Azure para MySQL](../aks/integrate-azure.md)
