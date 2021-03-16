---
title: Notas fiscais – reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de faturas com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 46cf34bd40832488985008a645f1da25eb87b9d9
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467367"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Modelo de fatura predefinido do reconhecedor de formulário

O reconhecedor de formulários do Azure pode analisar e extrair informações de faturas de vendas usando seus modelos de fatura pré-criados. A API de fatura permite que os clientes façam faturas em uma variedade de formatos e retornem dados estruturados para automatizar o processamento da fatura. Ele combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/concept-recognizing-text.md) com a nota fiscal que entende os modelos de aprendizado profundo para extrair informações importantes das faturas em inglês. Ele extrai o texto, as tabelas e as informações como Customer, Vendor, ID da fatura, data de vencimento da fatura, total, valor da nota fiscal devido, valor do imposto, envio, cobrança, itens de linha e muito mais. A API de fatura predefinida está publicamente disponível no formulário versão prévia do Recognizer v 2.1.

## <a name="what-does-the-invoice-service-do"></a>O que o serviço de fatura faz?

A API de nota fiscal extrai campos de chave e itens de linha de faturas e os retorna em uma resposta JSON estruturada organizada. As notas fiscais podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API de fatura extrairá a saída estruturada de todas essas notas fiscais. 

![Exemplo de fatura da contoso](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de fatura do reconhecedor de formulário, vá para a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

Você precisará de uma assinatura do Azure ([crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto de extremidade de [recurso do reconhecedor de formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e uma chave para experimentar o serviço de fatura do reconhecedor de formulário. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Exemplo de fatura analisada" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>A operação de análise de fatura

A operação [analisar fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) usa uma imagem ou um PDF de uma fatura como entrada e extrai os valores de interesse. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>A operação obter resultado da fatura analisar

A segunda etapa é chamar a operação [obter resultado da fatura de obtenção](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de análise de fatura. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação de análise não foi iniciada.<br /><br />em execução: a operação de análise está em andamento.<br /><br />falha: falha na operação de análise.<br /><br />êxito: a operação de análise foi bem-sucedida.|

Quando o campo **status** tiver o valor **êxito** , a resposta JSON incluirá os resultados de compreensão da nota fiscal, tabelas extraídas e resultados de reconhecimento de texto opcionais, se solicitado. O resultado de compreensão da nota fiscal é organizado como um dicionário de valores de campo nomeado, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos correspondentes do Word. Ele também inclui os itens de linha extraídos onde cada item de linha contém a quantidade, a descrição, o PreçoUnitário, a quantidade etc. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta à operação get analisar resultado da fatura será a representação estruturada da fatura com todas as informações extraídas. Consulte aqui para obter um [arquivo de nota fiscal de exemplo](media/sample-invoice.jpg) e sua saída de [nota fiscal de exemplo](media/invoice-example-new.jpg)de saída estruturada.

A saída JSON tem três partes: 
* `"readResults"` o nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* `"pageResults"` o nó contém as tabelas e células extraídas com suas caixas delimitadoras, confiança e uma referência às linhas e palavras em "readResults".
* `"documentResults"` o nó contém os valores específicos da fatura e os itens de linha que o modelo descobriu. É aqui que você encontrará todos os campos da fatura, como ID da fatura, enviar para, faturar, cliente, total, itens de linha e muito mais.

## <a name="example-output"></a>Saída de exemplo

O serviço de fatura extrairá o texto, tabelas e 26 campos de fatura. A seguir estão os campos extraídos de uma nota fiscal na resposta de saída JSON (a saída abaixo usa esta [fatura de exemplo](media/sample-invoice.jpg)).

|Nome| Tipo | Descrição | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | Cliente sendo faturado | Microsoft Corp |  |
| CustomerId | string | ID de referência para o cliente | CID-12345 |  |
| PurchaseOrder | string | Um número de referência de ordem de compra | PO-3333 | | 
| InvoiceId | string | ID para esta fatura específica (geralmente "número da nota fiscal") | INV-100 | | 
| InvoiceDate | date | Data em que a nota fiscal foi emitida | 15/11/2019 | 2019-11-15 | 
| DueDate | date | O pagamento de data para esta fatura é devido | 12/15/2019 | 2019-12-15 |
| Nome_do_Fornecedor | string | Fornecedor que criou esta fatura | CONTOSO LTD. | |
| VendorAddress | string | Endereço para correspondência do fornecedor | 123 456th St Nova York, NY, 10001 | |
| VendorAddressRecipient | string | Nome associado ao VendorAddress | Sede da contoso | |
| CustomerAddress | string | Endereço para correspondência do cliente | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | Nome associado ao CustomerAddress | Microsoft Corp | |
| BillingAddress | string | Endereço de cobrança explícito para o cliente | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | Nome associado ao BillingAddress | Serviços da Microsoft | |
| ShippingAddress | string | Endereço de envio explícito para o cliente | 123 navio St, Redmond, WA, 98052 | |
| ShippingAddressRecipient | string | Nome associado ao ShippingAddress | Entrega da Microsoft | |
| SubTotal | número | Campo de subtotal identificado nesta fatura | $100 | 100 | 
| TotalTax | número | Campo de imposto total identificado nesta fatura | US$ 10,00 | 10 |
| InvoiceTotal | número | Total de novos encargos associados a esta fatura | $110 | 110 |
| AmountDue |  número | Valor total devido ao fornecedor | $610 | 610 |
| Endereço de email | string | Endereço de serviço explícito ou endereço de Propriedade do cliente | 123 serviço St, Redmond, WA, 98052 | |
| ServiceAddressRecipient | string | Nome associado ao catálogo de endereços | Serviços da Microsoft | |
| RemittanceAddress | string | Remessa explícita ou endereço de pagamento para o cliente | 123 remeter St Nova York, NY, 10001 |  |
| RemittanceAddressRecipient | string | Nome associado ao RemittanceAddress | Cobrança da contoso |  |
| Userstartdate | date | Primeira data para o período de serviço (por exemplo, um período de serviço de lista de utilitários) | 14/10/2019 | 2019-10-14 |
| Fileenddate | date | Data de término do período de serviço (por exemplo, um período de serviço de lista de utilitários) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | número | Saldo explícito anteriormente não pago | US$ 500,00 | 500 |

A seguir estão os itens de linha extraídos de uma nota fiscal na resposta de saída JSON (a saída abaixo usa este [exemplo de fatura](./media/sample-invoice.jpg))  

|Nome| Tipo | Descrição | Text (item de linha #1) | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| Itens | string | Linha de texto de cadeia de caracteres completa do item de linha | 3/4/2021 A123 Consulting Services 2 horas $30 10% $60 | |
| Valor | número | A quantidade do item de linha | $60 | 100 |
| Descrição | string | A descrição de texto para o item de linha da nota fiscal | Serviço de consultoria | Serviço de consultoria |
| Quantidade | número | A quantidade para este item de linha de fatura | 2 | 2 |
| UnitPrice | número | O preço líquido ou bruto (dependendo da configuração de fatura bruta da fatura) de uma unidade deste item | $30 | 30 |
| ProductCode | string| Código do produto, número do produto ou SKU associado ao item de linha específico | A123 | |
| Unidade | string| A unidade do item de linha, por exemplo, kg, lb, etc. | horas | |
| Data | data| Data correspondente a cada item de linha. Geralmente, essa é uma data em que o item de linha foi enviado | 3/4/2021| 2021-03-04 |
| Imposto | número | Imposto associado a cada item de linha. Os valores possíveis incluem valor do imposto,% de imposto e imposto Y/N | 10% | |


## <a name="next-steps"></a>Próximas etapas

- Experimente suas próprias faturas e exemplos na [interface do usuário de exemplo do reconhecedor de formulário](https://fott-preview.azurewebsites.net/).
- Conclua um [início rápido do reconhecedor de formulário](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de faturas com o reconhecedor de formulário na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
