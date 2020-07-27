---
title: Mecanismo de Regras para a terminologia e a arquitetura do Azure Front Door
description: Este artigo fornece uma visão geral do recurso mecanismo de regras do Azure Front Door.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 1332f4b21f971d23055c158c2aebdd3316f1bd39
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512207"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>O que é o mecanismo de regras para o Azure Front Door? 

O mecanismo de regras permite que você personalize como as solicitações HTTP são tratadas na borda e dá mais controle sobre o comportamento do seu aplicativo Web. O mecanismo de regras para o Azure Front Door é composto por vários recursos principais, incluindo:

- Impor o HTTPS e garantir que todos os usuários finais interajam com o conteúdo em uma conexão segura.
- Implementar cabeçalhos de segurança para impedir vulnerabilidades baseadas em navegador como HSTS (HTTP Strict-Transport-Security), X-XSS-Protection, Content-Security-Policy e X-Frame-Options, bem como cabeçalhos Access-Control-Allow-Origin para cenários do CORS (compartilhamento de recursos entre origens). Os atributos baseados em segurança também podem ser definidos com cookies.
- Rotear solicitações para versões móveis ou de desktop do seu aplicativo com base nos padrões do conteúdo de cabeçalhos de solicitação, cookies ou cadeias de consulta.
- Usar funcionalidades de redirecionamento para retornar redirecionamentos 301, 302, 307 e 308 ao cliente, a fim de redirecioná-lo para novos nomes de host, caminhos e protocolos.
- Modificar dinamicamente a configuração de cache da rota com base nas solicitações de entrada.
- Reescrever o caminho da URL de solicitação e encaminhar a solicitação para o back-end apropriado no seu pool de back-end configurado.

## <a name="architecture"></a>Arquitetura 

O mecanismo de regras lida com solicitações na borda. Depois de configurar o mecanismo de regras, quando uma solicitação atingir o ponto de extremidade do Front Door, o WAF será executado primeiro, seguido da configuração do mecanismo de regras associado ao seu front-end/domínio. Quando uma configuração do mecanismo de regras é executada, isso significa que a regra de roteamento pai já é uma correspondência. A execução de todas as ações em cada uma das regras dentro da configuração do mecanismo de regras depende de todas as condições de correspondência dentro da regra serem atendidas. Se uma solicitação não corresponder a nenhuma das condições na configuração do mecanismo de regra, a regra de roteamento padrão será executada. 

Por exemplo, na configuração abaixo, um mecanismo de regras é configurado para acrescentar um cabeçalho de resposta que alterará a idade máxima do controle de cache se a condição de correspondência for atendida. 

![ação do cabeçalho de resposta](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Em outro exemplo, vemos que o mecanismo de regras está configurado para enviar um usuário para uma versão móvel do site quando a condição de correspondência, o tipo de dispositivo, é verdadeira. 

![substituição de configuração de rota](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Em ambos os exemplos, quando nenhuma das condições de correspondência é atendida, a regra de rota especificada é executada. 

## <a name="terminology"></a>Terminologia 

Com o mecanismo de regras do AFD, você pode criar uma série de configurações de mecanismo de regras, cada uma composta por um conjunto de regras. Veja a seguir a descrição de algumas terminologias úteis que você encontrará ao configurar o mecanismo de regras. 

- *Configuração do mecanismo de regras*: um conjunto de regras aplicadas a uma regra de rota. Cada configuração é limitada a 25 regras. Você pode criar até 10 configurações. 
- *Regra do mecanismo de regras*: uma regra composta por até 10 condições de correspondência e cinco ações.
- *Condição de correspondência*: há várias condições de correspondência que podem ser utilizadas para analisar suas solicitações de entrada. Uma regra pode conter até 10 condições de correspondência. As condições de correspondência são avaliadas com um operador **AND**. Uma lista completa de condições de correspondência pode ser encontrada [aqui](front-door-rules-engine-match-conditions.md). 
- *Ação*: as ações determinam o que acontece às suas solicitações de entrada – ações de cabeçalho de solicitação/resposta, encaminhamento, redirecionamentos e regravações estão disponíveis hoje. Uma regra pode conter até cinco ações; no entanto, uma regra pode conter apenas uma substituição de configuração de rota.  Uma lista completa de ações pode ser encontrada [aqui](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba como definir sua primeira [Configuração do mecanismo de regras](front-door-tutorial-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
