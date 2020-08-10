---
title: Exportar dados de IoT Central | Microsoft Docs
description: Como usar a nova exportação de dados para exportar seus dados de IoT para o Azure e destinos de nuvem personalizados.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036548"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Exportar dados de IoT para destinos de nuvem usando a exportação de dados (versão prévia)

> [!Note]
> Procurando a exportação de dados herdados? Você pode encontrar documentos de exportação de dados [aqui](./howto-export-data.md). Para saber mais sobre as diferenças entre a nova exportação de dados e a exportação de dados herdados, consulte a [tabela de comparação](#comparison-of-legacy-data-export-and-new-data-export).

Este artigo descreve como usar os novos recursos de visualização de exportação de dados no Azure IoT Central. Você pode usar esse recurso para exportar continuamente seus dados de IoT filtrados e aprimorados para seus serviços de nuvem. Você pode usar a exportação de dados para enviar por push as alterações quase em tempo real para outras partes da sua solução de nuvem para percepções, análises e armazenamento de caminho quente. 

 Por exemplo, você pode:
-   Exportar continuamente dados de telemetria e alterações de propriedade no formato JSON em tempo quase real
-   Filtrar esses fluxos de dados para exportar recursos específicos que correspondam às condições personalizadas
-   Enriquecer os fluxos de dados com valores personalizados e valores de Propriedade do dispositivo
-   Enviar esses dados para destinos como hubs de eventos do Azure, barramento de serviço do Azure, armazenamento de BLOBs do Azure e pontos de extremidade de webhook

> [!Note]
> Ao ativar a exportação de dados, você obtém apenas os dados desse momento em diante. No momento, os dados não podem ser recuperados por um horário em que a exportação de dados estava desativada. Para reter mais dados históricos, ative a exportação de dados antecipadamente.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a exportação de dados (versão prévia), você deve ter um aplicativo v3 e deve ter permissões de exportação de dados.

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

O destino de exportação deve existir antes de você configurar a exportação de dados. Estes são os tipos de destinos disponíveis:
  - Hubs de eventos do Azure
  - Fila do Barramento de Serviço do Azure
  - Tópico do Barramento de Serviço do Azure
  - Armazenamento do Blobs do Azure
  - webhook

### <a name="create-an-event-hubs-destination"></a>Criar um destino dos hubs de eventos

Se você não tiver um namespace de hubs de eventos existente para exportar para o, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

2. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

3. Gere uma chave que será usada em IoT Central para configurar a exportação de dados. 
    - Clique na instância do hub de eventos que você criou. 
    - Clique em **configurações/políticas de acesso compartilhado**. 
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** . 
    - Copie a cadeia de conexão primária ou secundária. Você usará isso para configurar um novo destino no IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Criar um destino de fila ou de tópico do barramento de serviço

Se você não tiver um namespace do barramento de serviço existente para exportar para o, siga estas etapas:

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Para criar uma fila ou um tópico para exportar para o, vá para o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico**.

3. Gere uma chave que será usada em IoT Central para configurar a exportação de dados. 
    - Clique na fila ou no tópico que você criou. 
    - Clique em **configurações/políticas de acesso compartilhado**. 
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** . 
    - Copie a cadeia de conexão primária ou secundária. Você usará isso para configurar um novo destino no IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Criar um destino de armazenamento de BLOBs do Azure

Se você não tiver uma conta de armazenamento do Azure existente para a qual exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre como criar novas [contas de armazenamento de BLOBs do Azure](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento Azure data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). A exportação de dados só pode gravar dados em contas de armazenamento que dão suporte a blobs de blocos. A lista a seguir mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Uso Geral v2|
    |Standard|Uso Geral v1|
    |Standard|Armazenamento de blob|
    |Premium|Armazenamento de blobs de blocos|

2. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

3. Gere uma cadeia de conexão para sua conta de armazenamento acessando **configurações/chaves de acesso**. Copie uma das duas cadeias de conexão.

### <a name="create-a-webhook-endpoint"></a>Criar um ponto de extremidade de webhook
Você pode fornecer um ponto de extremidade HTTP disponível publicamente para que os dados sejam exportados para o. Você pode criar um ponto de extremidade de webhook de teste usando RequestBin. Tenha em mente que o RequestBin tem um limite de solicitação definido antes que as solicitações sejam limitadas.

1. Abra [RequestBin](https://requestbin.net/).
2. Crie um novo RequestBin e copie a URL do Compartimento.

## <a name="set-up-data-export"></a>Configurar a exportação de dados

Agora que você tem um destino para exportar dados para o, siga estas etapas para configurar a exportação de dados.

1. Entre no aplicativo IoT Central.

2. No painel esquerdo, selecione **exportação de dados**.

    > [!Tip]
    > Se você não vir a **exportação de dados** no painel esquerdo, você não tem permissões para configurar a exportação de dados em seu aplicativo. Contate o administrador para configurar a exportação de dados.

3. Selecione o botão **+ nova exportação** . 

4. Insira um nome de exibição para sua nova exportação e verifique se a alternância **habilitada** está ativada para que os dados sejam fluidos.

## <a name="1-choose-the-type-of-data-to-export"></a>1. escolha o tipo de dados a ser exportado
Você pode escolher entre exportar continuamente diferentes tipos de dados. Estes são os tipos de dados com suporte:

| Tipo de dados | Descrição | Formato de dados |
| :------------- | :---------- | :----------- |
|  Telemetria | Exporte mensagens de telemetria de dispositivos quase em tempo real. Cada mensagem exportada conterá o conteúdo completo da mensagem do dispositivo original, normalizado.   |  [Formato de mensagem de telemetria](#telemetry-format)   |
| Alterações de propriedade | Exporte alterações para propriedades de dispositivo e de nuvem quase em tempo real. Para propriedades de dispositivo somente leitura, as alterações nos valores relatados serão exportadas. Para propriedades de leitura/gravação, os valores relatados e desejados serão exportados. | [Formato de mensagem de alteração de propriedade](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (opcional) adicionar filtros
Adicione filtros para reduzir a quantidade de dados exportados com base em condições de filtro. Há diferentes tipos de filtros disponíveis para cada tipo de dados a ser exportado.

### <a name="telemetry-filters"></a>Filtros de telemetria
  - **Filtro de capacidade**: se você escolher um item de telemetria no menu suspenso, o fluxo exportado conterá apenas telemetria que atenda à condição de filtro. Se você escolher um item de propriedade de dispositivo ou de nuvem no menu suspenso, o fluxo exportado só conterá telemetria de dispositivos que têm propriedades que correspondem à condição de filtro.
  - **Filtro de propriedade de mensagem**: dispositivos que usam os SDKs de dispositivo têm permissão para enviar *Propriedades de mensagem* ou *Propriedades de aplicativo* em cada mensagem de telemetria, que é um recipiente de pares chave-valor geralmente usado para marcar a mensagem com identificadores personalizados. Você pode criar um filtro de propriedade de mensagem digitando a chave de propriedade de mensagem que está procurando e especificando uma condição. Somente mensagens de telemetria com propriedades de mensagem que correspondam à condição de filtro especificada serão exportadas. Somente operadores de comparação de cadeia de caracteres têm suporte (Equals, não é igual a, Contains, não contém, existe, não existe). [Saiba mais sobre as propriedades do aplicativo nos documentos do Hub IOT](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filtros de alterações de propriedade
**Filtro de propriedade**: escolha um item de propriedade na lista suspensa e o fluxo exportado só conterá alterações na propriedade selecionada que atenda à condição de filtro.

## <a name="3-optional-add-enrichments"></a>3. (opcional) adicionar aprimoramentos
Adicione aprimoramentos para enriquecer mensagens exportadas com metadados adicionais em pares chave-valor. Estes são os aprimoramentos disponíveis para tipos de dados de telemetria e de alterações de propriedade:
  - **Cadeia de caracteres personalizada**: adiciona uma cadeia de caracteres estática personalizada a cada mensagem. Insira qualquer chave e insira qualquer valor de cadeia de caracteres.
  - **Propriedade**: adiciona a propriedade relatada do dispositivo atual ou o valor da propriedade de nuvem a cada mensagem. Insira qualquer chave e escolha um dispositivo ou uma propriedade de nuvem. Se a mensagem exportada for de um dispositivo que não tem o dispositivo ou a propriedade de nuvem especificada, a mensagem exportada não terá esse aperfeiçoamento.

## <a name="4-add-destinations"></a>4. Adicionar destinos
Crie um novo destino ou adicione um destino que você já criou. 
  
1. Clique no link **criar um novo destino** . Preencha as seguintes informações:
    - **Nome de destino**: o nome de exibição do destino no IOT central
    - **Tipo de destino**: escolha o tipo de destino. Se você ainda não fez isso, [Configure o destino de exportação](#set-up-export-destination)
    - Para hubs de eventos do Azure, fila ou tópico do barramento de serviço do Azure, Cole a cadeia de conexão para o recurso. 
    - Para o armazenamento de BLOBs do Azure, Cole a cadeia de conexão para o recurso e insira o nome do contêiner (diferencia maiúsculas de minúsculas).
    - Para webhook, Cole a URL de retorno de chamada para o ponto de extremidade do webhook. 
    - Clique em **Criar**

2. Clique em **+ destino** e escolha um destino na lista suspensa. Você adiciona até cinco destinos a uma única exportação.

3. Depois de concluir a configuração da exportação, clique em **salvar**. Depois de alguns minutos, seus dados aparecerão em seus destinos.

## <a name="export-contents-and-format"></a>Exportar conteúdo e formato

### <a name="azure-blob-storage-destination"></a>Destino do armazenamento de BLOBs do Azure

Os dados são exportados uma vez por minuto, com cada arquivo que contém o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Alterações de propriedade: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Para procurar os arquivos exportados no portal do Azure, navegue até o arquivo e selecione a guia **Editar blob** .

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Hubs de eventos do Azure e destinos do barramento de serviço do Azure

Os dados são exportados quase em tempo real. Os dados estão no corpo da mensagem e estão no formato JSON codificado como UTF-8. 

No recipiente de propriedades de anotações ou do sistema da mensagem, você pode localizar `iotcentral-device-id` , `iotcentral-application-id` , `iotcentral-message-source` e `iotcentral-message-type` quais têm os mesmos valores que os campos correspondentes no corpo da mensagem.

### <a name="webhook-destination"></a>Destino do webhook
Para destinos de WebHooks, os dados também são exportados quase em tempo real. Os dados estão no corpo da mensagem estão no mesmo formato que para os hubs de eventos e o barramento de serviço.


## <a name="telemetry-format"></a>Formato de telemetria
Cada mensagem exportada contém uma forma normalizada da mensagem completa que o dispositivo enviou no corpo da mensagem no formato JSON codificado como UTF-8. As informações adicionais incluídas em cada mensagem incluem:

- `applicationId`do aplicativo IoT Central
- `messageSource`que é *telemetria* para exportar dados de telemetria
- `deviceId`do dispositivo que enviou a mensagem de telemetria
- `schema`é o nome e a versão do esquema de carga
- `templateId`é a ID de modelo de dispositivo associada ao dispositivo
- `enrichments`os aprimoramentos foram configurados na exportação
- `messageProperties`são as partes adicionais de dados que o dispositivo enviou junto com a mensagem. Isso também é conhecido como *Propriedades do aplicativo*, [saiba mais nos documentos do Hub IOT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Para os hubs de eventos e o barramento de serviço, IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de telemetria exportada:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Formato de alterações de propriedade

Cada mensagem ou registro representa uma alteração em um dispositivo ou uma propriedade de nuvem. Para as propriedades do dispositivo, somente as alterações no valor relatado são exportadas como uma mensagem separada. As informações adicionais incluídas na mensagem exportada incluem:

- `applicationId`do aplicativo IoT Central
- `messageSource`que são *Propriedades* para exportar dados de alterações de propriedade
- `messageType`que é *cloudPropertyChange* ou *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`do dispositivo cujas propriedades foram alteradas
- `schema`é o nome e a versão do esquema de carga
- `templateId`é a ID de modelo de dispositivo associada ao dispositivo
- `enrichments`os aprimoramentos foram configurados na exportação

Para hubs de eventos e barramento de serviço, IoT Central exporta dados de novas mensagens para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de alteração de propriedade exportada recebida no armazenamento de BLOBs do Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Comparação de exportação de dados herdados e exportação de novos dados
Esta é uma tabela que realça as diferenças entre a exportação de dados herdada e a nova exportação de dados. Você pode aprender sobre a exportação de dados herdados [aqui](howto-export-data.md).

| Capacidades  | Exportação de dados herdados | Nova exportação de dados |
| :------------- | :---------- | :----------- |
| Tipos de dados disponíveis | Telemetria, dispositivos, modelos de dispositivo | Telemetria, alterações de propriedade |
| Filtragem | Nenhum | Depende do tipo de dados exportado. Para telemetria, filtragem por telemetria, propriedades de mensagem, valores de propriedade |
| Aprimoramentos | Nenhum | Enriquecer com uma cadeia de caracteres personalizada ou um valor de propriedade no dispositivo |
| Destinos | Hubs de eventos do Azure, filas e tópicos do barramento de serviço do Azure, armazenamento de BLOBs do Azure | O mesmo que para exportação de dados herdados e WebHooks| 
| Aplicativos com suporte | V2, V3 | Somente V3 |
| Limites notáveis | 5 exportações por aplicativo, 1 destino por exportação | 10 exportações-conexões de destino por aplicativo | 

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como usar a nova exportação de dados, vá para a próxima etapa:

> [!div class="nextstepaction"]
> [Como usar a análise no IoT Central](./howto-create-analytics.md)
