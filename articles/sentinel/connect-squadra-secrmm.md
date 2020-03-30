---
title: Conecte os dados secRMM da Squadra Technologies ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados secRMM da Squadra Technologies ao Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588103"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Conecte seus dados secRMM da Squadra Technologies ao Azure Sentinel 

> [!IMPORTANT]
> O conector de dados squadra Technologies Security Removível Media Manager (secRMM) no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


O conector secRMM da Squadra Technologies permite conectar facilmente os registros de soluções de segurança squadra Technologies secRMM com o Azure Sentinel. Ele permite visualizar dashboards, criar alertas personalizados e melhorar a investigação. Este conector fornece insights sobre eventos de armazenamento removível USB. A integração entre a Squadra Technologies secRMM e o Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configure e conecte squadra technologies secRMM 

Squadra Technologies secRMM pode integrar e exportar logs diretamente para o Azure Sentinel.
1. No portal Do Azure Sentinel, clique em Conectores de dados e selecione Squadra Technologies secRMM e, em seguida, abra a página do conector.

2. Siga os [passos descritos no guia de onboarding da Squadra Technologies para o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) para obter dados Squadra secRMM no Azure Sentinel.   


## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados são exibidos no Log Analytics em CustomLogs secRMM_CL.
Para usar o esquema relevante no Log Analytics para o SecRMM da Squadra Technologies, procure secRMM_CL.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar squadra Technologies secRMM ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

