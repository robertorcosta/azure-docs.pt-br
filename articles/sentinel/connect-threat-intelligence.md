---
title: Conecte dados de inteligência de ameaças ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados de inteligência de ameaças ao Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: eec07a01edc6b126bb7cd3a814912ea5c5b14195
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529091"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Conecte dados de provedores de inteligência de ameaças

> [!IMPORTANT]
> Os conectores de dados do Threat Intelligence no Azure Sentinel estão atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel permite importar os indicadores de ameaça que sua organização está usando, o que pode aumentar a capacidade dos analistas de segurança de detectar e priorizar ameaças conhecidas. Vários recursos do Azure Sentinel ficam disponíveis ou são aprimorados:

- **O Analytics** inclui um conjunto de modelos de regras programados que você pode permitir para gerar alertas e incidentes com base em correspondências de eventos de log de seus indicadores de ameaça.

- **Os workbooks** fornecem informações resumidas sobre os indicadores de ameaça importados para o Azure Sentinel e quaisquer alertas gerados a partir de regras de análise que correspondam aos seus indicadores de ameaça.

- **As** consultas de caça permitem que os investigadores de segurança usem indicadores de ameaça no contexto de cenários comuns de caça.

- **Os notebooks** podem usar indicadores de ameaça quando você investiga anomalias e caça comportamentos maliciosos.

Você pode transmitir indicadores de ameaça para o Azure Sentinel usando um dos produtos da plataforma integrada de inteligência de ameaças (TIP) listados na próxima seção, conectando-se a servidores TAXII ou usando integração direta com a [API tiIndicators de segurança de gráficos do Microsoft.](https://aka.ms/graphsecuritytiindicators)

## <a name="integrated-threat-intelligence-platform-products"></a>Produtos integrados de plataforma de inteligência de ameaças

- [Plataforma de inteligência de ameaças de código aberto MISP](https://www.misp-project.org/)
    
    Para obter um script de exemplo que forneça aos clientes instâncias MISP para migrar indicadores de ameaça para a API de segurança do Gráfico do Microsoft, consulte o [MISP para o Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Para baixar o ThreatStream Integrator and Extensions e as instruções para conectar a inteligência do ThreatStream à API de segurança do Gráfico do Microsoft, consulte a página [de downloads do ThreatStream.](https://ui.threatstream.com/downloads)

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Para obter instruções guiadas, consulte [Enviar CoOCs para a API de segurança do gráfico da Microsoft usando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Plataforma ThreatConnect](https://threatconnect.com/solution/)

    Para obter informações, consulte [ThreatConnect Integrations](https://threatconnect.com/integrations/) e procure a API de segurança do Microsoft Graph na página.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Conecte o Azure Sentinel à sua plataforma de inteligência de ameaças

## <a name="prerequisites"></a>Pré-requisitos  

- Função Azure AD do administrador global ou administrador de segurança para conceder permissões ao seu produto TIP ou aplicativo personalizado que usa integração direta com a API microsoft Graph Security tiIndicators.

- Leia e escreva permissões no espaço de trabalho do Azure Sentinel para armazenar seus indicadores de ameaça.

## <a name="instructions"></a>Instruções

1. [Registre um aplicativo](/graph/auth-v2-service#1-register-your-app) no Azure Active Directory para obter um ID de aplicativo, um aplicativo secreto e o ID do inquilino do Azure Active Directory. Você precisa desses valores para quando configurar seu produto ou aplicativo TIP integrado que usa integração direta com a API tiIndicators de segurança do Gráfico do Microsoft.

2. [Configure permissões de API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) para o aplicativo registrado: Adicione a permissão do aplicativo Microsoft Graph **ThreatIndicators.ReadWrite.OwnedBy** ao seu aplicativo registrado.

3. Peça ao administrador do inquilino do Azure Active Directory que conceda o consentimento do administrador ao aplicativo registrado para sua organização. Do portal Azure: **Azure Active Directory** > **App registros** > **\<_nome_>** > do aplicativo**Exibir aPI Permissões** > **Conceder consentimento para \<nome>de _inquilino_**.

4. Configure seu produto ou aplicativo TIP que usa integração direta com a API tiIndicators do Microsoft Graph Security para enviar indicadores ao Azure Sentinel especificando o seguinte:
    
    a. Os valores para o ID do aplicativo registrado, documento secreto e id do inquilino.
    
    b. Para o produto de destino, especifique o Azure Sentinel.
    
    c. Para a ação, especifique o alerta.

5. No portal Azure, navegue até os**conectores** **azure Sentinel** > Data e selecione o conector **Threat Intelligence Platforms (Preview).**

6. Selecione **Abrir a página do conector**e, em seguida, **Conectar**.

7. Para visualizar os indicadores de ameaça importados para o Azure Sentinel, navegue até **o Azure Sentinel - Logs** > **SecurityInsights**e, em seguida, expanda **o ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Conecte o Azure Sentinel aos servidores TAXII

## <a name="prerequisites"></a>Pré-requisitos  

- Leia e escreva permissões no espaço de trabalho do Azure Sentinel para armazenar seus indicadores de ameaça.

- TAXI 2.0 servidor URI e ID de coleção.

## <a name="instructions"></a>Instruções

1. No portal Azure, navegue até os**conectores** **azure Sentinel** > Data e selecione o conector **Threat Intelligence - TAXII (Preview).**

2. Selecione **Abrir a página do conector**.

3. Especifique as informações necessárias e opcionais nas caixas de texto.

4. Selecione **Adicionar** para ativar a conexão ao servidor TAXII 2.0.

5. Se você tiver servidores TAXII 2.0 adicionais: Repita as etapas 3 e 4.

6. Para visualizar os indicadores de ameaça importados para o Azure Sentinel, navegue até **o Azure Sentinel - Logs** > **SecurityInsights**e, em seguida, expanda **o ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar seu provedor de inteligência de ameaças ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos.

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
