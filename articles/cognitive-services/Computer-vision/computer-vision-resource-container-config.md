---
title: Configure os contêineres - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como configurar as configurações necessárias e opcionais para os recipientes Reconhecer texto na Visão de Computador.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879302"
---
# <a name="configure-computer-vision-docker-containers"></a>Configurar recipientes Docker de visão de computador

Você configura o ambiente de tempo de execução do contêiner Computer Vision usando os `docker run` argumentos de comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)configurações [`Eula`](#eula-setting) e configurações são usadas em conjunto, e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não vai começar. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A `ApiKey` configuração especifica a `Cognitive Services` chave de recurso do Azure usada para rastrear as informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve [`Billing`](#billing-configuration-setting) ser uma chave válida para o recurso _Serviços Cognitivos_ especificado para a configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Gerenciamento de recursos de **serviços cognitivos,** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o uri ponto final do recurso _Serviços Cognitivos_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa configuração e o valor deve ser um URI de ponto final válido para um recurso _de Serviços Cognitivos_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Visão Geral **dos Serviços Cognitivos,** rotulado`Endpoint`

Lembre-se `vision/v1.0` de adicionar o roteamento ao URI de ponto final, conforme mostrado na tabela a seguir. 

|Obrigatório| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de Pesquisa Visual Computacional não usam montagens de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](computer-vision-how-to-install-containers.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](computer-vision-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos Docker `\`nas seções a seguir usam a barra traseira, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem de argumento**: Não altere a ordem dos argumentos a menos que você esteja muito familiarizado com os contêineres Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A tecla de `Computer Vision` ponto final do `Computer Vision` recurso na página Chaves do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturamento `Computer Vision` está disponível na página Visão Geral do Azure.| Consulte [a coleta de parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).
> O valor do ApiKey é a `Cognitive Services` **chave** da página chaves do Azure Resource.

## <a name="container-docker-examples"></a>Exemplos de Container Docker

Os exemplos de Docker a seguir são para o contêiner Ler.

### <a name="basic-example"></a>Exemplo básico

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de registro 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Próximas etapas

* Revisar [como instalar e executar contêineres](computer-vision-how-to-install-containers.md).
