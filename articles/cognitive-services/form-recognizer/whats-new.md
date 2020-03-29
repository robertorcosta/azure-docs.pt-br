---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as últimas alterações na API do Reconhecimento de Formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155164"
---
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço Dereconhecer formulário é atualizado continuamente. Use este artigo para ficar atualizado com melhorias de recursos, correções e atualizações de documentação.

> [!NOTE]
> As iniciações rápidas e guias para Reconhecedor de Formulário sempre usam a versão mais recente da API, a menos que especificado.

## <a name="march-2020"></a>março de 2020 

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui melhorias de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares de tecla/valor na mesma linha de texto. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>A ferramenta de rotulagem de amostra de reconhecimento de formulário sou agora de código aberto

A Ferramenta de Rotulagem de Amostra de Reconhecimento de Formulário está agora disponível como um projeto de código aberto. Você pode integrá-lo dentro de suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a Ferramenta de Rotulagem de Amostra de Reconhecimento de Formulário, revise a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Tipos de valor de rotulagem

Os tipos de valor estão agora disponíveis para uso com a Ferramenta de Rotulagem de Amostra de Reconhecimento de Formulário. Esses tipos de valor são suportados atualmente: 

* String
* Número 
* Integer
* Data 
* Hora

Esta imagem mostra como é a seleção de tipo de valor dentro da ferramenta de rotulagem de amostra de reconhecimento de formulário:

> [!div class="mx-imgBorder"]
> ![Seleção de tipo de valor com ferramenta de rotulagem de amostra](./media/whats-new/formre-value-type.png)

A tabela extraída está disponível `pageResults`na saída JSON em .

### <a name="table-visualization"></a>Visualização de tabela 

A Ferramenta de Rotulagem do Reconhecimento de Formulário exibe agora tabelas que foram reconhecidas no documento. Isso permite que você visualize as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar com a Ferramenta de Rotulagem de Amostra de Reconhecimento de Formulário. Esse recurso pode ser alternado on/off usando a opção camadas. 

Este é um exemplo de como as tabelas são reconhecidas e extraídas:

> [!div class="mx-imgBorder"]
> ![Visualização da tabela usando a ferramenta de rotulagem de amostras](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> As tabelas de rotulagem não são suportadas. Se as tabelas não forem reconhecidas e extraídas automaticamente, você só poderá rotulá-las como pares de chave/valor. Ao rotular tabelas como pares de chave/valor, por favor, marque cada célula como um valor.

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

* O TLS 1.2 agora é aplicado para todas as solicitações HTTP para este serviço. Para obter mais informações, consulte [a segurança dos Serviços Cognitivos do Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Janeiro de 2020

Esta versão introduz o Form Recognizer 2.0 (visualização). Nas seções abaixo, você encontrará mais informações sobre novos recursos, melhorias e alterações. 

### <a name="new-features"></a>Novos recursos

* **Modelo personalizado**
  * **Trem com etiquetas** Agora você pode treinar um modelo personalizado com dados rotulados manualmente. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.
  * **API assíncrona** Você pode usar chamadas async da API para treinar e analisar grandes conjuntos de dados e arquivos.
  * **Suporte a arquivos TIFF** Agora você pode treinar com e extrair dados de documentos TIFF.
  * **Melhorias na precisão da extração**

* **Modelo de recebimento predefinido**
  * **Valores de gorjeta** Agora você pode extrair quantidades de gorjeta e outros valores manuscritos.
  * **Extração de itens de linha** Você pode extrair valores de itens de linha de recibos.
  * **Valores de confiança** Você pode ver a confiança do modelo para cada valor extraído.
  * **Melhorias na precisão da extração**

* **Extração de layout** Agora você pode usar a API de layout para extrair dados de texto e dados de tabela de seus formulários.

### <a name="custom-model-api-changes"></a>Mudanças de API de modelo personalizado

Todas as APIs para treinamento e uso de modelos personalizados foram renomeadas, e alguns métodos síncronos são agora assíncronos. A seguir estão as principais mudanças:

* O processo de formação de um modelo é agora assíncrono. Você inicia o treinamento através da chamada **API /custom/models.** Esta chamada retorna um ID de operação, que você pode passar para **personalizado/models/{modelID}** para retornar os resultados do treinamento.
* A extração de chave/valor é agora iniciada pela chamada API **/custom/models/{modelID}/analyze.** Esta chamada retorna um ID de operação, que você pode passar para **personalizado/models/{modelID}/analyzeResults/{resultID}** para retornar os resultados de extração.
* Os IDs de operação para a operação Train são agora encontrados no cabeçalho de **localização** de respostas HTTP, não no cabeçalho **Operação-Localização.**

### <a name="receipt-api-changes"></a>Alterações na API de recebimento

As APIs para leitura de recibos de vendas foram renomeadas.

* A extração de dados de recebimento agora é iniciada pela chamada API **/prebuilt/receipt/analyze.** Esta chamada retorna um ID de operação, que você pode passar para **/prebuilt/receipt/analyzeResults/{resultID}** para retornar os resultados de extração.

### <a name="output-format-changes"></a>Alterações do formato de saída

As respostas json para todas as chamadas de API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Veja as partidas rápidas para exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Próximas etapas

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).