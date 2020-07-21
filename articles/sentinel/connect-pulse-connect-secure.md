---
title: Conectar o pulso conectar dados seguros ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Pulse Connect proteger dados ao Azure Sentinel.
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530905"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Conectar seu Pulse Connect seguro ao Azure Sentinel

Este artigo explica como conectar seu dispositivo do [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) ao Azure Sentinel. O conector de dados seguros do Pulse Connect permite que você conecte facilmente seu pulso conectar logs seguros com o Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre o Pulse Connect é segura e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Encaminhar o pulso conectar logs seguros ao agente de syslog  

Configure o Pulse Connect Secure para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector **seguro do Pulse Connect** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página **seguro do Pulse Connect** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Pulse Connect seguro ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.