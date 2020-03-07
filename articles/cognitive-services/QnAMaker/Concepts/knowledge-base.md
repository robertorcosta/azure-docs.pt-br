---
title: Importando de fontes de dados-QnA Maker
description: Uma base de dados de conhecimento QnA Maker consiste em um conjunto de conjuntos de QnA (perguntas e respostas) e metadados opcionais associados a cada par de QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389160"
---
# <a name="importing-from-data-sources"></a>Importar de fontes de dados

Uma base de dados de conhecimento consiste em conjuntos de perguntas e respostas trazidos por arquivos e URLs públicos.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento de uma fonte de dados. Os locais de fonte de dados são **URLs ou arquivos públicos**, que não exigem autenticação.

Os [arquivos do SharePoint](../how-to/add-sharepoint-datasources.md), protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser arquivos, não páginas da Web. Se a URL terminar com uma extensão da Web, como. ASPX, ele não será importado para o QnA Maker do SharePoint.

## <a name="chit-chat-content"></a>Conteúdo do chat Chit

O conjunto de conteúdo do Chit Chat QnA é oferecido como uma fonte de dados de conteúdo completa em vários idiomas e estilos de conversação. Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero. Saiba [como adicionar](../how-to/chit-chat-knowledge-base.md) esse conjunto de conteúdo à sua base de dados de conhecimento.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um arquivo de `.tsv` estruturado que contenha perguntas e respostas. Essas informações ajudam a QnA Maker agrupar os conjuntos de respostas de perguntas e os atributos para uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato de várias transformações estruturado por meio de importação

Você pode criar conversas com vários folheios em um formato de arquivo `.tsv`. O formato fornece a capacidade de criar as conversas de passagem múltipla analisando os logs de chat anteriores (com outros processos, não usando QnA Maker) e, em seguida, criar o arquivo de `.tsv` por meio da automação. Importe o arquivo para substituir a base de dados de conhecimento existente.

> [!div class="mx-imgBorder"]
> ![modelo conceitual de três níveis de pergunta de vários desligamentos](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para uma `.tsv`de vários desligamentos, específica a várias transformações, é **solicitada**. Um exemplo `.tsv`, mostrado no Excel, mostra as informações a serem incluídas para definir os filhos de vários desligamentos:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **displayOrder** é numérico e o **exibirtexto** é texto que não deve incluir redução.

> [!div class="mx-imgBorder"]
> ![exemplo de pergunta de várias pontas, conforme mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportar como exemplo

Se você não tiver certeza de como representar o conjunto de QnA no arquivo de `.tsv`, crie o conjunto no portal de QnA Maker, salve e, em seguida, exporte a base de dados de conhecimento para obter um exemplo de como representar o conjunto.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

Use a [referência de redução](../reference-markdown-format.md) QnA Maker para ajudá-lo a Formatar suas respostas.

[Visão geral do QnA Maker](../Overview/overview.md)

Criar e editar uma base de dados de conhecimento com:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
