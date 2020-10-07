---
title: Importando de fontes de dados-QnA Maker
description: Saiba como importar pares de perguntas e respostas de fontes de dados-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776945"
---
# <a name="importing-from-data-sources"></a>Importar de fontes de dados

Uma base de dados de conhecimento consiste em pares de perguntas e respostas trazidos por arquivos e URLs públicos.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento de uma fonte de dados. Os locais de fonte de dados são **URLs ou arquivos públicos**, que não exigem autenticação.

Os [arquivos do SharePoint](../how-to/add-sharepoint-datasources.md), protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser arquivos, não páginas da Web. Se a URL terminar com uma extensão da Web, como .ASPX, ela não será importada do SharePoint para o QnA Maker.

## <a name="chit-chat-content"></a>Conteúdo do chat Chit

O conjunto de conteúdo do Chit Chat QnA é oferecido como uma fonte de dados de conteúdo completa em vários idiomas e estilos de conversação. Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero. Saiba [como adicionar](../how-to/chit-chat-knowledge-base.md) esse conjunto de conteúdo à sua base de dados de conhecimento.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um `.tsv` arquivo estruturado que contenha perguntas e respostas. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato de várias transformações estruturado por meio de importação

Você pode criar conversas com vários folheios em um `.tsv` formato de arquivo. O formato fornece a capacidade de criar as conversas de passagem múltipla analisando os logs de chat anteriores (com outros processos, não usando QnA Maker) e, em seguida, criar o `.tsv` arquivo por meio da automação. Importe o arquivo para substituir a base de dados de conhecimento existente.

> [!div class="mx-imgBorder"]
> ![Modelo conceitual de 3 níveis de pergunta de vários desligamentos](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para uma passagem múltipla `.tsv` , específica para a múltipla ativação, é **solicitada**. Um exemplo `.tsv` , mostrado no Excel, mostra as informações a serem incluídas para definir os filhos de troca múltipla:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **displayOrder** é numérico e o **exibirtexto** é texto que não deve incluir redução.

> [!div class="mx-imgBorder"]
> ![Exemplo de pergunta de várias pontas, como mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportar como exemplo

Se você não tiver certeza de como representar seu par de QnA no `.tsv` arquivo:
* Use este [exemplo para download do GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou crie o par no portal de QnA Maker, salve e, em seguida, exporte a base de dados de conhecimento para obter um exemplo de como representar o par.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Confira também

Use a [referência de redução](../reference-markdown-format.md) QnA Maker para ajudá-lo a Formatar suas respostas.

[Visão geral do QnA Maker](../Overview/overview.md)

Criar e editar uma base de dados de conhecimento com:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
