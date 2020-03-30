---
title: Ingerir dados telemétricos históricos
description: Este artigo descreve como ingerir dados históricos de telemetria.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064444"
---
# <a name="ingest-historical-telemetry-data"></a>Ingerir dados telemétricos históricos

Este artigo descreve como ingerir dados históricos de sensores no Azure FarmBeats.

Ingerindo dados históricos de recursos de Internet das Coisas (IoT), como dispositivos e sensores, é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingere os dados históricos para FarmBeats em um formato canônico.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, certifique-se de que você instalou o FarmBeats e coletou dados históricos de seus dispositivos IoT. Você também precisa habilitar o acesso do parceiro, conforme mencionado nas etapas a seguir.

## <a name="enable-partner-access"></a>Habilite o acesso ao parceiro

Você precisa habilitar a integração do parceiro à sua instância Azure FarmBeats. Essa etapa cria um cliente que tem acesso à sua instância Azure FarmBeats como parceiro do dispositivo e fornece os seguintes valores necessários nas etapas subsequentes:

- Ponto final da API: Esta é a\<URL do Datahub, por exemplo, https:// datahub>.azurewebsites.net
- ID do locatário
- ID do Cliente
- Segredo do cliente
- String de conexão EventHub

Siga estas etapas:

> [!NOTE]
> Você deve ser um administrador para fazer as seguintes etapas.

