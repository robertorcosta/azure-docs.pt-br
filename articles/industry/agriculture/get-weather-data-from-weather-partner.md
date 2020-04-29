---
title: Obter dados meteorológicos de parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos de parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266156"
---
# <a name="get-weather-data-from-weather-partners"></a>Obter dados meteorológicos de parceiros meteorológicos

O Azure FarmBeats ajuda a trazer dados meteorológicos de seus provedores de dados meteorológicos usando uma estrutura de conector baseada em Docker. Usando essa estrutura, os provedores de dados meteorológicos implementam um Docker que pode ser integrado ao FarmBeats. Atualmente, há suporte para os seguintes provedores de dados meteorológicos:

[Dados do NOAA de conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/)

Os dados meteorológicos podem ser usados para gerar informações acionáveis e criar modelos de ia/ML em FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, verifique se você instalou o FarmBeats. **Há suporte para a integração do clima na versão 1.2.11 ou superior**. Para instalar o Azure FarmBeats, consulte [instalar o FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Habilitar a integração do clima com o FarmBeats

Para começar a obter dados meteorológicos em seu hub de dados do FarmBeats, siga as etapas abaixo:

1. Vá para o Swagger do hub de dados do FarmBeats (https://yourdatahub.azurewebsites.net/swagger)

2. Navegue até a API/Partner e faça uma solicitação POST com a seguinte carga de entrada:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Por exemplo, para obter dados meteorológicos do NOAA por conjuntos de dados abertos do Azure, use a carga abaixo. Você pode modificar o nome e a descrição de acordo com sua preferência.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Para obter mais informações sobre o objeto de parceiro, consulte o [Apêndice](get-weather-data-from-weather-partner.md#appendix)

   A etapa anterior provisionará os recursos para habilitar o Docker a ser executado no ambiente FarmBeats do cliente.  

   Leva cerca de 10-15 minutos para provisionar os recursos acima.

3. Verifique o status do objeto/Partner que você criou na etapa 2. Para fazer isso, faça uma solicitação GET na API/Partner e verifique o **status** do objeto de parceiro. Depois que o FarmBeats provisiona o parceiro com êxito, o status é definido como **ativo**.

4. Navegue até a API do/JobType e faça uma solicitação GET no mesmo. Verifique os trabalhos meteorológicos que são criados como parte do processo de adição de parceiro na etapa 2. O campo **pipelinename** nos trabalhos meteorológicos será do seguinte formato: "partner-name_partner-type_job-Name".

5. Agora, sua instância do FarmBeats tem um parceiro de dados meteorológico ativo e você pode executar trabalhos para solicitar dados meteorológicos para um local específico (Latitude/Longitude) e um intervalo de datas. As JobType (s) terão detalhes sobre quais parâmetros são necessários para executar trabalhos meteorológicos.

   Por exemplo, para dados de NOAA de conjuntos abertos do Azure, seguintes JobType (s) serão criados:

   - get_weather_data (obter dados meteorológicos ISD/históricos)
   - get_weather_forecast_data (obter dados de clima do GFS/previsão)

6. Anote a **ID** e os parâmetros do (s) JobType (es).

7. Navegue até a API/Jobs e faça uma solicitação POST em/Jobs com a seguinte carga de entrada:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Por exemplo, para executar **get_weather_data**, use a seguinte carga:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. A etapa anterior executará os trabalhos meteorológicos conforme definido no Docker de parceiro e ingerirá os dados meteorológicos no FarmBeats. Você pode verificar o status do trabalho fazendo uma solicitação GET em/Jobs e procurar **CurrentState** na resposta. Depois de concluído, o CurrentState é definido como **Succeeded**.

## <a name="query-ingested-weather-data"></a>Consultar dados meteorológicos ingeridos

Depois que os trabalhos meteorológicos forem concluídos, você poderá consultar dados de clima ingeridos para criar modelos ou informações acionáveis. Há duas maneiras de acessar e consultar dados meteorológicos do FarmBeats:

- API e
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Consultar usando a API REST

Para consultar dados meteorológicos usando a API REST do amFarmBeats, siga as etapas abaixo:

1. No Swagger do hub de dados dohttps://yourdatahub.azurewebsites.net/swagger)FarmBeats (, navegue até a API do/WeatherDataLocation e faça uma solicitação get. A resposta terá objetos/WeatherDataLocation criados para o local (Latitude/Longitude) que foi especificado como parte da execução do trabalho. Anote a **ID** e a **weatherDataModelId** do (s) objetos.

2. Crie uma GET/{id} na API/WeatherDataModel para o **weatherDataModelId** , conforme observado na etapa 1. O "modelo de dados meteorológicos" tem todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, a **medida de clima** dentro do objeto de modelo de **dados meteorológicos** tem detalhes sobre quais informações de clima são suportadas e em quais tipos e unidades. Por exemplo,

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Anote a resposta da chamada GET/{id} para o modelo de dados meteorológico.

3. Navegue até a API de **telemetria** e faça uma solicitação post com a seguinte carga de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A resposta que contém os dados meteorológicos que estão disponíveis para o intervalo de tempo especificado terá a seguinte aparência:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

No exemplo anterior, a resposta tem dados para dois carimbos de data/hora junto com o nome da medida ("temperatura") e os valores dos dados meteorológicos relatados nos dois carimbos de data/hora. Você precisará consultar o modelo de dados meteorológico associado (conforme descrito na etapa 2 acima) para interpretar o tipo e a unidade dos valores relatados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta usando Azure Time Series Insights (TSI)

O FarmBeats usa [Azure Time Series insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e Visualizar dados em escala de IOT--dados altamente contextuais e otimizados para a série temporal.

Os dados meteorológicos são recebidos em um EventHub e enviados para um ambiente de TSI dentro do grupo de recursos FarmBeats. Em seguida, os dados podem ser consultados diretamente do TSI. Para obter mais informações, consulte a [documentação do TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Siga as etapas para visualizar os dados em TSI:

1. Vá para **portal do Azure** > **grupo de recursos FarmBeats DataHub** > selecione ambiente de **Time Series insights** (TSI-XXXX) > **políticas de acesso de dados**. Adicionar usuário com acesso de leitor ou colaborador.

2. Vá para a página **visão geral** do ambiente de **Time Series insights** (TSI-XXXX) e selecione a **URL do time Series insights Explorer**. Agora você pode visualizar os dados ingeridos do clima.

Além do armazenamento, da consulta e da visualização dos dados meteorológicos, o TSI também permite a integração a um painel de Power BI. Para obter mais informações, consulte [Visualizar dados de time Series insights em Power bi](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Apêndice

|        Parceiro   |  Detalhes   |
| ------- | -------             |
|     DockerDetails-imageName         |          Nome da imagem do Docker. Por exemplo, docker.io/azurefarmbeats/farmbeats-noaa (imagem em hub.docker.com) ou myazureacr.azurecr.io/mydockerimage (imagem no registro de contêiner do Azure) e assim por diante. Se nenhum registro for fornecido, o padrão será hub.docker.com      |
|          DockerDetails - imageTag             |         Nome da marca da imagem do Docker. O padrão é "mais recente"     |
|  DockerDetails-credenciais      |  Credenciais para acessar o Docker privado. Isso será fornecido pelo parceiro ao cliente   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    SKU de VM do lote do Azure. Consulte [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para todas as máquinas virtuais do Linux disponíveis. Os valores válidos são: "Small", "ExtraLarge", "Large", "A8", "A9", "Medium", "a5", "A6", "a7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' a11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ', ' STANDARD_G2 ', ' STANDARD_G3 ', ' STANDARD_G4 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ', ' STANDARD_NC24 ', ' STANDARD_NC24r ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ', ' STANDARD_D2_V3 '. **O padrão é "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Não. de nós de computador dedicados para o pool do lote. O valor padrão é 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID de SKU do agente de nó do lote do Azure. Atualmente, só há suporte para o agente de nó do lote "Batch. Node. Ubuntu 18, 4".    |
| DockerDetails - partnerCredentials | credenciais para chamar a API de parceiro no Docker. O parceiro precisa fornecer essas informações aos clientes com base no mecanismo de autenticação com suporte por exemplo. Nome de usuário/senha ou chaves de API. |
| parceirotype | "Clima" (outros tipos de parceiros em FarmBeats são "sensor" e "imagens")  |
|  name   |   Nome desejado do parceiro no sistema FarmBeats   |
|  description |  Descrição   |

## <a name="next-steps"></a>Próximas etapas

Agora você consultou os dados do sensor de sua instância do FarmBeats do Azure. Agora, saiba como [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para seus farms.
