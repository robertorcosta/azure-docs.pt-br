---
title: Use a análise de comportamento de entidade para detectar ameaças avançadas | Microsoft Docs
description: Habilitar análise de comportamento de usuário e entidade no Azure Sentinel e configurar fontes de dados
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994067"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Habilitar UEBA (análise de comportamento de entidade e usuário) no Azure Sentinel 



## <a name="prerequisites"></a>Pré-requisitos

- O usuário deve receber as funções de administrador **global** ou de **administrador de segurança** no Azure ad para habilitar ou desabilitar o Ueba, mas não executá-lo.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Como habilitar a análise de comportamento de usuário e entidade

1. No menu de navegação do Azure Sentinel, selecione **comportamento da entidade (versão prévia)**.

1. Sob o título **Ativar**, alterne a alternância para **ativado**.

1. Clique no botão **selecionar fontes de dados** .

1. No painel **seleção de fonte de dados** , marque as caixas de seleção ao lado das fontes de dados nas quais você deseja habilitar Ueba e, em seguida, selecione **aplicar**.

    > [!NOTE]
    >
    > Na parte inferior do painel **seleção de fonte de dados** , você verá uma lista de fontes de dados com suporte Ueba que ainda não habilitou. 
    >
    > Depois de habilitar o UEBA, você terá a opção de conectar novas fontes de dados, para habilitá-las para o UEBA diretamente do painel do conector de dados, se elas forem compatíveis com UEBA.

1. Selecione **ir para pesquisa de entidade**. Isso o levará para o painel pesquisa de entidade, que, de agora em diante, será o que você vê quando escolhe o **comportamento da entidade** no menu principal do Azure Sentinel.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a habilitar e configurar a UEBA (análise de comportamento de usuário e entidade) no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
