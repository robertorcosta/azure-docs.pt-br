---
title: Como instalar e executar contêineres para usar a API do Detector de Anomalias
titleSuffix: Azure Cognitive Services
description: Use algoritmos avançados da API do Detector de Anomalias para identificar anomalias em seus dados de série temporal.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220342"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Instale e execute recipientes do Detector de Anomalias (Visualização)

O Detector de Anomalias tem a seguinte funcionalidade de recurso de contêiner:

| Função | Recursos |
|--|--|
| Detector de anomalias | <li> Detecta anomalias como ocorrem em tempo real. <li> Detecta anomalias em todo o seu conjunto de dados como um lote. <li> Infere o intervalo normal esperado de seus dados. <li> Suporta ajuste de sensibilidade à detecção de anomalias para melhor se adequar aos seus dados. |

Para obter informações detalhadas sobre as APIs, consulte:
* [Saiba mais sobre o serviço de API do Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar os recipientes do Detector de Anomalias:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso do Detector de Anomalias |Para usar esses contêineres, será necessário ter:<br><br>Um recurso do Azure _Anomaly Detector_ para obter a chave de API associada e o URI de ponto final. Ambos os valores estão disponíveis nas páginas 'Visão Geral do Detector de **Anomalias'** e chaves do portal Azure e são necessários para iniciar o contêiner.<br><br>**{API_KEY}**: Uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **Visão Geral**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Primeiro, você deve preencher e enviar o formulário de solicitação de [solicitação de contêiner do detector de anomalias](https://aka.ms/adcontainer) para solicitar acesso ao contêiner.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos mínimos e recomendados da CPU e a memória para alocar para o recipiente Do Detector de Anomalias.

| QPS (consultas por segundo) | Mínimo | Recomendadas |
|-----------|---------|-------------|
| 10 QPS | 4 núcleos, memória de 1 GB | 8 núcleos de memória de 2 GB |
| 20 QPS | 8 núcleos, memória de 2 GB | Memória de 16 núcleos de 4 GB |

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner.

| Contêiner | Repositório |
|-----------|------------|
| cognitivo-serviços-detector de anomalias | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Puxão docker para o recipiente do Detector de Anomalias

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consultar o ponto final de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte os [parâmetros necessários](#gathering-required-parameters) para `{ENDPOINT_URI}` obter `{API_KEY}` detalhes sobre como obter os valores e valores.

[Exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um recipiente detector de anomalias a partir da imagem do contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Executando vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, execute cada contêiner com uma porta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Substitua `<container-registry>` e `<container-name>` pelos valores dos contêineres que você usa. Eles não precisam estar no mesmo contêiner. Você pode ter o recipiente do Detector de Anomalias e o contêiner LUIS funcionando no HOST juntos ou você pode ter vários recipientes do Detector de Anomalias funcionando. 

Execute o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Execute o segundo contêiner na porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cada contêiner seguinte deve estar em uma porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, http://localhost:5000, para APIs de contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](anomaly-detector-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres do Detector de Anomalias enviam informações de cobrança para o Azure, usando um recurso _do Detector de Anomalias_ em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar contêineres do Anomaly Detector. Em resumo:

* O Anomaly Detector fornece um contêiner Linux para Docker, encapsulando a detecção de anomalias com lote vs streaming, inferência de alcance esperado e ajuste de sensibilidade.
* As imagens dos contêineres são baixadas de um Registro privado de contêineres do Azure dedicado à visualização de contêineres.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres do Detector de Anomalias especificando o URI host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados de clientes (por exemplo, os dados das séries tempois que estão sendo analisados) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](anomaly-detector-container-configuration.md) para configurações
* [Implantar um recipiente detector de anomalias em instâncias de contêineres do Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço de API do Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
