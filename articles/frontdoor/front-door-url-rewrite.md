---
title: Porta frontal do Azure-regravação de URL | Microsoft Docs
description: Este artigo ajuda você a entender como a porta frontal do Azure faz a regravação da URL para suas rotas, se configurada.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471465"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Regeneração de URL (caminho personalizado de encaminhamento)
A porta frontal do Azure dá suporte à regravação de URL, permitindo que você configure um **caminho de encaminhamento personalizado** opcional para usar ao construir a solicitação para encaminhar para o back-end. Por padrão, se nenhum caminho de encaminhamento personalizado for fornecido, o Front Door copiará o caminho de URL de entrada para a URL usada na solicitação encaminhada. O cabeçalho do Host usado na solicitação encaminhada ficará conforme configurado no back-end selecionado. Leia [Cabeçalho de host de back-end](front-door-backend-pool.md#hostheader) para saber o que ele faz e como configurá-lo.

A melhor parte da regeneração de URL usando o caminho de encaminhamento personalizado é que ele copiará qualquer parte do caminho de entrada que corresponda ao caminho curinga para o caminho encaminhado (esses segmentos de caminho são os segmentos em **verde** no exemplo a seguir):
</br>
![Regeneração de URL do Azure Front Door][1]

## <a name="url-rewrite-example"></a>Exemplo de regeneração de URL
Considere uma regra de roteamento com os seguintes hosts de front-end e os caminhos configurados:

| Hosts      | Caminhos       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de solicitações de entrada, e a segunda coluna mostra qual seria a rota "mais específica" correspondente com 'Path'.  A terceira e as subsequentes colunas da primeira linha da tabela são exemplos de **Caminhos de Encaminhamento Personalizados** configurados, com o restante das linhas dessas colunas representando os exemplos de como seria o caminho de solicitação encaminhada se ele correspondesse à solicitação nessa linha.

Por exemplo, se lermos na segunda linha, veremos que para a solicitação de entrada `www.contoso.com/sub`, se o caminho de encaminhamento personalizado fosse `/`, o caminho encaminhado seria `/sub`. Se o caminho de encaminhamento personalizado fosse `/fwd/`, o caminho encaminhado seria `/fwd/sub`. E assim por diante nas colunas restantes. As partes **enfatizadas** dos caminhos abaixo representam as que fazem parte da correspondência curinga.


| Solicitação de entrada       | Caminho de correspondência mais específica | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| \.contoso.com/www            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**sub** contoso.com/da Web     | /\*                      | /**projeto**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| \.contoso.com/foo www         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.contoso.com/foo/www        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**barra** de contoso.com/foo/da Web | /foo/\*                  | /**ênfase**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Configurações opcionais
Há outras configurações opcionais, que você também pode especificar para qualquer configuração de regra de roteamento:

* **Configuração de Cache** – se esta opção for desabilitada ou não for especificada, as solicitações que correspondem a essa regra de roteamento não tentarão usar o conteúdo em cache, mas sempre buscarão no back-end. Leia mais sobre [Cache com o Front Door](front-door-caching.md).



## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg