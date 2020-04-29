---
title: Redirecionamento de URL da porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender como a Azure front door dá suporte ao redirecionamento de URL para suas rotas, se configurado.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295468"
---
# <a name="url-redirect"></a>Redirecionamento de URL
Você pode usar a porta frontal do Azure para redirecionar o tráfego. Você pode redirecionar o tráfego em vários níveis (protocolo, nome do host, caminho, Cadeia de caracteres de consulta) e toda a funcionalidade pode ser configurada para microserviços individuais, uma vez que o redirecionamento é baseado em caminho. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.
</br>

![Redirecionamento de URL de porta frontal do Azure][1]

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de status de resposta para que os clientes compreendam a finalidade do redirecionamento. Há suporte para os seguintes tipos de redirecionamento:

- **301 (movido permanentemente)**: indica que o recurso de destino recebeu um novo URI permanente e que qualquer referência futura a esse recurso deve usar um dos URIs incluídos. Use o código de status 301 para o redirecionamento de HTTP para HTTPS. 
- **302 (encontrado)**: indica que o recurso de destino reside temporariamente em um URI diferente. Como o redirecionamento pode ser alterado na ocasião, o cliente deve continuar a usar o URI de solicitação efetiva para solicitações futuras.
- **307 (redirecionamento temporário)**: indica que o recurso de destino reside temporariamente em um URI diferente e o agente do usuário não deve alterar o método de solicitação se ele executar um redirecionamento automático para esse URI. Como o redirecionamento pode mudar com o passar do tempo, o cliente deve continuar usando o URI de solicitação efetivo original para solicitações futuras.
- **308 (redirecionamento permanente)**: indica que o recurso de destino recebeu um novo URI permanente e que qualquer referência futura a esse recurso deve usar um dos URIs incluídos. Os clientes com recursos de edição de link, não precisarão revincular automaticamente as referências ao URI de solicitação efetiva para uma ou mais das novas referências enviadas pelo servidor, sempre que possível.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Você pode definir o protocolo que será usado para redirecionamento. Isso permite um dos casos de uso mais comuns do recurso de redirecionamento, que é definir HTTP para redirecionamento HTTPS.

- **Somente HTTPS**: defina o protocolo como somente HTTPS, se você estiver procurando redirecionar o tráfego de http para https. A porta frontal do Azure recomenda que você sempre defina o redirecionamento para HTTPS apenas.
- **Somente http**: isso redireciona a solicitação de entrada para http. Use esse valor somente se você quiser manter o tráfego HTTP que está, não criptografado.
- **Solicitação de correspondência**: essa opção retém o protocolo usado pela solicitação de entrada. Portanto, uma solicitação HTTP permanece HTTP e uma solicitação HTTPS permanece o redirecionamento de HTTPS post.

## <a name="destination-host"></a>Host de destino
Como parte da configuração de um roteamento de redirecionamento, você também pode alterar o nome do host ou domínio para a solicitação de redirecionamento. Você pode definir esse campo para alterar o nome do host na URL para o redirecionamento ou preservar o nome do host da solicitação de entrada. Portanto, usando esse campo, você pode redirecionar todas `https://www.contoso.com/*` as `https://www.fabrikam.com/*`solicitações enviadas para.

## <a name="destination-path"></a>Caminho de destino
Para casos em que você deseja substituir o segmento de caminho de uma URL como parte do redirecionamento, você pode definir esse campo com o novo valor de caminho. Caso contrário, você pode optar por preservar o valor do caminho como parte do redirecionamento. Portanto, usando esse campo, você pode redirecionar todas as `https://www.contoso.com/\*` solicitações `https://www.contoso.com/redirected-site`enviadas ao para.

## <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Você também pode substituir os parâmetros da cadeia de caracteres de consulta na URL redirecionada. Para substituir qualquer cadeia de caracteres de consulta existente da URL de solicitação de entrada, defina esse campo como ' replace ' e defina o valor apropriado. Caso contrário, você pode manter o conjunto original de cadeias de consulta definindo o campo como ' preserve '. Como exemplo, usando esse campo, você pode redirecionar todo o tráfego `https://www.contoso.com/foo/bar` enviado `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`para o. 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte da URL após ' # ', normalmente usado por navegadores para se colocar em uma seção específica em uma página. Você pode definir esse campo para adicionar um fragmento à URL de redirecionamento.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png