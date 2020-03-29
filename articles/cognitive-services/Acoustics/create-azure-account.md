---
title: Configuração da conta do lote do Projeto Acústico Azure
titlesuffix: Azure Cognitive Services
description: Isso descreve a configuração de uma conta do Azure Batch para uso com integrações de mecanismos do Project Acoustics Unity e Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855090"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta do lote do Projeto Acústico Azure
Isso descreve a configuração de uma conta do Azure Batch para uso com integrações de mecanismos do Project Acoustics Unity e Unreal.

## <a name="get-an-azure-subscription"></a>Obter uma assinatura do Azure
Uma [assinatura do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas do lote e armazenamento. Se você estiver se inscrevendo pela primeira vez, o Azure fornece alguns recursos gratuitos de tempo limitado e o crédito de US $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas de lote e armazenamento do Azure
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o seu Azure Batch e as contas do Armazenamento do Azure associadas.

Escolha as opções padrão para as contas de lote e armazenamento:
  
  ![Captura de tela do Azure Batch novas opções de contas mostrando configurações padrão](media/new-batch-account-create.png)

  ![Captura de tela do Azure Armazenamento de novas opções de contas mostrando configurações padrão](media/batch-storage-account-create.png)

Demora alguns minutos para o Azure implantar as contas. Procure uma notificação de conclusão no canto superior direito do portal.
  
  ![Captura de tela das contas do Azure implantadas](media/batch-accounts-deploy-notification.png)

Suas contas agora devem estar visíveis no seu painel.
  
  ![Captura de tela do painel do portal Azure mostrando uma conta de lote e armazenamento](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar a acústica da interface do usuário para assar com credenciais do Azure
Clique no link da conta do Lote no painel e, em seguida, clique no link **Chaves** na página da conta do Lote para acessar suas credenciais.
  
  ![Captura de tela da conta do Azure Batch com link para página Chaves destacada](media/batch-access-keys.png)

  ![Captura de tela da página de chaves da conta do Azure Batch com chaves de acesso](media/batch-keys-info.png)

Clique no link **Conta de armazenamento** na página para acessar suas credenciais da conta do Armazenamento do Azure.
  
  ![Captura de tela da página de chaves da conta do Azure Storage com chaves de acesso](media/storage-keys-info.png)

Digite essas credenciais no [plugin unity bake](unity-baking.md) ou [plugin unreal bake](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipos de nó e o suporte de região
O Project Acoustics requer nós Azure VM otimizados para fsv2 e sérieH que podem não ser suportados em todas as regiões do Azure. Por favor, verifique [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que você está escolhendo o local certo para sua conta do Lote.
![Captura de tela mostrando máquinas virtuais do Azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizando sua cota
As contas do Azure Batch são aprovisionadas na criação de contas com um limite de 20 núcleos de computação. Podemos querer aumentar esse limite para tempos de cozimento mais rápidos, porque você pode paralelenear sua carga de trabalho acústica em muitos nós, até o número de pontos de sonda em sua cena. Você pode solicitar um aumento de cota, clicando na **cota** link na sua página de portal de lote do Azure e, em seguida, clicando em **solicitação de aumento de cota**:

![Captura de tela da página Cota Do Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Próximas etapas
* Integre o plugin project acústico em seu projeto [Unity](unity-integration.md) ou [Unreal](unreal-integration.md)

