---
title: Instalar recipientes de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Instale e execute recipientes de fala. A conversão de fala em texto transcreve, em tempo real, fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474774"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instale e execute recipientes de serviço de fala (Preview)

Os contêineres permitem que você execute algumas das APIs de serviço de fala em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Neste artigo, você aprenderá como baixar, instalar e executar um contêiner Speech.

Os contêineres de fala permitem que os clientes construam uma arquitetura de aplicativo de voz otimizada tanto para recursos robustos na nuvem quanto para a localidade de borda. Há quatro recipientes diferentes disponíveis. Os dois recipientes padrão são **fala-a-texto** e **texto-para-fala**. Os dois recipientes personalizados são **o text-to-text personalizado** e **o text-to-speech personalizado**.

> [!IMPORTANT]
> Todos os recipientes de fala são atualmente oferecidos como parte de uma [pré-visualização pública "Gated".](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio) Um anúncio será feito quando os contêineres de fala progredirem para a Disponibilidade Geral (GA).

| Função | Recursos | Última |
|--|--|--|
| Conversão de fala em texto | Transcreve gravações contínuas de voz em tempo real ou em lote em texto com resultados intermediários. | 2.1.1 |
| Discurso personalizado para texto | Usando um modelo personalizado do [portal Custom Speech,](https://speech.microsoft.com/customspeech)transcreve gravações contínuas de voz em tempo real ou em lote em texto com resultados intermediários. | 2.1.1 |
| Conversão de texto em fala | Converte texto em fala natural com entrada de texto simples ou Linguagem de Marcação de Síntese de Fala (SSML). | 1.3.0 |
| Texto-para-fala personalizado | Usando um modelo personalizado do [portal Custom Voice,](https://aka.ms/custom-voice-portal)converte o texto em uma fala natural com entrada de texto simples ou Linguagem de Marcação de Síntese de Fala (SSML). | 1.3.0 |

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar recipientes de fala:

| Obrigatório | Finalidade |
|--|--|
| Mecanismo Docker | É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br> |
| Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`. |
| Recurso de fala | Para usar esses contêineres, será necessário ter:<br><br>Um recurso de _voz_ do Azure para obter a chave de API associada e o URI de ponto final. Ambos os valores estão disponíveis nas páginas "Visão Geral de **Fala"** do portal Azure e nas páginas Chaves. Ambos são obrigados a ligar o contêiner.<br><br>**{API_KEY}**: Uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **Visão Geral** |

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Preencha e envie o [formulário de solicitação de solicitação de filosdeado de serviços cognitivos](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte avançado de extensão vetorial

O **host** é o computador que executa o contêiner do Docker. O host *deve suportar* [extensões vetoriais avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar o suporte ao AVX2 nos hosts Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> O computador host é *necessário* para suportar o AVX2. O contêiner *não funcionará* corretamente sem o suporte AVX2.

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada recipiente de Fala.

# <a name="speech-to-text"></a>[Fala-a-texto](#tab/stt)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Conversão de fala em texto | 2 núcleos, memória de 2 GB | 4 núcleos, memória de 4 GB |

# <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Discurso personalizado para texto | 2 núcleos, memória de 2 GB | 4 núcleos, memória de 4 GB |

# <a name="text-to-speech"></a>[Texto-para-fala](#tab/tts)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Conversão de texto em fala | 1 núcleo, memória de 2 GB | 2 núcleos, memória de 3 GB |

# <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Texto-para-fala personalizado | 1 núcleo, memória de 2 GB | 2 núcleos, memória de 3 GB |

***

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!NOTE]
> O mínimo e o recomendado são baseados nos limites do Docker, *não* nos recursos da máquina host. Por exemplo, os contêineres de voz para texto mapeiam partes do mapa de um grande modelo de idioma, e *recomenda-se* que todo o arquivo se encaixe na memória, que é um adicional de 4-6 GB. Além disso, a primeira execução de qualquer recipiente pode demorar mais, já que os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para fala estão disponíveis no seguinte Registro de Contêiner.

# <a name="speech-to-text"></a>[Fala-a-texto](#tab/stt)

| Contêiner | Repositório |
|-----------|------------|
| Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

| Contêiner | Repositório |
|-----------|------------|
| Discurso personalizado para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Texto-para-fala](#tab/tts)

| Contêiner | Repositório |
|-----------|------------|
| Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

| Contêiner | Repositório |
|-----------|------------|
| Texto-para-fala personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker puxar para os recipientes de Discurso

# <a name="speech-to-text"></a>[Fala-a-texto](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker puxar para o recipiente Speech-to-text

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` etiqueta puxa `en-US` o local. Para locais adicionais, consulte [locais de fala para texto](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Locais de fala para texto

Todas as tags, exceto as seguintes, `latest` estão no seguinte formato e são sensíveis a maiúsculas e minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A seguinte tag é um exemplo do formato:

```
2.1.1-amd64-en-us-preview
```

Para todas as localidades suportadas do contêiner **de voz para texto,** consulte [tags de imagem de fala para texto](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Puxar docker para o recipiente de fala para texto personalizado

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Os `locale` `voice` recipientes de fala personalizados são determinados pelo modelo personalizado ingerido pelo recipiente.

# <a name="text-to-speech"></a>[Texto-para-fala](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker puxar para o recipiente texto-para-fala

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` etiqueta puxa `en-US` a `jessarus` localização e a voz. Para locais adicionais, consulte [locais de texto para discurso](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Locais de texto para fala

Todas as tags, exceto as seguintes, `latest` estão no seguinte formato e são sensíveis a maiúsculas e minúsculas:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A seguinte tag é um exemplo do formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Para todas as localidades suportadas e vozes correspondentes do contêiner **texto-para-fala,** consulte [as tags de imagem texto-para-fala](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Ao construir um POST HTTP de *texto para fala padrão,* a mensagem [SSML (Speech Synthesis Markup Language, linguagem de marcação de síntese de fala)](speech-synthesis-markup.md) requer um `voice` elemento com um `name` atributo. O valor é o local e a voz correspondentes do contêiner, também conhecido como ["nome curto".](language-support.md#standard-voices) Por exemplo, `latest` a tag teria `en-US-JessaRUS`um nome de voz de .

# <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Puxar docker para o recipiente personalizado texto-para-fala

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro de visualização de contêiner.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Os `locale` `voice` recipientes de fala personalizados são determinados pelo modelo personalizado ingerido pelo recipiente.

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](speech-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consultar o ponto final de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte os [parâmetros necessários](#gathering-required-parameters) para `{Endpoint_URI}` obter `{API_Key}` detalhes sobre como obter os valores e valores. Exemplos [adicionais](speech-container-configuration.md#example-docker-run-commands) `docker run` do comando também estão disponíveis.

# <a name="speech-to-text"></a>[Fala-a-texto](#tab/stt)

Para executar o contêiner *Fala-para-texto,* execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa *um recipiente de fala para texto* a partir da imagem do contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

O *contêiner de fala para texto personalizado* conta com um modelo de voz personalizado. O modelo personalizado tem que ter sido [treinado](how-to-custom-speech-train-model.md) usando o [portal de fala personalizado.](https://speech.microsoft.com/customspeech)

> [!IMPORTANT]
> O modelo Custom Speech precisa ser treinado a partir de uma das seguintes versões do modelo:
> * **20181201 (v3.3 Unificado)**
> * **20190520 (v4.14 Unificado)**
> * **20190701 (v4.17 Unificado)**<br>
> ![Modelo de contêiner de trem de fala personalizada](media/custom-speech/custom-speech-train-model-container-scoped.png)

O **ID do modelo de** voz personalizado é necessário para executar o contêiner. Pode ser encontrado na página de **treinamento** do portal de fala personalizado. No portal de voz personalizado, navegue até a página **treinamento** e selecione o modelo.
<br>

![Página de treinamento de fala personalizada](media/custom-speech/custom-speech-model-training.png)

Obtenha o **ID do modelo** para `ModelId` usar como `docker run` argumento para o parâmetro do comando.
<br>

![Detalhes do modelo de fala personalizado](media/custom-speech/custom-speech-model-details.png)

A tabela a seguir `docker run` representa os vários parâmetros e suas descrições correspondentes:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | O [suporte de volume do](https://docs.docker.com/storage/volumes/)computador host , que o docker usa para persistir o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina host. |
| `{MODEL_ID}` | O **ID do Modelo** de Fala Personalizada da página **treinamento** do portal de fala personalizado. |
| `{ENDPOINT_URI}` | O ponto final é necessário para medição e faturamento. Para obter mais informações, consulte [a coleta de parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave API é necessária. Para obter mais informações, consulte [a coleta de parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner *'Fala-para-texto' personalizado,* execute o seguinte `docker run` comando:

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

* Executa um *contêiner de fala para texto personalizado* da imagem do contêiner.
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória.
* Carrega o *modelo 'Fala-para-Texto' personalizado* da montagem de entrada de volume, por exemplo *C:\CustomSpeech*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo `ModelId` dado (se não encontrado na montagem de volume).
* Se o modelo personalizado foi baixado anteriormente, o `ModelId` é ignorado.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="text-to-speech"></a>[Texto-para-fala](#tab/tts)

Para executar o *contêiner texto-para-voz,* execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa *um contêiner texto-para-fala* a partir da imagem do contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

O *contêiner text-to-speech personalizado* conta com um modelo de voz personalizado. O modelo personalizado tem que ter sido [treinado](how-to-custom-voice-create-voice.md) usando o [portal de voz personalizado.](https://aka.ms/custom-voice-portal) O **ID do modelo de** voz personalizado é necessário para executar o contêiner. Pode ser encontrado na página de **treinamento** do portal de voz personalizado. No portal de voz personalizado, navegue até a página **treinamento** e selecione o modelo.
<br>

![Página de treinamento de voz personalizada](media/custom-voice/custom-voice-model-training.png)

Obtenha o **ID do modelo** para `ModelId` usar como argumento para o parâmetro do comando docker run.
<br>

![Detalhes personalizados do modelo de voz](media/custom-voice/custom-voice-model-details.png)

A tabela a seguir `docker run` representa os vários parâmetros e suas descrições correspondentes:

| Parâmetro | Descrição |
|---------|---------|
| `{VOLUME_MOUNT}` | O [suporte de volume do](https://docs.docker.com/storage/volumes/)computador host , que o docker usa para persistir o modelo personalizado. Por exemplo, *C:\CustomSpeech* onde a *unidade C* está localizada na máquina host. |
| `{MODEL_ID}` | O **ID do Modelo** de Fala Personalizada da página **treinamento** do portal de voz personalizado. |
| `{ENDPOINT_URI}` | O ponto final é necessário para medição e faturamento. Para obter mais informações, consulte [a coleta de parâmetros necessários](#gathering-required-parameters). |
| `{API_KEY}` | A chave API é necessária. Para obter mais informações, consulte [a coleta de parâmetros necessários](#gathering-required-parameters). |

Para executar o contêiner *Texto-para-voz personalizado,* execute o seguinte `docker run` comando:

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

* Executa um *contêiner personalizado texto-para-fala* a partir da imagem do contêiner.
* Aloca 2 núcleos de CPU e um gigabyte (GB) de memória.
* Carrega o modelo *personalizado texto-para-voz* a partir da montagem de entrada de volume, por exemplo *C:\CustomVoice*.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Baixa o modelo `ModelId` dado (se não encontrado na montagem de volume).
* Se o modelo personalizado foi baixado anteriormente, o `ModelId` é ignorado.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

***

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

| Contêineres | SDK Host URL | Protocolo |
|--|--|--|
| Fala-para-texto e Fala-a-texto personalizada | `ws://localhost:5000` | WS |
| Texto-para-fala e texto-para-fala personalizado | `http://localhost:5000` | HTTP |

Para obter mais informações sobre o uso de protocolos WSS e HTTPS, consulte [a segurança dos contêineres](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Texto-para-fala ou texto-para-fala personalizado

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, execute cada um deles com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

É possível ter esse contêiner e um contêiner dos Serviços Cognitivos do Azure em execução no HOST juntos. Também é possível ter vários contêineres do mesmo contêiner dos Serviços Cognitivos em execução.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Ao iniciar ou executar o contêiner, você pode ter problemas. Use uma [montagem](speech-container-configuration.md#mount-settings) de saída e habilite o registro. Isso permitirá que o contêiner gere arquivos de log que sejam úteis ao solucionar problemas.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres Speech enviam informações de cobrança para o Azure, usando um recurso *Speech* em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar contêineres de Speech. Em resumo:

* O Speech fornece quatro contêineres Linux para Docker, encapsulando vários recursos:
  * *Fala-a-texto*
  * *Discurso personalizado para texto*
  * *Texto-para-fala*
  * *Texto-para-fala personalizado*
* As imagens do contêiner são baixadas do registro de contêineres no Azure.
* Imagens de contêiner são executadas no Docker.
* Seja usando a API REST (somente texto para fala) ou o SDK (Fala-para-texto ou texto-para-fala) você especifica o URI host do contêiner. 
* Você é obrigado a fornecer informações de cobrança ao instanciar um contêiner.

> [!IMPORTANT]
>  Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revisar [configurar recipientes](speech-container-configuration.md) para configuração
* Saiba como [usar contêineres de serviço de fala com Kubernetes e Helm](speech-container-howto-on-premises.md)
* Use mais [contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
