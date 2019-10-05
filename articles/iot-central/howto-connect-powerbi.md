---
title: Visualize seus dados do Azure IoT Central em um painel do Power BI | Microsoft Docs
description: Use a solução do Power BI para Azure IoT Central para visualizar e analisar seus dados da IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: 3ce2f4304787107d0d6875333e4630dae8d7d1dd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973783"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise seus dados do Azure IoT Central em um painel do Power BI

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Este tópico aplica-se aos administradores.*

![Pipeline de solução do Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Use a solução do Power BI para Azure IoT Central para criar um dashboard avançado do Power BI para monitorar o desempenho dos seus dispositivos IoT. No seu painel do Power BI, você pode:
- Acompanhe quantos dados seus dispositivos estão enviando ao longo do tempo
- Comparar o volume de dados entre telemetria, estados e eventos
- Identifique dispositivos que estão relatando muitas medições
- Observe as tendências históricas das medições do dispositivo
- Identificar dispositivos problemáticos que enviam muitos eventos críticos

Esta solução configura o pipeline que usa os dados na sua conta de Armazenamento de Blobs do Azure da [Exportação de dados contínua](howto-export-data.md). Esses dados fluem por meio do Azure Functions, Azure Data Factory e o banco de dados SQL do Azure para processar e transformar os dados. A saída pode ser visualizada e analisada em um relatório do Power BI que você pode baixar como um arquivo PBIX. Todos esses recursos são criados em sua assinatura do Azure, para que você possa personalizar cada componente para atender às suas necessidades.

> [!Note] 
> A solução Power BI para o IoT Central do Azure funciona com aplicativos IoT Central que não dão suporte a Plug and Play IoT (aplicativos de visualização hoje)

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Obtenha a [solução do Power BI para Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) no Microsoft AppSource.

## <a name="prerequisites"></a>Pré-requisitos
A configuração da solução requer o seguinte:
- Acesso a uma assinatura do Azure
- IoT Central aplicativo que não dá suporte a IoT Plug and Play (aplicativos de visualização hoje)
- Exportação de dados contínua configurada para o armazenamento de BLOBs do Azure de seu aplicativo IoT Central
    - Verifique se o formato de dados é Avro
    - Recomendamos que você ative medições, dispositivos e fluxos de modelos de dispositivos para aproveitar ao máximo o painel do Power BI.
    - Saiba [como configurar a exportação de dados contínuas](howto-export-data-blob-storage.md)
- Power BI Desktop (versão mais recente)
- Power BI Pro (se você quiser compartilhar o painel com os outros)

## <a name="reports"></a>Relatórios

Dois relatórios são gerados automaticamente. 

O primeiro relatório mostra uma visão histórica das medições relatadas pelos dispositivos e divide os diferentes tipos de medições e dispositivos que enviaram o maior número de medições.

![Página de relatório do Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

O segundo relatório aprofunda os eventos e mostra uma visão histórica dos erros e avisos relatados. Ele também mostra quais dispositivos estão relatando o maior número de eventos de tudo para cima, bem como especificamente os eventos de erro e eventos de aviso.

![Página de relatório do Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Arquitetura
Todos os recursos criados podem ser acessados no portal do Azure. Tudo deve estar em um grupo de recursos.

![Exibição de grupo de recursos do portal do Azure](media/howto-connect-powerbi/azure-deployment.PNG)

As especificidades de cada recurso e de como ele é usado são descritas abaixo.

### <a name="azure-functions"></a>Verificação de
O aplicativo Azure Functions é disparado sempre que um novo arquivo é gravado no Armazenamento de Blobs. As funções extraem os campos dentro de cada arquivo de medidas, de dispositivos e de modelos de dispositivo e popula várias tabelas intermediárias do SQL a serem usadas pelo Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
O Azure Data Factory se conecta ao Banco de Dados SQL como um serviço vinculado. Ele executa as atividades de procedimento armazenado que processa os dados e os armazena nas tabelas de análise.

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
Essas tabelas são criadas automaticamente para popular os relatórios padrão. Explore esses esquemas no Power BI e será possível criar suas próprias visualizações nesses dados.

| Nome da tabela |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Custos estimados

Confira uma estimativa dos custos do Azure (Azure Functions, Data Factory, Azure SQL) envolvidos. Todos os preços estão em dólares. Tenha em mente que os preços variam por região, portanto, você sempre deve pesquisar os preços mais recentes dos serviços individuais para ver os preços reais.
Os padrões a seguir são definidos para você no modelo (é possível modificar qualquer um deles após a configuração):

- Azure Functions: Plano do Serviço de Aplicativo S1, US$ 74,40/mês
- Azure SQL S1, cerca de US$ 30/mês

Incentivamos você a saber mais sobre as várias opções de preços e se ajustar às coisas para se adequar às suas necessidades.

## <a name="resources"></a>Recursos

Acesse o AppSource para obter a [Solução do Power BI para Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a visualizar seus dados no Power BI, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como gerenciar dispositivos](howto-manage-devices.md)