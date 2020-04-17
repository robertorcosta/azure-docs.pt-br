---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as últimas alterações na API do Reconhecimento de Formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531075"
---
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço Dereconhecer formulário é atualizado continuamente. Use este artigo para ficar atualizado com melhorias de recursos, correções e atualizações de documentação.

> [!NOTE]
> As iniciações rápidas e guias para Reconhecedor de Formulário sempre usam a versão mais recente da API, a menos que especificado.

## <a name="march-2020"></a>Março de 2020 

### <a name="new-features"></a>Novos recursos

* **Tipos de valor para rotulagem** Agora você pode especificar os tipos de valores que você está rotulando com a ferramenta de rotulagem de amostra Do Form Recognizer. Os seguintes tipos de valor e variações são suportados atualmente:
  * `string`
    * padrão, `no-whitespaces`,`alphanumeric`
  * `number`
    * Padrão`currency`
  * `date` 
    * padrão, `dmy` `mdy`, ,`ymd`
  * `time`
  * `integer`

  Consulte o guia [da ferramenta de rotulagem Sample](./quickstarts/label-tool.md#specify-tag-value-types) para saber como usar esse recurso.


* **Visualização de tabela** A ferramenta de rotulagem de amostras agora exibe tabelas que foram reconhecidas no documento. Isso permite visualizar as tabelas que foram reconhecidas e extraídas do documento, antes da rotulagem e análise. Esse recurso pode ser alternado on/off usando a opção camadas.

  Este é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização da tabela usando a ferramenta de rotulagem de amostras](./media/whats-new/formre-table-viz.png)

    As tabelas extraídas estão disponíveis `"pageResults"`na saída JSON em .

  > [!IMPORTANT]
  > As tabelas de rotulagem não são suportadas. Se as tabelas não forem reconhecidas e extraídas automaticamente, você só poderá rotulá-las como pares de chave/valor. Ao rotular tabelas como pares de chave/valor, marque cada célula como um valor único.

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui melhorias de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares de tecla/valor na mesma linha de texto. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A ferramenta de rotulagem de amostras agora é de código aberto

A ferramenta de rotulagem de amostra seletor de reconhecimento de formulário saem agora disponíveis como um projeto de código aberto. Você pode integrá-lo dentro de suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rotulagem de amostra do Form Recognizer, revise a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

O TLS 1.2 agora é aplicado para todas as solicitações HTTP para este serviço. Para obter mais informações, consulte [a segurança dos Serviços Cognitivos do Azure](../cognitive-services-security.md).

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