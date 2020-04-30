---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as alterações mais recentes na API do reconhecedor de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531075"
---
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço do reconhecedor de formulário é atualizado em uma base contínua. Use este artigo para se manter atualizado com os aprimoramentos de recursos, correções e atualizações de documentação.

> [!NOTE]
> O guia de início rápido e guias do reconhecedor de formulário sempre usam a versão mais recente da API, a menos que especificado.

## <a name="march-2020"></a>Março de 2020 

### <a name="new-features"></a>Novos recursos

* **Tipos de valor para rotulagem** Agora você pode especificar os tipos de valores que está rotulando com a ferramenta de rotulagem de amostra do reconhecedor de formulário. Os tipos de valor e as variações a seguir têm suporte no momento:
  * `string`
    * padrão, `no-whitespaces`,`alphanumeric`
  * `number`
    * os`currency`
  * `date` 
    * padrão, `dmy`, `mdy`,`ymd`
  * `time`
  * `integer`

  Consulte o guia de [ferramentas de rótulo de exemplo](./quickstarts/label-tool.md#specify-tag-value-types) para saber como usar esse recurso.


* **Visualização de tabela** A ferramenta de rótulo de exemplo agora exibe tabelas que foram reconhecidas no documento. Isso permite que você exiba as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar. Esse recurso pode ser ativado/desativado usando a opção camadas.

  Este é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização de tabela usando a ferramenta de rótulo de exemplo](./media/whats-new/formre-table-viz.png)

    As tabelas extraídas estão disponíveis na saída JSON em `"pageResults"`.

  > [!IMPORTANT]
  > Não há suporte para rotular tabelas. Se as tabelas não forem reconhecidas e extrated automaticamente, você só poderá rotulá-las como pares de chave/valor. Ao rotular tabelas como pares de chave/valor, rotule cada célula como um valor exclusivo.

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui aprimoramentos de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares de chave/valor na mesma linha de texto. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A ferramenta de rótulo de amostra agora é de código-fonte aberto

A ferramenta de rotulagem de amostra do reconhecedor de formulário agora está disponível como um projeto de código-fonte aberto. Você pode integrá-lo em suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rótulo de exemplo do reconhecedor de formulário, examine a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

O TLS 1,2 agora é imposto para todas as solicitações HTTP para esse serviço. Para obter mais informações, consulte [segurança de serviços cognitivas do Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Janeiro de 2020

Esta versão apresenta o formulário Recognizer 2,0 (visualização). Nas seções a seguir, você encontrará mais informações sobre novos recursos, aprimoramentos e alterações. 

### <a name="new-features"></a>Novos recursos

* **Modelo personalizado**
  * **Treinar com rótulos** Agora você pode treinar um modelo personalizado com dados rotulados manualmente. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.
  * **API assíncrona** Você pode usar chamadas de API assíncronas para treinar e analisar grandes conjuntos de dados e arquivos.
  * **Suporte a arquivo TIFF** Agora você pode treinar e extrair dados de documentos TIFF.
  * **Aprimoramentos de precisão de extração**

* **Modelo de recebimento predefinido**
  * **Valores de gorjeta** Agora você pode extrair valores de gorjeta e outros valores manuscritos.
  * **Extração de item de linha** Você pode extrair valores de item de linha de recebimentos.
  * **Valores de confiança** Você pode exibir a confiança do modelo para cada valor extraído.
  * **Aprimoramentos de precisão de extração**

* **Extração de layout** Agora você pode usar a API de layout para extrair dados de texto e dados de tabela de seus formulários.

### <a name="custom-model-api-changes"></a>Alterações da API do modelo personalizado

Todas as APIs para treinamento e uso de modelos personalizados foram renomeadas e alguns métodos síncronos agora são assíncronos. Estas são as principais alterações:

* O processo de treinamento de um modelo agora é assíncrono. Você inicia o treinamento por meio da chamada à API do **/Custom/Models** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}** para retornar os resultados de treinamento.
* A extração de chave/valor agora é iniciada pela chamada à API **/Custom/Models/{modelID}/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}/analyzeResults/{resultid}** para retornar os resultados de extração.
* As IDs de operação para a operação de treinamento agora estão localizadas no cabeçalho **local** de respostas http, não no cabeçalho **Operation-Location** .

### <a name="receipt-api-changes"></a>Alterações da API de recebimento

As APIs para ler os recibos de vendas foram renomeadas.

* A extração de dados de recebimento agora é iniciada pela chamada à API **/prebuilt/Receipt/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **/prebuilt/Receipt/analyzeResults/{resultID}** para retornar os resultados de extração.

### <a name="output-format-changes"></a>Alterações de formato de saída

As respostas JSON para todas as chamadas de API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Consulte os guias de início rápido para obter exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Próximas etapas

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).