1. Baixe o [arquivo zip](https://aka.ms/farmbeatspartnerscriptv2)e extraia-o para a sua unidade local. Haverá um arquivo dentro do arquivo zip.

2. Faça login https://portal.azure.com/ e vá para os registros do > **aplicativo**do **diretório ativo do Azure**.

3. Selecione o **Registro de Aplicativo** criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome do seu FarmBeats Datahub.

4. Selecione **Expor uma API** > Selecionar **Adicionar um aplicativo cliente** e digitar **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verificar **escopo de autorização**. Isso dará acesso ao Azure CLI (Cloud Shell) para executar as seguintes etapas:

5. Abra o Azure Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal Azure.

    ![Barra de ferramentas do portal Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Certifique-se de que o ambiente está definido como **PowerShell**. Por padrão, está definido para Bash.

    ![Configuração da barra de ferramentas PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Faça o upload do arquivo da etapa 1 na instância do Cloud Shell.

    ![Carregar botão de barra de ferramentas](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Vá para o diretório onde o arquivo foi carregado. Por padrão, os arquivos são carregados para o diretório inicial o nome de usuário.

9. Execute o seguinte script. O script pede o ID do Inquilino, que pode ser obtido na**página Visão Geral**do Diretório > Ativo do **Azure**.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. Siga as instruções na tela para capturar os valores de **API Endpoint,** **ID do inquilino,** **ID do cliente,** **Client Secret**e **EventHub Connection String**.

## <a name="create-device-or-sensor-metadata"></a>Criar metadados de dispositivos ou sensores

 Agora que você tem as credenciais necessárias, você pode definir o dispositivo e os sensores. Para fazer isso, crie os metadados chamando as APIs do FarmBeats. Certifique-se de chamar as APIs como o aplicativo cliente que você criou na seção acima.

 O FarmBeats Datahub possui as seguintes APIs que permitem a criação e o gerenciamento de metadados de dispositivos ou sensores.

 > [!NOTE]
 > Como parceiro, você tem acesso apenas para ler, criar e atualizar os metadados; **a opção de exclusão é restrita ao parceiro.**

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um gateway ou um nó.
- /**Dispositivo**: O dispositivo corresponde a um dispositivo físico presente na fazenda.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
- /**Sensor**: O sensor corresponde a um sensor físico que registra valores. Um sensor é normalmente conectado a um dispositivo com um ID do dispositivo.  


|        DeviceModel   |  Sugestões   |
| ------- | -------             |
|     Tipo (nó, gateway)        |          Tipo do dispositivo - Nó ou Gateway      |
|          Fabricante            |         Nome do fabricante    |
|  Productcode                    |  Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800.  |
|            Portas          |     Nome e tipo da porta, que é digital ou analógico.
|     Nome                 |  Nome para identificar o recurso. Por exemplo, o nome do modelo ou o nome do produto.
      Descrição     | Forneça uma descrição significativa do modelo.
|    Propriedades          |    Propriedades adicionais do fabricante.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID do modelo do dispositivo associado.  |
|  HardwareId          | ID exclusivo para o dispositivo, como o endereço MAC.
|  ReportingInterval        |   Relatando intervalo em segundos.
|  Location            |  Latitude do dispositivo (-90 a +90), longitude (-180 a 180) e elevação (em metros).   
|ParentDeviceId       |    ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, um nó conectado a um gateway. Um nó tem o parentDeviceId como o gateway.  |
|    Nome            | Um nome para identificar o recurso. Os parceiros de dispositivo devem enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo parceiro for definido pelo usuário, então o mesmo nome definido pelo usuário deve ser propagado para FarmBeats.|
|     Descrição       |      Forneça uma descrição significativa. |
|     Propriedades    |  Propriedades adicionais do fabricante.
|     **Modelo de sensor**        |          |
|       Tipo (analógico, digital)          |      O tipo de sensor, seja analógico ou digital.       |
|          Fabricante            |       O fabricante do sensor.     |
|     Productcode| Código do produto ou nome do modelo ou número. Por exemplo, RS-CO2-N01. |
|       SensorMeasures > Nome       | Nome da medida do sensor. Apenas minúsculas são suportadas. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Este nome deve ser consistente com os dados de telemetria.  |
|          SensorMeasures > DataType       |Tipo de dados de telemetria. Atualmente, o dobro é suportado.|
|    SensorMeasures > Type    |Tipo de medição dos dados de telemetria do sensor. Os tipos definidos pelo sistema são AmbientTemperature, CO2, Depth, ElectricConductivity, LeafWetness, Length, LiquidLevel, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, Umidade Relativa, Salinidade, Umidade do Solo, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte a API /ExtendedType.|
|        Unidade de > sensores medidas              | Unidade de dados de telemetria de sensores. As unidades definidas pelo sistema são NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercúrio, PSI, Milímetro, Centimeter, Medidor de Polegada, Pés, Milha, Quilometria, MilhasPor Hora, MilhasPorSegundo, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquare MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricionContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour Para adicionar mais, consulte a API /ExtendedType.|
|    SensorMeasures > AgregaationType    |  Os valores não podem ser nenhum, médio, máximo, mínimo ou Desvio Padrão.  |
|          Nome            | Nome para identificar um recurso. Por exemplo, o nome do modelo ou o nome do produto.  |
|    Descrição        | Forneça uma descrição significativa do modelo.|
|   Propriedades       |  Propriedades adicionais do fabricante.|
|    **Sensor**      |          |
| HardwareId          |   Identificação exclusiva para o conjunto de sensores pelo fabricante.|
|  SensorModelId     |    ID do modelo de sensor associado.|
| Location          |  Latitude do sensor (-90 a +90), longitude (-180 a 180) e elevação (em metros).|
|   Nome do > de Porta        |  Nome e tipo da porta à que o sensor está conectado no dispositivo. Isso precisa ser o mesmo nome definido no modelo do dispositivo.|
|    DeviceID  |    ID do dispositivo ao que o sensor está conectado. |
| Nome            |   Nome para identificar recurso. Por exemplo, nome do sensor ou nome do produto e número do modelo ou código do produto.|
|    Descrição      | Forneça uma descrição significativa.|
|    Propriedades        |Propriedades adicionais do fabricante.|

Para obter mais informações sobre objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Solicitação de API para criar metadados

Para fazer uma solicitação de API, você combina o método HTTP (POST), a URL ao serviço de API e o URI a um recurso para consultar, enviar dados, criar ou excluir uma solicitação. Em seguida, você adiciona um ou mais cabeçalhos de solicitação HTTP. O URL para o serviço de API é o ponto final\<da API, ou seja, a URL do Datahub (https:// seu datahub>.azurewebsites.net).  

### <a name="authentication"></a>Autenticação

O FarmBeats Datahub usa a autenticação do portador, que precisa das seguintes credenciais que foram geradas na seção anterior:

- ID do Cliente
- Segredo do cliente
- ID do locatário

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token deve ser enviado nas solicitações de API subseqüentes, na seção cabeçalho, da seguinte forma:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

O seguinte código Python de amostra fornece o token de acesso, que pode ser usado para chamadas de API subseqüentes para FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

**Cabeçalhos de solicitação HTTP**

Aqui estão os cabeçalhos de solicitação mais comuns que devem ser especificados quando você faz uma chamada de API para farmBeats Datahub:

- **Tipo de conteúdo:** aplicação/json
- **Autorização**:> de acesso <ao portador
- **Aceitar**: aplicação/json

### <a name="input-payload-to-create-metadata"></a>Carga útil de entrada para criar metadados

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Dispositivo

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Modelo de sensor

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensor

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

A seguinte solicitação de amostra cria um dispositivo. Esta solicitação tem a entrada JSON como carga útil com o corpo de solicitação.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Abaixo está um código de exemplo em Python. O token de acesso usado nesta amostra é o mesmo recebido durante a autenticação.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> As APIs retornam IDs exclusivos para cada instância criada. Você deve reter os IDs para enviar as mensagens de telemetria correspondentes.

### <a name="send-telemetry"></a>Enviar telemetria

Agora que você criou os dispositivos e sensores no FarmBeats, você pode enviar as mensagens de telemetria associadas.

### <a name="create-a-telemetry-client"></a>Crie um cliente de telemetria

Você deve enviar a telemetria para o Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados. Para enviar dados de telemetria para farmbeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre como enviar telemetria, consulte [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Envie uma mensagem de telemetria como cliente

Depois de ter uma conexão estabelecida como cliente do Event Hubs, você pode enviar mensagens para o hub de eventos como JSON.

Aqui está o código Python de exemplo que envia a telemetria como cliente para um hub de eventos especificado:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Converta o formato histórico de dados do sensor em um formato canônico que o Azure FarmBeats entende. O formato de mensagem canônica é o seguinte:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

Depois de adicionar os dispositivos e sensores correspondentes, obtenha o ID do dispositivo e o ID do sensor na mensagem de telemetria, conforme descrito na seção anterior.

Aqui está um exemplo de uma mensagem de telemetria:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>Solução de problemas

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerindo dados históricos/de streaming de seus sensores

**Sintoma**: Dispositivos ou sensores são implantados, e você criou os dispositivos/sensores no FarmBeats e ingerida telemetria para o EventHub, mas você não pode obter ou visualizar dados de telemetria no FarmBeats.

**Ação corretiva:**

1. Certifique-se de ter feito o registro de parceiro apropriado - você pode verificar isso indo para o seu datahub swagger, navegar para API /Partner, Fazer um Get e verificar se o parceiro está registrado. Caso assim, siga os [passos aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar parceiro.

2. Certifique-se de que você criou os metadados (DeviceModel, Device, SensorModel, Sensor) usando as credenciais do cliente parceiro.

3. Certifique-se de que você usou o formato correto da mensagem de telemetria (conforme especificado abaixo):

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre detalhes de integração baseados em REST API, consulte [API REST](rest-api-in-azure-farmbeats.md).
