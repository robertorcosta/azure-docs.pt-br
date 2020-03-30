---
title: Visualização de dados em tempo real de dados frm Azure IoT Hub – Power BI
description: Use o Power BI para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados de sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954635"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensor em tempo real do Hub IoT usando o Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a visualizar dados do sensor em tempo real que recebe o hub IoT do Azure usando o Power BI. Se você quiser tentar visualizar os dados em seu hub de IoT com um aplicativo web, consulte [Use um aplicativo web para visualizar dados de sensores em tempo real do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

* Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.

* Criar, configurar e executar um trabalho do Stream Analytics para transferência de dados do seu hub IoT à sua conta do Power BI.

* Crie e publique um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>O que você precisa

* Complete o tutorial [do simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes artigos abrangem os seguintes requisitos:
  
  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * O aplicativo cliente que envia mensagens para o hub IoT do Azure.

* Uma conta do Power BI. (Experimente[power bi gratuitamente)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho do Stream Analytics

Vamos começar criando um trabalho do Stream Analytics. Depois de criar o trabalho, você deve definir as entradas, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: O nome do trabalho. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos que seu hub de IoT usa.

   **Local**: use o mesmo local do que o grupo de recursos.

   ![Criar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Abra o trabalho do Stream Analytics.

2. Em **Topologia do Trabalho**, selecione **Entradas**.

3. No painel **Entradas,** selecione **Adicionar entrada de fluxo**e selecione **IoT Hub** na lista de desída. No novo painel de entrada, digite as seguintes informações:

   **Alias de entrada**: Digite um alias exclusivo para a entrada.

   **Forneça ioT hub a partir de sua assinatura**: Selecione este botão de rádio.

   **Assinatura**: Selecione a assinatura do Azure que você está usando para este tutorial.

   **IoT Hub**: Selecione o Hub ioT que você está usando para este tutorial.

   **Ponto de extremidade**: selecione **Mensagens**.

   **Nome da política**de acesso compartilhado : Selecione o nome da política de acesso compartilhado que deseja que o trabalho do Stream Analytics use para o seu hub de IoT. Para este tutorial, você pode selecionar *o serviço*. A política *de serviço* sou criada por padrão em novos hubs de IoT e concede permissão para enviar e receber em pontos finais do lado da nuvem expostos pelo hub IoT. Para saber mais, consulte [controle de acesso e permissões](iot-hub-devguide-security.md#access-control-and-permissions).

   **Chave da política de acesso compartilhado**: Este campo é preenchido automaticamente com base na sua seleção para o nome da política de acesso compartilhado.

   **Grupo de consumidores**: Selecione o grupo de consumidores que você criou anteriormente.

   Deixe todos os outros campos em seus padrões.

   ![Adicionar uma entrada a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecione **Salvar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Saídas**.

2. No painel **Saídas,** **selecione Adicionar** e **Ligar BI**.

3. No **Power BI - Novo** painel de saída, selecione **Autorizar** e siga as instruções para entrar na sua conta power BI.

4. Depois de ter feito o fizer o fizer no Power BI, digite as seguintes informações:

   **Alias de saída**: Um alias exclusivo para a saída.

   **Workspace de grupo**: selecione o workspace do grupo de destino.

   **Nome do Conjunto de Dados**: insira um nome de conjunto de dados.

   **Nome da Tabela**: insira um nome de tabela.

   ![Adicionar uma saída a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecione **Salvar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho.

3. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho.

   ![Adicionar uma consulta a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecione **Salvar**.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, selecione **Visão geral**e selecione **Iniciar** > **agora** > **iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

![Executar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

1. Verifique se o aplicativo de exemplo está em execução em seu dispositivo. Se não, você pode consultar os tutoriais em [Configure seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione o espaço de trabalho que você usou, **My Workspace**.

4. Selecione **Conjuntos de dados**.

   Você deverá ver o conjunto de dados especificado quando você criou a saída para o trabalho do Stream Analytics.

5. Para o conjunto de dados criado, **selecione Adicionar relatório** (o primeiro ícone à direita do nome do conjunto de dados).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. No painel **Visualizações** da página de criação do relatório, selecione o ícone do gráfico de linha para adicionar um gráfico de linha.

   2. Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.

   4. Arraste **temperatura** para **Valores**.

      Um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

      ![Adicionar um gráfico de linhas para a temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para fazer isso, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Adicionar um gráfico de linhas para umidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecione **Salvar** para salvar o relatório.

9. Selecione **Relatórios** no painel esquerdo e selecione o relatório que você acabou de criar.

10. Selecione **Publicar arquivo** > **para web**.

11. Selecione **Criar código de incorporação**e, em seguida, **selecione Publicar**.

Você tem o link do relatório que você pode compartilhar com qualquer pessoa para acesso ao relatório e um trecho de código que você pode usar para integrar o relatório em seu blog ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

A Microsoft também oferece o [aplicativos móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para exibir e interagir com seus relatórios e painéis do Power BI em seu dispositivo móvel.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito o Power BI para visualizar dados do sensor em tempo real do seu Hub IoT do Azure.

Para outra maneira de visualizar dados do Azure IoT Hub, consulte [Use um aplicativo web para visualizar dados de sensores em tempo real do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
