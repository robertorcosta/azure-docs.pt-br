---
title: Terminologia - Personalizador
description: O personalizante usa terminologia a partir do aprendizado reforçado. Estes termos são usados no portal Azure e nas APIs.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624270"
---
# <a name="terminology"></a>Terminologia

O personalizante usa terminologia a partir do aprendizado reforçado. Estes termos são usados no portal Azure e nas APIs.

## <a name="conceptual-terminology"></a>Terminologia conceitual

* **Ciclo de aprendizagem**: Você cria um recurso Personalizador, chamado de ciclo de _aprendizagem,_ para cada parte do seu aplicativo que pode se beneficiar da personalização. Caso você tenha mais de uma experiência para personalização, crie um loop para cada uma.

* **Modelo**: Um modelo personalizado captura todos os dados aprendidos sobre o comportamento do usuário, obtendo dados de treinamento a partir da combinação dos argumentos que você envia para chamadas rank e reward, e com um comportamento de treinamento determinado pela Política de Aprendizagem.

## <a name="personalizer-configuration"></a>Configuração do personalizador

O personalizador é configurado a partir do [portal Azure](https://portal.azure.com).

* **Recompensas**: configure os valores padrão para tempo de espera de recompensa, recompensa padrão e política de agregação de recompensas.

* **Exploração**: configurar a porcentagem de chamadas rank para uso para exploração

* **Frequência de atualização do modelo**: Com que frequência o modelo é retreinado.

* **Retenção de dados**: Quantos dias de dados para armazenar. Isso pode impactar avaliações offline, que são usadas para melhorar seu ciclo de aprendizado.

## <a name="use-rank-and-reward-apis"></a>Use APIs de classificação e recompensa

* **Rank**: Dadas as ações com recursos e os recursos de contexto, use explorar ou explorar para retornar a ação superior (item de conteúdo).

    * **Ações**: Ações são os itens de conteúdo, como produtos ou promoções, para escolher. O Personalizador escolhe a ação superior (ID de ação de recompensa devolvida) para mostrar aos seus usuários através da API Rank.

    * **Contexto**: Para fornecer uma classificação mais precisa, forneça informações sobre seu contexto, por exemplo:
        * Seu usuário.
        * O dispositivo que ele está usando.
        * A hora atual.
        * Outros dados sobre a situação atual.
        * Dados históricos sobre o usuário ou o contexto.

        Seu aplicativo específico pode ter informações de contexto diferentes.

    * **[Características](concepts-features.md)**: Uma unidade de informações sobre um item de conteúdo ou um contexto de usuário. Certifique-se de usar apenas recursos agregados. Não use horários específicos, IDs de usuário ou outros dados não agregados como recursos.

        * Um _recurso de ação_ é metadados sobre o conteúdo.
        * Um _recurso de contexto_ são os metadados sobre o contexto em que o conteúdo é apresentado.

* **Exploração**: O serviço Personalizador está explorando quando, em vez de retornar a melhor ação, escolhe uma ação diferente para o usuário. O serviço Personalizador evita desvios e estagnação e pode se adaptar ao comportamento contínuo do usuário com a exploração.

* **Exploração**: O serviço Personalr usa o modelo atual para decidir a melhor ação com base em dados passados.

* **Duração do experimento**: A quantidade de tempo que o serviço Personalizador espera por uma recompensa, a partir do momento em que a chamada rank aconteceu para esse evento.

* **Eventos inativos**: Um evento inativo é aquele em que você chamou rank, mas você não tem certeza se o usuário jamais verá o resultado, devido a decisões de aplicativos do cliente. Os eventos inativos permitem criar e armazenar resultados de personalização e decidir como descartá-los posteriormente sem afetar o modelo de machine learning.


* **Recompensa**: Uma medida de como o usuário respondeu ao ID de ação de recompensa devolvido da API rank, como uma pontuação entre 0 a 1. O valor de 0 a 1 é definido pela lógica de negócios, com base em como a opção ajudou a atingir as metas de negócios de personalização. O ciclo de aprendizagem não armazena essa recompensa como histórico individual do usuário.

## <a name="offline-evaluations"></a>Avaliações offline

* **Avaliação**: Uma avaliação offline determina a melhor política de aprendizado para o seu loop com base nos dados do seu loop.

* **Política de Aprendizagem**: Como o Personalizador treina um modelo em cada evento será determinado por alguns parâmetros que afetam o funcionamento do algoritmo de aprendizagem de máquina. Um novo ciclo de aprendizado começa com uma política de **aprendizagem**padrão, que pode produzir desempenho moderado. Ao executar [avaliações,](concepts-offline-evaluation.md)o Personalr cria novas políticas de aprendizagem especificamente otimizadas para os casos de uso do seu loop. O personalização terá um desempenho significativamente melhor com políticas otimizadas para cada loop específico, gerados durante a Avaliação. A política de aprendizagem é nomeada _configurações de aprendizagem_ no **Modelo e configurações de aprendizado** para o recurso Personalizador no portal Azure.