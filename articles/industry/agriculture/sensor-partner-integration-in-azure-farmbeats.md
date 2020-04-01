---
title: Integração com parceiros de sensor
description: Este artigo descreve a integração do parceiro de sensores.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437478"
---
# <a name="sensor-partner-integration"></a>Integração com parceiros de sensor

Este artigo fornece informações sobre o componente Azure FarmBeats **Translator,** que permite a integração de parceiros de sensores.

Usando este componente, os parceiros podem se integrar ao FarmBeats usando apis do FarmBeats Datahub e enviar dados e telemetria do dispositivo do cliente para o FarmBeats Datahub. Uma vez que os dados estão disponíveis no FarmBeats, ele é visualizado usando o FarmBeats Accelerator e pode ser usado para fusão de dados e para a construção de modelos de machine learning/inteligência artificial.

## <a name="before-you-start"></a>Antes de começar

Para desenvolver o componente Tradutor, você precisará das seguintes credenciais que permitirão o acesso às APIs do FarmBeats.

- Ponto de extremidade de API
- ID do locatário
- ID do Cliente
- Segredo do cliente
- String de conexão EventHub

Consulte esta seção para obter as credenciais acima: [Habilite a integração do dispositivo](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Desenvolvimento de tradutor

**Integração com base em API REST**

Os recursos de integração de dados de sensores do FarmBeats são expostos através da API REST. Os recursos incluem definição de metadados, provisionamento de dispositivos e sensores e gerenciamento de dispositivos e sensores.

**Ingestão de telemetria**

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

**Desenvolvimento de API**

As APIs contêm documentação técnica swagger. Para obter mais informações sobre as APIs e suas solicitações ou respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsSwagger).

**Autenticação**

O FarmBeats usa a autenticação do Microsoft Azure Active Directory.O Azure App Service oferece suporte integrado de autenticação e autorização.

