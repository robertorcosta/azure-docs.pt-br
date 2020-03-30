---
title: Solução de problemas - Firewall de aplicativos Web do Azure
description: Este artigo fornece informações de solução de problemas para o WAF (Web Application Firewall) para o Gateway de aplicativos do Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046187"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Solucionar problemas do WaF (Web Application Firewall) para o Gateway de aplicativos do Azure

Existem algumas coisas que você pode fazer se as solicitações que devem passar pelo seu WAF (Web Application Firewall) forem bloqueadas.

Primeiro, certifique-se de ler a visão geral do [WAF](ag-overview.md) e os documentos de configuração do [WAF.](application-gateway-waf-configuration.md) Além disso, certifique-se de ter ativado o [monitoramento do WAF](../../application-gateway/application-gateway-diagnostics.md) Esses artigos explicam como o WAF funciona, como as regras do WAF funcionam e como acessar os logs do WAF.

## <a name="understanding-waf-logs"></a>Entendendo os logs waf

O objetivo dos logs waf é mostrar cada solicitação que é compatível ou bloqueada pelo WAF. É um livro de todas as solicitações avaliadas que são combinadas ou bloqueadas. Se você notar que o WAF bloqueia uma solicitação que não deveria (um falso positivo), você pode fazer algumas coisas. Primeiro, reduza e encontre o pedido específico. Procure nos registros para encontrar o URI específico, carimbo de data e hora ou iD de transação da solicitação. Quando você encontrar as entradas de log associadas, você pode começar a agir sobre os falsos positivos.

Por exemplo, digamos que você tenha um tráfego legítimo contendo a string *1=1* que você deseja passar através de seu WAF. Se você tentar a solicitação, o WAF bloqueia o tráfego que contém sua seqüência *de string 1=1* em qualquer parâmetro ou campo. Esta é uma seqüência frequentemente associada a um ataque de injeção SQL. Você pode olhar através dos registros e ver o carimbo de data e hora da solicitação e as regras que bloquearam/corresponderam.

