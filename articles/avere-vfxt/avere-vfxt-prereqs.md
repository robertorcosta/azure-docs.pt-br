---
title: Pré-requisitos de Avere vFXT – Azure
description: Pré-requisitos do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252539"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o Avere vFXT

Este artigo explica tarefas pré-requisitos para a criação de um cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

Comece criando uma nova assinatura do Azure. Use uma assinatura separada para cada projeto Avere vFXT para simplificar o rastreamento e a limpeza de despesas e para garantir que outros projetos não sejam afetados por cotas ou estrangulamento de recursos ao provisionar o fluxo de trabalho do cluster.

Para criar uma nova assinatura do Azure no portal do Azure:

1. Navegue até a [folha Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Clique no botão **+ Adicionar** na parte superior
1. Entre se solicitado
1. Selecione uma oferta e percorra as etapas para criar uma nova assinatura

## <a name="configure-subscription-owner-permissions"></a>Configure as permissões do proprietário da assinatura

Um usuário com permissões de proprietário para a assinatura deve criar o cluster do vFXT. A criação de clusters exige que o proprietário aceite os termos de serviço do software e autorize alterações nos recursos de rede e armazenamento.

Existem algumas soluçãos para permitir que um não proprietário crie um cluster Avere vFXT para Azure. Esses cenários envolvem restringir recursos e atribuir funções adicionais de controle de acesso baseado em função (RBAC) ao criador. Em todos esses casos, um proprietário de assinatura também deve [aceitar os termos do software Avere vFXT](#accept-software-terms) com antecedência.

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster Avere vFXT |
|----------|--------|-------|
| Administrador de grupo de recursos cria o vFXT | A rede virtual, o controlador de cluster e os nós de cluster devem ser criados dentro do grupo de recursos. | [Funções de Administrador de Acesso ao Usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) e [Contribuinte,](../role-based-access-control/built-in-roles.md#contributor) ambas escopo para o grupo de recursos de destino. |
| Use uma rede virtual externa existente | Os nós de controlador de cluster e cluster são criados dentro do grupo de recursos do vFXT, mas usam uma rede virtual existente em um grupo de recursos diferente. | (1) [Funções de administrador de acesso ao usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) e [contribuinte](../role-based-access-control/built-in-roles.md#contributor) escopo do grupo de recursos vFXT; e (2) [Contribuinte virtual da máquina,](../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [administrador de acesso ao usuário](../role-based-access-control/built-in-roles.md#user-access-administrator)e funções de [Contribuinte Avere](../role-based-access-control/built-in-roles.md#avere-contributor) escopo para o grupo de recursos da rede virtual. |
| Função personalizada para criadores de cluster | Sem restrições de colocação de recursos. Este método dá aos não proprietários privilégios significativos. | O proprietário da assinatura cria uma função RBAC personalizada, conforme explicado [neste artigo](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Verifique se você tem cota suficiente para os seguintes componentes do Azure. Se necessário, [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e componentes SSD aqui listados são para o próprio cluster vFXT. Lembre-se que você também precisa de cota para as VMs e SSDs que você usará para sua fazenda de cálculo.
>
> Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3 (um por nó de cluster) |
|Armazenamento SSD Premium|Espaço do sistema operacional de 200 GB, além de 1 a 4 TB de espaço do cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de Blob de LRS |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Aceitar os termos de software

> [!TIP]
> Pule esta etapa se um proprietário de assinatura criar o cluster Avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço para o software Avere vFXT. Se você não é um proprietário de assinatura, faça com que um proprietário de assinatura aceite os termos de antemão.

Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software de antemão:

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID da assinatura.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Execute este comando para aceitar os termos de serviço e habilitar acesso programático para a imagem do software Avere vFXT para Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Crie um ponto final de serviço de armazenamento em sua rede virtual (se necessário)

Um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego do Azure Blob local em vez de encaminhá-lo para fora da rede virtual. É recomendado para qualquer cluster Avere vFXT for Azure que use OZure Blob para armazenamento de dados back-end.

Se você criar uma nova rede virtual ao criar o cluster, um ponto final será criado automaticamente. Se você fornecer uma rede virtual existente, ela deve ter um ponto final do serviço de armazenamento da Microsoft se você quiser criar um novo contêiner de armazenamento Blob durante a criação de clusters.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Pule essa etapa se estiver criando uma nova rede virtual como parte da criação de clusters.
>* Um ponto final é opcional se você não estiver criando o armazenamento Blob durante a criação do cluster. Nesse caso, você pode criar o ponto final do serviço mais tarde se decidir usar o Azure Blob.

Crie o ponto final do serviço de armazenamento a partir do portal Azure.

1. No portal, abra sua lista de redes virtuais.
1. Selecione a rede virtual para o cluster.
1. Clique **em Pontos finais** de serviço no menu esquerdo.
1. Clique em **Adicionar** na parte superior.
1. Escolha o ``Microsoft.Storage``serviço .
1. Selecione a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Próximas etapas

Depois de completar esses pré-requisitos, você pode criar o cluster. Leia [Implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.
