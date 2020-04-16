---
title: Carregar dados do SAP Business Warehouse
description: Use a Fábrica de Dados Do Azure para copiar dados do SAP Business Warehouse (BW)
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415831"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando a Fábrica de Dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo mostra como usar a Fábrica de Dados Do Azure para copiar dados do SAP Business Warehouse (BW) via Open Hub para o Azure Data Lake Storage Gen2. Você pode usar um processo semelhante para copiar dados para outros [armazenamentos de dados de pia suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!TIP]
> Para obter informações gerais sobre a cópia de dados do SAP BW, incluindo a integração do SAP BW Open Hub e o fluxo de extração delta, consulte [Copiar dados do SAP Business Warehouse via Open Hub usando o Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory**: Se você não tiver um, siga as etapas para [criar uma fábrica de dados](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub Destination (OHD) com o tipo de destino "Tabela de banco**de dados" : Para criar um OHD ou verificar se seu OHD está configurado corretamente para integração de Data Factory, consulte a seção de configurações do [SAP BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) deste artigo.

- **O usuário SAP BW precisa das seguintes permissões:**

  - Autorização para Chamadas de Função Remota (RFC) e SAP BW.
  - Permissões para a atividade "Executar" do objeto de autorização **S_SDSAUTH.**

- **Um [tempo de execução de integração auto-hospedado (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) com conector SAP .NET 3.0**. Siga estas etapas de configuração:

  1. Instale e registre o tempo de execução de integração auto-hospedado, versão 3.13 ou posterior. (Este processo é descrito posteriormente neste artigo.)

  2. Baixe o [Conector SAP de 64 bits para Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) no site da SAP e instale-o no mesmo computador que o IR auto-hospedado. Durante a instalação, certifique-se de selecionar **Conjuntos de instalação para GAC** na caixa de diálogo **'etapas de configuração'** como mostra a imagem a seguir:

     ![Configure a caixa de diálogo SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Faça uma cópia completa do SAP BW Open Hub

No portal do Azure, acesse seu data factory. Selecione **O Monitor de & autor** para abrir a ui da fábrica de dados em uma guia separada.

1. Na página **Vamos começar,** selecione **Copiar dados** para abrir a ferramenta Copiar dados.

2. Na página **Propriedades,** especifique um **nome de tarefa**e selecione **Next**.

3. Na página do **armazenamento de dados Origem,** selecione **+Criar nova conexão**. Selecione **SAP BW Open Hub** na galeria de conectores e selecione **Continuar**. Para filtrar os conectores, você pode digitar **SAP** na caixa de pesquisa.

4. Na página **de conexão Especificar SAP BW Open Hub,** siga essas etapas para criar uma nova conexão.

   ![Criar página de serviço sap BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. A partir da **lista de tempo de execução de integração,** selecione um IR auto-hospedado existente. Ou, escolha criar um se você não tiver um ainda.

      Para criar um novo RI auto-hospedado, selecione **+Novo**e selecione **Auto-hosted**. Digite um **nome**e selecione **Next**. Selecione a **configuração Express** para instalar no computador atual ou siga as etapas **de configuração manual** fornecidas.

      Como mencionado nos [pré-requisitos,](#prerequisites)certifique-se de que você tenha o Conector SAP para Microsoft .NET 3.0 instalado no mesmo computador onde o IR auto-hospedado está sendo executado.

   2. Preencha o nome do **servidor**SAP BW, **número do sistema,** **ID do cliente,** **idioma** (se não for **EN),** **nome de usuário**e **senha.**

   3. Selecione **A conexão Teste** para validar as configurações e, em seguida, **selecione Concluir**.

   4. Uma nova conexão é criada. Selecione **Avançar**.

5. Na página Selecionar destinos **do Hub Aberto,** navegue pelos Destinos do Hub Aberto que estão disponíveis em seu SAP BW. Selecione o OHD para copiar dados e, em seguida, selecione **Next**.

   ![Selecione a tabela Destino do Hub Aberto SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se precisar de um. Se o seu OHD contiver apenas dados de uma única execução de processo de transferência de dados (DTP) com um único ID de solicitação, ou você tiver certeza de que seu DTP está concluído e você deseja copiar os dados, limpe a caixa de seleção **Exclua a Última Solicitação.**

   Saiba mais sobre essas configurações na seção de [configurações do SAP BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) deste artigo. Selecione **Validar** para verificar novamente quais dados serão devolvidos. Em seguida, selecione **Avançar**.

   ![Configurar o filtro SAP BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na página do **armazenamento de dados Destino,** selecione **+Criar nova conexão** > **Azure Data Lake Storage Gen2** > **Continue**.

8. Na página **Especificar a zure Data Lake Storage,** siga essas etapas para criar uma conexão.

   ![Crie uma página de serviço vinculada ao ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione sua conta com capacidade para data lake storage Gen2 na lista de paradas **Nome.**
   2. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Avançar**.

9. Na **página Escolher o arquivo de saída ou pasta,** digite **copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Avançar**.

   ![Escolha a página da pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Na página **de configuração do formato Arquivo,** **selecione 'A seguir** para usar as configurações padrão'.

    ![Especificar página de formato de sumidouro](media/load-sap-bw-data/specify-sink-format.png)

11. Na página **Configurações,** expanda **as configurações de desempenho**. Digite um valor para **grau de paralelismo de cópia,** como 5 para carregar do SAP BW em paralelo. Em seguida, selecione **Avançar**.

    ![Configurar configurações de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. Na página **Resumo**, verifique as configurações. Em seguida, selecione **Avançar**.

13. Na página **'Implantação',** selecione **Monitor** para monitorar o pipeline.

    ![Página Implantação](media/load-sap-bw-data/deployment.png)

14. Observe que a guia **Monitor** no lado esquerdo da página é automaticamente selecionada. A coluna **Ações** inclui links para exibir detalhes de execução de atividades e para executar o pipeline.

    ![Exibição de monitoramento de gasodutos](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para exibir as executações de atividades associadas à execução do pipeline, **selecione Exibir execução de atividades** na coluna **Ações.** Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Para voltar à exibição de pipeline-runs, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Tela de monitoramento de atividades](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes,** que é um ícone de óculos abaixo **Ações** na exibição de monitoramento de atividades. Os detalhes disponíveis incluem o volume de dados copiado da fonte para o dissipador, o throughput de dados, as etapas de execução e a duração e as configurações usadas.

    ![Detalhes de monitoramento de atividades](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para exibir o **ID de solicitação máximo,** volte à exibição de monitoramento de atividades e selecione **Saída** em **Ações**.

    ![Tela de saída de atividade](media/load-sap-bw-data/activity-output.png)

    ![Exibição de detalhes da saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental do SAP BW Open Hub

> [!TIP]
> Consulte [o fluxo de extração delta do conector SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o conector SAP BW Open Hub na Fábrica de Dados copia dados incrementais do SAP BW. Este artigo também pode ajudá-lo a entender a configuração básica do conector.

Agora, vamos continuar a configurar cópia incremental do SAP BW Open Hub.

A cópia incremental usa um mecanismo de "marca d'água alta" baseado no **ID**de solicitação . Esse ID é gerado automaticamente no SAP BW Open Hub Destination pelo DTP. O diagrama a seguir mostra este fluxo de trabalho:

![Gráfico de fluxo de fluxo de fluxo de fluxo de fluxo de fluxo de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

Na fábrica **de** dados Vamos começar a página, selecione **Criar pipeline a partir do modelo** para usar o modelo incorporado.

1. Procure **o SAP BW** para encontrar e selecionar a **cópia incremental do modelo SAP BW para Azure Data Lake Storage Gen2.** Este modelo copia dados para o Azure Data Lake Storage Gen2. Você pode usar um fluxo de trabalho semelhante para copiar para outros tipos de pia.

2. Na página principal do modelo, selecione ou crie as três conexões seguintes e selecione **Usar este modelo** no canto inferior direito da janela.

   - **Armazenamento Azure Blob**: Neste passo a passo, usamos o armazenamento Azure Blob para armazenar a marca d'água alta, que é o *ID de solicitação copiado máximo*.
   - **SAP BW Open Hub**: Esta é a fonte para copiar dados. Consulte o passo a passo da cópia completa anterior para obter uma configuração detalhada.
   - **Azure Data Lake Storage Gen2**: Este é o dissipador para copiar dados. Consulte o passo a passo da cópia completa anterior para obter uma configuração detalhada.

   ![Cópia incremental do modelo SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Este modelo gera um pipeline com as seguintes três atividades e as torna acorrentadas no sucesso: *Lookup,* *Copy Data*e *Web*.

   Vá para a guia **Parâmetros** do gasoduto. Você vê todas as configurações que você precisa fornecer.

   ![Cópia incremental da configuração SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique o nome da tabela Open Hub para copiar dados.

   - **Data_Destination_Container**: Especifique o destino do contêiner Azure Data Lake Storage Gen2 para copiar dados. Se o contêiner não existir, a atividade de cópia da Fábrica de Dados cria um durante a execução.
  
   - **Data_Destination_Directory**: Especifique o caminho da pasta no contêiner Azure Data Lake Storage Gen2 para copiar dados. Se o caminho não existir, a atividade de cópia da Fábrica de Dados criará um caminho durante a execução.
  
   - **HighWatermarkBlobContainer**: Especifique o recipiente para armazenar o valor da marca d'água.

   - **HighWatermarkBlobDirectory**: Especifique o caminho da pasta sob o recipiente para armazenar o valor da marca d'água.

   - **HighWatermarkBlobName**: Especifique o nome blob para `requestIdCache.txt`armazenar o valor da marca d'água alta, como . No armazenamento Blob, vá para o caminho correspondente de HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, como *container/path/requestIdCache.txt*. Crie uma bolha com conteúdo 0.

      ![Conteúdo do blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Neste modelo, usamos o WebActivity para chamar o Azure Logic Apps para definir o valor de marca d'água no armazenamento Blob. Ou, você pode usar o Azure SQL Database para armazená-lo. Use uma atividade de procedimento armazenada para atualizar o valor.

      Você deve primeiro criar um aplicativo lógico, como mostra a imagem a seguir. Em seguida, cole na **URL HTTP POST**.

      ![Configuração do App lógico](media/load-sap-bw-data/logic-app-config.png)

      1. Vá para o portal do Azure. Selecione um novo serviço **de Aplicativos Lógicos.** Selecione **+Blank Logic App** para ir ao **Logic Apps Designer**.

      2. Crie um gatilho de **Quando uma solicitação HTTP é recebida**. Especifique o corpo de solicitação HTTP da seguinte forma:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Adicione uma ação **Criar bolha.** Para **o caminho pasta** e nome **Blob,** use os mesmos valores que você configurou anteriormente em *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* e *HighWatermarkBlobName*.

      4. Clique em **Salvar**. Em seguida, copie o valor da **URL HTTP POST** para usar no pipeline da Fábrica de Dados.

4. Depois de fornecer os parâmetros do pipeline da Fábrica de Dados, selecione **Debug** > **Finish** para invocar uma execução para validar a configuração. Ou, **selecione Publicar** para publicar todas as alterações e, em seguida, **selecione Adicionar gatilho** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino do Hub Aberto SAP BW

Esta seção introduz a configuração do lado SAP BW para usar o conector SAP BW Open Hub na Fábrica de Dados para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configure a extração delta em SAP BW

Se você precisar de cópia histórica e cópia incremental ou apenas cópia incremental, configure a extração delta no SAP BW.

1. Crie o Destino do Hub Aberto. Você pode criar o OHD no SAP Transaction RSA1, que cria automaticamente o processo de transformação e transferência de dados necessários. Use as configurações a seguir:

   - **ObjectType**: Você pode usar qualquer tipo de objeto. Aqui, usamos **o InfoCube** como exemplo.
   - **Tipo de destino**: Selecione **tabela de banco de dados**.
   - **Tecla da tabela**: Selecione **tecla técnica**.
   - **Extração**: Selecione **Manter dados e inserir registros na tabela**.

   ![Criar caixa de diálogo delta SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Criar caixa de diálogo de extração SAP BW OHD delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Você pode aumentar o número de processos de trabalho SAP em execução paralela para o DTP:

   ![criar-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende o DTP em cadeias de processos.

   Um DTP delta para um cubo só funciona se as linhas necessárias não forem compactadas. Certifique-se de que a compactação do cubo BW não esteja funcionando antes do DTP para a tabela Open Hub. A maneira mais fácil de fazer isso é integrar o DTP em suas cadeias de processos existentes. No exemplo a seguir, o DTP (para o OHD) é inserido na cadeia de processos entre as etapas *Ajustar* (rollup agregado) e *Colapso* (compactação de cubos).

   ![Criar fluxograma da cadeia de processos SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configure a extração completa em SAP BW

Além da extração delta, você pode querer uma extração completa do mesmo Provedor de InfoProvedor SAP BW. Isso geralmente se aplica se você quiser fazer cópia completa, mas não incremental, ou você quiser [resincronizar a extração delta](#resync-delta-extraction).

Você não pode ter mais de um DTP para o mesmo OHD. Então, você deve criar um OHD adicional antes da extração delta.

![Criar SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para obter um OHD de carga completa, escolha opções diferentes das da extração delta:

- Em OHD: defina a opção **Extração** para **excluir dados e inserir registros**. Caso contrário, os dados serão extraídos muitas vezes quando você repetir o DTP em uma cadeia de processos BW.

- No DTP: Definir o **modo de extração** para **completo**. Você deve alterar o DTP criado automaticamente do **Delta** para **o Completo** imediatamente após a criação do OHD, como mostra esta imagem:

   ![Criar caixa de diálogo SAP BW OHD configurada para extração "Completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector BW Open Hub da Fábrica de Dados: Desative **Exclua a última solicitação**. Caso contrário, nada será extraído.

Você normalmente executa o DTP completo manualmente. Ou, você pode criar uma cadeia de processos para o DTP completo. É tipicamente uma cadeia separada que é independente de suas cadeias de processos existentes. Em ambos os casos, *certifique-se de que o DTP está concluído antes de iniciar a extração usando a cópia data factory*. Caso contrário, apenas dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar extração delta pela primeira vez

A primeira extração delta é tecnicamente uma *extração completa.* Por padrão, o conector SAP BW Open Hub exclui a última solicitação quando copia dados. Para a primeira extração delta, nenhum dado é extraído pela atividade de cópia da Fábrica de Dados até que um DTP subseqüente gere dados delta na tabela com um ID de solicitação separado. Há duas maneiras de evitar esse cenário:

- Desative a opção **Excluir última solicitação** para a primeira extração delta. Certifique-se de que o primeiro DTP delta está pronto antes de iniciar a extração delta da primeira vez.
-  Use o procedimento para resincronizar a extração delta, conforme descrito na próxima seção.

### <a name="resync-delta-extraction"></a>Extração delta de ressincronização

Os seguintes cenários alteram os dados em cubos SAP BW, mas não são considerados pelo DTP delta:

- Exclusão seletiva SAP BW (de linhas usando qualquer condição de filtro)
- SAP BW solicitar exclusão (de solicitações defeituosas)

Um SAP Open Hub Destination não é um destino de dados controlado pelo data mart (em todos os pacotes de suporte sap BW desde 2015). Assim, você pode excluir dados de um cubo sem alterar os dados no OHD. Em seguida, você deve resincronizar os dados do cubo com data factory:

1. Execute uma extração completa na Fábrica de Dados (usando um DTP completo em SAP).
2. Exclua todas as linhas na tabela Open Hub para o DTP delta.
3. Defina o status do Delta DTP **como Fetched**.

Depois disso, todas as extrações delta delta e delta da Fábrica de Dados funcionam como esperado.

Para definir o status do DTP delta como **Fetched,** você pode usar a seguinte opção para executar o DTP delta manualmente:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o suporte ao conector SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Conector SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
