---
title: Recibos-reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de recebimento com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724953"
---
# <a name="receipt-concepts"></a>Conceitos de recebimento

O reconhecedor AzureForm pode analisar recibos usando um de seus modelos predefinidos. A API de recebimento extrai informações de chave de recibos de vendas em inglês, como o nome do comerciante, a data da transação, o total da transação, os itens de linha e muito mais. 

## <a name="understanding-receipts"></a>Entendendo os recibos 

Muitas empresas e indivíduos ainda dependem da extração manual de dados de seus recibos de vendas, seja para relatórios de despesas comerciais, reembolsos, fins tributários, orçamento ou outras finalidades. Geralmente nesses cenários, as imagens do recebimento físico são necessárias para fins de validação.  

A extração automática de dados dessas confirmações pode ser complicada. Os recibos podem ser crumpleddos e difíceis de ler e as imagens do smartphone de recibos podem ser de baixa qualidade. Além disso, modelos de recebimento e campos podem variar muito por mercado, região e comerciante. Esses desafios na extração dos dados e na detecção de campos fazem com que o processamento de recibos seja um problema exclusivo.  

Usando o OCR (reconhecimento óptico de caracteres) e nosso modelo de recebimento predefinido, a API de recebimento permite esses cenários de processamento de recebimento. Como o modelo é previamente treinado em nossos dados, você pode facilmente analisar seus recibos em uma etapa &mdash; sem treinamento de modelo ou rótulo necessário.

![exemplo de recebimento](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>O que a API de recebimento faz? 

A API de recebimento predefinida extrai o conteúdo de recibos de vendas &mdash; o tipo de recibo que você normalmente obteria em um restaurante, varejista ou armazenamento de supermercado.

### <a name="fields-extracted"></a>Campos extraídos

* Nome do comerciante 
* Endereço do comerciante 
* Número de telefone do comerciante 
* Data da transação 
* Tempo de transação 
* Subtotal 
* Imposto 
* Total 
* Dica 
* Extração de item de linha (por exemplo, quantidade de item, preço do item, nome do item)

### <a name="additional-features"></a>Recursos adicionais

A API de recebimento também retorna as seguintes informações:

* Tipo de recibo (como discriminado, cartão de crédito e assim por diante)
* Nível de confiança de campo (cada campo retorna um valor de confiança associado)
* Texto bruto de OCR (saída de texto extraído por OCR para o recebimento inteiro)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Localidades com suporte 

* O recebimento predefinido **v 2.0** (GA) dá suporte a recibos de vendas na localidade en-US
* **Recebimento predefinido v 2.1-Preview. 1** (visualização pública) adiciona suporte adicional para as seguintes localidades de recebimento: 
  * EN-AU 
  * EN-AC 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Entrada de idioma 
  >
  > Confirmação predefinida v 2.1-Preview. 1 tem um parâmetro de solicitação opcional para especificar uma localidade de recebimento de outros mercados em inglês. Para recibos de vendas em inglês da Austrália (EN-AU), Canadá (EN-CA), Grã-Bretanha (EN-GB) e Índia (EN-IN), você pode especificar a localidade para obter resultados aprimorados. Se nenhuma localidade for especificada em v 2.1-Preview. 1, o modelo usará como padrão o modelo EN-US.

## <a name="customer-scenarios"></a>Cenários de clientes  

Os dados extraídos com a API de recebimento podem ser usados para executar uma variedade de tarefas. Veja a seguir alguns exemplos do que nossos clientes fizeram com a API de recebimento. 

### <a name="business-expense-reporting"></a>Relatório de despesas comerciais  

Com frequência, o arquivamento das despesas de negócios envolve o tempo de gastos inserindo manualmente dados de imagens de recibos. Com a API de recebimento, você pode usar os campos extraídos para automatizar parcialmente esse processo e analisar seus recibos rapidamente.  

Como a API de recebimento tem uma saída JSON simples, você pode usar os valores de campo extraídos de várias maneiras. Integre-se com aplicativos de despesas internos para preencher previamente os relatórios de despesas. Para saber mais sobre esse cenário, leia sobre como o Acumatica está utilizando a API de recebimento para [tornar o relatório de despesas um processo menos trabalhoso](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Auditoria e contabilidade 

A saída da API de recebimento também pode ser usada para executar a análise em um grande número de despesas em vários pontos no processo de relatório e reembolso de despesas. Você pode processar recibos para fazer a triagem para auditoria manual ou aprovações rápidas.  

A saída de recebimento também é útil para o livro geral-mantendo para uso pessoal ou comercial. Use a API de recebimento para transformar todos os dados brutos de imagem/PDF em uma saída digital que é acionável.

### <a name="consumer-behavior"></a>Comportamento do consumidor 

Os recibos contêm dados úteis que você pode usar para analisar o comportamento do consumidor e as tendências de compra.

