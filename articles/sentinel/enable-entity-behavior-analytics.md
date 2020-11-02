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
ms.date: 10/28/2020
ms.author: yelevin
ms.openlocfilehash: b769703f7e7054571a3b65a021c0123d8ae07078
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148228"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Habilitar UEBA (análise de comportamento de entidade e usuário) no Azure Sentinel 

> [!IMPORTANT]
>
> - O recurso UEBA agora está em **disponibilidade geral** nas seguintes regiões e Geografia do Azure Sentinel:
>    - Geografia Estados Unidos
>    - Região oeste da Europa
>    - Geografia da Austrália
>
> - Em todas as outras geografias e regiões, esse recurso permanece durante o tempo de **Visualização pública** e é fornecido sem um contrato de nível de serviço. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar ou desabilitar esse recurso (esses pré-requisitos não são necessários para usar o recurso):

- Seu usuário deve ser membro do Azure Active Directory da sua organização e não de um usuário convidado.

- O usuário deve receber as funções de administrador **global** ou de **administrador de segurança** no Azure AD.

- Seu usuário deve receber pelo menos uma das seguintes **funções do Azure** ( [saiba mais sobre o RBAC do Azure](roles.md)):
    - **Colaborador do Azure Sentinel** nos níveis de espaço de trabalho ou grupo de recursos.
    - **Log Analytics colaborador** nos níveis do grupo de recursos ou da assinatura.

- Seu espaço de trabalho não deve ter nenhum bloqueio de recurso do Azure aplicado a ele. [Saiba mais sobre o bloqueio de recursos do Azure](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Como habilitar a análise de comportamento de usuário e entidade

1. No menu de navegação do Azure Sentinel, selecione **comportamento da entidade** .

1. Sob o título **Ativar** , alterne a alternância para **ativado** .

1. Clique no botão **selecionar fontes de dados** .

1. No painel **seleção de fonte de dados** , marque as caixas de seleção ao lado das fontes de dados nas quais você deseja habilitar Ueba e, em seguida, selecione **aplicar** .

    > [!NOTE]
    >
    > Na parte inferior do painel **seleção de fonte de dados** , você verá uma lista de fontes de dados com suporte Ueba que ainda não habilitou. 
    >
    > Depois de habilitar o UEBA, você terá a opção de conectar novas fontes de dados, para habilitá-las para o UEBA diretamente do painel do conector de dados, se elas forem compatíveis com UEBA.

1. Selecione **ir para pesquisa de entidade** . Isso o levará para o painel pesquisa de entidade, que, de agora em diante, será o que você vê quando escolhe o **comportamento da entidade** no menu principal do Azure Sentinel.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a habilitar e configurar a UEBA (análise de comportamento de usuário e entidade) no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
