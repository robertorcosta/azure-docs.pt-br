---
title: Formato Markdown - QnA Maker
description: A seguir está a lista de formatos de marcação que você pode usar no texto de resposta do QnA Maker.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045405"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formato de markdown suportado no texto de resposta do QnA Maker

QnA Maker armazena responder texto como marcação. Há muitos sabores de marcação. Para garantir que o texto de resposta seja devolvido e exibido corretamente, use esta referência.

Use o tutorial **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar seu Markdown. O tutorial tem um recurso **Experimentar** para validação rápida de copiar/colar.

## <a name="supported-markdown-format"></a>Formato de marcação suportado

A seguir está a lista de formatos de marcação que você pode usar no texto de resposta do QnA Maker.

|Finalidade|Formatar|Exemplo de markdown|Renderização<br>como exibido no bot Chat|
|--|--|--|--|
Uma nova linha entre duas frases.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formato nova linha entre duas frases](./media/qnamaker-concepts-datasources/format-newline.png)|
|Cabeçalhos de h1 a `#` h6, o número de denota qual cabeçalho. 1 `#` é o h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formato com cabeçalhos de marcação](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formato com cabeçalhos de marcação H1 a H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Itálico |`*text*`|`How do I create a bot with *QnA Maker*?`|![formato com itálico](./media/qnamaker-concepts-datasources/format-italics.png)|
|Forte (negrito)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formato com marcação forte para negrito](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL para link|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato para URL (hiperlink)](./media/qnamaker-concepts-datasources/format-url.png)|
|*URL para imagem pública|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato para URL de imagem pública ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Tachado|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato para strikethrough](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Negrito e itálico|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formato para negrito e itálico](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL em negrito para link|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formato para URL em negrito](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL itálico para link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato para URL itálico](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Símbolos de marcação de fuga|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formato para URL itálico](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista ordenada|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>O exemplo anterior usa numeração automática incorporada à marcação.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>O exemplo anterior usa numeração explícita.|![formato para lista ordenada](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Lista não ordenada|`\n * item1 \n * item2`<br>ou<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formato para lista não ordenada](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listas aninhadas|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Você pode fazer listas ordenadas e desordenadas juntos. A guia `\t`indica o nível de recuo do elemento filho.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato para lista não ordenada aninhada](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato para lista ordenada aninhado](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*O QnA Maker não processa a imagem de forma alguma. É papel do aplicativo cliente renderizar a imagem.

Se você quiser adicionar conteúdo usando APIs de base de conhecimento de atualização/substituição e o conteúdo/arquivo contém tags html, você pode preservar o HTML em seu arquivo, garantindo que a abertura e o fechamento das tags sejam convertidos no formato codificado.

| Preservar HTML  | Representação no pedido de API  | Representação em KB |
|-----------|---------|-------------------------|
| Sim | \&lt;br\&gt; | &lt;Br&gt; |
| Sim | \&lt;h3\&gt;cabeçalho\&lt;/h3\&gt; | &lt;&gt;cabeçalho&lt;h3 /h3&gt; |

Além disso, CR LF(\r\n) são convertidos em \n no KB. LF(\n) é mantido como está. Se você quiser escapar de qualquer seqüência de fuga como um \t ou \n você pode usar barra invertida, por exemplo:\\\\' r\\\\n' e '\\\\t'

## <a name="next-steps"></a>Próximas etapas

Revisar [formatos](reference-tsv-format-batch-testing.md)de arquivo de teste em lote .
