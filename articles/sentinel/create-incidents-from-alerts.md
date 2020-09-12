---
title: Criar incidentes com base em alertas no Azure Sentinel | Microsoft Docs
description: Saiba como criar incidentes com base em alertas no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: f4c631360417afda41b7f48a46b618e7a4328aef
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660695"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Crie incidentes automaticamente com base em alertas de segurança da Microsoft

Alertas disparados nas soluções de segurança da Microsoft que estão conectadas ao Azure Sentinel, como Microsoft Cloud App Security e Microsoft defender for Identity (anteriormente Azure ATP), não criam automaticamente incidentes no Azure Sentinel. Por padrão, quando você conecta uma solução da Microsoft ao Azure Sentinel, qualquer alerta gerado nesse serviço será armazenado como dados brutos no Azure Sentinel, na tabela Alerta de Segurança no workspace do Azure Sentinel. É possível usar esses dados como todos os outros dados brutos que você conecta ao Sentinel.

É possível configurar facilmente o Azure Sentinel para criar incidentes automaticamente toda vez que um alerta é disparado em uma solução de segurança da Microsoft conectada, seguindo as instruções neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
É necessário [conectar as soluções de segurança da Microsoft](connect-data-sources.md#data-connection-methods) para habilitar a criação de incidentes com base em alertas de serviço de segurança.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Usar as Regras analíticas de criação de incidentes de segurança da Microsoft

Use as regras internas disponíveis no Azure Sentinel para escolher quais soluções de segurança da Microsoft conectadas devem criar incidentes do Azure Sentinel automaticamente em tempo real. Também é possível editar as regras para definir opções mais específicas para filtrar quais dos alertas gerados pela solução de segurança da Microsoft devem criar incidentes no Azure Sentinel. Por exemplo, você pode optar por criar incidentes do Azure Sentinel automaticamente somente de alertas de alta gravidade do Azure defender (anteriormente conhecido como central de segurança do Azure).

1. No portal do Azure, no Azure Sentinel, selecione **Análise**.

1. Selecione a guia **Modelos de regra** para ver todas as regras de análise internas.

    ![Modelos de regra](media/incidents-from-alerts/rule-templates.png)

1. Escolha o modelo de regra de análise da **Segurança da Microsoft** que você deseja usar e clique em **Criar regra**.

    ![Regra de análise de segurança](media/incidents-from-alerts/security-analytics-rule.png)

1. É possível modificar os detalhes da regra e optar por filtrar os alertas que criarão incidentes por severidade do alerta ou por texto contido no nome do alerta.  
      
    Por exemplo, se você escolher **Azure defender** (ainda pode ser chamado de central de segurança do Azure) no campo **serviço de segurança da Microsoft** e escolher **alto** no campo **Filtrar por severidade** , somente os alertas de alta severidade do Azure defender criarão automaticamente incidentes no Azure Sentinel.  

    ![Criar assistente de regra](media/incidents-from-alerts/create-rule-wizard.png)

1. Também é possível criar uma regra de **segurança da Microsoft** que filtra alertas de diferentes serviços de segurança da Microsoft clicando em **+Criar** e selecionando **Regra de criação de incidentes da Microsoft**.

    ![Regra de criação de incidentes](media/incidents-from-alerts/incident-creation-rule.png)

  É possível criar mais de uma regra de análise de **Segurança da Microsoft** por tipo de **serviço de segurança da Microsoft**. Isso não cria incidentes duplicados, pois cada regra é usada como um filtro. Mesmo que um alerta corresponda a mais de uma regra analítica de **Segurança da Microsoft**, ele cria apenas um incidente do Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Habilitar a geração de incidentes automaticamente durante a conexão
 Quando você conecta uma solução de segurança da Microsoft, pode selecionar se deseja que os alertas da solução de segurança gerem automaticamente incidentes no Azure Sentinel.

1. Conecte uma fonte de dados de solução de segurança da Microsoft. 

   ![Gerar incidentes de segurança](media/incidents-from-alerts/generate-security-incidents.png)

1. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure Sentinel, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados ao Azure Sentinel](quickstart-onboard.md) e [obtenha visibilidade de seus dados e de possíveis ameaças](quickstart-get-visibility.md).
