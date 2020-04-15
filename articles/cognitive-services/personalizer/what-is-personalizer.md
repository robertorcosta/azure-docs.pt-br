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
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: ac6d72cbb69796851e58098138d51bb0988be4ec
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520986"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Personalizador do Azure é um serviço de API baseado em nuvem que ajuda seu aplicativo cliente a escolher o melhor item de _conteúdo_ para mostrar a cada usuário. O serviço seleciona o melhor item entre os itens de conteúdo com base nas informações coletivas em tempo real que você fornece sobre o conteúdo e o contexto.

Após você apresentar o item de conteúdo ao seu usuário, o sistema monitora o comportamento do usuário e relata uma pontuação de recompensa de volta para o Personalizador, a fim de melhorar sua capacidade de selecionar o melhor conteúdo com base nas informações de contexto que recebe.

O **conteúdo** pode ser qualquer unidade de informação, como texto, imagens, URLs ou emails que você deseja selecionar para mostrar ao usuário.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Como o Personalizador seleciona o melhor item de conteúdo?

O Personalizador usa o **aprendizado por reforço** para selecionar o melhor item (_ação_) com base no comportamento coletivo e nas pontuações de recompensa entre todos os usuários. As ações são os itens de conteúdo, como artigos de notícias, filmes específicos ou produtos entre os quais escolher.

A chamada de **Classificação** usa o item de ação, em conjunto com recursos da ação, bem como recursos de contexto para selecionar o item de ação superior:

* **Ações com recursos** – itens de conteúdo com recursos específicos a cada item
* **Recursos de contexto** – recursos de seus usuários, seu contexto ou seu ambiente ao usar o aplicativo

A chamada de Classificação retorna a ID cujo item de conteúdo ou __ação__ será mostrado ao usuário no campo **ID da Ação de Recompensa**.
A __ação__ mostrada ao usuário é escolhida com modelos de machine learning, tentando maximizar a quantidade total de recompensas ao longo do tempo.

Vários cenários de exemplo são:

|Tipo de conteúdo|**Ações (com recursos)**|**Recursos de contexto**|ID da Ação de Recompensa Retornada<br>(exibir este conteúdo)|
|--|--|--|--|
|Lista de notícias|a. `The president...` (nacional, política, [texto])<br>b. `Premier League ...` (global, esportes, [texto, imagem, vídeo])<br> c. `Hurricane in the ...` (regional, clima, [texto, imagem]|Dispositivo do qual as notícias são lidas<br>Mês ou temporada<br>|um `The president...`|
|Lista de filmes|1. `Star Wars` (1977, [ação, aventura, fantasia], George Lucas)<br>2. `Hoop Dreams` (1994, [documentário, esportes], Steve James<br>3. `Casablanca` (1942, [romance, drama, guerra], Michael Curtiz)|Dispositivo no qual o filme é assistido<br>tamanho da tela<br>Tipo de usuário<br>|3. `Casablanca`|
|Lista de produtos|i. `Product A` (3 kg, $$$$, entrega em 24 horas)<br>ii. `Product B` (20 kg, $$, entrega em 2 semanas com alfândega)<br>III. `Product C` (3 kg, $$$, entrega em 48 horas)|Dispositivo no qual a compra é lida<br>Camada de gastos do usuário<br>Mês ou temporada|ii. `Product B`|

O Personalizador usou o aprendizado por reforço para selecionar a melhor ação, conhecida como _ID da ação de recompensa_, com base em uma combinação de:
* Modelo treinado – informações passadas recebidas pelo serviço do Personalizador
* Dados atuais – ações específicas com recursos e recursos de contexto

## <a name="when-to-call-personalizer"></a>Quando chamar o Personalizador

A **API** de [Classificação](https://go.microsoft.com/fwlink/?linkid=2092082) do Personalizador é chamada _sempre_ que você apresenta conteúdo, em tempo real. Isso é conhecido como um **evento**, indicado com uma _ID do evento_.

A **API** de [Recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) do Personalizador pode ser chamada em tempo real ou atrasada para se adequar melhor à sua infraestrutura. Você determina a pontuação de recompensa com base em suas necessidades empresariais. A pontuação de recompensa está entre 0 e 1. Ela pode ter um valor único, como 1 para bom e 0 para ruim ou um número produzido por um algoritmo criado considerando suas metas e métricas empresariais.

## <a name="personalizer-content-requirements"></a>Requisitos de conteúdo do Personalizador

Use o Personalizador quando seu conteúdo:

* Tiver um conjunto limitado de itens (máximo de 50) entre os quais selecionar. Se tiver uma lista maior, [use um mecanismo de recomendação](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) para reduzir a lista a 50 itens.
* Tiver informações que descrevem o conteúdo que você deseja classificar: _ações com recursos_ e _recursos de contexto_.
* Tiver um mínimo de mil eventos relacionados a conteúdo por dia para que o Personalizador seja eficaz. Se o Personalizador não receber o tráfego mínimo necessário, o serviço levará mais tempo para determinar o melhor item de conteúdo.

Como o Personalizador usa informações coletivas quase em tempo real para retornar o melhor item de conteúdo, o serviço não:
* Persiste e gerencia informações de perfil do usuário
* Registra em log as preferências ou o histórico de usuários individuais
* Exige conteúdo limpo e rotulado

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Como projetar e implementar o Personalizador para seu aplicativo cliente

1. [Projete](concepts-features.md) e planeje para conteúdo, **_ações_** e **_contexto_** . Determine o algoritmo de recompensa para a pontuação de **_Recompensa_** .
1. Cada [Recurso do Personalizador](how-to-settings.md) criado é considerado 1 Loop de Aprendizado. O loop receberá as chamadas de Classificação e Recompensa para o conteúdo ou a experiência do usuário.
1. Adicione o Personalizador ao seu site ou sistema de conteúdo:
    1. Adicione uma chamada de **Classificação** para o Personalizador em seu aplicativo, site ou sistema para determinar o melhor item de _conteúdo_ antes que o conteúdo seja mostrado ao usuário.
    1. Exiba o melhor item de _conteúdo_, que é a _ID da ação de recompensa_ retornada, para o usuário.
    1. Aplique o _algoritmo_ às informações coletadas sobre como o usuário se comportou, para determinar a pontuação de **recompensa**, como:

        |Comportamento|Pontuação de recompensa calculada|
        |--|--|
        |O usuário selecionou o melhor item de _conteúdo_ (ID da ação de recompensa)|**1**|
        |O usuário selecionou outro conteúdo|**0**|
        |O usuário deixou em pausa ou rolou a tela de maneira indecisa antes de selecionar o melhor item de _conteúdo_ (ID da ação de recompensa)|**0.5**|

    1. Adicione uma chamada de **Recompensa** enviando uma pontuação de recompensa entre 0 e 1
        * Imediatamente após mostrar seu conteúdo
        * Ou algum tempo depois em um sistema offline
    1. [Avalie seu loop](concepts-offline-evaluation.md) com uma avaliação offline após um período de uso. Uma avaliação offline permite testar e avaliar a eficácia do serviço do Personalizador sem alterar o código nem afetar a experiência do usuário.

## <a name="next-steps"></a>Próximas etapas


* [Como funciona o Personalizador](how-personalizer-works.md)
* [O que é o Aprendizado de Reforço?](concepts-reinforcement-learning.md)
* [Saiba mais sobre os recursos e as ações para a solicitação de Classificação](concepts-features.md)
* [Saiba como determinar a pontuação da solicitação de Premiação](concept-rewards.md)
* [Inícios Rápidos](sdk-learning-loop.md)
* [Tutorial](tutorial-use-azure-notebook-generate-loop-data.md)
* [Usar a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
