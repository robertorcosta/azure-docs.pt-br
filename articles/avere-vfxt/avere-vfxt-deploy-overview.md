---
title: Visão geral da implantação - Avere vFXT for Azure
description: Visão geral da implantação do Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153676"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral da implantação

Este artigo fornece uma visão geral das etapas necessárias para ativar e executar um cluster do Avere vFXT para Azure.

Várias tarefas são necessárias antes e depois de criar o cluster vFXT do Azure Marketplace. Ter uma noção clara do processo de início ao fim irá ajudá-lo a definir o esforço necessário.

## <a name="deployment-steps"></a>Etapas de implantação.

Após [planejar seu sistema](avere-vfxt-deploy-plan.md), você pode começar a criar seu cluster Avere vFXT.

Um modelo do Azure Resource Manager no Azure Marketplace coleta as informações necessárias e implanta automaticamente todo o cluster.

Depois que o cluster vFXT estiver em funcionamento, ainda há algumas etapas de configuração a serem tomadas antes de usá-lo. Se você criou um novo contêiner de armazenamento Blob, você vai querer mover seus dados para ele. Se você usar um sistema de armazenamento NAS, você precisa adicioná-lo depois que o cluster for criado. Você vai querer conectar clientes ao cluster.

Aqui está uma visão geral de todas as etapas.

1. Configurar pré-requisitos

   Antes de criar uma VM, você deve criar uma nova assinatura para o projeto Avere vFXT, configurar a propriedade da assinatura, verificar cotas e solicitar um aumento, se necessário, e aceitar os termos para usar o software Avere vFXT. Leia [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para instruções detalhadas.

1. Crie o cluster Avere vFXT

   Use o Azure Marketplace para criar o cluster Avere vFXT para Azure. Um modelo coleta as informações necessárias e executa scripts para criar o produto final.

   A criação do cluster envolve estas etapas, que são todas realizadas pelo modelo do marketplace:

   * Crie novas infra-estruturas de rede e grupos de recursos, se necessário
   * Criar um controlador de cluster

     O controlador de cluster é uma VM simples do que reside na mesma rede virtual que o cluster do Avere vFXT e tem o software personalizado necessário para criar e gerenciar o cluster. O controlador cria os nós vFXT e forma o cluster e também fornece uma interface de linha de comandos para gerenciar o cluster durante sua existência.

     Se você criar uma nova rede virtual ou sub-rede durante a implantação, seu controlador terá um endereço IP público. Isso significa que o controlador pode servir como um host de salto para se conectar ao cluster de fora da rede virtual.

   * Crie as VMs do nó de cluster

   * Crie o cluster a partir dos nódulos individuais

   * Opcionalmente, crie um novo recipiente Blob e configure-o como armazenamento back-end para o cluster

   A criação de cluster suiú-feira em detalhes no [Deploy the vFXT cluster](avere-vfxt-deploy.md).

1. Configurar o cluster

   Conecte-se à interface de configuração do Avere vFXT (Avere Control Panel) para personalizar as configurações do cluster. Opte pelo monitoramento de suporte e adicione seu sistema de armazenamento se estiver usando armazenamento de hardware ou recipientes Blob adicionais.

   * [Acessar o cluster do vFXT](avere-vfxt-cluster-gui.md)
   * [Habilitar o suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Clientes de montagem

   Siga as diretrizes em [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md) para aprender sobre o balanceamento de carga e como as máquinas clientes devem montar o cluster.

1. Adicione dados (se necessário)

   Como o Avere vFXT é um cache escalável de vários clientes, a melhor maneira de mover dados para um novo contêiner de armazenamento back-end é com uma estratégia de cópia de arquivo multi-cliente e multi-threaded.

   Se você precisar mover dados do conjunto de trabalho para um novo contêiner Blob ou outro sistema de armazenamento back-end, siga as instruções em [Mover dados para o cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Próximas etapas

Continue [a preparar-se para criar o Avere vFXT](avere-vfxt-prereqs.md) para concluir as tarefas pré-requisitos.