Para obter mais informações, consulte [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

O FarmBeats Datahub usa a autenticação do portador, que precisa das seguintes credenciais:
   - ID do Cliente
   - Segredo do cliente
   - ID do locatário

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token precisa ser enviado nas solicitações de API subseqüentes, na seção cabeçalho, da seguinte forma:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

O código Python de amostra a seguir fornece o token de acesso, que pode ser usado para chamadas subsequentes de API para FarmBeats.

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

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados quando você faz uma chamada de API para farmBeats Datahub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo | O formato da solicitação (Tipo<format>de conteúdo: aplicação/ ). Para farmbeats APIs Datahub, o formato é JSON. Content-Type: application/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada de API. Autorização:> de acesso <do Portador
Aceitar | O formato da resposta. Para farmbeats APIs Datahub, o formato é JSON. Aceitar: aplicação/json

**Pedidos de API**

Para fazer uma solicitação de API REST, você combina o método HTTP (GET, POST ou PUT), a URL para o serviço de API, o Uniform Resource Identifier (URI) a um recurso para consultar, enviar dados, atualizar ou excluir e um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço de API é o ponto final da API que você fornece. Aqui está uma amostra: https://\<seu datahub-site-nome>.azurewebsites.net

Opcionalmente, você pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

A seguinte solicitação de amostra é obter a lista de dispositivos.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A maioria das chamadas GET, POST e PUT requerem um órgão de solicitação JSON.

A seguinte solicitação de amostra é criar um dispositivo. (Esta amostra tem uma entrada JSON com o órgão de solicitação.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato de dados independente de linguagem comum que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [json.org](http://json.org).

## <a name="metadata-specifications"></a>Especificações de metadados

O FarmBeats Datahub possui as seguintes APIs que permitem aos parceiros de dispositivos criar e gerenciar metadados de dispositivos ou sensores.

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é gateway ou nó.
- /**Dispositivo**: O dispositivo corresponde a um dispositivo físico presente na fazenda.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medida do sensor, como temperatura ambiente e pressão.
- /**Sensor**: O sensor corresponde a um sensor físico que registra valores. Um sensor é normalmente conectado a um dispositivo com um ID do dispositivo.

  **Modelo de dispositivo** |  |
  --- | ---
  Tipo (nó, gateway)  | Tipo do dispositivo - Nó ou Gateway |
  Fabricante  | Nome do fabricante |
  Productcode  | Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800. |
  Portas  | Nome e tipo da porta, que é digital ou analógico.  |
  Nome  | Nome para identificar recurso. Por exemplo, nome do modelo ou nome do produto. |
  Descrição  | Forneça uma descrição significativa do modelo. |
  Propriedades  | Propriedades adicionais do fabricante. |
  **Dispositivo** |  |
  DeviceModelId  |ID do modelo do dispositivo associado. |
  HardwareId   |ID exclusivo para o dispositivo, como um endereço MAC.  |
  ReportingInterval |Relatando intervalo em segundos. |
  Location    |Latitude do dispositivo (-90 a +90), longitude (-180 a 180) e elevação (em metros). |
  ParentDeviceId | ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, se um nó estiver conectado a um gateway, o nó tem o parentDeviceID como gateway. |
  Nome  | Nome para identificar o recurso. Os parceiros de dispositivo precisam enviar um nome consistente com o nome do dispositivo no lado do parceiro do dispositivo. Se o nome do dispositivo for definido pelo usuário no lado parceiro do dispositivo, o mesmo nome definido pelo usuário deve ser propagado para FarmBeats.  |
  Descrição  | Forneça uma descrição significativa.  |
  Propriedades  |Propriedades adicionais do fabricante.  |
  **Modelo de sensor** |  |
  Tipo (analógico, digital)  |Mencione sensor analógico ou digital.|
  Fabricante  | Nome do fabricante. |
  Productcode  | Código do produto ou nome do modelo ou número. Por exemplo, RS-CO2-N01.  |
  SensorMeasures > Nome  | Nome da medida do sensor. Apenas minúsculas são suportadas. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Este nome deve ser consistente com os dados de telemetria. |
  SensorMeasures > DataType  | Tipo de dados de telemetria. Atualmente, o dobro é suportado. |
  SensorMeasures > Type  | Tipo de medição dos dados de telemetria do sensor. A seguir estão os tipos definidos pelo sistema: Temperatura Ambiente, CO2, Profundidade, Condutividade Elétrica, FolhaWetness, Comprimento, Líquido, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, Umidade Relativa, Salinidade, Umidade do Solo, SolarIdade, Estado, Tempode Duração, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiração Para adicionar mais, consulte a API /ExtendedType.
  Unidade de > sensores medidas | Unidade de dados de telemetria de sensores. A seguir estão as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterD Para adicionar mais, consulte a API /ExtendedType.
  SensorMeasures > AgregaationType  | Ou nenhum, médio, máximo, mínimo ou Desvio Padrão.
  SensorMeasures > Profundidade  | A profundidade do sensor em centímetros. Por exemplo, a medição da umidade 10 cm sob o solo.
  SensorMeasures > Descrição  | Forneça uma descrição significativa da medição.
  Nome  | Nome para identificar recurso. Por exemplo, o nome do modelo ou o nome do produto.
  Descrição  | Forneça uma descrição significativa do modelo.
  Propriedades  | Propriedades adicionais do fabricante.
  **Sensor**  |  |
  HardwareId  | Identificação exclusiva para o conjunto de sensores pelo fabricante.
  SensorModelId  | ID do modelo de sensor associado.
  Location  | Latitude do sensor (-90 a +90), longitude (-180 a 180) e elevação (em metros).
  Nome do > de Porta  |Nome e tipo da porta à que o sensor está conectado no dispositivo. Este deve ser o mesmo nome definido no modelo do dispositivo.
  deviceId  | ID do dispositivo ao que o sensor está conectado.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do sensor ou o nome do produto e o número do modelo ou o código do produto.
  Descrição  | Forneça uma descrição significativa.
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > As APIs retornam IDs exclusivos para cada instância criada. Este ID precisa ser retido pelo Tradutor para gerenciamento de dispositivos e sincronização de metadados.


**Sincronização de metadados**

O Tradutor deve enviar atualizações sobre os metadados. Por exemplo, os cenários de atualização são a mudança do nome do dispositivo ou do sensor e a mudança do local do dispositivo ou do sensor.

O Tradutor deve ter a capacidade de adicionar novos dispositivos ou sensores que foram instalados pelo usuário post linking do FarmBeats. Da mesma forma, se um dispositivo ou sensor foi atualizado pelo usuário, o mesmo deve ser atualizado no FarmBeats para o dispositivo ou sensor correspondente. Cenários típicos que requerem a atualização de um dispositivo ou sensor são uma mudança na localização de um dispositivo ou a adição de sensores em um nó.


> [!NOTE]
> A exclusão não é suportada para metadados de dispositivos ou sensores.
>
> Para atualizar metadados, é obrigatório ligar para /Get/{id} no dispositivo ou sensor, atualizar as propriedades alteradas e, em seguida, fazer um /Put/{id} para que quaisquer propriedades definidas pelo usuário não sejam perdidas.

### <a name="add-new-types-and-units"></a>Adicionar novos tipos e unidades

FarmBeats suporta a adição de novos tipos de medidas de sensores e unidades. Para obter mais informações sobre a API /ExtendedType, consulte [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Envie dados de telemetria para farmbeats

Para enviar dados de telemetria para farmbeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre dados de telemetria, consulte [Enviar telemetria a um hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Aqui está uma amostra do código Python que envia a telemetria como cliente para um hub de eventos especificado.

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

O formato de mensagem canônica é o seguinte:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Todos os nomes-chave na telemetria JSON devem ser minúsculos. Exemplos são dados de dispositivos e sensores.

Por exemplo, aqui está uma mensagem de telemetria:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> As seções a seguir estão relacionadas a outras alterações (por exemplo. UI, gerenciamento de erros etc.) que o parceiro do sensor pode se referir no desenvolvimento do componente Tradutor.


## <a name="link-a-farmbeats-account"></a>Vincular uma conta FarmBeats

Depois que os clientes comprarem e implantarem dispositivos ou sensores, eles podem acessar os dados do dispositivo e a telemetria no software dos parceiros do dispositivo como um serviço (SaaS). Os parceiros de dispositivo podem permitir que os clientes vinculem sua conta à instância farmbeats no Azure, fornecendo uma maneira de inserir as seguintes credenciais:

   - Nome de exibição (um campo opcional para os usuários definirem um nome para essa integração)
   - Ponto de extremidade de API
   - ID do locatário
   - ID do Cliente
   - Segredo do cliente
   - String de conexão EventHub
   - Data de início

   > [!NOTE]
   > A data de início permite o feed de dados históricos, ou seja, os dados da data especificada pelo usuário.

## <a name="unlink-farmbeats"></a>Desvincular FarmBeats

Os parceiros de dispositivos podem permitir que os clientes desvinculem uma integração FarmBeats existente. Desvincular o FarmBeats não deve excluir nenhum dispositivo ou metadados de sensor que foi criado no FarmBeats Datahub. Desvincular faz o seguinte:

   - Interrompe o fluxo de telemetria.
   - Exclui e apaga as credenciais de integração no parceiro do dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar integração FarmBeats

Os parceiros de dispositivo podem permitir que os clientes editem as configurações de integração do FarmBeats se o segredo do cliente ou a seqüência de conexões mudar. Neste caso, apenas os seguintes campos são editáveis:

   - Nome de exibição (se aplicável)
   - O segredo do cliente (deve ser exibido no formato "2x8************* ou no recurso Show/Hide em vez de texto claro)
   - String de conexão (deve ser exibida no formato "2x8************ ou recurso Show/Hide em vez de texto claro)

## <a name="view-the-last-telemetry-sent"></a>Veja a última telemetria enviada

Os parceiros de dispositivo podem permitir que os clientes visualizem o carimbo de data e hora da última telemetria enviada, que é encontrada em **Telemetria Enviada**. Este é o momento em que a última telemetria foi enviada com sucesso para farmbeats.

## <a name="troubleshooting-and-error-management"></a>Solução de problemas e gerenciamento de erros

**Opção de solução de problemas ou suporte**

Se o cliente não puder receber dados do dispositivo ou telemetria na instância farmBeats especificada, o parceiro do dispositivo deverá fornecer suporte e um mecanismo para solução de problemas.

**Retenção de dados de telemetria**

Os dados de telemetria também devem ser retidos por um período de tempo predefinido para que possam ser úteis na depuração ou reenvio da telemetria se ocorrer um erro ou perda de dados.

**Gerenciamento de erros ou notificação de erros**

Se um erro afetar os metadados do dispositivo ou do sensor ou a integração de dados ou o fluxo de dados de telemetria no sistema parceiro do dispositivo, o cliente deve receber uma notificação. Um mecanismo para resolver quaisquer erros também deve ser projetado e implementado.

**Lista de verificação de conexão**

Os fabricantes ou parceiros dos dispositivos podem usar a seguinte lista de verificação para garantir que as credenciais fornecidas pelo cliente sejam precisas:

   - Verifique se um token de acesso é recebido com as credenciais que foram fornecidas.
   - Verifique se uma chamada de API é bem sucedida com o token de acesso recebido.
   - Verifique se a conexão com o cliente eventHub está estabelecida.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a API REST, consulte [API REST](rest-api-in-azure-farmbeats.md).
