---
title: Base de conhecimento de design - QnA Maker
description: Uma base de conhecimento qnA Maker consiste em um conjunto de conjuntos de perguntas e respostas (QnA) e metadados opcionais associados a cada par de QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844306"
---
# <a name="question-and-answer-set-concepts"></a>Conceitos de conjunto de perguntas e respostas

Uma base de conhecimento consiste em conjuntos de perguntas e respostas (QnA).  Cada conjunto tem uma resposta e um conjunto contém todas as informações associadas a essa _resposta_. Uma resposta pode se assemelhar vagamente a uma linha de banco de dados ou a uma instância de estrutura de dados.

## <a name="question-and-answer-sets"></a>Conjuntos de pergunta e resposta

As configurações **necessárias** em um conjunto de perguntas e respostas (QnA) são:

* uma **pergunta** - texto de consulta de usuário, usado para qnA maker machine-learning, para alinhar com texto da pergunta do usuário com palavras diferentes, mas a mesma resposta
* a **resposta** - a resposta do conjunto é a resposta que é devolvida quando uma consulta de usuário é combinada com a pergunta associada

Cada conjunto é representado por um **ID**.

As configurações **opcionais** para um conjunto incluem:

* **Formas alternativas da pergunta** - isso ajuda qnA Maker retornar a resposta correta para uma variedade mais ampla de frases de perguntas
* **Metadados**: Metadados são tags associadas a um par de QnA e são representados como pares de valor-chave. As marcas de metadados são usadas para filtrar pares QnA e limitar o conjunto sobre o qual a correspondência da consulta será executada.
* **Solicitações de várias voltas,** usadas para continuar uma conversa de várias voltas

![Bases de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de modo editorial à base de dados de conhecimento

Se você não tiver conteúdo pré-existente para preencher a base de conhecimento, você pode adicionar conjuntos de QnA editorialmente no portal QnA Maker. Saiba como atualizar sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Como editar sua base de dados de conhecimento localmente

Após criar uma base de conhecimento, é recomendável que você faça as edições no texto da base de conhecimento no [portal QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar por meio de arquivos locais. No entanto, pode haver ocasiões em que você precisa editar uma base de conhecimento localmente.

Exporte a base de conhecimento na página **Configurações** e, em seguida, edite-a com o Microsoft Excel. Se você optar por usar outro aplicativo para editar seu arquivo exportado, o aplicativo poderá introduzir erros de sintaxe porque não está totalmente compatível com TSV. Em geral, os arquivos TSV do Microsoft Excel não introduzem nenhum erro de formatação.

Depois de concluir as edições, reimporte o arquivo TSV na página **Configurações**. Isso substitui totalmente a base de conhecimento atual pela base de conhecimento importada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida da base de dados de conhecimento no QnA Maker](./development-lifecycle-knowledge-base.md)