---
title: Pré-requisitos de Avere vFXT – Azure
description: Pré-requisitos do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: dce359d1567ee763cd988e778b1e0e44475388cc
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255360"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o Avere vFXT

Este artigo explica as tarefas de pré-requisito para a criação de um cluster do Avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

Comece criando uma nova assinatura do Azure. Use uma assinatura separada para cada projeto do Avere vFXT para permitir que você acompanhe facilmente todos os recursos e as despesas do projeto, proteja outros projetos de limitação durante um possível provisionamento de recursos e simplifique a limpeza.  

Para criar uma nova assinatura do Azure no portal do Azure:

* Navegue até a [folha Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Clique no botão **+ Adicionar** na parte superior
* Entre se solicitado
* Selecione uma oferta e percorra as etapas para criar uma nova assinatura

## <a name="configure-subscription-owner-permissions"></a>Configure as permissões do proprietário da assinatura

Um usuário com permissões de proprietário para a assinatura deve criar o cluster do vFXT. As permissões de proprietário da assinatura são necessárias para aceitar os termos de serviço do software e executar outras ações. 

Há alguns cenários de solução alternativa que permitem que um não proprietário crie um avere vFTX para o cluster do Azure. Esses cenários envolvem a restrição de recursos e a atribuição de funções adicionais ao criador. Em ambos os casos, um proprietário de assinatura também deve [aceitar os termos de software avere vFXT](#accept-software-terms) antes do tempo. 

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster avere vFXT | 
|----------|--------|-------|
| Administrador do grupo de recursos | A rede virtual, o controlador de cluster e os nós de cluster devem ser criados dentro do grupo de recursos | Funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) , ambas com escopo para o grupo de recursos de destino | 
| Vnet externa | O controlador de cluster e os nós de cluster são criados dentro do grupo de recursos, mas uma rede virtual existente em um grupo de recursos diferente é usada | (1) funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) com escopo para o grupo de recursos vFXT; e (2) [colaborador de máquina virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [administrador de acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator)e funções de [colaborador avere](../role-based-access-control/built-in-roles.md#avere-contributor) com escopo para o grupo de recursos de VNET. |
 
Uma alternativa é criar uma função de RBAC (controle de acesso baseado em função) personalizada antecipadamente e atribuir privilégios ao usuário, conforme explicado neste [artigo](avere-vfxt-non-owner.md). Esse método fornece permissões significativas para esses usuários. 

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Você precisa ter uma cota suficiente para os componentes do Azure a seguir. Se necessário, [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e componentes SSD aqui listados são para o próprio cluster vFXT. Você precisará de cota adicional para as VMs e o SSD que pretender usar para seu farm de computação.  Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3|
|Armazenamento SSD Premium|Espaço do sistema operacional de 200 GB, além de 1 a 4 TB de espaço do cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de Blob de LRS |

## <a name="accept-software-terms"></a>Aceitar os termos de software

> [!NOTE] 
> Essa etapa não será necessária se um proprietário de assinatura criar o cluster do Avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço para o software Avere vFXT. Se você não é um proprietário de assinatura, faça com que um proprietário de assinatura aceite os termos de antemão. Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software de antemão: 

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID da assinatura.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Execute este comando para aceitar os termos de serviço e habilitar acesso programático para a imagem do software Avere vFXT para Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Criar um ponto de extremidade de serviço de armazenamento em sua rede virtual (se necessário)

Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de BLOBs do Azure local, em vez de roteá-lo fora da rede virtual. É recomendável para qualquer vFXT avere para o cluster do Azure que usa o blob do Azure para armazenamento de dados de back-end. 

Se você estiver fornecendo uma vnet existente e criando um novo contêiner de blob do Azure para o armazenamento de back-end como parte da criação do cluster, você deverá ter um ponto de extremidade de serviço na vnet para o armazenamento da Microsoft. Esse ponto de extremidade deve existir antes da criação do cluster ou a criação falhará. 

Um ponto de extremidade de serviço de armazenamento é recomendado para qualquer vFXT avere para o cluster do Azure que usa o armazenamento de BLOBs do Azure, mesmo que você adicione o armazenamento posteriormente. 

> [!TIP] 
> * Ignore esta etapa se você estiver criando uma nova rede virtual como parte da criação do cluster. 
> * Essa etapa será opcional se você não estiver criando o armazenamento de blob durante a criação do cluster. Nesse caso, você poderá criar o ponto de extremidade de serviço posteriormente se decidir usar o blob do Azure.

Crie o ponto de extremidade do serviço de armazenamento no portal do Azure. 

1. No portal, clique em **Redes virtuais** à esquerda.
1. Selecione a vnet para o cluster. 
1. Clique em **Pontos de extremidade de serviço** à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Deixe o serviço como ``Microsoft.Storage`` e escolha a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Próxima etapa: Criar o cluster vFXT

Após concluir esses pré-requisitos, você pode passar à criação do cluster em si. Leia [Implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.