---
title: Integração com parceiros de imagens
description: Este artigo descreve a integração de parceiros de imagens.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131864"
---
# <a name="imagery-partner-integration"></a>Integração com parceiros de imagens

Este artigo descreve como usar o componente Azure FarmBeats Translator para enviar dados de imagens para o FarmBeats. Os dados de imagens agrícolas podem ser gerados a partir de várias fontes, como câmeras multiespectrais, satélites e drones. Os parceiros de imagens agrícolas podem se integrar à FarmBeats para fornecer aos clientes mapas personalizados para suas fazendas.

Os dados, uma vez disponíveis, podem ser visualizados através do FarmBeats Accelerator e potencialmente ser usados para fusão de dados e aprendizado de máquina/inteligência artificial (ML/AI) por empresas agrícolas ou integradores de sistemas de clientes.

FarmBeats fornece a capacidade de:

- Defina tipos de imagem personalizados, formato de origem e arquivo usando APIs /ExtendedType.
- Ingerir dados de imagens de várias fontes através das APIs /Scene e /SceneFile.

As informações a seguir se concentram em obter qualquer forma de imagem no sistema FarmBeats.

Quando você seleciona a seção **Imagens de Drone,** um pop-up é aberto para mostrar uma imagem de alta resolução do ortomosaico do drone. Você pode acessar o software parceiro, que ajuda a planejar voos de drones e obter dados brutos. Você continuará a usar o software do parceiro para planejamento de caminhos e costura de imagem ortomosaico.

Os parceiros de drones precisam permitir que os clientes vinculem sua conta de cliente com sua instância FarmBeats no Azure.

Você deve usar as seguintes credenciais no software de parceiro de drone para vincular farmbeats:

- Ponto de extremidade de API
- ID do locatário
- ID do Cliente
- Segredo do cliente

## <a name="api-development"></a>Desenvolvimento de API

As APIs contêm documentação técnica swagger. Para obter informações sobre as APIs e solicitações ou respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Autenticação

FarmBeats usa o Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). O Azure App Service oferece suporte integrado de autenticação e autorização. 

Para obter mais informações sobre o Azure AD, consulte [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

O FarmBeats Datahub usa a autenticação do portador, que precisa das seguintes credenciais:

- ID do Cliente
- Segredo do cliente
- ID do locatário

Usando as credenciais anteriores, o chamador pode solicitar um token de acesso, que precisa ser enviado nas solicitações de API subseqüentes, na seção cabeçalho, da seguinte forma:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A seguinte amostra de código Python recupera o token de acesso. Em seguida, você pode usar o token para chamadas de API subseqüentes para FarmBeats.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>Cabeçalhos de solicitação HTTP

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados quando você faz uma chamada de API para farmBeats Datahub.

**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo  | O formato da solicitação (Tipo<format>de conteúdo: aplicação/ ). Para farmbeats APIs Datahub, o formato é JSON. Content-Type: application/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada de API. Autorização:> de acesso <do Portador
Aceitar  | O formato da resposta. Para farmbeats APIs Datahub, o formato é JSON. Aceitar: aplicação/json


## <a name="api-requests"></a>Pedidos de API

Para fazer uma solicitação de API REST, você combina:

- O método HTTP (GET, POST e PUT).
- A URL para o serviço de API.
- O recurso URI (para consultar, enviar dados, atualizar ou excluir).
- Um ou mais cabeçalhos de solicitação HTTP.

Opcionalmente, você pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

A seguinte solicitação de amostra é obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT requerem um órgão de solicitação JSON.

A seguinte solicitação de amostra é criar um dispositivo. Esta amostra tem uma entrada JSON com o corpo de solicitação.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato de dados independente de linguagem comum que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Ingerir imagens em FarmBeats

Depois que o parceiro tem credenciais para se conectar ao FarmBeats Datahub, o parceiro dá as seguintes etapas no componente Tradutor.

1.  Crie um novo tipo estendido para os seguintes campos, de acordo com o tipo de imagens a serem carregadas:

    - **Fonte da cena**: Por exemplo, drone_partner_name
    - **Tipo de cena**: Por exemplo, drone
    - **Tipo de arquivo de cena**: Por exemplo, índice de clorofila
    - **Tipo de conteúdo de arquivo de cena**: Por exemplo, imagem/tiff

2.  Ligue para a API /Farms para obter a lista de fazendas de dentro do sistema Azure FarmBeats.
3.  Forneça ao cliente a capacidade de escolher uma única fazenda na lista de fazendas.

    O sistema parceiro deve mostrar a fazenda dentro do software parceiro para fazer o planejamento do caminho e a coleta de drones e imagens.

4.  Ligue para a API /Scene e forneça detalhes necessários para criar uma nova cena com uma identificação de cena única.
5.  Receba uma URL SAS blob para carregar as imagens necessárias no FarmBeats Datahub, no contexto da fazenda escolhida, no sistema FarmBeats.

Aqui está um fluxo detalhado nas chamadas da API.

### <a name="step-1-extendedtype"></a>Passo 1: ExtendedType

Verifique na API /ExtendedType para ver se o tipo e a fonte de arquivo estão disponíveis no FarmBeats. Para isso, ligue para um GET na API /ExtendedType.

Aqui estão os valores definidos pelo sistema:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Este passo é uma configuração única. O escopo deste novo tipo de cena está limitado à assinatura em que o Azure FarmBeats está instalado.

Por exemplo, para adicionar SceneSource: "SlantRange", você faz um PUT no ID da API /ExtendedType com a carga útil de entrada "SceneSource" chave.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

O campo verde é a nova adição aos valores de origem de cena definidos pelo sistema.

### <a name="step-2-get-farm-details"></a>Passo 2: Obtenha detalhes da fazenda

As cenas (.tiff ou arquivos .csv) estão no contexto de uma fazenda. Você precisa obter os detalhes da fazenda fazendo um GET na API /Farm. A API retorna a lista de fazendas que estão disponíveis no FarmBeats. Você pode selecionar a fazenda para a a que deseja ingerir os dados.

RESPOSTA GET /Farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Passo 3: Crie uma id de cena (chamada POST)

Crie uma nova cena (.tiff ou arquivo .csv) com as informações dadas, que fornece a data, seqüência e id da fazenda com a qual a cena está associada. Os metadados associados à cena podem ser definidos em propriedades, o que inclui a duração e o tipo de medida.

Criar uma nova cena cria uma nova cena, que está associada à fazenda. Depois que o ID de cena é criado, o usuário pode usar o mesmo para criar um novo arquivo (.tiff ou .csv) e armazenar o conteúdo do arquivo.

Exemplo de carga útil para a chamada POST na API /Scene:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Resposta da API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Criar um arquivo de cena**

A cena que id retornou na etapa 3 é a entrada para o arquivo da cena. O arquivo de cena retorna um token de URL SAS, que é válido por 24 horas.

Se o usuário precisar de uma maneira programática de carregar um fluxo de imagens, o SDK de armazenamento blob pode ser usado para definir um método usando o ID, localização e URL do arquivo de cena.

Exemplo de carga de entrada para a chamada POST na API /SceneFile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Resposta da API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

A chamada POST para a API /SceneFile retorna uma URL de upload sas, que pode ser usada para carregar o arquivo .csv ou .tiff usando o cliente de armazenamento Ou biblioteca Do Azure Blob.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre detalhes de integração baseados em Rest API, consulte [API REST](rest-api-in-azure-farmbeats.md).
