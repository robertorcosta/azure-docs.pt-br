---
title: Conecte os dados do Firewall do Aplicativo Web ao Azure Sentinel
description: Saiba como conectar dados de firewall de aplicativos da Microsoft ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588171"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Conecte dados do firewall de aplicativos web da Microsoft



Você pode transmitir logs do Firewall de aplicativos microsoft (WAF) do Azure Application Gateway. Este WAF protege seus aplicativos contra vulnerabilidades comuns da Web, como injeção SQL e scripting entre sites, e permite personalizar regras para reduzir falsos positivos. Siga estas instruções para transmitir os logs de firewall do aplicativo Microsoft Web no Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicativo existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Conecte-se ao firewall de aplicativos web da Microsoft

Se você já tiver o firewall de aplicativos da Microsoft, certifique-se de ter um recurso de gateway existente.
Uma vez que o firewall do aplicativo web da Microsoft seja implantado e receba dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.
    
1. No portal Azure Sentinel, selecione **conectores de dados**.
1. Na página Conectores de dados, selecione o azulejo **WAF.**
1. Vá para [o recurso](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) Application Gateway e escolha seu WAF.
    1. Selecione **Configurações de diagnóstico**.
    1. Selecione **+ Adicione a configuração de diagnóstico** a tabela.
    1. Na página **Configurações** de Diagnóstico, digite um **Nome** e **selecione Enviar para Registrar Análises**.
    1. Em **Log Analytics Workspace,** selecione o espaço de trabalho do Azure Sentinel.
    1. Selecione os tipos de log que deseja analisar. Recomendamos: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Para usar o esquema relevante no Log Analytics para os alertas de firewall de aplicativos da Microsoft, procure **o AzureDiagnostics**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o firewall de aplicativos da Microsoft ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
