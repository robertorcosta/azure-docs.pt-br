---
title: Azure Web Application Firewall (WAF) v2 regras personalizadas no Gateway de Aplicativos
description: Este artigo fornece uma visão geral das regras personalizadas do WaF (Web Application Firewall) v2 no Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031724"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regras personalizadas para o Web Application Firewall v2 no Gateway de aplicativo do Azure

O WaF (Azure Application Gateway Web Application Firewall) v2 vem com um conjunto de regras pré-configurado e gerenciado pela plataforma que oferece proteção contra muitos tipos diferentes de ataques. Esses ataques incluem scripting entre sites, injeção de SQL e outros. Se você é um admin WAF, você pode querer escrever suas próprias regras para aumentar as regras de crs (core rule set). Suas regras podem bloquear ou permitir tráfego solicitado com base em critérios correspondentes.

As regras personalizadas permitem que você crie suas próprias regras que são avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. As regras personalizadas contêm um nome de regra, prioridade de regra e uma série de condições de correspondência. Se essas condições forem atendidas, uma ação será tomada (para permitir ou bloquear).

Por exemplo, você pode bloquear todas as solicitações de um endereço IP na faixa 192.168.5.4/24. Nesta regra, o operador é *IPMatch*, o matchValues é o intervalo de endereçoIP (192.168.5.4/24), e a ação é bloquear o tráfego. Você também define o nome e a prioridade da regra.

As regras personalizadas suportam o uso da lógica de composição para tornar as regras mais avançadas que atendem às suas necessidades de segurança. Por exemplo, (Condição 1 **e** Condição 2) **ou** Condição 3). Isso significa que, se a Condição 1 **e** a Condição 2 forem atendidas, **ou** se a Condição 3 for atendida, o WAF deve tomar as medidas especificadas na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra são sempre compostas usando **e**. Por exemplo, bloqueie o tráfego de um endereço IP específico e somente se eles estiverem usando um determinado navegador.

Se você quiser **ou** duas condições diferentes, as duas condições devem estar em regras diferentes. Por exemplo, bloqueie o tráfego de um endereço IP específico ou bloqueie o tráfego se eles estiverem usando um navegador específico.

> [!NOTE]
> O número máximo de regras aduaneiras waf é 100. Para obter mais informações sobre os limites do Application Gateway, consulte [os limites de assinatura e serviço do Azure, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Expressões regulares também são suportadas em regras personalizadas, assim como nos conjuntos de regras do CRS. Por exemplo, consulte Exemplos 3 e 5 em [Criar e usar regras personalizadas de firewall de aplicativos web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo o bloqueio vs.

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, você pode bloquear todo o tráfego proveniente de uma série de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vem de um navegador específico.

Para permitir algo, `-Action` certifique-se de que o parâmetro está definido como **Permitir**. Para bloquear algo, `-Action` certifique-se de que o parâmetro está definido como **Bloquear**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Os `$BlockRule` mapas anteriores para a seguinte regra personalizada no Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Esta regra personalizada contém um nome, prioridade, uma ação e o conjunto de condições de correspondência que devem ser cumpridas para que a ação ocorra. Para obter mais explicações sobre esses campos, consulte as seguintes descrições de campo. Por exemplo, regras personalizadas, consulte [Criar e usar regras personalizadas de firewall de aplicativos web](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

O nome da regra.  Aparece nos registros.

### <a name="priority-required"></a>Prioridade [necessária]

- Determina a ordem de avaliação de regras. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1 a 100. 
- Deve ser único em todas as regras personalizadas. Uma regra com prioridade 40 é avaliada antes de uma regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [necessário]

Atualmente, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [necessária]

Deve ser uma das variáveis:

- RemoteAddr – Endereço IP/nome de host da conexão remota do computador
- RequestMethod – MÉTODO DE solicitação HTTP (GET, POST, PUT, DELETE, e assim por diante.)
- QueryString – Variável no URI
- PostArgs – Argumentos enviados no órgão DO CORREIO. As regras personalizadas que usam essa variável de correspondência só serão aplicadas se o cabeçalho 'Tipo de conteúdo' estiver definido como 'application/x-www-form-urlencoded' e 'multipart/form-data'.
- RequestUri – URI da solicitação
- RequestHeaders – Cabeçalhos da solicitação
- RequestBody – Este contém todo o corpo de solicitação como um todo. As regras personalizadas que usam essa variável de correspondência só são aplicadas se o cabeçalho 'Tipo de conteúdo' estiver definido como 'aplicativo/x-www-form-urlencoded'. 
- RequestCookies – Cookies da solicitação

### <a name="selector-optional"></a>Seletor [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se o matchVariable for RequestHeaders, o seletor pode estar no cabeçalho *do Agente de Usuário.*

### <a name="operator-required"></a>Operador [necessário]

Deve ser um dos seguintes operadores:

- IPMatch - usado apenas quando a variável de correspondência é *RemoteAddr*
- Equals – a entrada é a mesma do MatchValue
- Contém
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (pré-visualização)

### <a name="negate-condition-optional"></a>Condição de negação [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transforme [opcional]

Uma lista de strings com nomes de transformações a serem fariam antes da partida é tentada. Estas podem ser as seguintes transformações:

- Letras minúsculas
- Trim
- Urldecode
- Urlencode 
- Remover Nulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [necessários]

Lista de valores a serem igualados, que podem ser considerados como *sendo OR*'ed. Por exemplo, pode ser endereços IP ou outras strings. O formato de valor depende do operador anterior.

### <a name="action-required"></a>Ação [necessária]

- Permitir – Autoriza a transação, pulando todas as outras regras. A solicitação especificada é adicionada à lista de permissão e, uma vez combinada, a solicitação interrompe uma avaliação adicional e é enviada para o pool de backend. As regras que estão na lista de permitir não são avaliadas para quaisquer regras personalizadas ou regras gerenciadas.
- Bloco – Bloqueia a transação com base no *SecDefaultAction* (modo de detecção/prevenção). Assim como a ação Permitir, uma vez que a solicitação é avaliada e adicionada à lista de blocos, a avaliação é interrompida e a solicitação é bloqueada. Qualquer solicitação depois disso atende às mesmas condições não será avaliada e apenas será bloqueada. 
- Log – Permite que a regra escreva para o log, mas permite que o resto das regras corra para avaliação. As demais regras aduaneiras são avaliadas por ordem de prioridade, seguidas pelas regras gerenciadas.

## <a name="geomatch-custom-rules-preview"></a>Regras personalizadas do Geomatch (visualização)

As regras personalizadas permitem que você crie regras personalizadas para atender às necessidades exatas de seus aplicativos e políticas de segurança. Você pode restringir o acesso aos seus aplicativos web por país/região. Para obter mais informações, consulte [regras personalizadas do Geomatch (visualização)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre regras personalizadas, [crie suas próprias regras personalizadas.](create-custom-waf-rules.md)
