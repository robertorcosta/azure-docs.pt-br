---
title: Conectar os dados do Secrmm Technologies Squadra ao Azure sentinela | Microsoft Docs
description: Saiba como conectar os dados do Secrmm Technologies Squadra ao Azure Sentinel.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588103"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Conecte seus dados do Secrmm Technologies Squadra ao Azure Sentinel 

> [!IMPORTANT]
> O conector de dados Squadra (Secrmm Technologies Security removível Media Manager) no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


O conector do Secrmm Technologies Squadra permite que você conecte facilmente os logs de solução de segurança do Secrmm Technologies Squadra com o Azure Sentinel. Ele permite exibir painéis, criar alertas personalizados e melhorar a investigação. Esse conector fornece informações sobre eventos de armazenamento removível USB. A integração entre o Secrmm Technologies Squadra e o Azure Sentinel usa a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurar e conectar o Secrmm Technologies Squadra 

O secrmm Technologies Squadra pode integrar e exportar logs diretamente para o Azure Sentinel.
1. No portal do Azure Sentinel, clique em conectores de dados e selecione Secrmm Technologies Squadra e abra a página conector.

2. Siga as etapas descritas no guia de [integração do Secrmm Technologies para o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) para obter dados de secrmm Squadra no Azure sentinela.   


## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em CustomLogs secRMM_CL.
Para usar o esquema relevante em Log Analytics para o Secrmm Technologies Squadra, procure secRMM_CL.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Secrmm Technologies Squadra ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

