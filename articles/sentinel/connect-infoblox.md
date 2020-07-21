---
title: Conectar dados de NIOS (Infoblox Network Identity Operating System) ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de NIOS (Infoblox Network Identity Operating System) ao Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530947"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Conectar seu Infoblox NIOS ao Azure Sentinel

Este artigo explica como conectar seu [dispositivo nios (sistema operacional de identidade de rede) Infoblox](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ao Azure Sentinel. O conector de dados Infoblox NIOS permite que você conecte facilmente seus logs do Infoblox com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Infoblox NIOS e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Encaminhar logs do Infoblox para o agente de syslog  

Configure o Infoblox para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector do **Infoblox NIOS** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página **INFOBLOX nios** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Infoblox NIOS ao Azure sentinela. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.