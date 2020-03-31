---
title: Regra personalizada de firewall de aplicativos da Web para porta frontal do Azure
description: Aprenda a usar as regras personalizadas do WaF (Web Application Firewall, firewall de aplicativos da Web) que protegem seus aplicativos web contra ataques maliciosos.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475817"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para firewall de aplicativos web com porta frontal do Azure

O WaF (Azure Web Application Firewall) com o Front Door permite que você controle o acesso aos seus aplicativos web com base nas condições definidas. Uma regra WAF personalizada consiste em um número de prioridade, tipo de regra, condições de correspondência e uma ação. Existem dois tipos de regras personalizadas: regras de correspondência e regras de limite de taxa. Uma regra de correspondência controla o acesso com base em um conjunto de condições de correspondência, enquanto uma regra de limite de taxa controla o acesso com base em condições de correspondência e nas taxas de solicitações recebidas. Você pode desativar uma regra personalizada para evitar que ela seja avaliada, mas ainda assim manter a configuração. 

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de jogo e tipos de ação

Você pode controlar o acesso com uma regra WAf personalizada que define um número de prioridade, um tipo de regra, uma matriz de condições de correspondência e uma ação. 

- **Prioridade:** é um inteiro único que descreve a ordem de avaliação das regras do WAF. Regras com valores de menor prioridade são avaliadas antes de regras com valores mais elevados. Os números prioritários devem ser únicos entre todas as regras personalizadas.

- **Ação:** define como encaminhar uma solicitação se uma regra WAF for compatível. Você pode escolher uma das ações abaixo a serem aplicadas quando uma solicitação corresponder a uma regra personalizada.

    - *Permitir* - O WAF encaminha a missão para o back-end, registra uma entrada em logs e saídas WAF.
    - *Bloqueio* - A solicitação é bloqueada, o WAF envia resposta ao cliente sem encaminhar a solicitação para o back-end. O WAF registra uma entrada nos registros waf.
    - *Log* - O WAF registra uma entrada nos logs waf e continua a avaliar a próxima regra.
    - *Redirecionamento* - O WAF redireciona a solicitação para um URI especificado, registra uma entrada em logs WAF e sai.

- **Condição de correspondência:** define uma variável de correspondência, um operador e valor de correspondência. Cada regra pode conter várias condições de correspondência. Uma condição de correspondência pode ser baseada em localização geográfica, endereços IP do cliente (CIDR), tamanho ou correspondência de strings. A correspondência de cordas pode ser contra uma lista de variáveis de correspondência.
  - **Variável de correspondência:**
    - RequestMethod
    - QueryString
    - PostArgs
    - Requesturi
    - RequestHeader
    - RequestBody
    - Cookies
  - **Operador:**
    - Qualquer: é frequentemente usado para definir a ação padrão se nenhuma regra for correspondida. Qualquer um é compatível com todos os operadores.
    - Igual a
    - Contém
    - Menosque: restrição de tamanho
    - MaiorThan: restrição de tamanho
    - LessthanOrEqual: restrição de tamanho
    - MaiorthanorEqual: restrição de tamanho
    - BeginsWith
    - EndsWith
    - Regex
  
  - **A Regex** não suporta as seguintes operações: 
    - Referências de fundo e captura de subexpressões
    - Afirmações arbitrárias de largura zero
    - Referências de subrotina e padrões recursivos
    - Padrões condicionais
    - Verbos de controle de retrocesso
    - A diretiva \C single-byte
    - A diretiva \R newline match
    - O \K start of match reset directive
    - Chamadas e código incorporado
    - Agrupamento atômico e quantificadores possessivos

  - **Negação [opcional]:** Você pode definir a condição *de negação* como verdadeira se o resultado de uma condição deve ser negado.
      
  - **Transformar [opcional]:** Uma lista de strings com nomes de transformações a serem fariam antes da partida é tentada. Estas podem ser as seguintes transformações:
     - Letras Maiúsculas 
     - Letras minúsculas
     - Trim
     - Remover Nulls
     - Urldecode
     - Urlencode
     
   - **Valor da correspondência:** Os valores do método de solicitação HTTP suportados incluem:
     - GET
     - POST
     - PUT
     - HEAD
     - Delete (excluir)
     - LOCK
     - UNLOCK
     - PERFIL
     - OPÇÕES
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - COPIAR
     - MOVE

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemplo de regras personalizadas waf com base em parâmetros http

Aqui está um exemplo que mostra a configuração de uma regra personalizada com duas condições de correspondência. As solicitações são de um site especificado, conforme definido pelo referrer, e a seqüência de consultas não contém "senha".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Uma configuração de exemplo para bloquear o método "PUT" é mostrada como abaixo:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Restrição de tamanho

Você pode criar uma regra personalizada que especifica a restrição de tamanho em parte de uma solicitação recebida. Por exemplo, abaixo da regra bloqueia uma Url com mais de 100 caracteres.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Próximas etapas
- [Configure uma política de firewall de aplicativos da Web usando o Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Saiba mais sobre [o Firewall de Aplicativos Web com a Porta da Frente](afds-overview.md)
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).

