---
title: Porta da Frente do Azure - monitoramento de saúde backend | Microsoft Docs
description: Este artigo ajuda você a entender como o Azure Front Door monitora a saúde de seus backends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471567"
---
# <a name="health-probes"></a>Investigações de integridade

Para determinar a saúde e a proximidade de cada backend de um determinado ambiente front door, cada ambiente da Porta da Frente envia periodicamente uma solicitação HTTP/HTTPS sintética para cada um dos seus backends configurados. O Front Door, em seguida, usa as respostas desses testes para determinar os "melhores" back-ends aos quais ele deve rotear as solicitações reais do cliente. 

> [!WARNING]
> Como o Front Door tem muitos ambientes de borda globalmente, o volume de solicitações de sonda de saúde para seus backends pode ser bastante alto - variando de 25 solicitações a cada minuto até até 1200 solicitações por minuto, dependendo da freqüência do teste de saúde configurado. Com a freqüência padrão do teste de 30 segundos, o volume do teste no backend deve ser de cerca de 200 solicitações por minuto.

## <a name="supported-protocols"></a>Protocolos com suporte

O Front Door dá suporte para envio de investigações via protocolos HTTP ou HTTPS. Essas investigações são enviadas pelas mesmas portas TCP configuradas para rotear solicitações de clientes e não podem ser substituídas.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP suportados para sondas de saúde

Front Door suporta os seguintes métodos HTTP para o envio das sondas de saúde:

1. **GET:** O método GET significa recuperar qualquer informação (na forma de uma entidade) identificada pelo Request-URI.
2. **CABEÇA:** O método HEAD é idêntico ao GET, exceto que o servidor NÃO DEVE retornar um corpo de mensagem na resposta. Para novos perfis de Porta frontal, por padrão, o método do teste é definido como HEAD.

> [!NOTE]
> Para reduzir a carga e o custo em seus backends, o Front Door recomenda o uso de solicitações de HEAD para sondas de saúde.

## <a name="health-probe-responses"></a>Respostas de investigação de integridade

| Respostas  | Descrição | 
| ------------- | ------------- |
| Determinar integridade  |  Um código de status 200 OK indica que o back-end está íntegro. Quaisquer outros resultados são considerados uma falha. Se por algum motivo (incluindo falha de rede) uma resposta HTTP válida não for recebida para uma investigação, a investigação será contada como uma falha.|
| Medir latência  | A latência é o tempo total medido a partir do momento imediatamente antes de enviarmos a solicitação de investigação até o momento em que recebemos o último byte da resposta. Usamos uma nova conexão TCP para cada solicitação, portanto, essa medida não é tendenciosa em relação aos back-ends com conexões passivas existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como o Front Doo l determina a integridade do back-end

O Azure Front Door usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a saúde.

1. Excluir back-ends desabilitados.

2. Exclua back-ends com erros de investigações de integridade:
    * Essa seleção é feita observando as últimas _n_ respostas de investigação de integridade. Se pelo menos _x_ estiver íntegro, o back-end será considerado íntegro.

    * _n_ é configurado alterando a propriedade SampleSize em configurações de balanceamento de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired em configurações de balanceamento de carga.

3. Fora do conjunto de back-ends íntegros no pool de back-end, o Front Door também mede e mantém a latência (tempo de resposta) para cada back-end.


## <a name="complete-health-probe-failure"></a>Falha completa na investigação de integridade

Se as investigações de integridade falharem em todos os back-end de um pool de back-end, o Front Door considerará todos os back-ends íntegros e roteará o tráfego em uma distribuição round robin em todos eles.

Uma vez que qualquer backend retorne a um estado saudável, então a Porta da Frente retomará o algoritmo normal de balanceamento de carga.

## <a name="disabling-health-probes"></a>Desabilitando testes de saúde

Se você tiver um único backend em seu pool de backend, você pode optar por desativar os testes de saúde reduzindo a carga no backend do aplicativo. Mesmo que você tenha vários backends na piscina de backend, mas apenas um deles está em estado habilitado, você pode desativar as sondas de saúde.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
