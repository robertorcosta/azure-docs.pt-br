---
title: O que é o Personalizador?
titleSuffix: Azure Cognitive Services
description: O Personalizador é um serviço de API baseado em nuvem que permite escolher a melhor experiência a ser mostrada aos usuários, aprendendo com o comportamento deles em tempo real.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707944"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O Personalizador do Azure é um serviço de API baseado em nuvem que permite que seu aplicativo escolha a melhor experiência a ser mostrada aos usuários, aprendendo com o comportamento coletivo deles em tempo real.

* Forneça informações sobre os usuários e o conteúdo e receba a ação principal a ser mostrada aos usuários. 
* Não é necessário limpar nem rotular os dados antes de usar o Personalizador.
* Forneça comentários ao Personalizador quando for conveniente para você. 
* Exiba a análise em tempo real. 

Veja uma demonstração de [como o Personalizador funciona](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Como funciona o Personalizador?

O Personalizador usa modelos de machine learning para descobrir qual ação deve ter a classificação mais alta em um contexto. Seu aplicativo cliente fornece uma lista de possíveis ações, com informações sobre elas, e informações sobre o contexto, que podem incluir informações sobre o usuário, o dispositivo etc. O Personalizador determina a ação a ser tomada. Depois que o aplicativo cliente usa a ação escolhida, ele fornece comentários ao Personalizador na forma de uma pontuação de recompensa. Após o recebimento dos comentários, o Personalizador atualiza automaticamente o próprio modelo usado para classificações futuras. Com o passar do tempo, o Personalizador treinará um modelo que pode sugerir a melhor ação a ser escolhida em cada contexto com base em seus recursos.

## <a name="how-do-i-use-the-personalizer"></a>Como fazer para usar o Personalizador?

![Como usar o Personalizador para escolher qual vídeo será mostrado a um usuário](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Escolha uma experiência em seu aplicativo a ser personalizada.
1. Crie e configure uma instância do Serviço de Personalização no portal do Azure. Cada instância é um Loop do Personalizador.
1. Use a [API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) para chamar o Personalizador com informações (_recursos_) sobre os usuários e o conteúdo (_ações_). Você não precisa fornecer dados limpos e rotulados antes de usar o Personalizador. As APIs podem ser chamadas diretamente ou usando SDKs disponíveis para diferentes linguagens de programação.
1. No aplicativo cliente, mostre ao usuário a ação selecionada pelo Personalizador.
1. Use a [API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) para fornecer comentários ao Personalizador, indicando se o usuário selecionou a ação do Personalizador. Essa é uma _[pontuação de recompensa](concept-rewards.md)_ .
1. Exiba a análise no portal do Azure para avaliar como o sistema está funcionando e como seus dados estão ajudando a personalização.

## <a name="where-can-i-use-personalizer"></a>Em que situações posso usar o Personalizador?

Por exemplo, seu aplicativo cliente pode adicionar o Personalizador para:

* Personalizar qual artigo é realçado em um site de notícias.    
* Otimizar o posicionamento de anúncios em um site.
* Exibir um "item recomendado" personalizado em um site de compras.
* Sugerir a aplicação de elementos de interface do usuário, como filtros, a uma foto específica.
* Escolher uma resposta do chatbot para esclarecer a intenção do usuário ou sugerir uma ação.
* Priorizar as sugestões do que um usuário deve fazer como a próxima etapa em um processo empresarial.

O Personalizador não é um serviço destinado a persistir e gerenciar informações de perfil do usuário nem para registrar as preferências ou o histórico de usuários individuais. O Personalizador aprende com os recursos de cada interação na ação de um contexto de um único modelo que pode obter recompensas máximas quando ocorrem recursos semelhantes. 

## <a name="personalization-for-developers"></a>Personalização para desenvolvedores

O serviço Personalizador tem duas APIs:

* *Classificação*: use a API de classificação para determinar qual _ação_ mostrar, no _contexto_ atual. As ações são enviadas como uma matriz de objetos JSON, com uma ID e informações (_recursos_) sobre cada um. O contexto é enviado como outro objeto JSON. A API retorna a actionId que seu aplicativo deve renderizar para o usuário.
* *Recompensa*: Depois que o usuário interage com seu aplicativo, você mede o quão bem a personalização funcionou com um número entre 0 e 1 e a envia como uma [pontuação de recompensa](concept-rewards.md). 

![Sequência básica de eventos para personalização](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Próximas etapas

* [O que há de novo no Personalizador?](whats-new.md)
* [Como o Personalizador funciona?](how-personalizer-works.md)
* [O que é o Aprendizado de Reforço?](concepts-reinforcement-learning.md)
* [Saiba mais sobre os recursos e as ações para a solicitação de Classificação](concepts-features.md)
* [Saiba como determinar a pontuação da solicitação de Premiação](concept-rewards.md)
* [Usar a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
