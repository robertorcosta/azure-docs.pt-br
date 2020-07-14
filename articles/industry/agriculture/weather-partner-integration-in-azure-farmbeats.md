---
title: Integração de parceiros meteorológicos
description: Este artigo descreve como um provedor de dados meteorológicos pode se integrar ao FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: d4ceb25b9b81c831ed1b285a875742ebfaa6d24f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232637"
---
# <a name="weather-partner-integration"></a>Integração de parceiros meteorológicos

Este artigo fornece informações sobre o componente Docker do **conector** do Azure FarmBeats que os provedores de dados de clima podem desenvolver para integrar com o FarmBeats aproveitando suas APIs e enviar dados meteorológicos para o FarmBeats. Depois que os dados estiverem disponíveis no FarmBeats, eles poderão ser usados para a fusão de dados e para a criação de modelos de inteligência artificial/aprendizado de máquina.

 > [!NOTE]
 > Para a finalidade desta documentação, usaremos uma implementação de referência criada usando o NOAA dos conjuntos de documentos abertos do Azure e está disponível em [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > A imagem do Docker correspondente está disponível em[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Um parceiro meteorológico precisará fornecer uma imagem/programa do Docker (com especificações mencionadas abaixo) e hospedar a imagem do Docker em um registro de contêiner acessível pelos clientes. O parceiro meteorológico precisará fornecer as seguintes informações para seus clientes:

- URL da imagem do Docker
- Marca de imagem do Docker
- Chaves/credenciais para acessar a imagem do Docker
- Credenciais/chaves de API específicas do cliente para acessar os dados do sistema do parceiro meteorológico
- Detalhes de SKU de VM (os parceiros podem fornecer isso caso o Docker tenha requisitos específicos de VM; caso contrário, os clientes podem escolher entre SKUs de VM com suporte no Azure)

Usando as informações acima do Docker, o cliente registrará um parceiro meteorológico em sua instância FarmBeats. Para saber mais sobre como os clientes podem usar o Docker para ingerir dados meteorológicos no FarmBeats, consulte o guia para [obter dados meteorológicos](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)

## <a name="connector-docker-development"></a>Desenvolvimento do Docker do conector

**Integração com base em API REST**

As APIs FarmBeats contêm documentação técnica do Swagger. Para obter informações sobre todas as APIs e suas solicitações ou respostas correspondentes, consulte [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Se você tiver instalado o FarmBeats, poderá acessar seu Swagger do FarmBeats em[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger)

Observe que "-API" é anexado ao nome do site FarmBeats.
O ponto de extremidade da API será:[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net)

### <a name="datahub-lib"></a>Datahub lib

O FarmBeats fornecerá uma lib que pode ser usada pelo parceiro meteorológico. A lib está atualmente disponível como parte da implementação de referência [aqui](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). No futuro, o mesmo estará disponível como um SDK para vários idiomas.

### <a name="authentication"></a>Autenticação

**Autenticação com APIs FarmBeats**

O FarmBeats usa a autenticação de portador e as APIs podem ser acessadas fornecendo um token de acesso na seção de cabeçalho da solicitação, como abaixo:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

O token de acesso pode ser solicitado de uma função do Azure que está em execução na instância de FarmBeats do cliente. A URL da função do Azure será fornecida ao programa do Docker como um argumento e o token de acesso poderá ser obtido fazendo uma solicitação GET na URL. A resposta da URL conterá o token de acesso. A Datahub lib fornece funções auxiliares para permitir que os parceiros obtenham o token de acesso. Mais detalhes [aqui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

O token de acesso é válido somente por algumas horas e precisa ser solicitado novamente quando expirado.

**Autenticação com APIs do lado do parceiro**

Para permitir que os clientes se autentiquem com as APIs do lado do parceiro durante a execução do Docker, os clientes precisam fornecer as credenciais durante o registro do parceiro da seguinte maneira:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
O serviço de API serializa esse dicter e o armazena em um [keyvault](https://docs.microsoft.com/azure/key-vault/basic-concepts).

[Azure data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é usado para orquestrar trabalhos meteorológicos e girar recursos para executar o código do Docker. Ele também fornece um mecanismo para enviar dados por push com segurança para a VM na qual o trabalho do Docker é executado. As credenciais de API, que agora são armazenadas com segurança no keyvault são lidas como cadeias de caracteres seguras do keyvault e disponibilizadas como propriedades estendidas no diretório de trabalho do contêiner do Docker como activity.jsem (o caminho para o arquivo é "/mnt/working_dir/activity.js") o código do Docker pode ler as credenciais desse arquivo durante o tempo de execução para acessar as APIs do lado do parceiro em nome do cliente. As credenciais estarão disponíveis no arquivo da seguinte maneira:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Observe que o "partnerCredentials" estará disponível da maneira exata fornecida pelo cliente durante o registro do parceiro

A FarmBeats lib fornece funções auxiliares para permitir que os parceiros leiam as credenciais das propriedades da atividade. Mais detalhes [aqui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

O tempo de vida do arquivo é apenas durante a execução do código do Docker e será excluído após o término da execução do Docker.

Para obter mais detalhes sobre como funcionam as atividades e os pipelines do ADF, consulte [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**Cabeçalhos de solicitação HTTP**

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados quando você faz uma chamada à API para FarmBeats.

**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo | O formato da solicitação (Content-Type: application/<format>). Para APIs do Datahub do FarmBeats, o formato é JSON. Content-Type: application/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada à API. Autorização: Bearer <token-de-acesso>
Aceitar | O formato da resposta. Para APIs do Datahub do FarmBeats, o formato é JSON. Aceitar: application/json

## <a name="data-format"></a>Formato de dados

O JSON é um formato de dados comum independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para saber mais, confira [json.org](http://json.org).

## <a name="docker-specifications"></a>Especificações do Docker

O programa Docker precisa ter dois componentes: **Bootstrap** e **trabalhos**. Pode haver mais de um trabalho.

### <a name="bootstrap"></a>Bootstrap

Esse componente deve ser executado quando o cliente inicia o registro do Docker no FarmBeats. Os argumentos (arg1, arg2) que serão passados para este programa são:

- Ponto de extremidade de API do FarmBeats: ponto de extremidade de API do FarmBeats para solicitações de API: esse é o ponto de extremidade para fazer chamadas à API para a implantação FarmBeats.
- URL da função do Azure: Este é seu próprio ponto de extremidade pessoal que lhe dará seu token de acesso para APIs FarmBeats. Basta chamar GET nesta URL para que o token de acesso seja buscado em sua resposta.

A responsabilidade da inicialização é criar os metadados necessários para que os usuários possam executar seus trabalhos para obter dados meteorológicos. Consulte a implementação de referência [aqui](https://github.com/azurefarmbeats/noaa_docker). Você pode atualizar o bootstrap_manifest.jsno arquivo de acordo com suas necessidades e o programa de inicialização de referência criará os metadados necessários para você.

Os metadados a seguir são criados como parte desse processo. 

 > [!NOTE]
 > **Observe** que, se você atualizar o bootstrap_manifest.jsno arquivo conforme mencionado na [implementação de referência](https://github.com/azurefarmbeats/noaa_docker), não será necessário criar os metadados abaixo, pois a inicialização criará o mesmo com base no arquivo de manifesto.

- /**WeatherDataModel**: um WeatherDataModel é um modelo para representar dados meteorológicos e corresponde a conjuntos de dados diferentes fornecidos pela origem. Por exemplo, um DailyForecastSimpleModel pode fornecer a temperatura média, umidade e informações de precipitação uma vez por dia, enquanto uma DailyForecastAdvancedModel pode fornecer muito mais informações em uma granularidade de hora. Você pode criar qualquer número de WeatherDataModels.
- /**JobType**: o FarmBeats tem um sistema de gerenciamento de trabalhos extensível. Como um provedor de dados meteorológicos, você terá diferentes conjuntos/APIs (por exemplo, GetDailyForecasts) – você pode habilitá-los em FarmBeats como JobType. Depois que um JobType é criado, um cliente pode disparar trabalhos desse tipo para obter dados meteorológicos para seu local/farm de interesse (consulte JobType e APIs de trabalho no [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Trabalhos

Esse componente será invocado toda vez que um usuário FarmBeats executar um trabalho de seu/JobType que você criou como parte do processo de inicialização. O comando Docker execute para o trabalho é definido como parte do **/JobType** que você criou.
- A responsabilidade do trabalho será buscar dados da origem e enviá-los por push para FarmBeats. Os parâmetros necessários para obter os dados devem ser definidos como parte do/JobType no processo de inicialização.
- Como parte do trabalho, o programa precisará criar um **/WeatherDataLocation** com base no/WeatherDataModel que foi criado como parte do processo de inicialização. O **/WeatherDataLocation** corresponde a um local (lat/long) que é fornecido pelo usuário como um parâmetro para o trabalho.

### <a name="details-of-the-objects"></a>Detalhes dos objetos

  **WeatherDataModel** |  |
  --- | ---
  Nome  | Nome do modelo de dados meteorológicos |
  Descrição  | Fornece uma descrição significativa da configuração. |
  Propriedades  | Propriedades adicionais definidas pelo provedor de dados. |
  Nome > weatherMeasures  | Nome da medida do clima. Por exemplo humidity_max |
  weatherMeasures > DataType  | Double ou enum. Se enum, measureEnumDefinition é necessário |
  weatherMeasures > measureEnumDefinition  | Necessário somente se DataType for enum. Por exemplo {"NoRAiN": 0, "neve": 1, "Drizzle": 2, "Rain": 3} |
  Tipo de > weatherMeasures  | tipo de dados de telemetria do clima. Por exemplo, "RelativeHumidity". A seguir estão os tipos definidos pelo sistema: AmbientTemperature, nounit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte a API/ExtendedType ou na [seção adicionar tipos e unidades](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) abaixo
  Unidade de > weatherMeasures | Unidade de dados de telemetria do clima. A seguir, estão as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, consulte a API/ExtendedType ou na [seção adicionar tipos e unidades](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) abaixo.
  Agregatype de > weatherMeasures  | Qualquer de nenhum, média, máximo, mínimo, i, soma, total
  Profundidade de > de weatherMeasures  | A profundidade do sensor em centímetros. Por exemplo, a medida da umidade de 10 cm sob o solo.
  Descrição de > weatherMeasures  | Fornece uma descrição significativa da medida. |
  **JobType** |  |
  Nome  | nome do trabalho-por exemplo Get_Daily_Forecast; o trabalho que o cliente executará para obter dados meteorológicos|
  pipelineDetails > parâmetros > nome  | nome do parâmetro |
  pipelineDetails > > tipo de parâmetros | qualquer cadeia de caracteres, int, float, bool, array |
  parâmetros de > pipelineDetails > IsRequired | Boolean true se o parâmetro for necessário, false se não; o padrão é true |
  parâmetros > de pipelineDetails > defaultValue | Valor padrão do parâmetro |
  pipelineDetails > parâmetros > descrição | Descrição do parâmetro |
  Propriedades  | Propriedades adicionais do fabricante.
  Propriedades > **programRunCommand** | comando Docker execute – esse comando será executado quando o cliente executar o trabalho meteorológico. |
  **WeatherDataLocation** |  |
  weatherDataModelId  | ID do WeatherDataModel correspondente que foi criado durante a inicialização|
  local  | representa latitude, longitude e elevação |
  Nome | Nome do objeto |
  Descrição | Descrição |
  farmid | **opcional** ID do farm – fornecido pelo cliente como parte do parâmetro de trabalho |
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, confira [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > As APIs retornam IDs exclusivas para cada instância criada. Essa ID precisa ser retida pelo Tradutor para o gerenciamento de dispositivos e a sincronização de metadados.

**Sincronização de metadados**

O Docker do conector deve ter a capacidade de enviar atualizações nos metadados. Exemplos de cenários de atualização são: adição de novos parâmetros meteorológicos no conjunto de recursos do provedor meteorológico, adição de funcionalidade (por exemplo, Adição de previsão de 30 dias)

> [!NOTE]
> A exclusão não tem suporte para os metadados, por exemplo. modelo de dados meteorológicos.
>
> Para atualizar os metadados, é obrigatório chamar/Get/{ID} no modelo de dados meteorológico, atualizar as propriedades alteradas e, em seguida, fazer uma/Put/{ID} para que todas as propriedades definidas pelo usuário não sejam perdidas.

## <a name="weather-data-telemetry-specifications"></a>Especificações de dados meteorológicos (telemetria)

Os dados meteorológicos são mapeados para uma mensagem canônica enviada por push a um hub de eventos do Azure para processamento. O Azure EventHub é um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados. Para enviar dados meteorológicos para o FarmBeats, você precisará criar um cliente que envia mensagens para um hub de eventos no FarmBeats. Para saber mais sobre como enviar telemetria, consulte [enviando telemetria para um hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

Aqui está um exemplo de código Python que envia telemetria como cliente para um hub de eventos especificado.

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

O formato da mensagem canônica é o seguinte:

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Por exemplo, aqui está uma mensagem de telemetria:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Solução de problemas e gerenciamento de erros

**Log de erros**

Como o trabalho do parceiro será executado na estrutura de trabalho existente – os erros são registrados da mesma maneira que erros para outros trabalhos pré-existentes em FarmBeats (como GetFarmData, SensorPlacement, etc.). A atividade do ADF em execução no pipeline do ADF registra o STDERR e STDOUT. Ambos os arquivos estão disponíveis na conta de armazenamento "datahublogs-xxx" dentro do grupo de recursos FarmBeats.

A Datahub lib fornece funções auxiliares para habilitar o registro em log como parte dos logs de Datahub geral. Mais detalhes [aqui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Solução de problemas ou suporte**

Caso o cliente não possa receber dados meteorológicos na instância FarmBeats especificada, o parceiro meteorológico deve fornecer suporte e um mecanismo para solucionar os mesmos problemas.

## <a name="add-extendedtype"></a>Adicionar Extended

O FarmBeats oferece suporte à adição de novos tipos e unidades de medida de sensor. Observe que um parceiro meteorológico pode adicionar novas unidades/tipos atualizando o bootstrap_manifest.jsno arquivo na implementação de referência [aqui](https://github.com/azurefarmbeats/noaa_docker)

Para adicionar um novo tipo de WeatherMeasure, por exemplo, "PrecipitationDepth", siga as etapas abaixo.

1. Faça uma solicitação GET em/ExtendedType com a consulta Filter-Key = WeatherMeasureType
2. Observe a ID do objeto retornado.
3. Adicione o novo tipo à lista no objeto retornado e faça uma solicitação PUT no/ExtendedType{ID} com a nova lista a seguir. A carga de entrada deve ser a mesma que a resposta recebida acima e a nova unidade anexada ao final da lista de valores.

Para saber mais sobre a API do /ExtendedType, confira [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Próximas etapas

Agora você tem um conector do Connector que se integra ao FarmBeats. Em seguida, você pode ver como obter dados meteorológicos usando o Docker no FarmBeats. Consulte [obter dados meteorológicos](get-weather-data-from-weather-partner.md).
