---
title: Diretrizes de formato de documento de importação - QnA Maker
description: Entenda como os tipos de URLs são usados para importar e criar conjuntos de QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651834"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos importados e URLs

Revise essas diretrizes de formatação para obter os melhores resultados para o seu conteúdo.

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um arquivo ou URL, o QnA Maker converte e armazena seu conteúdo no [formato de marcação](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas `\n\n`linhas no texto, tais como . Um conhecimento do formato de marcação ajuda você a entender o conteúdo convertido e gerenciar seu conteúdo base de conhecimento.

Se você adicionar ou editar seu conteúdo diretamente em sua base de conhecimento, use a **formatação de marcação** para criar conteúdo de texto rico ou alterar o conteúdo do formato de marcação que já está na resposta. O QnA Maker suporta grande parte do formato de marcação para trazer recursos de texto ricos ao seu conteúdo. No entanto, o aplicativo cliente, como um bot de bate-papo, pode não suportar o mesmo conjunto de formatos de marcação. É importante testar a exibição de respostas do aplicativo cliente.

## <a name="basic-document-formatting"></a>Formatação básica de documentos

O QnA Maker identifica seções e subseções e relacionamentos no arquivo com base em pistas visuais como:

* tamanho da fonte
* estilo de fonte
* Numeração
* cores

|Exemplos de documento|
|--|
||



## <a name="product-manuals"></a>Manuais de produtos

Normalmente, um manual é o material de diretrizes que acompanha um produto. Ele ajuda o usuário a configurar, usar, manter e solucionar problemas do produto. Quando o QnA Maker processa um manual, ele extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Abaixo está um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manual de produto para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais com uma tabela de conteúdo e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicos.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros arquivos

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, contanto que tenham uma estrutura e um layout claros. Estes incluem: Folhetos, diretrizes, relatórios, white papers, artigos científicos, políticas, livros, etc. Veja um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Abaixo está um exemplo de um documento semiestruturado sem um índice:

 ![Documento semiestruturado de armazenamento de Blobs do Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Documento de QnA Estruturado

O formato para Pergunta-Respostas estruturas em arquivos DOC é na forma de Perguntas e Respostas alternadas por linha, uma pergunta por linha seguida pela respectiva resposta na linha seguinte, conforme mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento do word de QnA estruturado:

 ![Exemplo de documento do QnA estruturado em uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Arquivos *TXT*, *TSV* e *XLS* Estruturados

QnAs na forma de arquivos *.txt*, *.tsv* ou *.xls* estruturados também podem ser carregadas para o QnA Maker para criar ou ampliar uma base de conhecimento.  Podem ser texto sem formatação ou ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

As colunas adicionais no arquivo de origem são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo Excel estruturado

Abaixo está um exemplo de arquivo *.xls* de QnA estruturada, com conteúdo HTML:

 ![Exemplo do QnA estruturado em uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para resposta única no arquivo Excel

Abaixo está um exemplo de um arquivo QnA *.xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para resposta única no arquivo Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Depois que o arquivo é importado, o par de perguntas e respostas fica na base de conhecimento, conforme mostrado abaixo:

 ![Captura de tela de perguntas alternativas para resposta única importada em base de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um arquivo .tsv estruturado que contenha informações de fonte de dados. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatação de documentos de várias voltas

* Use títulos e subtítulos para denotar hierarquia. Por exemplo, você pode h1 para denotar o QnA pai e h2 para denotar o QnA que deve ser tomado como solicitação. Use o tamanho da posição pequena para denotar a hierarquia subseqüente. Não use estilo, cor ou algum outro mecanismo para implicar estrutura em seu documento, o QnA Maker não extrairá as solicitações de várias voltas.
* O primeiro caractere da posição deve ser capitalizado.
* Não termine um título com `?`um ponto de interrogação, .


|Exemplos de documento|
|--|
||