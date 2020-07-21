---
title: Conectar alertas do Orca ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de alerta de segurança do Orca ao Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530966"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Conectar seus alertas do Orca ao Azure Sentinel 

O Orca Security Alerts Connector permite que você traga facilmente alertas da solução de segurança de [alertas da orca](https://orca.security/) para o Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre os alertas de segurança do Orca e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Configurar e conectar alertas de segurança do Orca

Os alertas de segurança da orca podem integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **Orca Security Alerts** e **abra a página do conector**.

2. Consulte https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration para concluir a integração da plataforma orca.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em **CustomLogs** na tabela **OrcaAlerts_CL** .
Para usar o esquema relevante no Log Analytics para os alertas do Orca, procure `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os alertas de segurança do Orca ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

