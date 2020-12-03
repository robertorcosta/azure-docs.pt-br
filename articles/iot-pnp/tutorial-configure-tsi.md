---
title: Usar o Time Series Insights para armazenar e analisar a telemetria do dispositivo IoT Plug and Play do Azure | Microsoft Docs
description: Configure um ambiente do Time Series Insights e conecte o hub IoT para exibir e analisar a telemetria por meio dos seus dispositivos IoT Plug and Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ad5c6f205fc832eb125e52b4135990fc58742e62
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453234"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Versão prévia do tutorial: criar o Time Series Insights Gen2 e conectar-se a ele para armazenar, visualizar e analisar a telemetria de dispositivos do IoT Plug and Play

Neste tutorial, você aprenderá a criar e configurar de modo correto um ambiente do [TSI](../time-series-insights/overview-what-is-tsi.md) (Azure Time Series Insights Gen2) para integrá-lo à sua solução de IoT Plug and Play. Use o TSI para coletar, processar, armazenar, consultar e visualizar dados de série temporal na escala da IoT (Internet das Coisas).

Primeiro, você provisionará um ambiente do TSI e conectará o hub IoT como uma origem do evento de streaming. Depois, você trabalhará com a sincronização de modelos para criar o [Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md) com base nos arquivos de modelos de exemplo da [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl) usados para o controlador de temperatura e os dispositivos de termostato.

> [!NOTE]
> Essa integração está em versão prévia. A forma como os modelos de dispositivos DTDL são mapeados para o Modelo de Série Temporal poderá ser alterada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Até agora, você tem:

* Um Hub IoT do Azure.
* Uma instância do DPS vinculada ao seu hub IoT, com um registro de dispositivo individual para seu dispositivo IoT Plug and Play.
* Uma conexão com o hub IoT por meio de um dispositivo de um só componente ou de vários componentes, transmitindo os dados simulados.

Evitar a necessidade de instalar a CLI do Azure localmente. É possível usar o Azure Cloud Shell para configurar serviços de nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Preparar a origem do evento

O hub IoT criado anteriormente será a [origem do evento](../time-series-insights/concepts-streaming-ingestion-event-sources.md) do seu ambiente do TSI.

