---
title: Como configurar um recipiente para API do Detector de Anomalias
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução do contêiner `docker run` API do Detector de Anomalias é configurado usando os argumentos de comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875173"
---
# <a name="configure-anomaly-detector-containers"></a>Configurar contêineres do Detector de Anomalias

O ambiente de tempo de execução do `docker run` contêiner Detector de **Anomalias** é configurado usando os argumentos de comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Usado para rastrear informações de cobrança.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|Sim|[Cobrança](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Sim|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não|[Proxy HTTP](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não|[Registro em log](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|Não|[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)configurações [`Eula`](#eula-setting) e configurações são usadas em conjunto, e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não vai começar. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser [`Billing`](#billing-configuration-setting) uma chave válida para o recurso Detector de _Anomalias_ especificado para a configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Gerenciamento de recursos **do Detector de Anomalias,** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o URI de ponto final do recurso _Anomaly Detector_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa configuração e o valor deve ser um URI de ponto final válido para um recurso _do Detector de Anomalias_ no Azure.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Visão Geral **do Detector de Anomalias,** rotulado`Endpoint`

|Obrigatório| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Uri ponto final de faturamento. Para obter mais informações sobre a obtenção do URI de faturamento, consulte [a coleta de parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters). Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os recipientes do Detector de Anomalias não usam suportes de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](anomaly-detector-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes do Detector de Anomalias não usam isso.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](anomaly-detector-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos Docker `\`nas seções seguintes usam a barra traseira, como um caractere de continuação de linha para uma concha bash. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. Por exemplo, o caractere de continuação de linha para o Windows é um acento circunflexo, `^`. Substitua a barra invertida pelo acento circunflexo. 
* **Ordem de argumento**: Não altere a ordem dos argumentos a menos que você esteja muito familiarizado com os contêineres Docker.

Substitua o valor `{}`entre parênteses, com seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A tecla de `Anomaly Detector` ponto final do `Anomaly Detector` recurso na página Chaves do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturamento `Anomaly Detector` está disponível na página Visão Geral do Azure.| Consulte [a coleta de parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](anomaly-detector-container-howto.md#billing).
> O valor apiKey é a **chave** da página de chaves do azure Anomaly Detector Resource. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemplos de docker do detector de anomalias

Os exemplos de Docker a seguir são para o recipiente Do detector de anomalias. 

### <a name="basic-example"></a>Exemplo básico 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de registro em log com argumentos de linha de comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Próximas etapas

* [Implantar um recipiente detector de anomalias em instâncias de contêineres do Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço de API do Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
