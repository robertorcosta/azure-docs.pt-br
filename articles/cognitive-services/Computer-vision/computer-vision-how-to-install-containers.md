---
title: Como instalar e executar contêineres - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Pesquisa Visual Computacional neste tutorial passo a passo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 310a7578fe9d3af8c4600968178cb677658feaa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254322"
---
# <a name="install-and-run-read-containers-preview"></a>Instalar e executar ler contêineres (visualização)

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Os contêineres permitem executar as APIs de Pesquisa Visual Computacional em seu próprio ambiente. Contêineres são excelentes para especificar requisitos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner da Pesquisa Visual Computacional.

O contêiner *ler* permite detectar e extrair *texto impresso* de imagens de vários objetos com diferentes superfícies e planos de fundo, como recibos, pôsteres e cartões de visita. Além disso, o contêiner de *leitura* detecta *texto manuscrito* em imagens e fornece suporte a arquivos PDF, TIFF e de várias páginas. Para obter mais informações, consulte a documentação da API de [leitura](concept-recognizing-text.md#read-api) .

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar os contêineres:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Pesquisa Visual Computacional recurso |Para usar o contêiner, você precisará ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página **visão geral**|

## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contêiner

Preencha e envie o [formulário de solicitação](https://aka.ms/csgate) para solicitar aprovação para executar o contêiner. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão de vetor avançado

O computador **host** é o computador que executa o contêiner do Docker. O host *deve dar suporte* a [extensões de vetor avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar o suporte a AVX2 em hosts Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> O computador host é *necessário* para dar suporte a AVX2. O contêiner *não* funcionará corretamente sem o suporte do AVX2.

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para leitura estão disponíveis.

| Contêiner | Registro de contêiner/repositório/nome da imagem |
|-----------|------------|
| Leia 3,0-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Leia 3,1-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner.

### <a name="docker-pull-for-the-read-container"></a>Pull do Docker para o contêiner de leitura

# <a name="version-31-preview"></a>[Versão 3,1-visualização](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](computer-vision-resource-container-config.md) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` `{API_KEY}` valores e.

[Exemplos](computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

# <a name="version-31-preview"></a>[Versão 3,1-visualização](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa o contêiner de leitura da imagem de contêiner.
* Aloca 8 núcleos de CPU e 18 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

Esse comando:

* Executa o contêiner de leitura da imagem de contêiner.
* Aloca 8 núcleos de CPU e 18 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

---


Há outros [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

Se você precisar de uma taxa de transferência maior (por exemplo, ao processar arquivos de várias páginas), considere implantar vários contêineres v 3.0 ou v 3.1 [em um cluster kubernetes](deploy-computer-vision-on-premises.md), usando o [armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create) e a [fila do Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction).

Se você estiver usando o armazenamento do Azure para armazenar imagens para processamento, poderá criar uma [cadeia de conexão](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) para usar ao chamar o contêiner.

Para localizar a cadeia de conexão:

1. Navegue até **contas de armazenamento** na portal do Azure e localize sua conta.
2. Clique em **chaves de acesso** na lista de navegação à esquerda.
3. A cadeia de conexão será localizada abaixo da **cadeia de conexão**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. 

# <a name="version-31-preview"></a>[Versão 3,1-visualização](#tab/version-3-1)

Use o host, `http://localhost:5000`, para as APIs do contêiner. Você pode exibir o caminho do Swagger em: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Use o host, `http://localhost:5000`, para as APIs do contêiner. Você pode exibir o caminho do Swagger em: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Leitura assíncrona


# <a name="version-31-preview"></a>[Versão 3,1-visualização](#tab/version-3-1)

Você pode usar as `POST /vision/v3.1/read/analyze` `GET /vision/v3.1/read/operations/{operationId}` operações e em conjunto para ler de forma assíncrona uma imagem, semelhante a como o serviço de pesquisa Visual computacional usa as operações REST correspondentes. O método POST assíncrono retornará um `operationId` que é usado como o identificador para a solicitação HTTP Get.


Na interface do usuário do Swagger, selecione o `asyncBatchAnalyze` para expandi-lo no navegador. Em seguida, selecione **experimentar**  >  **escolher arquivo**. Neste exemplo, usaremos a imagem a seguir:

![guias vs espaços](media/tabs-vs-spaces.png)

Quando a POSTAgem assíncrona for executada com êxito, ela retornará um código de status **HTTP 202** . Como parte da resposta, há um `operation-location` cabeçalho que contém o ponto de extremidade do resultado para a solicitação.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` é a URL totalmente qualificada e é acessado por meio de um http Get. Aqui está a resposta JSON da execução da `operation-location` URL da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Você pode usar as `POST /vision/v3.0/read/analyze` `GET /vision/v3.0/read/operations/{operationId}` operações e em conjunto para ler de forma assíncrona uma imagem, semelhante a como o serviço de pesquisa Visual computacional usa as operações REST correspondentes. O método POST assíncrono retornará um `operationId` que é usado como o identificador para a solicitação HTTP Get.

Na interface do usuário do Swagger, selecione o `asyncBatchAnalyze` para expandi-lo no navegador. Em seguida, selecione **experimentar**  >  **escolher arquivo**. Neste exemplo, usaremos a imagem a seguir:

![guias vs espaços](media/tabs-vs-spaces.png)

Quando a POSTAgem assíncrona for executada com êxito, ela retornará um código de status **HTTP 202** . Como parte da resposta, há um `operation-location` cabeçalho que contém o ponto de extremidade do resultado para a solicitação.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` é a URL totalmente qualificada e é acessado por meio de um http Get. Aqui está a resposta JSON da execução da `operation-location` URL da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

> [!IMPORTANT]
> Se você implantar vários contêineres de leitura atrás de um balanceador de carga, por exemplo, em Docker Compose ou kubernetes, você deverá ter um cache externo. Como o contêiner de processamento e o contêiner de solicitação GET podem não ser iguais, um cache externo armazena os resultados e os compartilha entre contêineres. Para obter detalhes sobre as configurações de cache, consulte [configurar pesquisa Visual computacional contêineres do Docker](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Leitura síncrona

Você pode usar a operação a seguir para ler de forma síncrona uma imagem. 

# <a name="version-31-preview"></a>[Versão 3,1-visualização](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

---

Quando a imagem é lida em sua totalidade, e somente em seguida, a API retorna uma resposta JSON. A única exceção a isso é se ocorrer um erro. Quando ocorre um erro, o JSON a seguir é retornado:

```json
{
    "status": "Failed"
}
```

O objeto de resposta JSON tem o mesmo grafo de objeto que a versão assíncrona. Se você for um usuário de JavaScript e quiser a segurança de tipo, considere usar TypeScript para converter a resposta JSON.

Para obter um exemplo de caso de uso, consulte a <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">área <span class="docon docon-navigate-external x-hidden-focus"></span> restrita do TypeScript aqui</a> e selecione **executar** para visualizar sua facilidade de uso.

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](./computer-vision-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de serviços cognitivas enviam informações de cobrança para o Azure, usando o recurso correspondente em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Pesquisa Visual Computacional. Em resumo:

* Pesquisa Visual Computacional fornece um contêiner do Linux para o Docker, encapsulando leitura.
* As imagens de contêiner são baixadas do registro de contêiner "visualização do contêiner" no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de leitura especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examinar [Visão geral da Pesquisa Visual Computacional](overview.md) para saber mais sobre como reconhecer texto impresso e manuscrito
* Veja a [API da Pesquisa Visual Computacional](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
