---
title: Configurações do contêiner Docker - LUIS
titleSuffix: Azure Cognitive Services
description: O ambiente de runtime do contêiner do LUIS é configurado usando argumentos de comando `docker run`. O LUIS tem várias configurações obrigatórias e outras configurações opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 48a9856c58a815eabcc0b105efcd548e66ddd552
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874204"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar contêineres do Docker do Serviço Inteligente de Reconhecimento Vocal 

O ambiente **de tempo** de execução do `docker run` contêiner Language Understanding (LUIS) é configurado usando os argumentos de comando. O LUIS tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as [configurações de montagem](#mount-settings) de entrada e as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Sim|[ApiKey](#apikey-setting)|Usado para rastrear informações de cobrança.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|Sim|[Cobrança](#billing-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Sim|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não|[Proxy HTTP](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não|[Registro em log](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|Sim|[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting)configurações [`Eula`](#eula-setting) e configurações são usadas em conjunto, e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não vai começar. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Configuração ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve [`Billing`](#billing-setting) ser uma chave válida para o recurso _Serviços Cognitivos_ especificado para a configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Gerenciamento de recursos de **serviços cognitivos,** em **Chaves**
* Portal LUIS: **Página de configurações de chaves e ponto final.** 

Não use a chave inicial ou de criação. 

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Configuração de cobrança

A `Billing` configuração especifica o uri ponto final do recurso _Serviços Cognitivos_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa configuração e o valor deve ser um URI de ponto final válido para um recurso _de Serviços Cognitivos_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal Azure: Visão Geral **dos Serviços Cognitivos,** rotulado`Endpoint`
* Portal LUIS: **Página de configurações de chaves e ponto final,** como parte do uri ponto final.

| Obrigatório | Nome | Tipo de dados | Descrição |
|----------|------|-----------|-------------|
| Sim      | `Billing` | string | Uri ponto final de faturamento. Para obter mais informações sobre a obtenção do URI de faturamento, consulte [a coleta de parâmetros necessários](luis-container-howto.md#gathering-required-parameters). Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

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

O contêiner do LUIS não usa montagens de entrada ou saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](luis-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e as permissões de localização de montagem do host. 

A tabela a seguir descreve as configurações com suporte.

|Obrigatório| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | String | O destino de montagem de entrada. O valor padrão é `/input`. Esse é o local dos arquivos de pacote do LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de consulta do LUIS e logs do contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](luis-container-howto.md#stop-the-container).

* Esses exemplos usam o diretório `C:` fora da unidade para evitar quaisquer conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço. 
* não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente, use o console/terminal correto, a sintaxe de pastas para montagens e o caractere de continuação da linha para o seu sistema. Esses exemplos assumem um console Windows `^`com um caractere de continuação de linha . Como o contêiner é um sistema operacional Linux, o suporte de destino usa uma sintaxe de pasta no estilo Linux.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A tecla de `LUIS` ponto final do `LUIS` recurso na página Chaves do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturamento `LUIS` está disponível na página Visão Geral do Azure.| Consulte [a coleta de parâmetros necessários](luis-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado. Para mais informações, consulte [Faturamento](luis-container-howto.md#billing).
> O valor apiKey é a **chave** da página Chaves e Pontos finais no `Cognitive Services` portal LUIS e também está disponível na página chaves de recursos do Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo abaixo tem o menor número de argumentos possível para executar o contêiner:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exemplo de ApplicationInsights

O exemplo a seguir define o argumento ApplicationInsights para enviar telemetria ao Application Insights enquanto o contêiner está em execução:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Exemplo de registro 

O comando a seguir `Logging:Console:LogLevel`define o nível de [`Information`](https://msdn.microsoft.com)registro, para configurar o nível de registro para . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](luis-container-howto.md)
* Consulte a [Solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade LUIS.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
