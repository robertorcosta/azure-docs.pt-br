---
title: Conectar dados de firewall do aplicativo Web ao Azure Sentinel
description: Saiba como conectar dados do firewall do aplicativo Web da Microsoft ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588171"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Conectar dados do firewall do aplicativo Web da Microsoft



Você pode transmitir logs do WAF (Microsoft Web Application Firewall) do gateway de Aplicativo Azure. Esse WAF protege seus aplicativos contra vulnerabilidades comuns da Web, como injeção de SQL e script entre sites, e permite que você personalize regras para reduzir falsos positivos. Siga estas instruções para transmitir os logs do firewall do aplicativo Web da Microsoft para o Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicativo existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Conectar-se ao firewall do aplicativo Web da Microsoft

Se você já tiver o Firewall do aplicativo Web da Microsoft, verifique se você tem um recurso de gateway existente.
Depois que o Firewall do aplicativo Web da Microsoft for implantado e estiver obtendo dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.
    
1. No portal do Azure Sentinel, selecione **conectores de dados**.
1. Na página conectores de dados, selecione o bloco **WAF** .
1. Vá para [recurso](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) de gateway de aplicativo e escolha seu WAF.
    1. Selecione **Configurações de diagnóstico**.
    1. Selecione **+ Adicionar configuração de diagnóstico** na tabela.
    1. Na página **configurações de diagnóstico** , digite um **nome** e selecione **Enviar para log Analytics**.
    1. Em **log Analytics espaço de trabalho** , selecione o espaço de trabalho do Azure Sentinel.
    1. Selecione os tipos de log que você deseja analisar. Recomendamos: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Para usar o esquema relevante no Log Analytics para os alertas de firewall do aplicativo Web da Microsoft, procure **AzureDiagnostics**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Firewall do aplicativo Web da Microsoft ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
