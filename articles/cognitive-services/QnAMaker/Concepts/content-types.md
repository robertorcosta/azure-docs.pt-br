---
title: Tipos de conteúdo-QnA Maker
description: Os tipos de conteúdo incluem muitos documentos estruturados padrão, como PDF, DOC e TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843430"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que você pode adicionar a uma base de dados de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, como PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipos de dados de arquivo e URL

A tabela a seguir resume os tipos de conteúdo e formatos de arquivo com suporte no QnA Maker.

|Tipo de Fonte|Tipo de conteúdo| Exemplos|
|--|--|--|
|URL|Perguntas frequentes<br> (simples, com seções ou com uma página inicial de tópicos)<br>Páginas de suporte <br> (artigos de instrução de uma página, artigos de solução de problemas etc.)|[Perguntas frequentes simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Perguntas frequentes com links](https://www.microsoft.com/en-us/software-download/faq),<br> [Perguntas frequentes com home page de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Perguntas frequentes,<br> Manual do Produto,<br> Folhetos,<br> Papel,<br> Política de folheto,<br> Guia de suporte,<br> QnA estruturado,<br> etc.|[QnA.doc estruturado](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exemplo produto Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exemplo semi-estruturado.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exemplo White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Exemplo de multi-Turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Arquivo QnA estruturado<br> (incluindo suporte RTF, HTML)|[Exemplo de QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Arquivo QnA estruturado|[Exemplo de chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se você precisar de autenticação para sua fonte de dados, considere os seguintes métodos para obter esse conteúdo em QnA Maker:

* Baixar o arquivo manualmente e importar para o QnA Maker
* Adicionar arquivo do local do [SharePoint](../how-to/add-sharepoint-datasources.md) autenticado

## <a name="url-content"></a>Conteúdo da URL

Dois tipos de documentos podem ser importados por meio de **URL** no QnA Maker:

* URLs de perguntas frequentes
* URLs de suporte

Cada tipo indica um formato esperado.

## <a name="file-based-content"></a>Conteúdo baseado em arquivo

Você pode adicionar arquivos a uma base de dados de conhecimento de uma fonte pública ou seu sistema de arquivos local, no [portal de QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Diretrizes de formato de conteúdo

QnA Maker adiciona conteúdo de arquivo e URL, convertendo o conteúdo em conjuntos de QnA, armazenados como redução (`.md`). O conteúdo é adequado para uma base de dados de conhecimento em que o conteúdo é organizado em algum formato estruturado e é representado em seções bem definidas. As seções podem ser divididas em subseções ou subtópicos. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

QnA Maker identifica seções e subseções e relações no conteúdo com base em pistas visuais, como tamanho da fonte, estilo da fonte, numeração, cores, etc. O conteúdo semiestruturado inclui manuais, perguntas frequentes, diretrizes, políticas, folhetos, panfletos e muitos outros tipos de arquivos.

## <a name="faq-urls"></a>URLs de perguntas frequentes

O QnA Maker pode dar suporte a páginas da Web de perguntas frequentes de três maneiras diferentes: páginas de Perguntas Frequentes Simples, páginas de Perguntas Frequentes com links, páginas de Perguntas Frequentes com uma Página Inicial de Tópicos.


### <a name="plain-faq-pages"></a>Páginas de perguntas frequentes simples

Este é o tipo mais comum de página de perguntas frequentes em que as respostas a seguem imediatamente às perguntas na mesma página.

Abaixo está um exemplo de uma página de perguntas frequentes simples:

![Exemplo de página de perguntas frequentes simples para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de perguntas frequentes com links

Nesse tipo de página de perguntas frequentes, as perguntas são agregadas e vinculadas a respostas em seções diferentes da mesma página ou em páginas diferentes.

Abaixo está um exemplo de uma página de perguntas frequentes com links nas seções que estão na mesma página:

 ![Exemplo de página de perguntas frequentes com link de seção para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>Páginas de perguntas frequentes com uma página inicial de Tópicos

Esse tipo de perguntas frequentes tem uma página inicial com Tópicos, em que cada tópico é um link para as QnAs relevantes em uma página diferente. Aqui, o QnA Maker rastreia todas as páginas vinculadas para extrair as perguntas e respostas correspondentes.

Abaixo está um exemplo de uma página de perguntas frequentes em que uma página inicial de tópicos tem links para seções de perguntas frequentes em diferentes páginas.

 ![Exemplo de página de perguntas frequentes com link profundo para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>Suporte a Urls

O QnA Maker pode processar páginas da web de suporte semiestruturadas, como artigos da web que descrevem como executar uma tarefa específica, como diagnosticar e resolver um problema específico e quais são as práticas recomendadas para um determinado processo. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

> [!NOTE]
> Extração para artigos de suporte é um recurso novo e está nos estágios iniciais. Funciona melhor para páginas simples, que também são estruturadas e não contêm cabeçalhos/rodapés complexos.

![O QnA Maker dá suporte à extração de páginas web semi-estruturadas em que a estrutura limpa é apresentada com cabeçalhos hierárquicos](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Arquivos PDF/DOC

O QnA Maker pode processar o conteúdo semiestruturado em um arquivo PDF ou documento e convertê-la em QnAs. Um bom arquivo que pode ser extraído também é um arquivo em que o conteúdo está organizado em alguma forma estruturada e é representado em seções bem definidas. As seções adicionais podem ser divididas em subseções ou subtópicos. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

QnA Maker identifica seções e subseções e relações no arquivo com base em pistas visuais, como tamanho da fonte, estilo da fonte, numeração, cores, etc. Arquivos de documento ou PDF semiestruturados podem ser manuais, perguntas frequentes, diretrizes, políticas, folhetos, panfletos e muitos outros tipos de arquivos. Abaixo estão alguns tipos de exemplo desses arquivos.

### <a name="product-manuals"></a>Manuais de produtos

Normalmente, um manual é o material de diretrizes que acompanha um produto. Ele ajuda o usuário a configurar, usar, manter e solucionar problemas do produto. Quando o QnA Maker processa um manual, ele extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Abaixo está um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manual de produto para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais com uma tabela de conteúdo e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicos.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros arquivos

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, contanto que tenham uma estrutura e um layout claros. Eles incluem: folhetos, diretrizes, relatórios, White papers, documentos científicos, políticas, livros, etc. Veja um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Abaixo está um exemplo de um documento semiestruturado sem um índice:

 ![Documento semiestruturado de armazenamento de Blobs do Azure](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento de QnA estruturado

O formato para Pergunta-Respostas estruturas em arquivos DOC é na forma de Perguntas e Respostas alternadas por linha, uma pergunta por linha seguida pela respectiva resposta na linha seguinte, conforme mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento do word de QnA estruturado:

 ![Exemplo de documento do QnA estruturado em uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Arquivos *TXT*, *TSV* e *XLS* Estruturados

QnAs na forma de arquivos *.txt*, *.tsv* ou *.xls* estruturados também podem ser carregadas para o QnA Maker para criar ou ampliar uma base de conhecimento.  Podem ser texto sem formatação ou ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

As colunas adicionais no arquivo de origem são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo do Excel estruturado

Abaixo está um exemplo de arquivo *.xls* de QnA estruturada, com conteúdo HTML:

 ![Exemplo do QnA estruturado em uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel

Veja abaixo um exemplo de um arquivo QnA *. xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Depois que o arquivo for importado, o par de perguntas e respostas estará na base de dados de conhecimento, conforme mostrado abaixo:

 ![Captura de tela de perguntas alternativas para uma única resposta importada na base de dados de conhecimento](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um arquivo ou URL, QnA Maker converte e armazena seu conteúdo no [formato de redução](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, como `\n\n`. Um conhecimento do formato de redução ajuda você a entender o conteúdo convertido e gerenciar seu conteúdo da base de dados de conhecimento.

Se você adicionar ou editar seu conteúdo diretamente na sua base de dados de conhecimento, use a **formatação de redução** para criar conteúdo de Rich Text ou alterar o conteúdo do formato de redução que já está na resposta. QnA Maker dá suporte a grande parte do formato de redução para trazer recursos de Rich Text para seu conteúdo. No entanto, o aplicativo cliente, como um bot de chat, pode não dar suporte ao mesmo conjunto de formatos de redução. É importante testar a exibição de respostas do aplicativo cliente.

Saiba mais na [documentação de referência de redução QnA Maker](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Testar seu Markdown

Use o tutorial **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar seu Markdown. O tutorial tem um recurso **Experimentar** para validação rápida de copiar/colar.

## <a name="next-steps"></a>Próximos passos

* Entenda como projetar e gerenciar [conjuntos de perguntas e respostas (QnA)](question-answer-set.md)