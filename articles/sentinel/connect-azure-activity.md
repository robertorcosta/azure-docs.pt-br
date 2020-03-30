---
title: Conecte os dados de atividade do Azure ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar os dados do Azure Activity ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124982"
---
# <a name="connect-data-from-azure-activity-log"></a>Conecte dados do registro de atividades do Azure

Você pode transmitir logs do [Azure Activity log](../azure-monitor/platform/platform-logs-overview.md) no Azure Sentinel com um único clique. O registro atividade é um registro de assinatura que registra e exibe eventos de nível de assinatura em todo o Azure, desde dados operacionais do Azure Resource Manager até atualizações sobre eventos de Saúde do Serviço. Usando o registro atividade, você pode determinar o 'o que, quem e quando' para qualquer operação de gravação (PUT, POST, DELETE) realizada nos recursos da sua assinatura. Você também pode aprender o status da operação e outras propriedades relevantes. O registro de atividades não inclui operações de leitura (GET) para recursos que usam o modelo Classic/"RDFE". 

## <a name="prerequisites"></a>Pré-requisitos

- Seu usuário deve ter permissões de contribuinte para o espaço de trabalho do Log Analytics.
- Seu usuário deve ter permissões do Leitor para qualquer assinatura cujos logs você deseja transmitir no Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configure o conector Azure Activity

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique em **Atividade do Azure**e, em seguida, no botão **de página do conector Aberto** no canto inferior direito.

2. Na guia **Instruções,** clique no link **Configurar atividade do Azure activity >** link.

3. No **painel de log do Azure Activity,** selecione as assinaturas cujos registros você deseja transmitir no Azure Sentinel. 

4. No painel de assinatura que abre à direita, clique **em Conectar**.

5. Para usar o esquema relevante no Log Analytics para alertas de atividade do Azure, digite `AzureActivity` na janela de consulta.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o registro de atividades do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