> [!IMPORTANT]
> Desabilite as rotas existentes do Hub IoT. Há um problema conhecido quando você usa um hub IoT como uma origem do evento do TSI com o [roteamento](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configurado. Desabilite temporariamente os pontos de extremidade de roteamento e, quando o hub IoT estiver conectado ao TSI, habilite-os novamente.

Crie um grupo de consumidores exclusivo no hub IoT para consumo do TSI. Substitua `my-pnp-hub` pelo nome do hub IoT usado anteriormente:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Escolher a ID da Série Temporal

Ao provisionar seu ambiente do TSI, você precisará escolher uma *ID da Série Temporal*. É importante escolher a ID da Série Temporal apropriada, pois essa propriedade é imutável e não poderá ser alterada após a definição. Uma ID da Série Temporal é como uma chave de partição de banco de dados. A ID da Série Temporal funciona como a chave primária do Modelo de Série Temporal. Para saber mais, confira [Melhores práticas para a escolha de uma ID da Série Temporal](../time-series-insights/how-to-select-tsid.md).

Como usuário do IoT Plug and Play, especifique uma _chave composta_ que consiste em `iothub-connection-device-id` e `dt-subject` como a ID da Série Temporal. O Hub IoT adiciona essas propriedades do sistema que contêm a identificação do dispositivo IoT Plug and Play e os nomes dos componentes do dispositivo, respectivamente.

Mesmo que os modelos de dispositivos IoT Plug and Play não usem os componentes no momento, você deverá incluir `dt-subject` como parte de uma chave composta de modo que possa usá-los no futuro. Como a ID da Série Temporal é imutável, a Microsoft recomenda habilitar essa opção caso você precise dela no futuro.

> [!NOTE]
> Os exemplos abaixo referem-se ao dispositivo de **TemperatureController** de vários componentes, mas os conceitos são os mesmos para o dispositivo de **Termostato** sem componente.

## <a name="provision-your-tsi-environment"></a>Provisionar seu ambiente do TSI

Esta seção descreve como provisionar seu ambiente do Azure Time Series Insights Gen2.

O seguinte comando:

* Cria uma conta de armazenamento do Azure para o [armazenamento frio](../time-series-insights/concepts-storage.md#cold-store) do ambiente, projetada para a retenção de longo prazo e a análise de dados históricos.
  * Substitua `mytsicoldstore` por um nome exclusivo para a sua conta de armazenamento frio.
* Cria um ambiente do Azure Time Series Insights Gen2, incluindo o armazenamento warm com um período de retenção de sete dias e um armazenamento frio para a retenção infinita.
  * Substitua `my-tsi-env` por um nome exclusivo para seu ambiente do TSI.
  * Substitua `my-pnp-resourcegroup` pelo nome do grupo de recursos usado durante a configuração.
  * `iothub-connection-device-id, dt-subject` é a propriedade da ID da Série Temporal.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Conecte a origem do evento do Hub IoT. Substitua `my-pnp-resourcegroup`, `my-pnp-hub` e `my-tsi-env` pelos valores escolhidos. O seguinte comando referencia o grupo de consumidores do TSI criado anteriormente:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Navegue até o seu grupo de recursos no [portal do Azure](https://portal.azure.com) e escolha o novo ambiente do Time Series Insights. Acesse a *URL do Gerenciador do Time Series Insights* mostrada na visão geral da instância:

![Página de visão geral do portal](./media/tutorial-configure-tsi/view-environment.png)

No Gerenciador, você verá três instâncias:

* &lt;a identificação do dispositivo PnP&gt;, thermostat1
* &lt;a identificação do dispositivo PnP&gt;, thermostat2
* &lt;a identificação do dispositivo PnP&gt;, `null`

> [!NOTE]
> A terceira marca representa a telemetria do próprio **TemperatureController**, como o conjunto de trabalho da memória do dispositivo. Como essa é uma propriedade de nível superior, o valor do nome do componente é nulo. Em uma etapa posterior, você atualizará isso para um nome mais amigável.

![Exibição do gerenciador 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurar a conversão do modelo

Em seguida, você converterá o modelo de dispositivo DTDL no modelo de ativo do TSI (Azure Time Series Insights). O Modelo de Série Temporal do TSI é uma ferramenta de modelagem semântica para a contextualização de dados dentro do TSI. O Modelo de Série Temporal tem três componentes principais:

* [Instâncias do Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-instances). As instâncias são representações virtuais da série temporal. As instâncias são identificadas exclusivamente pela ID da Série Temporal.
* [Hierarquias do Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). As hierarquias organizam instâncias especificando nomes de propriedade e suas relações.
* [Tipos de Modelos de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-types). Os tipos ajudam você a definir [variáveis](../time-series-insights/concepts-variables.md) ou fórmulas para cálculos. Os tipos são associados a uma instância específica.

### <a name="define-your-types"></a>Definir os tipos

Você pode começar ingerindo dados no Azure Time Series Insights Gen2 sem ter um modelo predefinido. Quando a telemetria é recebida, o TSI tenta resolver automaticamente as instâncias de série temporal com base nos valores da propriedade da ID da Série Temporal. Todas as instâncias recebem o *tipo padrão*. Você precisa criar um tipo manualmente para categorizar corretamente as instâncias. Os seguintes detalhes mostram o método mais simples para sincronizar os modelos de dispositivo DTDL com os tipos do Modelo de Série Temporal:

* O identificador de modelo do gêmeo digital passa a ser a ID do tipo.
* O nome do tipo pode ser o nome do modelo ou o nome de exibição.
* A descrição do modelo passa a ser a descrição do tipo.
* Pelo menos uma variável de tipo é criada para cada telemetria com um esquema numérico.
  * Somente tipos de dados numéricos podem ser usados para as variáveis. Porém, se um valor é enviado como outro tipo que possa ser convertido, `"0"`, por exemplo, você pode usar uma função de [conversão](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions), como `toDouble`.
* O nome da variável pode ser o nome da telemetria ou o nome de exibição.
* Ao definir a Expressão de Série Temporal da variável, veja o nome da telemetria na conexão e o respectivo tipo de dados.

| DTDL JSON | JSON do tipo de Modelo de Série Temporal | Valor de exemplo |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matriz)| `variables` (objeto)  | Veja o exemplo abaixo

![DTDL para o tipo de Modelo de Série Temporal](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Este exemplo mostra três variáveis, mas cada tipo pode ter até 100. Variáveis diferentes podem referenciar o mesmo valor de telemetria para fazer diferentes cálculos, conforme necessário. Para obter a lista completa de filtros, agregações e funções escalares, confira [Sintaxe da Expressão de Série Temporal do Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Abra um editor de texto e salve o seguinte JSON na unidade local:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

No Gerenciador do Time Series Insights, acesse a guia **Modelo** selecionando o ícone de modelo à esquerda. Selecione **Tipos** e escolha **Carregar JSON**:

![Carregar](./media/tutorial-configure-tsi/upload-type.png)

Selecione **Escolher arquivo**, escolha o JSON que você salvou anteriormente e selecione **Carregar**

Você verá o tipo de **Controlador de Temperatura** recém-definido.

### <a name="create-a-hierarchy"></a>Criar uma hierarquia

Crie uma hierarquia para organizar as marcas no pai **TemperatureController**. O exemplo simples a seguir tem um só nível, mas as soluções de IoT costumam ter muitos níveis de aninhamento para contextualizar as marcas na posição física e semântica dentro de uma organização.

Escolha **Hierarquias** e selecione **Adicionar hierarquia**. Insira *Frota de Dispositivos* como o nome e crie um nível chamado *Nome do Dispositivo*. Depois, selecione **Salvar**.

![Adicionar uma hierarquia](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribuir instâncias ao tipo correto

Em seguida, altere o tipo das instâncias e coloque-as na hierarquia.

Selecione a guia **Instâncias**, localize a instância que representa o conjunto de trabalho do dispositivo e escolha o ícone **Editar** na extrema direita:

![Editar instâncias](./media/tutorial-configure-tsi/edit-instance.png)

Selecione a lista suspensa **Tipo** e escolha **Controlador de Temperatura**. Insira *defaultComponent, <your device name>* para atualizar o nome da instância que representa todas as marcas de nível superior associadas ao seu dispositivo.

![Alterar o tipo de instância](./media/tutorial-configure-tsi/change-type.png)

Antes de selecionar Salvar, escolha a guia **Campos de Instância** e marque a caixa **Frota de Dispositivos**. Insira `<your device name> - Temp Controller` para agrupar a telemetria e selecione **Salvar**.

![Como atribuir à hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita as etapas anteriores para atribuir as marcas do termostato à hierarquia e ao tipo corretos.

## <a name="view-your-data"></a>Exibir seus dados

Volte ao painel de gráficos e expanda **Frota de Dispositivos > seu dispositivo**. Selecione **thermostat1**, escolha a variável **Temperatura** e selecione **Adicionar** para adicionar o valor ao gráfico. Faça o mesmo para **thermostat2** e o valor de **workingSet** do **defaultComponent**.

![Alterar o tipo de instância do thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as várias opções de gráficos, incluindo o dimensionamento de intervalos e os controles do eixo y, confira [Gerenciador do Azure Time Series Insights](../time-series-insights/concepts-ux-panels.md).

* Para obter uma visão geral detalhada do Modelo de Série Temporal do seu ambiente, confira o artigo [Modelo de Série Temporal no Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md).

* Para saber mais sobre as APIs de consulta e a sintaxe da Expressão de Série Temporal, confira [APIs de consulta do Azure Time Series Insights Gen2](/rest/api/time-series-insights/reference-query-apis.md).
