---
title: Adicionar a ação de consulta nos aplicativos lógicos | Microsoft Docs
description: Visão geral da ação de consulta para executar ações, como filtrar matriz.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 10332b95e0d385d7155003efcc52b4bae3969313
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973761"
---
# <a name="get-started-with-the-query-action"></a>Introdução à ação de consulta
Usando a ação de consulta, você pode trabalhar com lotes e matrizes para executar os fluxos de trabalho para:

* Criar uma tarefa para todos os registros de alta prioridade a partir de um banco de dados.
* Salvar todos os anexos em PDF dos emails em um blob do Azure.

Para começar a usar a ação de consulta em um aplicativo lógico, consulte [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Usar a ação de consulta
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. 
[Saiba mais sobre ações](../connectors/apis-list.md).  

Atualmente, a ação de consulta tem uma operação, chamada matriz de filtro, que é exposta no designer. Isso permite que você consulte uma matriz e retorne um conjunto de resultados filtrados.

Veja como é possível adicioná-lo em um aplicativo lógico:

1. Selecione o botão **Nova Etapa** .
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa da ação, digite **filtro** para listar a ação **Filtrar matriz**.
   
    ![Selecionar a ação de consulta](./media/connectors-native-query/using-action-1.png)
4. Selecione uma matriz para filtrar. (A captura de tela a seguir mostra a matriz de resultados de uma pesquisa do Twitter.)
5. Crie uma condição para avaliar em cada item. (A captura de tela a seguir filtra os tweets dos usuários que têm mais de 100 seguidores.)
   
    ![Concluir a ação de consulta](./media/connectors-native-query/using-action-2.png)
   
    A ação produzirá uma nova matriz que contém somente os resultados que atendem aos requisitos do filtro.
6. Clique no canto superior esquerdo da barra de ferramentas para salvar e seu aplicativo lógico será salvo e publicado (ativado).

\* Se você estiver chamando um ponto de extremidade HTTP e receber uma resposta JSON, use a ação _Analisar JSON_ para analisar a resposta JSON. Sem realizar esta etapa, a _Matriz de filtro_ verá somente o Corpo e não entenderá a estrutura do conteúdo JSON.

## <a name="query-action"></a>Ação de consulta
Veja os detalhes da ação com suporte deste conector. O conector tem uma ação possível.

| Ação | Descrição |
| --- | --- |
| Filtrar matriz |Avalia uma condição para cada item em uma matriz e retorna os resultados |

## <a name="action-details"></a>Detalhes da ação
A ação de consulta vem com uma ação possível. As tabelas a seguir descrevem os campos de entrada obrigatórios e opcionais para a ação e os detalhes de saída correspondentes associados ao uso da ação.

### <a name="filter-array"></a>Matriz do filtro
Estes são os campos de entrada para a ação, o que cria uma solicitação HTTP de saída.
Um * significa que é um campo obrigatório.

| Display name | Nome da propriedade | Descrição |
| --- | --- | --- |
| De* |from |A matriz a ser filtrada |
| Condição* |where |A condição a ser avaliada para cada item |

<br>

### <a name="output-details"></a>Detalhes de saída
A seguir, os detalhes de saída para a resposta HTTP.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Matriz filtrada |array |Uma matriz que contém um objeto para cada resultado filtrado |

## <a name="next-steps"></a>Próximas etapas
Agora, experimente a plataforma e [crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

