---
title: Avaliação de recursos - Personalizador
titleSuffix: Azure Cognitive Services
description: Quando você executa uma avaliação em seu recurso Personalizador a partir do portal Azure, o Personalr fornece informações sobre quais características de contexto e ações estão influenciando o modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242423"
---
# <a name="feature-evaluation"></a>Avaliação de recurso

Quando você executa uma avaliação em seu recurso Personalizador a partir do [portal Azure,](https://portal.azure.com)o Personalr fornece informações sobre quais características de contexto e ações estão influenciando o modelo. 

Isso é útil para:

* Imagine recursos adicionais que você poderia usar, inspirando-se em quais características são mais importantes no modelo.
* Veja quais recursos não são importantes e potencialmente remova-os ou analise melhor o que pode estar afetando o uso.
* Fornecer orientação às equipes editoriais ou de curadoria sobre novos conteúdos ou produtos que valham a pena trazer para o catálogo.
* Soluciona problemas e erros comuns que acontecem ao enviar recursos para o Personalr.

As características mais importantes têm pesos mais fortes no modelo. Como essas características têm um peso mais forte, elas tendem a estar presentes quando o Personalr obtém recompensas mais altas.

## <a name="getting-feature-importance-evaluation"></a>Obtendo avaliação de importância de destaque

Para ver os resultados de importância, você deve executar uma avaliação. A avaliação cria rótulos de características leíveis por humanos com base nos nomes de recursos observados durante o período de avaliação.

As informações resultantes sobre a importância dos recursos representam o modelo on-line do Personalizador atual. A avaliação analisa a importância do modelo salvo na data final do período de avaliação, após passar por todo o treinamento realizado durante a avaliação, com a atual política de aprendizagem online. 

Os resultados de importância do recurso não representam outras políticas e modelos testados ou criados durante a avaliação.  A avaliação não incluirá recursos enviados ao Personalr após o término do período de avaliação.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Como interpretar a avaliação da importância do longa-metragem

O Personalando avalia os recursos criando "grupos" de recursos que têm importância semelhante. Um grupo pode ser dito ter uma importância global mais forte do que outros, mas dentro do grupo, a ordenação de características é alfabética.

As informações sobre cada recurso incluem:

* Se o recurso vem de Contexto ou Ações.
* Chave de recurso e valor.

Por exemplo, um aplicativo de pedidos de sorvete pode ver "Context.Weather:Hot" como um recurso muito importante.

O personalizador exibe correlações de recursos que, quando levados em conta em conjunto, produzem recompensas mais altas.

Por exemplo, você pode ver "Context.Weather:Hot *with* Action.MenuItem:IceCream" bem como "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Ações que você pode tomar com base na avaliação de recursos

### <a name="imagine-additional-features-you-could-use"></a>Imagine recursos adicionais que você poderia usar

Inspire-se nas características mais importantes do modelo. Por exemplo, se você ver "Context.MobileBattery:Low" em um aplicativo móvel de vídeo, você pode pensar que o tipo de conexão também pode fazer com que os clientes escolham ver um clipe de vídeo em vez de outro, em seguida, adicione recursos sobre tipo de conectividade e largura de banda em seu aplicativo.

### <a name="see-what-features-are-not-important"></a>Veja quais características não são importantes

Potencialmente remover recursos sem importância ou analisar melhor o que pode afetar o uso. Os recursos podem ser baixos por muitas razões. Pode ser que genuinamente o recurso não afete o comportamento do usuário. Mas também pode significar que o recurso não é aparente para o usuário. 

Por exemplo, um site de vídeo pode ver que "Action.VideoResolution=4k" é um recurso de baixa importância, contradizendo a pesquisa do usuário. A causa pode ser que o aplicativo nem sequer menciona ou mostra a resolução do vídeo, para que os usuários não mudem seu comportamento com base nele.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornecer orientação às equipes editoriais ou de curadoria

Forneça orientações sobre novos conteúdos ou produtos que valham a pena trazer para o catálogo. O Personalizador foi projetado para ser uma ferramenta que aumenta o discernimento humano e as equipes. Uma maneira de fazer isso é fornecendo informações a grupos editoriais sobre o que há sobre produtos, artigos ou conteúdo que impulsiona o comportamento. Por exemplo, o cenário do aplicativo de vídeo pode mostrar que há um recurso importante chamado "Action.VideoEntities.Cat:true", levando a equipe editorial a trazer mais vídeos de gato.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Solucionando problemas e erros comuns

Problemas e erros comuns podem ser corrigidos alterando o código do aplicativo para que ele não envie recursos inapropriados ou formatados incorretamente para o Personalr. 

Erros comuns ao enviar recursos incluem o seguinte:

* Envio de informações pessoais identificáveis (PII). PiI específico para um indivíduo (como nome, número de telefone, números de cartão de crédito, endereços IP) não deve ser usado com personalizador. Se seu aplicativo precisar rastrear usuários, use um UUID não identificador ou algum outro número de ID do usuário. Na maioria dos cenários isso também é problemático.
* Com um grande número de usuários, é improvável que a interação de cada usuário pese mais do que toda a interação da população, então o envio de IDs de usuário (mesmo que não-PII) provavelmente adicionará mais ruído do que valor ao modelo.
* Enviando campos de data-hora como carimbos de tempo precisos em vez de valores de tempo featurizados. Ter recursos como Context.TimeStamp.Day=Monday ou "Context.TimeStamp.Hour"="13" é mais útil. Haverá no máximo 7 ou 24 valores de recurso para cada um. Mas "Context.TimeStamp":"1985-04-12T23:20:50.52Z" é tão preciso que não haverá como aprender com isso porque nunca mais acontecerá.

## <a name="next-steps"></a>Próximas etapas

Entenda [escalabilidade e desempenho](concepts-scalability-performance.md) com personalizador.

