---
title: Usar o Time Series Insights para armazenar e analisar a telemetria do dispositivo IoT Plug and Play do Azure | Microsoft Docs
description: Configure um ambiente do Time Series Insights e conecte o Hub IoT para exibir e analisar a telemetria de seus dispositivos do IoT Plug and Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422239"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Tutorial: criar o Time Series Insights Gen2 e conectar-se a ele para armazenar, visualizar e analisar a telemetria de dispositivos do IoT Plug and Play

Neste tutorial, você aprenderá a criar e configurar de modo correto um ambiente do [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) para se integrar à solução IoT Plug and Play. Com o TSI, será possível coletar, processar, armazenar, consultar e visualizar dados de série temporal em escala IoT (Internet das Coisas).

Primeiro, você provisionará um ambiente do TSI e conectará o Hub IoT como a origem do evento de streaming. Depois você trabalhará com a sincronização de modelos para criar o Modelo de Série Temporal do ambiente do TSI com base nos arquivos de modelos de exemplo do [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl) usados para o controlador de temperatura e os dispositivos termostato.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Evitar a necessidade de instalar a CLI do Azure localmente. É possível usar o Azure Cloud Shell para configurar serviços de nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Seleção de IDs da Série Temporal

Durante o provisionamento de seu ambiente do TSI, você deverá selecionar uma ID da Série Temporal. Selecionar a ID da Série Temporal apropriada é fundamental, pois a propriedade é imutável e não poderá ser alterada após a configuração. Escolher uma ID do Time Series é como escolher uma chave de partição para um banco de dados. Em geral, a ID do TS deve ser o nó folha do modelo de ativo. Isso significa que você normalmente desejará selecionar a propriedade da ID do ativo ou sensor mais granular que estiver emitindo a telemetria.

Como um usuário do IoT Plug and Play, ao selecionar sua ID do TS, é pertinente saber sobre a presença de [componentes](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) em seus modelos do dispositivo. 

