---
title: Configurar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Os serviços de fala fornecem cada contêiner com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 15739b735b67e29ed07521d31857f1b176447487
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491051"
---
# <a name="configure-speech-service-containers"></a>Configurar contêineres de serviço de fala

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para aproveitar os recursos robustos de nuvem e a localidade de borda. Os quatro contêineres de fala para os quais damos suporte agora são, conversão de texto em **texto**, **personalizada de fala para texto**, conversão de textos em **fala**e **conversão personalizada**.

O ambiente de tempo de execução do contêiner de **fala** é configurado usando os argumentos do comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _fala_ especificado para a definição de configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: gerenciamento **de recursos de fala** , em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade do recurso de _fala_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _fala_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: visão geral **da fala** , rotulada `Endpoint`

|Obrigatório| Nome | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia de caracteres | URI do ponto de extremidade de cobrança. Para obter mais informações sobre o URI de cobrança, consulte [coletando parâmetros necessários](speech-container-howto.md#gathering-required-parameters). |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de fala padrão não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. No entanto, os contêineres de fala personalizados dependem de montagens de volume.

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](speech-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões do local de montagem do host. 

|Opcional| Nome | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia de caracteres | Os contêineres de fala padrão não usam isso. Os contêineres de fala personalizados usam [montagens de volume](#volume-mount-settings). |
|Opcional| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os contêineres de fala personalizados usam [montagens de volume](https://docs.docker.com/storage/volumes/) para manter modelos personalizados. Você pode especificar uma montagem de volume adicionando a opção `-v` (ou `--volume`) ao comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) .

Os modelos personalizados são baixados na primeira vez que um novo modelo é ingerido como parte do comando personalizado de execução do Docker de contêiner de fala. As execuções sequenciais da mesma `ModelId` para um contêiner de fala personalizado usarão o modelo baixado anteriormente. Se a montagem de volume não for fornecida, os modelos personalizados não poderão ser persistidos.

A configuração de montagem de volume consiste em três campos de `:` de cor separados:

1. O primeiro campo é o nome do volume no computador host, por exemplo, *C:\input*.
2. O segundo campo é o diretório no contêiner, por exemplo, */usr/local/Models*.
3. O terceiro campo (opcional) é uma lista de opções separada por vírgulas, para obter mais informações, consulte [usar volumes](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exemplo de montagem de volume

```bash
-v C:\input:/usr/local/models
```

Esse comando monta o diretório *C:\input* do computador host no diretório */usr/local/Models* do contêineres.

> [!IMPORTANT]
> As configurações de montagem de volume são aplicáveis somente a contêineres de **texto em fala** e de **fala personalizada para texto** . Os contêineres padrão de **fala para texto** e **conversão de texto em fala** não usam montagens de volume.

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](speech-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Placeholder | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Speech` na página chaves do `Speech` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Speech` do Azure.| Consulte [coletando parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para obter mais informações, confira [Cobrança](#billing-configuration-setting).
> O valor de ApiKey é a **chave** da página de chaves de recurso de fala do Azure. 

## <a name="speech-container-docker-examples"></a>Exemplos do Docker do contêiner de fala

Os exemplos do Docker a seguir são para o contêiner de fala. 

# <a name="speech-to-texttabstt"></a>[Conversão de fala em texto](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico de conversão de fala em texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de log para conversão de fala em texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Exemplo básico para Fala Personalizada para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Exemplo de log para Fala Personalizada para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="text-to-speechtabtss"></a>[Conversão de texto em fala](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico para conversão de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de log para conversão de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Exemplo básico para conversão de texto em fala personalizada

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Exemplo de log para conversão de texto em fala personalizada

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

***

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](speech-container-howto.md)
