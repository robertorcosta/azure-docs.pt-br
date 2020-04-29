---
title: Design base de dados de conhecimento-QnA Maker
description: Uma base de dados de conhecimento QnA Maker consiste em um conjunto de conjuntos de QnA (perguntas e respostas) e metadados opcionais associados a cada par de QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756791"
---
# <a name="question-and-answer-set-concepts"></a>Conceitos do conjunto de perguntas e respostas

Uma base de dados de conhecimento consiste em conjuntos de perguntas e respostas (QnA).  Cada conjunto tem uma resposta e um conjunto contém todas as informações associadas a essa _resposta_. Uma resposta pode se assemelhar livremente a uma linha de banco de dados ou a uma instância de estrutura de dado.

## <a name="question-and-answer-sets"></a>Conjuntos de pergunta e resposta

As configurações **necessárias** em um conjunto de perguntas e respostas (QnA) são:

* um texto de **pergunta** da consulta do usuário, usado para o aprendizado de máquina de QnA Maker, para se alinhar com o texto da pergunta do usuário com palavras diferentes, mas a mesma resposta
* a **resposta-a resposta do** conjunto é a resposta retornada quando uma consulta de usuário é correspondida com a pergunta associada

Cada conjunto é representado por uma **ID**.

As configurações **opcionais** para um conjunto incluem:

* **Formas alternativas da pergunta** – isso ajuda a QnA Maker retornar a resposta correta para uma variedade maior de frases de pergunta
* **Metadados**: os metadados são marcas associadas a um par de QnA e são representados como pares de chave-valor. As marcas de metadados são usadas para filtrar pares QnA e limitar o conjunto sobre o qual a correspondência da consulta será executada.
* **Prompts de múltipla ativação**, usados para continuar uma conversa com vários folheios

![Bases de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de modo editorial à base de dados de conhecimento

Se você não tiver conteúdo pré-existente para preencher a base de dados de conhecimento, poderá adicionar pares de QnA editorial no portal de QnA Maker. Saiba como atualizar sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Como editar sua base de dados de conhecimento localmente

Após criar uma base de conhecimento, é recomendável que você faça as edições no texto da base de conhecimento no [portal QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar por meio de arquivos locais. No entanto, pode haver ocasiões em que você precisa editar uma base de conhecimento localmente.

Exporte a base de conhecimento na página **Configurações** e, em seguida, edite-a com o Microsoft Excel. Se você optar por usar outro aplicativo para editar o arquivo exportado, o aplicativo poderá introduzir erros de sintaxe porque não é compatível totalmente com TSV. Em geral, os arquivos TSV do Microsoft Excel não introduzem nenhum erro de formatação.

Depois de concluir as edições, reimporte o arquivo TSV na página **Configurações**. Isso substitui totalmente a base de conhecimento atual pela base de conhecimento importada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida da base de dados de conhecimento no QnA Maker](./development-lifecycle-knowledge-base.md)