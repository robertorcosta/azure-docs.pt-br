---
title: Instalar contêineres de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Instale e execute contêineres de fala. A conversão de fala em texto transcreve, em tempo real, fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: dapine
ms.openlocfilehash: 5d30693eb13104504d1cf27ffdbfb8d098d4ef9e
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367765"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalar e executar contêineres do serviço de fala (versão prévia)

Os contêineres permitem executar algumas das APIs do serviço de fala em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner de fala.

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para recursos de nuvem robustos e localidade de borda. Há quatro contêineres diferentes disponíveis. Os dois contêineres padrão são Text **-to-Text** e **conversão de texto em fala**. Os dois contêineres personalizados são **fala personalizada para texto** e **texto em fala personalizado**.

> [!IMPORTANT]
> Atualmente, todos os contêineres de fala são oferecidos como parte de uma [Visualização pública "restrita"](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Um comunicado será feito quando os contêineres de fala progredirem para disponibilidade geral (GA).

| Função | Recursos | Mais recente |
|--|--|--|
| Conversão de fala em texto | Transcreve as gravações contínuas de fala em tempo real ou de áudio em lotes em texto com resultados intermediários. | 2.0.0 |
| Fala Personalizada para texto | Usar um modelo personalizado do [portal de fala personalizada](https://speech.microsoft.com/customspeech), transcreve gravações contínuas em tempo real ou de áudio em lotes em texto com resultados intermediários. | 2.0.0 |
| Conversão de texto em fala | Converte texto em fala natural-som com entrada de texto sem formatação ou SSML (linguagem de marcação de síntese de fala). | 1.3.0 |
| Conversão de texto em fala personalizada | Usando um modelo personalizado do [portal de voz personalizado](https://aka.ms/custom-voice-portal), o converte o texto em fala de som natural com entrada de texto sem formatação ou SSML (linguagem de marcação de síntese de fala). | 1.3.0 |

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Os seguintes pré-requisitos antes de usar os contêineres de fala:

| Obrigatório | Finalidade |
|--|--|
| Mecanismo Docker | É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br> |
| Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`. |
| Recurso de fala | Para usar esses contêineres, será necessário ter:<br><br>Um recurso de _fala_ do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral de **fala** e chaves de portal do Azure. Eles são necessários para iniciar o contêiner.<br><br>**{Api_key}** : uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : o ponto de extremidade conforme fornecido na página **visão geral** |

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Preencha e envie o [formulário de solicitação de contêineres de fala dos serviços cognitivas](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão de vetor avançado

O **host** é o computador que executa o contêiner do Docker. O host *deve dar suporte* a [extensões de vetor avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar o suporte a AVX2 em hosts Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> O computador host é *necessário* para dar suporte a AVX2. O contêiner *não* funcionará corretamente sem o suporte do AVX2.

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de fala.

# <a name="speech-to-texttabstt"></a>[Conversão de fala em texto](#tab/stt)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Conversão de fala em texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Fala Personalizada para texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em fala](#tab/tts)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Conversão de texto em fala | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Conversão de texto em fala personalizada | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

***

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!NOTE]
> O mínimo e recomendado são baseados nos limites do Docker, *não* nos recursos da máquina host. Por exemplo, os contêineres de conversão de texto na memória mapeiam partes de um modelo de linguagem grande e é *recomendável* que todo o arquivo caiba na memória, que é de 4-6 GB adicionais. Além disso, a primeira execução de qualquer um dos contêineres pode levar mais tempo, pois os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para fala estão disponíveis no registro de contêiner a seguir.

# <a name="speech-to-texttabstt"></a>[Conversão de fala em texto](#tab/stt)

| Contêiner | Repositório |
|-----------|------------|
| Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

| Contêiner | Repositório |
|-----------|------------|
| Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Conversão de texto em fala](#tab/tts)

| Contêiner | Repositório |
|-----------|------------|
| Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

| Contêiner | Repositório |
|-----------|------------|
| Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull do Docker para os contêineres de fala

# <a name="speech-to-texttabstt"></a>[Conversão de fala em texto](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull do Docker para o contêiner de conversão de fala em texto

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A marca de `latest` extrai a localidade `en-US`. Para localidades adicionais, consulte [localidades de fala para texto](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Localidades de conversão de fala em texto

Todas as marcas, com exceção de `latest` estão no seguinte formato e diferenciam maiúsculas de minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
2.0.0-amd64-en-us-preview
```

Para todas as localidades com suporte do contêiner de **fala a texto** , consulte [marcas de imagem de fala para texto](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Pull do Docker para o contêiner de Fala Personalizada para texto

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> O `locale` e `voice` para contêineres de fala personalizados são determinados pelo modelo personalizado ingerido pelo contêiner.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em fala](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Pull do Docker para o contêiner de conversão de texto em fala

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A marca de `latest` extrai a localidade `en-US` e `jessarus` voz. Para localidades adicionais, consulte [localidades de conversão de texto em fala](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Localidades de conversão de texto em fala

Todas as marcas, com exceção de `latest` estão no seguinte formato e diferenciam maiúsculas de minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A marca a seguir é um exemplo do formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Para todas as localidades com suporte e as vozes correspondentes do contêiner de **conversão de texto em fala** , confira [marcas de imagem de texto em fala](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Ao construir um HTTP POST *padrão de conversão de texto em fala* , a mensagem de [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md) requer um elemento `voice` com um atributo `name`. O valor é a localidade de contêiner correspondente e voz, também conhecido como ["nome curto"](language-support.md#standard-voices). Por exemplo, a marca de `latest` teria um nome de voz de `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull do Docker para o contêiner personalizado de conversão de texto em fala

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> O `locale` e `voice` para contêineres de fala personalizados são determinados pelo modelo personalizado ingerido pelo contêiner.

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](speech-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os valores de `{Endpoint_URI}` e de `{API_Key}`. [Exemplos](speech-container-configuration.md#example-docker-run-commands) adicionais do comando `docker run` também estão disponíveis.

# <a name="speech-to-texttabstt"></a>[Conversão de fala em texto](#tab/stt)

Para executar o contêiner de *conversão de fala em texto* , execute o comando `docker run` a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de *conversão de fala em texto* da imagem de contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

O contêiner de *fala personalizada para texto* depende de um modelo de fala personalizado. O modelo personalizado deve ter sido [treinado](how-to-custom-speech-train-model.md) usando o [portal de fala personalizado](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> O modelo de Fala Personalizada precisa ser treinado a partir de uma das seguintes versões de modelo:
> * **20181201 (v 3.3 unificado)**
> * **20190520 (v 4.14 unificado)**
> * **20190701 (v 4.17 unificado)**<br>
> ![Fala Personalizada modelo de contêiner de treinamento](media/custom-speech/custom-speech-train-model-container-scoped.png)

A ID do **modelo** de fala personalizado é necessária para executar o contêiner. Ele pode ser encontrado na página de **treinamento** do portal de fala personalizado. No portal de fala personalizado, navegue até a página de **treinamento** e selecione o modelo.
<br>

![Página de treinamento de fala personalizada](media/custom-speech/custom-speech-model-training.png)

Obtenha a **ID do modelo** para usar como o argumento para o parâmetro `ModelId` do comando `docker run`.
<br>

![Detalhes do modelo de fala personalizado](media/custom-speech/custom-speech-model-details.png)

A tabela a seguir representa os vários parâmetros `docker run` e suas descrições correspondentes:

| Parâmetro | DESCRIÇÃO |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)do computador host, que o Docker usa para manter o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada no computador host. |
| `{MODEL_ID}` | A **ID do modelo** de fala personalizada da página de **treinamento** do portal de fala personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner de *fala personalizada para texto* , execute o seguinte comando de `docker run`:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de *fala personalizada para texto* a partir da imagem de contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Carrega o modelo de *fala personalizada para texto* da montagem de entrada de volume, por exemplo, *C:\CustomSpeech*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo de acordo com o `ModelId` (se não for encontrado na montagem do volume).
* Se o modelo personalizado tiver sido baixado anteriormente, o `ModelId` será ignorado.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="text-to-speechtabtts"></a>[Conversão de texto em fala](#tab/tts)

Para executar o contêiner de *conversão de texto em fala* , execute o comando `docker run` a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de *conversão de texto em fala* da imagem de contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

O contêiner de *texto em fala personalizado* depende de um modelo de voz personalizado. O modelo personalizado deve ter sido [treinado](how-to-custom-voice-create-voice.md) usando o [portal de voz personalizado](https://aka.ms/custom-voice-portal). A ID do **modelo** de voz personalizado é necessária para executar o contêiner. Ele pode ser encontrado na página de **treinamento** do portal de voz personalizado. No portal de voz personalizado, navegue até a página de **treinamento** e selecione o modelo.
<br>

![Página de treinamento de voz personalizada](media/custom-voice/custom-voice-model-training.png)

Obtenha a **ID do modelo** para usar como o argumento para o `ModelId` parâmetro do comando de execução do Docker.
<br>

![Detalhes do modelo de voz personalizado](media/custom-voice/custom-voice-model-details.png)

A tabela a seguir representa os vários parâmetros `docker run` e suas descrições correspondentes:

| Parâmetro | DESCRIÇÃO |
|---------|---------|
| `{VOLUME_MOUNT}` | A montagem de [volume](https://docs.docker.com/storage/volumes/)do computador host, que o Docker usa para manter o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada no computador host. |
| `{MODEL_ID}` | A **ID do modelo** de fala personalizada da página de **treinamento** do portal de voz personalizado. |
| `{ENDPOINT_URI}` | O ponto de extremidade é necessário para medição e cobrança. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave de API é necessária. Para obter mais informações, consulte [coletando parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner de *texto em fala personalizado* , execute o seguinte comando de `docker run`:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de *conversão de texto em fala personalizado* da imagem de contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Carrega o modelo de *conversão de texto em fala personalizado* da montagem de entrada de volume, por exemplo, *C:\CustomVoice*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo de acordo com o `ModelId` (se não for encontrado na montagem do volume).
* Se o modelo personalizado tiver sido baixado anteriormente, o `ModelId` será ignorado.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

***

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

| Contêineres | URL do host do SDK | Protocolo |
|--|--|--|
| Conversão de fala em texto e Fala Personalizada em texto | `ws://localhost:5000` | WS |
| Conversão de texto em fala e Text-to-Speech personalizado | `http://localhost:5000` | HTTP |

Para obter mais informações sobre como usar os protocolos do WSS e HTTPS, consulte [segurança do contêiner](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Conversão de texto em fala ou personalizada para fala

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, execute cada um deles com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

É possível ter esse contêiner e um contêiner dos Serviços Cognitivos do Azure em execução no HOST juntos. Também é possível ter vários contêineres do mesmo contêiner dos Serviços Cognitivos em execução.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>solução de problemas

Ao iniciar ou executar o contêiner, você poderá ter problemas. Use uma [montagem](speech-container-configuration.md#mount-settings) de saída e habilite o registro em log. Isso permitirá que o contêiner gere arquivos de log que são úteis ao solucionar problemas.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de fala enviam informações de cobrança para o Azure, usando um recurso de *fala* em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres de fala. Em resumo:

* A fala fornece quatro contêineres do Linux para o Docker, encapsulando vários recursos:
  * *Conversão de fala em texto*
  * *Fala Personalizada para texto*
  * *Conversão de texto em fala*
  * *Conversão de texto em fala personalizada*
* As imagens de contêiner são baixadas do registro de contêiner no Azure.
* Imagens de contêiner são executadas no Docker.
* Se estiver usando a API REST (somente conversão de texto em fala) ou o SDK (fala-para-texto ou conversão de texto em fala), especifique o URI do host do contêiner. 
* Você precisa fornecer informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
>  Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Examinar [definir contêineres](speech-container-configuration.md) para definições de configuração
* Saiba como [usar contêineres de serviço de fala com kubernetes e Helm](speech-container-howto-on-premises.md)
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
