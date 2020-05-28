---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as alterações mais recentes na API do reconhecedor de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: e3bf279142383ccdb3e82ffee49bd593640bb937
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996815"
---
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço do reconhecedor de formulário é atualizado em uma base contínua. Use este artigo para se manter atualizado com os aprimoramentos de recursos, correções e atualizações de documentação.

> [!NOTE]
> O guia de início rápido e guias do reconhecedor de formulário sempre usam a versão mais recente da API, a menos que especificado.

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Novos recursos
* **Suporte do SDK para a visualização pública da API v 2.0 do Forms Recognizer** Este mês, expandimos nosso suporte de serviço para incluir um SDK de visualização para o formulário Recognizer v 2.0 (versão prévia). Use os links abaixo para começar a usar o idioma de sua escolha: 
   * [SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer?view=azure-java-preview)
   * [SDK do Python](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-previewr)
   * [SDK do JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview)

  O novo SDK dá suporte a todos os recursos da API REST v 2.0 para o reconhecedor de formulário. Por exemplo, você pode treinar um modelo com ou sem rótulos e extrair texto, pares chave-valor e tabelas de seus formulários, extrair dados de recibos com o serviço de recebimentos pré-criados e extrair texto e tabelas com o serviço de layout de seus documentos. Você pode compartilhar seus comentários sobre os SDKs por meio do [formulário de comentários do SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Copiar modelo personalizado** Agora você pode copiar modelos entre regiões e assinaturas usando o novo recurso de modelo personalizado de cópia. Antes de invocar a API de modelo personalizado de cópia, primeiro você deve obter autorização para copiar no recurso de destino chamando a operação de cópia de autorização no ponto de extremidade do recurso de destino.
   * [Gerar uma autorização de cópia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModelAuthorization) API REST
   * [Copiar um modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModel) API REST 

### <a name="security-improvements"></a>Aprimoramentos de segurança

* As chaves gerenciadas pelo cliente agora estão disponíveis para FormRecognizer. Para obter mais informações, consulte [criptografia de dados em repouso para o reconhecedor de formulário](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Use identidades gerenciadas para acessar recursos do Azure com Azure Active Directory. Para obter mais informações, consulte [autorizar o acesso a identidades gerenciadas](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Março de 2020 

### <a name="new-features"></a>Novos recursos

* **Tipos de valor para rotulagem** Agora você pode especificar os tipos de valores que está rotulando com a ferramenta de rotulagem de amostra do reconhecedor de formulário. Os tipos de valor e as variações a seguir são compatíveis no momento:
  * `string`
    * padrão, `no-whitespaces`, `alphanumeric`
  * `number`
    * padrão, `currency`
  * `date` 
    * padrão, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Consulte o guia de [ferramentas de rótulo de exemplo](./quickstarts/label-tool.md#specify-tag-value-types) para saber como usar esse recurso.


* **Visualização de tabela** A ferramenta de rótulo de exemplo agora exibe tabelas que foram reconhecidas no documento. Isso permite que você exiba as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar. Esse recurso pode ser ativado/desativado usando a opção camadas.

  Este é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização de tabela usando a ferramenta de rótulo de exemplo](./media/whats-new/formre-table-viz.png)

    As tabelas extraídas estão disponíveis na saída JSON em `"pageResults"` .

  > [!IMPORTANT]
  > Não há suporte para rotular tabelas. Se as tabelas não forem reconhecidas e extrated automaticamente, você só poderá rotulá-las como pares de chave/valor. Ao rotular tabelas como pares de chave/valor, rotule cada célula como um valor exclusivo.

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui aprimoramentos de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares de chave/valor na mesma linha de texto. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A ferramenta de rótulo de amostra agora é de código-fonte aberto

A ferramenta de rotulagem de amostra do reconhecedor de formulário agora está disponível como um projeto de código-fonte aberto. Você pode integrá-lo em suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rótulo de exemplo do reconhecedor de formulário, examine a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

Agora o TLS 1.2 é obrigatório para todas as solicitações HTTP a este serviço. Para saber mais, confira [Segurança nos Serviços Cognitivos do Azure](../cognitive-services-security.md).

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