---
title: Visão geral da implantação - Avere vFXT for Azure
description: Visão geral da implantação do Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255414"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral da implantação

Este artigo fornece uma visão geral das etapas necessárias para ativar e executar um cluster do Avere vFXT para Azure.

Várias tarefas são necessárias antes e depois de criar o cluster vFXT do Azure Marketplace. Ter uma noção clara do processo de início ao fim irá ajudá-lo a definir o esforço necessário. 

## <a name="deployment-steps"></a>Etapas de implantação.

Após [planejar seu sistema](avere-vfxt-deploy-plan.md), você pode começar a criar seu cluster Avere vFXT. 

Um modelo do Azure Resource Manager no Azure Marketplace coleta as informações necessárias e implanta automaticamente todo o cluster. 

Depois que o cluster vFXT estiver em execução, você precisará saber como conectar clientes a ele e, se necessário, como mover seus dados para o novo contêiner de armazenamento do Blob.  

Aqui está uma visão geral de todas as etapas.

1. Configurar pré-requisitos 

   Antes de criar uma VM, você deve criar uma nova assinatura para o projeto Avere vFXT, configurar a propriedade da assinatura, verificar cotas e solicitar um aumento, se necessário, e aceitar os termos para usar o software Avere vFXT. Leia [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para instruções detalhadas.

1. Crie o cluster Avere vFXT 

   Use o Azure Marketplace para criar o cluster Avere vFXT para Azure. Um modelo coleta as informações necessárias e executa scripts para criar o produto final.

   A criação do cluster envolve estas etapas, que são todas realizadas pelo modelo do marketplace: 

   * Criar nova infraestrutura de rede e grupos de recursos se necessário
   * Criar um *controlador do cluster*  

     O controlador de cluster é uma VM simples do que reside na mesma rede virtual que o cluster do Avere vFXT e tem o software personalizado necessário para criar e gerenciar o cluster. O controlador cria os nós vFXT e forma o cluster e também fornece uma interface de linha de comandos para gerenciar o cluster durante sua existência.

     Se você criar uma nova vnet durante a implantação, o controlador terá um endereço IP público. Isso significa que o controlador pode servir como um host de salto para se conectar ao cluster de fora da vnet.

   * Criar as VMs do nó de cluster

   * Configurando as VMs do nó de cluster para formar o cluster

   * Opcionalmente, criar um novo contêiner de Blob e configurá-lo como um armazenamento de back-end para o cluster

1. Configurar o cluster 

   Conecte-se à interface de configuração do Avere vFXT (Avere Control Panel) para personalizar as configurações do cluster. Opte pelo monitoramento de suporte e adicione seu sistema de armazenamento se você estiver usando um datacenter local.

   * [Acessar o cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Habilitar o suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Clientes de montagem

   Siga as diretrizes em [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md) para aprender sobre o balanceamento de carga e como as máquinas clientes devem montar o cluster.

1. Adicione dados (se necessário)

   Como o Avere vFXT é um cache escalonável de vários clientes, a melhor maneira de mover dados para um novo contêiner de armazenamento de back-end é com a estratégia de cópia de arquivos multithread e de vários clientes. Leia [Mover dados para o cluster vFXT](avere-vfxt-data-ingest.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

Continue em [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para concluir as tarefas preliminares para implantar o Avere vFXT para Azure. 