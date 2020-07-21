---
title: Conectar dados do sistema de gerenciamento de ataques do illusive ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do sistema de gerenciamento de ataques do illusive ao Azure Sentinel.
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
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530946"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Conectar seu sistema de gerenciamento de ataques do illusive ao Azure Sentinel

Este artigo explica como conectar seu [sistema de gerenciamento de ataques do illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) ao Azure Sentinel. O conector de dados do sistema de gerenciamento de ataques illusive permite que você compartilhe os dados de análise da superfície de ataque do illusive e os logs de incidentes com o Azure Sentinel e exiba essas informações em painéis dedicados que oferecem insights sobre o risco de superfície de ataque da sua organização (painel do ADS) e acompanhe a movimentação lateral não autorizada na rede da sua organização (painel de anúncios)

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Encaminhar logs do sistema de gerenciamento de ataques de illusive para o agente de syslog  

Configure o sistema de gerenciamento de ataques para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Faça logon no console do illusive e navegue até configurações->relatórios.

1. Localizar Serversץ de syslog

1. Forneça as seguintes informações:
   - Nome do host: endereço IP do agente de syslog do Linux ou nome do host FQDN
   - Porta: 514
   - Protocolo: TCP
   - Mensagens de auditoria: enviar mensagens de auditoria para o servidor

1. Para adicionar o servidor syslog, clique em Adicionar.

1. Para usar o esquema relevante em Log Analytics para o sistema de gerenciamento de ataques illusive, pesquise CommonSecurityLog.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o sistema de gerenciamento de ataques illusive ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
