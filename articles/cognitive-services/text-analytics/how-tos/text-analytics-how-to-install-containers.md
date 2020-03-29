---
title: Instalar e executar contêineres - Text Analytics
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Análise de Texto neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037516"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres da Análise de Texto

Os contêineres permitem que você execute as APIs de análise de texto em seu próprio ambiente e são ótimos para seus requisitos específicos de segurança e governança de dados. Os contêineres text analytics fornecem processamento avançado de linguagem natural sobre texto bruto e incluem três funções principais: análise de sentimentos, extração de frases-chave e detecção de linguagem. Atualmente, não há suporte para vinculação de entidade em um contêiner.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> A conta gratuita é limitada a 5.000 transações por mês e apenas as camadas de preços **Free** e **Standard** são <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">válidas <span class="docon docon-navigate-external x-hidden-focus"></span> </a> para contêineres. Para obter mais informações sobre as taxas de solicitação de transação, consulte [Limites de dados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres do Text Analytics, você deve ter o computador host e ambientes de contêineres.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de Análise de Texto:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso de Análise de Texto |Para usar o contêiner, você precisará ter:<br><br>Um recurso do Azure [Text Analytics](../../cognitive-services-apis-create-account.md) para obter a chave de API associada e o URI de ponto final. Ambos os valores estão disponíveis nas páginas Visão Geral e Chaves da Análise de Texto no portal do Azure e são necessários para iniciar o contêiner.<br><br>**{API_KEY}**: Uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **Visão Geral**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 gigahertz (GHz) ou mais rápidos, bem como a memória, em gigabytes (GB), para alocar para cada contêiner de Análise de Texto.

# <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para análise de texto estão disponíveis no Microsoft Container Registry.

# <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker puxar para os contêineres text analytics

# <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consultar o ponto final de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. Consulte [Os parâmetros necessários](#gathering-required-parameters) para `{ENDPOINT_URI}` obter `{API_KEY}` detalhes sobre como obter os valores e valores.

[Exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

# <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST.

Use o host, `http://localhost:5000`, para APIs de contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](../text-analytics-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de Análise de Texto enviam informações de cobrança para Azure, usando um recurso _Análise de Texto_ na conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Análise de Texto. Em resumo:

* O Text Analytics fornece três contêineres Linux para Docker, encapsulando vários recursos:
   * *Extração de Frases-chave*
   * *Detecção de Idioma*
   * *Análise de Sentimentos*
* Imagens de contêiner são baixadas de um MCR (Registro de Contêiner da Microsoft) no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de análise de texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](../text-analytics-resource-container-config.md) para configurações
* Consulte [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.
