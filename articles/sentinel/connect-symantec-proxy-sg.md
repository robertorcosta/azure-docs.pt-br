---
title: Conectar dados do Symantec ProxySG ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Symantec ProxySG ao Azure Sentinel.
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
ms.openlocfilehash: 51a5c5de7bce07aa6a54b9ff81e957c38cfffdd2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530918"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Conectar seu Symantec ProxySG ao Azure Sentinel

Este artigo explica como conectar seu dispositivo [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) ao Azure Sentinel. O conector de dados do Symantec ProxySG permite que você conecte facilmente seus logs do Symantec ProxySG com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Symantec ProxySG e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Encaminhe os logs do Symantec ProxySG para o agente de syslog  

Configure o Symantec ProxySG para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector do **Symantec ProxySG** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página do **Symantec ProxySG** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Symantec ProxySG ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.