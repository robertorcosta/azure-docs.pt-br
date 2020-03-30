---
title: Conecte o Forcepoint DLP ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar o Forcepoint DLP ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588239"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Conecte seu Forcepoint DLP ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Forcepoint Data Loss Prevention (DLP) no Azure Sentinel está atualmente em visualização pública. Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector DLP Forcepoint permite exportar automaticamente dados de incidentes DLP para o Azure Sentinel. Você pode usá-lo para obter visibilidade nas atividades do usuário e incidentes de perda de dados. Ele também permite correlações com dados de cargas de trabalho do Azure e outros feeds, e melhora a capacidade de monitoramento com workbooks dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Configure e conecte o Forcepoint DLP

Configure o Forcepoint DLP para encaminhar dados de incidentes no formato JSON para o seu espaço de trabalho Azure via API REST, conforme explicado no [Guia de Integração do Forcepoint DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Encontre seus dados

Depois que o conector DLP forcepoint estiver configurado, os dados aparecerão no Log Analytics em CustomLogs **ForcepointDLPEvents_CL**.


Para usar o esquema relevante no Log Analytics para Forcepoint DLP, procure **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar o Forcepoint DLP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.