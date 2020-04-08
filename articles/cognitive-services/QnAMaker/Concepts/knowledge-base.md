---
title: Importação de fontes de dados - QnA Maker
description: Uma base de conhecimento qnA Maker consiste em um conjunto de conjuntos de perguntas e respostas (QnA) e metadados opcionais associados a cada par de QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804275"
---
# <a name="importing-from-data-sources"></a>Importar de fontes de dados

Uma base de conhecimento consiste em conjuntos de perguntas e respostas trazidos por URLs e arquivos públicos.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento a partir de uma fonte de dados. Os locais de origem dos dados são **URLs ou arquivos públicos,** que não requerem autenticação.

[Os arquivos SharePoint,](../how-to/add-sharepoint-datasources.md)protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser arquivos, não páginas da Web. Se a URL terminar com uma extensão web, como . ASPX, ele não importará para QnA Maker do SharePoint.

## <a name="chit-chat-content"></a>Conteúdo de bate-papo chit

O conjunto de conteúdo QnA do bate-papo Chit é oferecido como uma fonte completa de dados de conteúdo em vários idiomas e estilos de conversação. Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero. [Saiba como adicionar](../how-to/chit-chat-knowledge-base.md) este conteúdo definido à sua base de conhecimento.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer `.tsv` um arquivo estruturado que contenha perguntas e respostas. Essas informações ajudam o QnA Maker a agrupar os conjuntos de perguntas e atribuí-las a uma determinada fonte de dados.

| Pergunta  | Resposta  | Fonte| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato multi-turno estruturado através da importação

Você pode criar as conversas de `.tsv` vários turnos em um formato de arquivo. O formato fornece a você a capacidade de criar as conversas de vários turnos analisando logs de bate-papo anteriores (com outros processos, não usando O Criador de QnA), e então criar o `.tsv` arquivo através da automação. Importe o arquivo para substituir a base de conhecimento existente.

> [!div class="mx-imgBorder"]
> ![Modelo conceitual de 3 níveis de questão multi-turn](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para um `.tsv`multi-turn , específico para multi-turn é **Prompts**. Um `.tsv`exemplo , mostrado no Excel, mostra as informações a serem incluempara definir as crianças em vários turnos:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **displayOrder** é numérico e o **displayText** é um texto que não deve incluir marcação.

> [!div class="mx-imgBorder"]
> ![Exemplo de pergunta de várias voltas como mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportar como exemplo

Se você não tiver certeza de como `.tsv` representar seu par de QnA no arquivo:
* Use este [exemplo para download do GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou crie o conjunto no portal QnA Maker, salve e exporte a base de conhecimento para um exemplo de como representar o conjunto.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Confira também

Use a referência QnA Maker [Markdown](../reference-markdown-format.md) para ajudá-lo a formatar suas respostas.

[Visão geral do QnA Maker](../Overview/overview.md)

Crie e edite uma base de conhecimento com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
