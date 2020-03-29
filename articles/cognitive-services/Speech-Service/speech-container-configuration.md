---
title: Configurar recipientes de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala fornece a cada contêiner uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro e a telemetria e as configurações de segurança dos seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037320"
---
# <a name="configure-speech-service-containers"></a>Configurar contêineres de serviço de fala

Os contêineres de fala permitem que os clientes construam uma arquitetura de aplicativo de fala otimizada para aproveitar os recursos robustos da nuvem e a localidade de borda. Os quatro recipientes de fala que apoiamos agora são, **fala-a-texto,** **discurso-a-texto personalizado,** **texto-para-fala**e **texto-para-fala personalizado.**

O ambiente de tempo de execução do contêiner **Speech** é configurado usando os argumentos de `docker run` comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)configurações [`Eula`](#eula-setting) e configurações são usadas em conjunto, e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não vai começar. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e _Speech_ o valor deve [`Billing`](#billing-configuration-setting) ser uma chave válida para o recurso Speech especificado para a configuração.

Essa configuração pode ser localizada no seguinte local:

- Portal Azure: Gerenciamento de recursos **do discurso,** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o URI de ponto final do recurso _Speech_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa configuração e o valor deve ser um URI de ponto final válido para um recurso _De fala_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

- Portal Azure: Visão geral **do discurso,** rotulado`Endpoint`

| Obrigatório | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Sim | `Billing` | String | Uri ponto final de faturamento. Para obter mais informações sobre a obtenção do URI de faturamento, consulte [a coleta de parâmetros necessários](speech-container-howto.md#gathering-required-parameters). Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os recipientes Standard Speech não usam suportes de entrada ou saída para armazenar dados de treinamento ou serviço. No entanto, os recipientes de fala personalizados dependem de montagens de volume.

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](speech-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e as permissões de localização de montagem do host.

| Opcional | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Não permitido | `Input` | String | Os recipientes Standard Speech não usam isso. Os recipientes de fala personalizados usam [suportes de volume](#volume-mount-settings).                                                                                    |
| Opcional | `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os recipientes de fala personalizados usam [suportes de volume](https://docs.docker.com/storage/volumes/) para persistir modelos personalizados. Você pode especificar uma `-v` montagem `--volume`de volume adicionando a opção (ou ) ao comando [docker run.](https://docs.docker.com/engine/reference/commandline/run/)

Modelos personalizados são baixados na primeira vez que um novo modelo é ingerido como parte do comando custom speech container docker run. As corridas seqüenciais `ModelId` do mesmo para um recipiente de voz personalizado usarão o modelo anteriormente baixado. Se o suporte de volume não for fornecido, os modelos personalizados não poderão ser persistidos.

A configuração de montagem `:` de volume consiste em três campos separados de cor:

1. O primeiro campo é o nome do volume na máquina host, por _exemplo, c:\input_.
2. O segundo campo é o diretório no recipiente, por exemplo _/usr/local/modelos_.
3. O terceiro campo (opcional) é uma lista de opções separadas por comma, para obter mais informações veja [volumes de uso](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exemplo de montagem de volume

```bash
-v C:\input:/usr/local/models
```

Este comando monta o diretório _de entrada c:\da_ máquina host para o diretório de contêineres _/usr/local/models._

> [!IMPORTANT]
> As configurações de montagem de volume são aplicáveis apenas aos contêineres **de texto** para texto personalizado e de texto **personalizado.** Os recipientes padrão **de fala para texto** e **texto-para-fala** não usam montagens de volume.

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`. Quando em execução, o contêiner continuará a ser executado até que você o [pare](speech-container-howto.md#stop-the-container).

- **Caractere de continuação de linha**: Os comandos Docker `\`nas seções a seguir usam a barra traseira, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host.
- **Ordem de argumento**: Não altere a ordem dos argumentos a menos que você esteja familiarizado com os contêineres Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | A tecla de `Speech` ponto final do `Speech` recurso na página Chaves do Azure.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | O valor do ponto final de faturamento `Speech` está disponível na página Visão Geral do Azure. | Consulte [a coleta de parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado. Para mais informações, consulte [Faturamento](#billing-configuration-setting).
> O valor apiKey é a **chave** da página chaves do Recurso de Fala do Azure.

## <a name="speech-container-docker-examples"></a>Exemplos de Docker de contêiner de fala

Os exemplos de Docker a seguir são para o contêiner Speech.

## <a name="speech-to-text"></a>[Fala-a-texto](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico para Fala-para-texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de registro de fala para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Exemplo básico para discurso personalizado para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Exemplo de registro de fala personalizada para texto

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

## <a name="text-to-speech"></a>[Texto-para-fala](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico para texto-para-fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de registro para texto-para-fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Exemplo básico para texto-para-fala personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Exemplo de registro para texto-para-fala personalizado

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

---

## <a name="next-steps"></a>Próximas etapas

- Reveja [Como instalar e executar contêineres](speech-container-howto.md)
