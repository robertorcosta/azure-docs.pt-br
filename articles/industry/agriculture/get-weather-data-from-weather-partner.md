---
title: Obtenha dados meteorológicos de parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos de parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266156"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenha dados meteorológicos de parceiros meteorológicos

O Azure FarmBeats ajuda você a trazer dados meteorológicos de seus provedores de dados meteorológicos usando um Connector Framework baseado em docker. Usando essa estrutura, os provedores de dados meteorológicos implementam um docker que pode ser integrado ao FarmBeats. Atualmente, os seguintes provedores de dados meteorológicos são suportados:

[Dados noaa dos conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/)

Os dados meteorológicos podem ser usados para gerar insights acionáveis e construir modelos de IA/ML no FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, certifique-se de que você instalou o FarmBeats. **A integração meteorológica é suportada na versão 1.2.11 ou superior**. Para instalar o Azure FarmBeats, consulte [Instalar FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Habilite a integração meteorológica com farmbeats

Para começar a obter dados meteorológicos no seu hub FarmBeats Data, siga os passos abaixo:

1. Vá para o seu farmbeats data hub swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Navegue até a API /Partner e faça uma solicitação POST com a seguinte carga de entrada:

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

   Por exemplo, para obter dados meteorológicos da NOAA pelo Azure Open Datasets, use a carga abaixo. Você pode modificar o nome e a descrição conforme sua preferência.

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
   > Para obter mais informações sobre o objeto Parceiro, consulte [apêndice](get-weather-data-from-weather-partner.md#appendix)

   A etapa anterior provisionará os recursos para permitir que o docker seja executado no ambiente FarmBeats do cliente.  

   Leva cerca de 10-15 minutos para prover os recursos acima.

3. Verifique o status do objeto /Partner que você criou na etapa 2. Para fazer isso, faça uma solicitação GET na API /Partner e verifique o **status** do objeto parceiro. Uma vez que o FarmBeats provisão o parceiro com sucesso, o status é definido como **Ativo**.

4. Navegue até a API /JobType e faça uma solicitação GET no mesmo. Verifique os trabalhos meteorológicos que são criados como parte do processo de adição do Parceiro na etapa 2. O campo **pipelineName** nos trabalhos meteorológicos será do seguinte formato: "parceiro-name_partner-type_job-nome".

5. Agora, sua instância FarmBeats tem um parceiro ativo de dados meteorológicos e você pode executar trabalhos para solicitar dados meteorológicos para um local específico (latitude/longitude) e um intervalo de datas. O JobType(s) terá detalhes sobre quais parâmetros são necessários para executar trabalhos meteorológicos.

   Por exemplo, para dados NOAA do Azure Open Datasets, os seguintes JobType(s) serão criados:

   - get_weather_data (Obter dados meteorológicos históricos/ISD)
   - get_weather_forecast_data (Obter GFS/dados meteorológicos de previsão)

6. Anote o **ID** e os parâmetros do JobType(s).

7. Navegue até a API /Jobs e faça uma solicitação post on /Jobs com a seguinte carga de entrada:

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

   Por exemplo, para executar **get_weather_data,** use a seguinte carga útil:

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

8. A etapa anterior executará os trabalhos meteorológicos conforme definido no docker parceiro e ingerirá dados meteorológicos em FarmBeats. Você pode verificar o status do trabalho fazendo uma solicitação GET on /Jobs e procurar o **Estado atual** na resposta. Uma vez concluído, o Estado atual é definido **como Bem Sucedido**.

## <a name="query-ingested-weather-data"></a>Consulta de dados meteorológicos ingeridos

Depois que os trabalhos meteorológicos estiverem concluídos, você pode consultar dados meteorológicos ingeridos para construir modelos ou insights acionáveis. Existem duas maneiras de acessar e consultar dados meteorológicos do FarmBeats:

- API e
- Visão da série temporal (TSI).

### <a name="query-using-rest-api"></a>Consulta usando API REST

Para consultar dados meteorológicos usando a API FarmBeats REST, siga as etapas abaixo:

1. Em seu hub farmbeatshttps://yourdatahub.azurewebsites.net/swagger)data (, navegue até a API /WeatherDataLocation e faça uma solicitação GET. A resposta terá o objeto /WeatherDataLocation criado para o local (latitude/longitude) que foi especificado como parte do trabalho executado. Anote o **ID** e o **weatherDataModelId** do objeto(s).

2. Faça uma API GET/{id} em /WeatherDataModel para o **weatherDataModelId** conforme observado na etapa 1. O "Modelo de Dados Meteorológicos" tem todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, **a Medida Meteorológica** dentro do objeto Weather Data **Model** tem detalhes sobre quais informações meteorológicas são suportadas e em quais tipos e unidades. Por exemplo,

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

   Anote a resposta da chamada GET/{id} para o Modelo de Dados Meteorológicos.

3. Navegue até a API **de telemetria** e faça uma solicitação POST com a seguinte carga de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A resposta que contém dados meteorológicos disponíveis para o intervalo de tempo especificado será assim:

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

No exemplo anterior, a resposta tem dados de dois carimbos de tempo, juntamente com o nome da medida ("Temperatura") e valores dos dados meteorológicos relatados nos dois carimbos de tempo. Você precisará consultar o Modelo de Dados Meteorológicos associados (conforme descrito na etapa 2 acima) para interpretar o tipo e a unidade dos valores relatados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta usando O Zure Time Series Insights (TSI)

O FarmBeats usa [o Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e visualizar dados em escala IoT — dados altamente contextualizados e otimizados para séries tempo.

Os dados meteorológicos são recebidos em um EventHub e, em seguida, empurrados para um ambiente TSI dentro do grupo de recursos FarmBeats. Os dados podem então ser consultados diretamente a partir do TSI. Para obter mais informações, consulte [a documentação tsi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Siga as etapas para visualizar dados no TSI:

1. Vá para o grupo de > **recursos FarmBeats DataHub** **do Portal Azure**> ambiente selecionado de **Insights de séries** de tempo (tsi-xxxx) > **Políticas de Acesso a Dados**. Adicione o usuário com acesso ao Leitor ou Contribuinte.

2. Vá para a página **Visão Geral** do ambiente Time **Series Insights** (tsi-xxxx) e selecione a **URL do Explorador de Insights da série do tempo**. Agora você pode visualizar os dados meteorológicos ingeridos.

Além de armazenar, consultar e visualizar dados meteorológicos, o TSI também permite a integração a um painel power bi. Para obter mais informações, consulte [Visualize dados do Time Series Insights in Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Apêndice

|        Partner (parceiro)   |  Detalhes   |
| ------- | -------             |
|     DockerDetails - imageName         |          Nome da imagem do Docker. Por exemplo, docker.io/azurefarmbeats/farmbeats-noaa (imagem em hub.docker.com) OU myazureacr.azurecr.io/mydockerimage (imagem no Registro de Contêineres do Azure) e assim por diante. Se nenhum registro for fornecido, a inadimplência será hub.docker.com      |
|          DockerDetails - imageTag             |         Nome da imagem do docker. Padrão é "mais recente"     |
|  DockerDetails - credenciais      |  Credenciais para acessar o docker privado. Isso será fornecido pelo parceiro ao cliente   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Veja [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) todas as máquinas virtuais Linux disponíveis. Os valores válidos são: "Pequeno", "ExtraGrande", "Grande", "A8", "A9", "Médio", 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3 STANDARD_D1', 'STANDARD_D1', 'STANDARD_D1', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_D1', 'STANDARD_G4 STANDARD_D1'. , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1'STANDARD_NC24, 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24r STANDARD_A9', 'STANDARD_A9', 'STANDARD_NC12', 'STANDARD_A9', 'STANDARD_A9', 'STANDARD_NC12', 'STANDARD_A9', 'STANDARD_A9', 'STANDARD_NC12', 'STANDARD_A9', 'STANDARD_A9', 'STANDARD_NC12', , 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **Padrão é "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Não. de nós de computador dedicados para pool de lotes. O valor padrão é 1. |
|    DockerDetails - azureBatchVMDetails - nóAgentSKUID          |    Azure Batch Node Agent SKU ID. Atualmente, apenas o agente de nó de lote "batch.node.ubuntu 18.04" é suportado.    |
| DockerDetails - partnerCredentials | credenciais para chamar a API parceira em docker. O parceiro precisa dar essas informações aos seus clientes com base no mecanismo auth que é suportado, por exemplo. Nome de usuário/senha ou Chaves de API. |
| partnerType | "Weather" (Outros tipos de parceiros em FarmBeats são "Sensor" e "Imagery")  |
|  name   |   Nome desejado do parceiro no sistema FarmBeats   |
|  descrição |  Descrição   |

## <a name="next-steps"></a>Próximas etapas

Agora você tem dados de sensores consultados da sua instância Azure FarmBeats. Agora, aprenda a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para suas fazendas.
