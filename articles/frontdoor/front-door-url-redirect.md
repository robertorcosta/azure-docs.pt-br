---
title: Porta da Frente do Azure - Redirecionamento de URL | Microsoft Docs
description: Este artigo ajuda você a entender como o Azure Front Door suporta o redirecionamento de URL para suas rotas, se configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295468"
---
# <a name="url-redirect"></a>Redirecionamento de URL
Você pode usar o Azure Front Door para redirecionar o tráfego. Você pode redirecionar o tráfego em vários níveis (protocolo, nome de host, caminho, seqüência de consultas) e toda a funcionalidade pode ser configurada para microsserviços individuais à medida que o redirecionamento é baseado em caminho. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.
</br>

![URL da porta da frente do Azure redireciona][1]

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de status de resposta para que os clientes entendam o propósito do redirecionamento. Os seguintes tipos de redirecionamento são suportados:

- **301 (Movido permanentemente)**: Indica que o recurso de destino foi atribuído a um novo URI permanente e quaisquer referências futuras a este recurso devem usar um dos URIs incluídos. Use o código de status 301 para http para redirecionamento HTTPS. 
- **302 (Encontrado)**: Indica que o recurso de destino reside temporariamente um URI diferente. Uma vez que o redirecionamento pode ser alterado em algumas ocasiões, o cliente deve continuar a usar o URI de solicitação efetiva para solicitações futuras.
- **307 (Redirecionamento temporário)**: Indica que o recurso de destino reside temporariamente em um URI diferente e o agente do usuário NÃO deve alterar o método de solicitação se ele realizar um redirecionamento automático para esse URI. Uma vez que o redirecionamento pode mudar com o tempo, o cliente deve continuar usando o URI de solicitação efetiva original para solicitações futuras.
- **308 (Redirecionamento permanente)**: Indica que o recurso de destino foi atribuído a um novo URI permanente e quaisquer referências futuras a este recurso devem usar um dos URIs incluídos. Clientes com recursos de edição de links, devem revincular automaticamente referências ao URI de solicitação efetiva para uma ou mais das novas referências enviadas pelo servidor, sempre que possível.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Você pode definir o protocolo que será usado para redirecionamento. Isso permite um dos casos de uso mais comuns do recurso de redirecionamento, que é definir HTTP para redirecionamento HTTPS.

- **SOMENTE HTTPS**: Defina o protocolo apenas para HTTPS, se você estiver procurando redirecionar o tráfego de HTTP para HTTPS. O Azure Front Door recomenda que você sempre defina o redirecionamento apenas para HTTPS.
- **HTTP somente**: Isso redireciona a solicitação recebida para HTTP. Use este valor somente se quiser manter seu HTTP de tráfego, ou seja, não criptografado.
- **Solicitação de correspondência**: Esta opção mantém o protocolo usado pela solicitação recebida. Assim, uma solicitação HTTP permanece HTTP e uma solicitação HTTPS permanece em redirecionamento de post https.

## <a name="destination-host"></a>Host de destino
Como parte da configuração de um roteamento de redirecionamento, você também pode alterar o nome do host ou o domínio para a solicitação de redirecionamento. Você pode definir este campo para alterar o nome do host na URL para o redirecionamento ou preservar o nome do host da solicitação recebida. Assim, usando este campo você pode redirecionar todas as solicitações enviadas `https://www.contoso.com/*` para `https://www.fabrikam.com/*`.

## <a name="destination-path"></a>Caminho de destino
Para os casos em que você deseja substituir o segmento de caminho de uma URL como parte do redirecionamento, você pode definir este campo com o novo valor de caminho. Caso contrário, você pode optar por preservar o valor do caminho como parte do redirecionamento. Então, usando este campo, você pode redirecionar `https://www.contoso.com/redirected-site`todas as solicitações enviadas para `https://www.contoso.com/\*` .

## <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Você também pode substituir os parâmetros da seqüência de consulta na URL redirecionada. Para substituir qualquer seqüência de consulta existente da URL de solicitação recebida, defina este campo como 'Substituir' e, em seguida, defina o valor apropriado. Caso contrário, você pode reter o conjunto original de strings de consulta definindo o campo como 'Preservar'. Como exemplo, usando este campo, você pode `https://www.contoso.com/foo/bar` redirecionar todo o tráfego enviado para `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`. 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte da URL após '#', normalmente usada pelos navegadores para pousar em uma seção específica em uma página. Você pode definir este campo para adicionar um fragmento à URL de redirecionamento.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png