No exemplo a seguir, você pode ver que quatro regras são acionadas durante a mesma solicitação (usando o campo TransactionId). O primeiro diz que coincidiu porque o usuário usou uma URL numérica/IP para a solicitação, o que aumenta a pontuação de anomalia em três, já que é um aviso. A próxima regra que correspondeu é 942130, que é a que você está procurando. Você pode ver o *1=1* no `details.data` campo. Isso aumenta ainda mais a pontuação da anomalia em três novamente, pois também é um aviso. Geralmente, cada regra que tem a ação **Combinada** aumenta a pontuação da anomalia, e neste momento a pontuação da anomalia seria de seis. Para obter mais informações, consulte [o modo de pontuação da Anomalia](ag-overview.md#anomaly-scoring-mode).

As duas últimas entradas de registro mostram que a solicitação foi bloqueada porque a pontuação da anomalia foi alta o suficiente. Essas entradas têm uma ação diferente das outras duas. Eles mostram que *bloquearam* o pedido. Essas regras são obrigatórias e não podem ser desativadas. Eles não devem ser considerados como regras, mas mais como infra-estrutura central dos internos do WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Corrigindo falsos positivos

Com essas informações, e o conhecimento de que a regra 942130 é a que correspondia à seqüência *1=1,* você pode fazer algumas coisas para impedir que isso bloqueie seu tráfego:

- Use uma lista de exclusão

   Consulte a [configuração waf](application-gateway-waf-configuration.md#waf-exclusion-lists) para obter mais informações sobre listas de exclusão.
- Desabilite a regra.

### <a name="using-an-exclusion-list"></a>Usando uma lista de exclusão

Para tomar uma decisão informada sobre como lidar com um falso positivo, é importante se familiarizar com as tecnologias que seu aplicativo usa. Por exemplo, digamos que não há um servidor SQL em sua pilha de tecnologia, e você está recebendo falsos positivos relacionados a essas regras. Desativar essas regras não necessariamente enfraquece sua segurança.

Um benefício de usar uma lista de exclusão é que apenas uma parte específica de uma solicitação está sendo desativada. No entanto, isso significa que uma exclusão específica é aplicável a todo o tráfego que passa pelo seu WAF porque é uma configuração global. Por exemplo, isso pode levar a um problema se *1=1* for uma solicitação válida no corpo para um determinado aplicativo, mas não para outros. Outro benefício é que você pode escolher entre corpo, cabeçalhos e cookies a serem excluídos se uma determinada condição for atendida, em vez de excluir toda a solicitação.

Ocasionalmente, há casos em que parâmetros específicos são passados para o WAF de uma maneira que pode não ser intuitiva. Por exemplo, há um token que é passado ao autenticar usando o Azure Active Directory. Este token, *__RequestVerificationToken*, geralmente são passados como um Cookie de solicitação. No entanto, em alguns casos em que os cookies são desativados, esse token também é passado como um atributo de solicitação ou "arg". Se isso acontecer, você precisa garantir que *__RequestVerificationToken* seja adicionado à lista de exclusão como **um nome de atributo de Solicitação** também.

![Exclusões](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Neste exemplo, você deseja excluir o nome do **atributo Solicitar** que é igual a *texto1*. Isso é evidente porque você pode ver o nome do atributo nos registros de firewall: **dados: Dados combinados: 1=1 encontrado dentro de ARGS:text1: 1=1**. O atributo é **text1**. Você também pode encontrar este nome de atributo algumas outras maneiras, ver [Encontrar nomede atributo de solicitação](#finding-request-attribute-names).

![Listas de exclusão de WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Desabilitando regras

Outra maneira de contornar um falso positivo é desativar a regra que correspondia à entrada que o WAF achava malicioso. Uma vez que você analisou os registros waf e reduziu a regra para 942130, você pode desativá-la no portal Azure. Consulte Personalizar as regras de [firewall de aplicativos da Web através do portal Azure](application-gateway-customize-waf-rules-portal.md).

Um benefício de desativar uma regra é que se você conhece todo o tráfego que contém uma determinada condição que normalmente será bloqueada é o tráfego válido, você pode desativar essa regra para todo o WAF. No entanto, se for apenas tráfego válido em um caso de uso específico, você abrirá uma vulnerabilidade desativando essa regra para todo o WAF, uma vez que é uma configuração global.

Se você quiser usar o Azure PowerShell, consulte [Personalizar regras de firewall de aplicativos da Web através do PowerShell](application-gateway-customize-waf-rules-powershell.md). Se você quiser usar o Azure CLI, consulte [Personalizar regras de firewall de aplicativos da Web através do Azure CLI](application-gateway-customize-waf-rules-cli.md).

![Regras de WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Encontrar nomes de atributos de solicitação

Com a ajuda do [Fiddler,](https://www.telerik.com/fiddler)você inspeciona solicitações individuais e determina quais campos específicos de uma página web são chamados. Isso pode ajudar a excluir certos campos da inspeção usando listas de exclusão.

Neste exemplo, você pode ver que o campo onde a seqüência *de 1=1* foi inserida é chamado **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Este é um campo que você pode excluir. Para saber mais sobre listas de exclusão, consulte [limites de tamanho e exclusão de solicitações de aplicativos da Web](application-gateway-waf-configuration.md#waf-exclusion-lists). Você pode excluir a avaliação neste caso configurando a seguinte exclusão:

![Exclusão do WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Você também pode examinar os registros de firewall para obter as informações para ver o que você precisa adicionar à lista de exclusão. Para habilitar o registro, consulte [saúde back-end, registros de diagnóstico e métricas para Gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md).

Examine o registro de firewall e visualize o arquivo PT1H.json durante a hora em que a solicitação desejada inspecionar ocorreu.

Neste exemplo, você pode ver que você tem quatro regras com o mesmo TransactionID, e que todas elas ocorreram ao mesmo tempo:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Com o seu conhecimento de como as regras do CRS funcionam, e que o conjunto de regras do CRS 3.0 funciona com um sistema de pontuação de anomalias (consulte [O Firewall de Aplicação da Web para o Gateway de Aplicação do Azure](ag-overview.md)) você sabe que as duas regras inferiores com a ação: a propriedade bloqueada está **bloqueando** com base na pontuação total da anomalia. As regras para se concentrar são as duas melhores.

A primeira entrada é registrada porque o usuário usou um endereço IP numérico para navegar até o Gateway do aplicativo, o que pode ser ignorado neste caso.

A segunda (regra 942130) é a interessante. Você pode ver nos detalhes que ele correspondia a um padrão (1=1), e o campo é chamado **text1**. Siga as **mesmas** etapas anteriores para excluir o **nome do atributo de solicitação** que equivale a **1=1**.

## <a name="finding-request-header-names"></a>Encontrar nomes de cabeçalho de solicitação

Fiddler é uma ferramenta útil mais uma vez para encontrar nomes de cabeçalho de solicitação. Na captura de tela a seguir, você pode ver os cabeçalhos para esta solicitação GET, que incluem *Tipo de Conteúdo,* *Agente de Usuário*e assim por diante.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Outra maneira de ver cabeçalhos de solicitação e resposta é olhar dentro das ferramentas de desenvolvedor do Chrome. Você pode pressionar F12 ou clicar com o botão direito do mouse -> **Inspecionar** -> **ferramentas do desenvolvedor**e selecionar a guia **Rede.** Carregue uma página da Web e clique na solicitação que deseja inspecionar.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Encontrar nomes de cookies de solicitação

Se a solicitação contiver cookies, a guia **Cookies** pode ser selecionada para visualizá-los no Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restringir parâmetros globais para eliminar falsos positivos

- Desativar a inspeção do corpo da solicitação

   Ao definir **O órgão de solicitação de inspeção** para desligar, os órgãos de solicitação de todo o tráfego não serão avaliados pelo seu WAF. Isso pode ser útil se você souber que os órgãos de solicitação não são maliciosos para sua aplicação.

   Ao desativar esta opção, apenas o órgão de solicitação não é inspecionado. Os cabeçalhos e cookies permanecem inspecionados, a menos que os individuais sejam excluídos usando a funcionalidade da lista de exclusão.

- Limites de tamanho de arquivo

   Ao limitar o tamanho do arquivo para o seu WAF, você está limitando a possibilidade de um ataque acontecer com seus servidores web. Ao permitir que arquivos grandes sejam carregados, o risco de seu backend ser sobrecarregado aumenta. Limitar o tamanho do arquivo a um caso de uso normal para o seu aplicativo é apenas outra maneira de evitar ataques.

   > [!NOTE]
   > Se você sabe que seu aplicativo nunca precisará de qualquer upload de arquivo acima de um determinado tamanho, você pode restringir isso definindo um limite.

## <a name="firewall-metrics-waf_v1-only"></a>Métricas de firewall (somente WAF_v1)

Para firewalls de aplicativos web v1, as seguintes métricas estão agora disponíveis no portal: 

1. Contagem de solicitações bloqueadas do aplicativo da Web O número de solicitações que foram bloqueadas
2. Contagem de regras bloqueadas do Firewall do Aplicativo da Web Todas as regras que foram correspondidas **e** a solicitação foi bloqueada
3. Distribuição total de regras do Firewall de Aplicativos da Web Todas as regras que foram combinadas durante a avaliação
     
Para habilitar métricas, selecione a guia **Métricas** no portal e selecione uma das três métricas.

## <a name="next-steps"></a>Próximas etapas

Veja [como configurar o firewall de aplicativos web no Application Gateway](tutorial-restrict-web-traffic-powershell.md).
