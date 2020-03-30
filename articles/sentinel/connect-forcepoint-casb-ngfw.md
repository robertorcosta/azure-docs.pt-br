---
title: Conecte os produtos Forcepoint ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar produtos Forcepoint ao Azure Sentinel.
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588222"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Conecte seus produtos Forcepoint ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados dos produtos Forcepoint no Azure Sentinel está atualmente em visualização pública. Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo explica como conectar seus produtos Forcepoint ao Azure Sentinel. 

Os conectores de dados Forcepoint permitem conectar o Forcepoint Cloud Access Security Broker e os logs do Firewall Forcepoint Next Generation com o Azure Sentinel. Desta forma, você pode exportar automaticamente logs definidos pelo usuário para o Azure Sentinel em tempo real. O conector dá visibilidade enriquecida às atividades do usuário registradas pelos produtos Forcepoint. Ele também permite uma correlação adicional com dados de cargas de trabalho do Azure e outros feeds, e melhora a capacidade de monitoramento com workbooks dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Faça logs de produtos Forward Forcepoint para o agente Syslog 

Configure o produto Forcepoint para encaminhar mensagens Syslog no formato CEF para o espaço de trabalho do Azure através do agente Syslog.

1. Configure o produto Forcepoint para a integração do Azure Sentinel conforme descrito nos seguintes guias de instalação:
 - [Guia de Integração CASB do Forcepoint](https://frcpnt.com/casb-sentinel)
 - [Guia de Integração do Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Procure commonsecuritylog para usar o esquema relevante no Log Analytics com o nome DeviceVendor contém 'Forcepoint'. 

3. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).



## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar produtos Forcepoint ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).

- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.