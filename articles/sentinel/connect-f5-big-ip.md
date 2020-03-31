---
title: Conecte os dados DO F5 BIG-IP ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados F5 BIG-IP ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588273"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Conecte seu aparelho F5 BIG-IP 

> [!IMPORTANT]
> O conector de dados F5 BIG-IP no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector F5 BIG-IP permite conectar facilmente todos os seus logs F5 BIG-IP com o Azure Sentinel, para visualizar livros de trabalho, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre a rede da sua organização e melhora os recursos de operação de segurança. A integração entre f5 BIG-IP e Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configure e conecte f5 BIG-IP 

O F5 BIG-IP pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal Do Azure Sentinel, clique em **Conectores de dados** e selecione **F5 BIG-IP** e, em seguida, **abra a página do conector**. 
1. Para conectar seu F5 BIG-IP, você tem que postar uma declaração JSON no ponto final da API do sistema. Para obter instruções sobre como fazer isso, consulte [Integrando o F5 BIG-IP com o Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na página do conector F5 BIG-IP, copie o ID do espaço de trabalho e a chave principal e cole-os conforme instruído em [dados de streaming para o Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Depois de completar as instruções F5 BIG-IP, na página do conector Do Azure Sentinel, você verá os tipos de dados conectados.
1. Para usar o esquema relevante no Log Analytics para os eventos F5 BIG-IP, procure **por F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar f5 BIG-IP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


