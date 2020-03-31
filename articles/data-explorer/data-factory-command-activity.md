---
title: Use comandos de controle do Azure Data Explorer na fábrica de dados do Azure
description: Neste tópico, use comandos de controle do Azure Data Explorer na Fábrica de Dados do Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264498"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Use a atividade de comando da Fábrica de Dados do Azure para executar os comandos de controle do Azure Data Explorer

[O Azure Data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado em nuvem que permite executar uma combinação de atividades sobre os dados. Use o ADF para criar fluxos de trabalho orientados a dados para orquestrar e automatizar a movimentação de dados e a transformação de dados. A atividade **do Azure Data Explorer Command** na Fábrica de Dados Do Azure permite executar [comandos de controle do Azure Data Explorer](/azure/kusto/concepts/#control-commands) dentro de um fluxo de trabalho ADF. Este artigo ensina como criar um pipeline com uma atividade de pesquisa e atividade forEach contendo uma atividade de comando do Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md)
* Uma fonte de dados.
* [Uma fábrica de dados](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

1. Selecione a ferramenta **Lápis Autor.** 
1. Crie um novo pipeline **+** selecionando e selecione **Pipeline** a partir do drop-down.

   ![criar novo pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Criar uma atividade de lookup

Uma [atividade de pesquisa](/azure/data-factory/control-flow-lookup-activity) pode recuperar um conjunto de dados de qualquer fonte de dados suportada pelo Azure Data Factory. A saída da atividade de pesquisa pode ser usada em uma atividade ForEach ou outra.

1. No painel **Atividades,** em **Geral,** selecione a atividade **De procurar.** Arraste e jogue na tela principal à direita.
 
    ![selecionar atividade de procuração](media/data-factory-command-activity/select-activity.png)

1. A tela agora contém a atividade de pesquisa criada. Use as guias abaixo da tela para alterar quaisquer parâmetros relevantes. Em **geral,** renomeie a atividade. 

    ![editar atividade de procurar](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Clique na área de lona vazia para visualizar as propriedades do gasoduto. Use a guia **Geral** para renomear o pipeline. Nosso oleoduto é chamado *pipeline-4-docs.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Crie um conjunto de dados do Azure Data Explorer em atividade de pesquisa

1. Em **Configurações,** selecione o conjunto de **dados**Azure Data Explorer Source pré-criado ou selecione **+ Novo** para criar um novo conjunto de dados.
 
    ![adicionar conjunto de dados nas configurações de pesquisa](media/data-factory-command-activity/lookup-settings.png)

1. Selecione o conjunto de dados **Do Azure Data Explorer (Kusto)** na janela **Novo conjunto de dados.** Selecione **Continuar** a adicionar o novo conjunto de dados.

   ![selecionar novo conjunto de dados](media/data-factory-command-activity/select-new-dataset.png) 

1. Os novos parâmetros do conjunto de dados do Azure Data Explorer são visíveis em **Configurações**. Para atualizar os parâmetros, selecione **Editar**.

    ![configurações de pesquisa com conjunto de dados do Azure Data Explorer](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. A nova guia **AzureDataExplorerTable** é aberta na tela principal. 
    * Selecione **Geral** e edite o nome do conjunto de dados. 
    * Selecione **Conexão** para editar as propriedades do conjunto de dados. 
    * Selecione o **serviço Linked** na queda ou selecione **+ Novo** para criar um novo serviço vinculado.

    ![Editar propriedades do conjunto de dados do Azure Data Explorer](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Ao criar um novo serviço vinculado, a página **New Linked Service (Azure Data Explorer)** é aberta:

    ![Novo serviço vinculado do ADX](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selecione **Nome** para serviço vinculado ao Azure Data Explorer. Adicionar **descrição,** se necessário.
   * Em **Conectar via tempo de execução de integração,** altere as configurações atuais, se necessário. 
   * No **método de seleção de contas,** selecione seu cluster usando um dos dois métodos: 
        * Selecione o botão de rádio **por assinatura do Azure** e selecione sua conta **de assinatura do Azure.** Em seguida, selecione seu **Cluster**. Observe que a lista de parada será apenas clusters que pertencem ao usuário.
        * Em vez disso, **selecione Digite manualmente** o botão de rádio e digite seu **Ponto final** (URL de cluster).
    * Especificar o **Inquilino**.
    * Digite **o ID principal do serviço**. O ID principal deve ter as permissões adequadas, de acordo com o nível de permissão exigido pelo comando que está sendo usado.
    * Selecione o botão **principal do serviço** e digite a **tecla principal do serviço**.
    * Selecione seu banco de **dados** no menu suspenso. Alternativamente, selecione **Editar** caixa de seleção e digite o nome do seu banco de dados.
    * Selecione **Conexão de teste** para testar a conexão de serviço vinculada que você criou. Se você puder se conectar à sua configuração, uma conexão de marca de seleção verde será **exibida com sucesso.**
    * Selecione **Concluir** para concluir a criação de serviços vinculados.

1. Depois de configurar um serviço vinculado, em **AzureDataExplorerTable** > **Connection**, adicione o nome **da tabela.** Selecione **Os dados de visualização**para garantir que os dados sejam apresentados corretamente.

   Seu conjunto de dados está pronto e você pode continuar editando seu pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Adicione uma consulta à sua atividade de consulta

1. Em **configurações de pipeline-4-docs** > **Settings** adicione uma consulta na caixa de texto **consulta,** por exemplo:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Altere o **tempo de consulta** ou **sem truncação** e somente propriedades **da primeira linha,** conforme necessário. Nesse fluxo, mantemos o **tempo de saída** padrão da consulta e deschecamos as caixas de seleção. 

    ![Configurações finais da atividade de procuração](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Crie uma atividade para cada 

A atividade [For-Each](/azure/data-factory/control-flow-for-each-activity) é usada para iterar sobre uma coleção e executar atividades especificadas em um loop. 

1. Agora você adiciona uma atividade for-Cada ao pipeline. Essa atividade processará os dados retornados da atividade Procurar. 
    * No painel **Atividades,** em **Iteração & Condicional,** selecione a atividade **ForEach** e arraste e solte-a na tela.
    * Desenhe uma linha entre a saída da atividade De pesquisa e a entrada da atividade ForEach na tela para conectá-las.

        ![Atividade ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selecione a atividade ForEach na tela. Na guia **Configurações** abaixo:
    * Verifique a caixa de seleção **seqüencial** para obter um processamento seqüencial dos resultados da Pesquisa ou deixe-a sem controle para criar processamento paralelo.
    * Definir **contagem de lotes**.
    * Em **Itens,** forneça a seguinte referência ao valor de saída: * @activity('Lookup1').output.value*

       ![Configurações da atividade ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Crie uma atividade do Azure Data Explorer Command dentro da atividade ForEach

1. Clique duas vezes na atividade ForEach na tela para abri-la em uma nova tela para especificar as atividades dentro do ForEach.
1. No painel **Atividades,** no **Azure Data Explorer,** selecione a atividade do **Comando Azure Data Explorer** e arraste e solte-o na tela.

    ![Atividade de comando do Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Na guia **Conexão,** selecione o mesmo Serviço Vinculado criado anteriormente.

    ![guia de conexão de atividade do explorador de dados azure](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na guia **Comando,** forneça o seguinte comando:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    O **Comando** instrui o Azure Data Explorer a exportar os resultados de uma determinada consulta para um armazenamento blob, em um formato compactado. Ele funciona assíncronamente (usando o modificador assíncrono).
    A consulta aborda a coluna de banco de dados de cada linha no resultado da atividade Procurar. O **tempo de intervalo do Comando** pode ser deixado inalterado.

    ![atividade de comando](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > A atividade de comando tem os seguintes limites:
    > * Limite de tamanho: tamanho de resposta de 1 MB
    > * Limite de tempo: 20 minutos (padrão), 1 hora (máximo).
    > * Se necessário, você pode anexar uma consulta ao resultado usando [o AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), para reduzir o tamanho/tempo resultante.

1.  Agora o oleoduto está pronto. Você pode voltar para a exibição principal do pipeline clicando no nome do pipeline.

    ![Pipeline de comando do Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selecione **Depurar** antes de publicar o pipeline. O progresso do gasoduto pode ser monitorado na guia **Saída.**

    ![azure data explorer saída de atividade de atividade](media/data-factory-command-activity/command-activity-output.png)

1. Você pode **publicar tudo** e, em seguida, **adicionar gatilho** para executar o pipeline. 

## <a name="control-command-outputs"></a>Saídas de comando de controle

A estrutura da saída de atividade de comando é detalhada abaixo. Esta saída pode ser usada pela próxima atividade no pipeline.

### <a name="returned-value-of-a-non-async-control-command"></a>Valor retornado de um comando de controle não assíncrono

Em um comando de controle não assíncrono, a estrutura do valor retornado é semelhante à estrutura do resultado da atividade de Aparência. O `count` campo indica o número de registros devolvidos. Um campo `value` de matriz fixa contém uma lista de registros. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Valor retornado de um comando de controle de async

Em um comando de controle assíncrono, a atividade pesquisa a tabela de operações nos bastidores, até que a operação assíncrona seja concluída ou a tempos de saída. Portanto, o valor devolvido conterá `.show operations OperationId` o resultado daquele determinado imóvel **OperacionalId.** Verifique os valores das propriedades **Estado** e **Status,** para verificar a conclusão bem sucedida da operação.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [copiar dados para o Azure Data Explorer usando o Azure Data Factory](data-factory-load-data.md).
* Saiba mais sobre o uso do [modelo Azure Data Factory para cópia em massa do banco de dados para o Azure Data Explorer](data-factory-template.md).