![Seleção de IDs do TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Caso esteja executando o Início Rápido e o dispositivo do Hub IoT represente o [Termostato](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), use `iot-hub-connection-device-id` como a ID do TS.

* Caso esteja seguindo um dos tutoriais para executar o [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) de vários componentes, use a chave composta da seção abaixo, gravada como `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Provisionar o ambiente do Azure Time Series Insights Gen2

O comando abaixo faz o seguinte:

* Cria uma conta de armazenamento do Azure para o [armazenamento frio](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) do ambiente, projetada para a retenção de longo prazo e a análise de dados históricos.
  * Substitua `mytsicoldstore` por um nome exclusivo para sua conta.
* Cria um ambiente do Azure Time Series Insights Gen2, incluindo o armazenamento warm com um período de retenção de 7 dias e um armazenamento frio para a retenção infinita. 
  * Substitua `my-tsi-env` por um nome exclusivo para seu ambiente do TSI 
  * Substitua `my-pnp-resourcegroup` pelo nome do grupo de recursos usado durante a configuração
  * Substitua `my-ts-id-property` pelo valor da propriedade da ID do TS com base nos critérios de seleção acima

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Agora, você vai configurar o Hub IoT criado anteriormente como a [origem do evento](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) de seu ambiente. Quando a origem do evento estiver conectada, o TSI indexará eventos do hub, começando com o primeiro evento da fila.

Primeiro, crie um grupo de consumidores exclusivo no Hub IoT para seu ambiente do TSI. Substitua `my-pnp-hub` pelo nome do Hub IoT usado anteriormente.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Conectar o Hub IoT. Substitua `my-pnp-resourcegroup`, `my-pnp-hub` e `my-tsi-env` por seus respectivos valores.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Acesse o grupo de recursos no [portal do Azure](https://portal.azure.com) e selecione o ambiente do Time Series Insights recém-criado. Acesse a *URL do Gerenciador do Time Series Insights* mostrada na visão geral da instância.

![Página de visão geral do portal](./media/tutorial-configure-tsi/view-environment.png)

No gerenciador, você verá os dois termostatos conectados em "Todas as hierarquias". Em seguida, você criará o Modelo de Série Temporal com base em seu modelo do dispositivo.

![Exibição do gerenciador 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Sincronização de modelos entre a Linguagem de Definição de Gêmeos Digitais e o Time Series Insights Gen2

Em seguida, você converterá o modelo do dispositivo DTDL para o modelo de ativo no TSI (Time Series Insights) do Azure. O Modelo de Série Temporal do TSI é uma ferramenta de modelagem semântica para a contextualização de dados dentro do TSI. O Modelo de Série Temporal tem três componentes principais:

* [Instâncias do Modelo de Série Temporal](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). As instâncias são representações virtuais da série temporal. As instâncias serão identificadas exclusivamente pela ID do TS.
* [Hierarquias do Modelo de Série Temporal](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). As hierarquias organizam instâncias especificando nomes de propriedade e suas relações.
* [Tipos de Modelos de Série Temporal](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Os tipos ajudam a definir [variáveis](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) ou fórmulas para cálculos. Os tipos são associados a uma instância específica.

> [!NOTE]
> Os exemplos abaixo são para o TemperatureController de vários componentes.

### <a name="define-your-types"></a>Como definir os Tipos

É possível começar a ingerir dados no Azure Time Series Insights Gen2 sem ter um modelo previamente definido. Quando a telemetria for recebida, o TSI tentará resolver de modo automático as instâncias da série temporal com base no valor da propriedade da ID do TS. Todas as instâncias serão atribuídas com o *Tipo Padrão*. Será necessário criar manualmente um Tipo para representar seus modelos. A imagem abaixo mostrará um método simples de sincronizar um modelo DTDL e um Tipo TSM:

![Obter o DTDL para o Tipo TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* O DTMI (identificador de modelos de gêmeos digitais) se tornará a ID do Tipo
* O nome do Tipo poderá ser o nome do modelo ou o nome de exibição
* A descrição do modelo se tornará a descrição do Tipo
* No mínimo uma variável do Tipo será criada para cada componente de telemetria que tenha um esquema numérico. 
  * Somente os tipos de dados numéricos poderão ser usados para as variáveis. No entanto, caso um valor seja enviado como uma cadeia de caracteres analisável, `"0"` por exemplo, você poderá usar uma função de [conversão](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions), como `toDouble`
* O nome da variável poderá ser o nome de telemetria ou o nome de exibição
* Ao definir a variável do TSX (Expressão de Série Temporal), confira o nome da telemetria na conexão e o tipo de dados.

> [!NOTE]
> Este exemplo mostrará somente duas variáveis: uma agregação e uma numérica. No entanto, cada Tipo poderá ter até 100 variáveis. Variáveis diferentes podem referenciar o mesmo valor de telemetria para executar diferentes cálculos, conforme necessário. Para obter a lista completa de filtros, agregações e funções de valor escalar, exiba a documentação de [sintaxe da Expressão de Série Temporal do Time Series Insights Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Abra o editor de texto de sua escolha e salve o JSON abaixo na unidade local:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

No Gerenciador do Time Series Insights, acesse a guia Modelo clicando no ícone de modelo à esquerda. Clique em **Tipos** e selecione **Carregar JSON**:

![Carregar](./media/tutorial-configure-tsi/upload-type.png)

Clique em **Escolher arquivo**, selecione o JSON que você salvou anteriormente e clique em **Carregar**

Você verá o Tipo de Termostato recém-definido.

### <a name="optional---create-a-hierarchy"></a>Alternativa: criar uma hierarquia

Como alternativa, é possível criar uma hierarquia para organizar dois componentes do termostato no pai TemeraptureController.

Clique em *Hierarquias* e selecione *Adicionar uma hierarquia*. Insira `Device Fleet` como o nome e crie um nível chamado `Device Name`. Depois clique em *Salvar*.

![Adicionar uma hierarquia](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribuir instâncias ao tipo correto

Em seguida, você vai alterar o Tipo de suas instâncias e, como alternativa, as posicionará dentro da hierarquia

Selecione a guia *Instâncias* e clique no ícone *Editar* mais à direita.

![Editar instâncias](./media/tutorial-configure-tsi/edit-instance.png)

Clique na lista suspensa chamada Tipo e selecione `Thermostat`. 

![Alterar o tipo de instância](./media/tutorial-configure-tsi/change-type.png)

Caso tenha criado uma hierarquia, clique em *Campos de instância* e marque a caixa `Device Fleet`. Insira `Temperature Controller` como o Valor do dispositivo pai e clique em *Salvar*.

![Como atribuir à hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita as etapas acima para o segundo Termostato.

### <a name="view-your-data"></a>Exibir seus dados

Volte para o painel de gráficos e expanda as opções Frota de Dispositivos e TemperatureController. Clique em thermostat1, selecione a variável `Temperature` e clique em *Adicionar* para encontrar o valor. Faça o mesmo com o thermostat2.

![Alterar o tipo de instância do thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as várias opções de gráficos, incluindo o dimensionamento de intervalos e os controles do eixo y, exiba a documentação do [Gerenciador do Time Series Insights do Azure](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels).

* Para obter uma visão geral detalhada do Modelo de Série Temporal do ambiente, confira [este](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) artigo.

* Para saber mais detalhes sobre APIs de consulta e a sintaxe da Expressão de Série Temporal, [clique aqui](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




