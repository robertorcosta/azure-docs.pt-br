---
title: Instalar e executar contêineres - Face
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como baixar, instalar e executar contêineres para face neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165988"
---
# <a name="install-and-run-face-containers-preview"></a>Instale e execute recipientes de rosto (Visualização)

O Azure Cognitive Services Face fornece um contêiner Linux padronizado para Docker que detecta rostos humanos em imagens. Também identifica atributos, que incluem marcos faciais, como narizes e olhos, sexo, idade e outras características faciais previstas pela máquina. Além da detecção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são as mesmas usando uma pontuação de confiança. O Face também pode comparar rostos com um banco de dados para ver se já existe um rosto semelhante ou idêntico. Ele também pode organizar rostos semelhantes em grupos usando traços visuais compartilhados.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar os contêineres de serviço Face.

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| O Mecanismo Docker deve ser instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> No Windows, o Docker também deve ser configurado para suportar contêineres Linux.<br><br>|
|Familiaridade com o Docker | Você precisa de uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêineres. Você também precisa `docker` de conhecimento de comandos básicos.| 
|Recurso facial |Para usar o recipiente, você deve ter:<br><br>Um recurso Azure **Face** e a chave API associada e o URI de ponto final. Ambos os valores estão disponíveis nas páginas **Visão Geral** e **Chaves** para o recurso. Eles são obrigados a ligar o contêiner.<br><br>**{API_KEY}**: Uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **Visão Geral**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos mínimos e recomendados da CPU e a memória para alocar para cada contêiner de serviço Face.

| Contêiner | Mínimo | Recomendadas | Transações por segundo<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Face | 1 núcleo, memória de 2 GB | 1 núcleo, memória de 4 GB |10, 20|

* Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
* Transações por segundo (TPS).

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem do recipiente com puxar docker

Imagens de contêiner para o serviço Face estão disponíveis. 

| Contêiner | Repositório |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull para o contêiner de Detecção Facial

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Use o recipiente

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o recipiente](#run-the-container-with-docker-run) com as configurações de faturamento necessárias. Há outros [exemplos](./face-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consultar o ponto final de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o recipiente com docker run

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte os [parâmetros necessários](#gathering-required-parameters) para `{ENDPOINT_URI}` obter `{API_KEY}` detalhes sobre como obter os valores e valores.

[Exemplos](face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um recipiente facial da imagem do contêiner.
* Aloca um núcleo de CPU e 4 GB de memória.
* Expõe a porta TCP 5000 e aloca um pseudo TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

Há outros [exemplos](./face-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As `Eula` `Billing`opções `ApiKey` e as opções devem ser especificadas para executar o recipiente ou o recipiente não é iniciada. Para mais informações, consulte [Faturamento](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, `http://localhost:5000`, para APIs de contêiner.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](./face-resource-container-config.md#mount-settings) de saída e o registro estiver ativado, o contêiner gerará arquivos de registro que são úteis para solucionar problemas que acontecem enquanto você inicia ou executa o contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres do serviço Face enviam informações de cobrança ao Azure usando um recurso Face em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para como baixar, instalar e executar contêineres de serviço Face. Em resumo:

* As imagens do contêiner são baixadas no Registro de Contêineres do Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de serviço Face especificando o URI host do contêiner.
* Você deve especificar informações de cobrança quando instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres de Serviços Cognitivos não têm licença para funcionar sem serem conectados ao Azure para medição. Os clientes devem habilitar os contêineres para comunicar informações de cobrança com o serviço de medição o tempo todo. Os contêineres dos Serviços Cognitivos não enviam dados do cliente, como imagem ou texto que está sendo analisado, para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Para configurações de configuração, consulte [Configure containers](face-resource-container-config.md).
* Para saber mais sobre como detectar e identificar rostos, consulte [Visão geral do Face](Overview.md).
* Para obter informações sobre os métodos suportados pelo contêiner, consulte a [API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Para usar mais contêineres de Serviços Cognitivos, consulte [os contêineres dos Serviços Cognitivos](../cognitive-services-container-support.md).
