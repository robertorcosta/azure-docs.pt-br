---
title: Azure Front Door | Microsoft Docs
description: Este artigo fornece uma visão geral do Azure Front Door. Descubra se ele é a escolha certa para balancear a carga do tráfego de usuário para seu aplicativo.
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
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515791"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>O que é o mecanismo de regras para o Azure Front Door? 

O mecanismo de regras permite que você personalize como as solicitações HTTP são tratadas na borda e dá mais controle sobre o comportamento do seu aplicativo Web. O mecanismo de regras para o Azure Front Door é composto por vários recursos principais, incluindo:

- Roteamento baseado em cabeçalho – roteie solicitações com base nos padrões no conteúdo de cabeçalhos de solicitação, cookies e cadeias de consulta.
- Roteamento baseado em parâmetro – aproveite uma série de condições de correspondência, incluindo argumentos post, cadeias de caracteres de consulta, cookies e métodos de solicitação, para rotear solicitações com base nos parâmetros de solicitação HTTP. 
- Substituições de configurações de rota: 
    - use funcionalidades de redirecionamento para retornar redirecionamentos de 301/302/307/308 para o cliente para redirecionar para novos nomes de host, caminhos e protocolos. 
    - Use os recursos de encaminhamento para reescrever o caminho da URL de solicitação sem fazer um redirecionamento tradicional e encaminhar a solicitação para o back-end apropriado em seu pool de back-end configurado. 
    - Personalize sua configuração de cache e altere dinamicamente uma rota do encaminhamento para armazenamento em cache com base nas condições de correspondência. 

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="architecture"></a>Arquitetura 

O mecanismo de regras lida com solicitações na borda. Depois de configurar o mecanismo de regras, quando uma solicitação atingir o ponto de extremidade do Front Door, o WAF será executado primeiro, seguido da configuração do mecanismo de regras associado ao seu front-end/domínio. Quando uma configuração do mecanismo de regras é executada, isso significa que a regra de roteamento pai já é uma correspondência. A execução de todas as ações em cada uma das regras dentro da configuração do mecanismo de regras depende de todas as condições de correspondência dentro da regra serem atendidas. Se uma solicitação não corresponder a nenhuma das condições na configuração do mecanismo de regra, a regra de roteamento padrão será executada. 

Por exemplo, na configuração abaixo, um mecanismo de regras é configurado para acrescentar um cabeçalho de resposta que alterará a idade máxima do controle de cache se a condição de correspondência for atendida. 

![ação do cabeçalho de resposta](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Em outro exemplo, vemos que o mecanismo de regras está configurado para enviar um usuário para uma versão móvel do site quando a condição de correspondência, o tipo de dispositivo, é verdadeira. 

![substituição de configuração de rota](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Em ambos os exemplos, quando nenhuma das condições de correspondência é atendida, a regra de rota especificada é executada. 

## <a name="terminology"></a>Terminologia 

Com o mecanismo de regras do AFD, você pode criar uma série de configurações de mecanismo de regras, cada uma composta por um conjunto de regras. Veja a seguir a descrição de algumas terminologias úteis que você encontrará ao configurar o mecanismo de regras. 

- *Configuração do mecanismo de regras*: um conjunto de regras aplicadas a uma regra de rota. Cada configuração é limitada a cinco regras. Você pode criar até 10 configurações. 
- *Regra do mecanismo de regras*: uma regra composta por até 10 condições de correspondência e cinco ações.
- *Condição de correspondência*: há várias condições de correspondência que podem ser utilizadas para analisar suas solicitações de entrada. Uma regra pode conter até 10 condições de correspondência. As condições de correspondência são avaliadas com um operador **AND**. Uma lista completa de condições de correspondência pode ser encontrada [aqui](front-door-rules-engine-match-conditions.md). 
- *Ação*: as ações determinam o que acontece às suas solicitações de entrada – ações de cabeçalho de solicitação/resposta, encaminhamento, redirecionamentos e regravações estão disponíveis hoje. Uma regra pode conter até cinco ações; no entanto, uma regra pode conter apenas uma substituição de configuração de rota.  Uma lista completa de ações pode ser encontrada [aqui](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba como definir sua primeira [Configuração do mecanismo de regras](front-door-tutorial-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
