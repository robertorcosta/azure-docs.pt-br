---
title: Listas de exclusão de firewall de aplicativos da Web no Azure Front Door - portal Azure
description: Este artigo fornece informações sobre a configuração de listas de exclusão no Azure Front com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925924"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>WAF (Web Application Firewall) com listas de exclusão do Front Door Service 

Às vezes, o WAF (Web Application Firewall, firewall de aplicativos da Web) pode bloquear uma solicitação que você deseja permitir para o seu aplicativo. Por exemplo, o Active Directory insere tokens usados para autenticação. Esses tokens podem conter caracteres especiais que podem desencadear um falso positivo das regras do WAF. As listas de exclusões do WAF permitem a você omitir certos atributos de solicitação de uma avaliação do WAF.  Uma lista de exclusão pode ser configurada usando [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal Azure. O exemplo a seguir mostra a configuração do portal Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configure listas de exclusão usando o portal Azure
**Gerenciar exclusões** é acessível a partir do portal WAF **regras gerenciadas**

![Gerenciar](../media/waf-front-door-exclusion/exclusion1.png)
![a exclusão gerenciar exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Uma lista de ![exclusão de exemplo: Gerenciar exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Este exemplo exclui o valor no campo de cabeçalho do *usuário.* Uma solicitação válida pode incluir o campo do *usuário* que contém uma string que aciona uma regra de injeção SQL. Você pode excluir o parâmetro do *usuário* neste caso para que a regra WAF não avalie nada no campo.

Os seguintes atributos podem ser adicionados às listas de exclusão por nome. Os valores dos campos que você usa não são avaliados contra as regras do WAF, mas seus nomes são avaliados. As listas de exclusão removem a inspeção do valor do campo.

* Solicitar nome de cabeçalho
* Solicitar nome de cookie
* Nome args de seqüência de consulta
* Solicitar nome de corpo args

Você pode especificar um cabeçalho de solicitação exato, corpo, cookie ou uma correspondência de atributo de cadeia de consulta.  Outra opção é especificar correspondências parciais. Os seguintes operadores são os critérios de correspondência suportados:

- **Equals**: esse operador é usado para uma correspondência exata. Por exemplo, para selecionar um cabeçalho chamado **bearerToken,** use o operador equals com o seletor definido como **bearerToken**.
- **Começa com**: esse operador corresponde com todos os campos que começam com o valor do seletor especificado.
- **Termina com**: esse operador corresponde com todos os campos de solicitação que terminam com o valor do seletor especificado.
- **Contém**: esse operador corresponde com todos os campos de solicitação que contenham o valor do seletor especificado.
- **Igual a qualquer**: Este operador corresponde a todos os campos de solicitação. * é o valor do seletor.

Nomes de cabeçalho e cookie são insensíveis ao caso.

Você pode aplicar a lista de exclusão a todas as regras dentro do conjunto de regras gerenciados, às regras para um grupo de regras específico ou a uma única regra, como mostrado no exemplo anterior. 

## <a name="next-steps"></a>Próximas etapas

Depois de configurar suas configurações de WAF, aprenda a visualizar seus registros waf. Para obter mais informações, consulte [os diagnósticos da Porta da Frente](../afds/waf-front-door-monitor.md).
