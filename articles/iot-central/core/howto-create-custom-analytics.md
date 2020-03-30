---
title: Estender a Central de IoT do Azure com análises personalizadas | Microsoft Docs
description: Como desenvolvedor de soluções, configure um aplicativo IoT Central para fazer análises e visualizações personalizadas. Esta solução usa o Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158190"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estender o Azure IoT Central com análises personalizadas usando o Azure Databricks

Este guia de como fazer mostra, como desenvolvedor de soluções, como um desenvolvedor de soluções, como estender seu aplicativo IoT Central com análises e visualizações personalizadas. O exemplo usa um espaço de trabalho [do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) para analisar o fluxo de telemetria Central de IoT e gerar visualizações, como [gráficos de caixa](https://wikipedia.org/wiki/Box_plot).

Este guia de como fazer mostra como estender a IoT Central além do que ela já pode fazer com as [ferramentas de análise incorporadas](./howto-create-custom-analytics.md).

Neste guia de como fazer, você aprende como:

* Transmitir telemetria a partir de um aplicativo IoT Central usando *exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar telemetria de dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicativo IoT Central

Crie um aplicativo IoT Central no site do [gerenciador de aplicativos Central do Azure IoT](https://aka.ms/iotcentral) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicativo | Análise na loja – monitoramento de condições |
| Nome do aplicativo | Aceite o padrão ou escolha seu próprio nome |
| URL | Aceite o padrão ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Seu inquilino do Diretório Ativo do Azure |
| Assinatura do Azure | Sua assinatura do Azure |
| Região | Sua região mais próxima |

Os exemplos e capturas de tela deste artigo usam a região **dos Estados Unidos.** Escolha um local próximo a você e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicativo inclui dois dispositivos de termostato simulados que enviam telemetria.

### <a name="resource-group"></a>Resource group

Use o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **IoTCentralAnalysis** para conter os outros recursos que você cria. Crie seus recursos do Azure no mesmo local que seu aplicativo IoT Central.

### <a name="event-hubs-namespace"></a>Namespace do Hubs de Eventos

Use o [portal Azure para criar um namespace do Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha seu nome de namespace |
| Tipo de preço | Basic |
| Subscription | Sua assinatura |
| Resource group | IoTCentralAnalysis |
| Location | Leste dos EUA |
| Unidades de produtividade | 1 |

### <a name="azure-databricks-workspace"></a>Espaço de trabalho do Azure Databricks

Use o [portal Azure para criar um Serviço de Databricks do Azure](https://portal.azure.com/#create/Microsoft.Databricks) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome do workspace    | Escolha seu nome do espaço de trabalho |
| Subscription | Sua assinatura |
| Resource group | IoTCentralAnalysis |
| Location | Leste dos EUA |
| Camada de preços | Standard |

Quando você criou os recursos necessários, seu grupo de recursos **IoTCentralAnalysis** se parece com a seguinte captura de tela:

![Grupo de recursos de análise central de IoT](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Você pode configurar um aplicativo IoT Central para exportar continuamente a telemetria para um hub de eventos. Nesta seção, você cria um hub de eventos para receber telemetria a partir do seu aplicativo IoT Central. O hub de eventos oferece a telemetria ao seu trabalho de Stream Analytics para processamento.

1. No portal Azure, navegue até o namespace do Event Hubs e selecione **+ Event Hub**.
1. Nomeie o **centro de eventos centralexportar**e selecione **Criar**.
1. Na lista de hubs de eventos em seu namespace, selecione **centralexport .** Em seguida, escolha **políticas de acesso compartilhado**.
1. Selecione **+ Adicionar**. Crie uma política chamada **Listen** with the **Listen** claim.
1. Quando a diretiva estiver pronta, selecione-a na lista e copie o **valor-chave principal da seqüência de strings** Connection.
1. Anote essa seqüência de conexões, use-a mais tarde quando configurar seu notebook Databricks para ler a partir do centro de eventos.

O namespace do Event Hubs parece a seguinte captura de tela:

![Namespace do Hubs de Eventos](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

No site do gerenciador de [aplicativos Azure IoT Central,](https://aka.ms/iotcentral) navegue até o aplicativo IoT Central que você criou a partir do modelo Contoso. Nesta seção, você configura o aplicativo para transmitir a telemetria de seus dispositivos simulados para o seu hub de eventos. Para configurar a exportação:

1. Navegue até a página **De Exportação de Dados,** selecione **+ Novo**e, em seguida, **Azure Event Hubs**.
1. Use as seguintes configurações para configurar a exportação e, em seguida, **selecione Salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome de exibição | Exportar para hubs de eventos |
    | habilitado | Por |
    | Namespace do Hubs de Eventos | Seu nome de namespace do Event Hubs |
    | Hub de Eventos | centralexportação |
    | Medidas | Por |
    | Dispositivos | Desativado |
    | Modelos de Dispositivo | Desativado |

![Configuração de exportação de dados](media/howto-create-custom-analytics/cde-configuration.png)

Aguarde até que o status de exportação esteja **em execução** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configurar o espaço de trabalho databricks

No portal Azure, navegue até o serviço Azure Databricks e selecione **Launch Workspace**. Uma nova guia é aberta no seu navegador e inscreve você no seu espaço de trabalho.

### <a name="create-a-cluster"></a>Criar um cluster

Na página **Azure Databricks,** a lista de tarefas comuns, selecione **Novo cluster**.

Use as informações na tabela a seguir para criar seu cluster:

| Configuração | Valor |
| ------- | ----- |
| Nome do cluster | análise central |
| Modo cluster | Standard |
| Versão de tempo de execução de databricks | 5.5 LTS (Scala 2.11, Faísca 2.4.3) |
| Versão do Python | 3 |
| Habilitar o dimensionamento automático | Não |
| Termine após minutos de inatividade | 30 |
| Tipo de trabalhador | Standard_DS3_v2 |
| Trabalhos | 1 |
| Tipo de driver | O mesmo que o trabalhador |

A criação de um cluster pode levar vários minutos, aguarde que a criação do cluster seja concluída antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

Na página **Clusters,** aguarde até que o estado de cluster esteja **em execução**.

As etapas a seguir mostram como importar a biblioteca que sua amostra precisa para o cluster:

1. Na página **Clusters,** aguarde até que o estado do cluster interativo **centralanalysis** esteja **em execução**.

1. Selecione o cluster e escolha a guia **Bibliotecas.**

1. Na guia **Bibliotecas,** escolha **Instalar novo**.

1. Na página **Instalar biblioteca,** escolha **Maven** como fonte da biblioteca.

1. Na caixa de texto **Coordenadas,** digite o seguinte valor:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Escolha **Instalar** para instalar a biblioteca no cluster.

1. O status da biblioteca está agora **instalado:**

    ![Biblioteca instalada](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importar um notebook Databricks

Use as seguintes etapas para importar um notebook Databricks que contenha o código Python para analisar e visualizar sua telemetria Central ioT:

1. Navegue até a página **workspace** em seu ambiente Databricks. Selecione a isto latente ao lado do nome da sua conta e, em seguida, escolha **Importar**.

1. Escolha importar de uma URL e digite o seguinte endereço:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar o notebook, escolha **Importar**.

1. Selecione o **Espaço de trabalho** para visualizar o notebook importado:

    ![Notebook importado](media/howto-create-custom-analytics/import-notebook.png)

1. Edite o código na primeira célula Python para adicionar a seqüência de conexões Event Hubs que você salvou anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Análise de execução

Para executar a análise, você deve anexar o notebook ao cluster:

1. Selecione **'''Destacado',** em seguida, selecione o cluster **centralde análise.**
1. Se o cluster não estiver funcionando, inicie-o.
1. Para iniciar o notebook, selecione o botão executar.

Você pode ver um erro na última célula. Se assim for, verifique se as células anteriores estão em execução, espere um minuto para que alguns dados sejam gravados para armazenamento e, em seguida, execute a última célula novamente.

### <a name="view-smoothed-data"></a>Ver dados suavizados

No notebook, role até o celular 14 para ver um gráfico da umidade média de rolamento por tipo de dispositivo. Este enredo é atualizado continuamente à medida que a telemetria de streaming chega:

![Enredo de telemetria suavizado](media/howto-create-custom-analytics/telemetry-plot.png)

Você pode redimensionar o gráfico no caderno.

### <a name="view-box-plots"></a>Exibir parcelas da caixa

No caderno, role até a célula 20 para ver as parcelas da [caixa.](https://en.wikipedia.org/wiki/Box_plot) Os gráficos da caixa são baseados em dados estáticos, então para atualizá-los, você deve reexecutar a célula:

![Parcelas de caixa](media/howto-create-custom-analytics/box-plots.png)

Você pode redimensionar as parcelas no caderno.

## <a name="tidy-up"></a>Limpar

Para arrumar o que fazer e evitar custos desnecessários, exclua o grupo de recursos **IoTCentralAnalysis** no portal Azure.

Você pode excluir o aplicativo IoT Central da página **Gerenciamento** dentro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu como:

* Transmitir telemetria a partir de um aplicativo IoT Central usando *exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar dados de telemetria.

Agora que você sabe como criar análises personalizadas, o próximo passo sugerido é aprender como [gerenciar seu aplicativo](howto-administer.md).
