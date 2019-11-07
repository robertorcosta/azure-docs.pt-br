---
title: Sincronizar dados do Banco de Dados SQL do Azure no Edge usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre a sincronização de dados entre o Banco de Dados SQL do Azure no Edge e o armazenamento de Blobs do Azure
keywords: banco de dados sql no edge,sincronização de dados no banco de dados sql no edge, data factory do banco de dados sql no edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509200"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Tutorial: Sincronizar dados do Banco de Dados SQL no Edge para o Armazenamento de Blobs do Azure usando o Azure Data Factory

Neste tutorial, você usa o Azure Data Factory para sincronizar dados de maneira incremental de uma tabela em uma instância do Banco de Dados SQL do Azure no Edge para um Armazenamento de Blobs do Azure.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não criou um banco de dados ou uma tabela em sua implantação do Banco de Dados SQL do Azure no Edge, use um dos seguintes métodos para criar um:

* Use o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou o [Azure Data Studio](/sql/azure-data-studio/download/) para se conectar ao Banco de dados SQL no Edge e executar um script SQL para criar o banco de dados e a tabela.
* Crie um banco de dados SQL e uma tabela usando [SQLCMD](/sql/tools/sqlcmd-utility/), conectando-se diretamente ao módulo do Banco de Dados SQL no Edge. Para saber mais, confira [Conectar-se ao Mecanismo de Banco de Dados usando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Use SqlPackage.exe para implantar um arquivo dacpac no contêiner do Banco de Dados SQL no Edge. Isso pode ser automatizado especificando o URI do arquivo SqlPackage como parte da configuração das propriedades desejadas dos módulos ou usando diretamente a ferramenta de cliente SqlPackage.exe para implantar um dacpac no Banco de Dados SQL no Edge.

    Para baixar o sqlpackage, consulte [Baixar e instalar o sqlpackage](/sql/tools/sqlpackage-download/). Os comandos de amostra para SqlPackage.exe são fornecidos a seguir, mas verifique a documentação do sqlpackage para obter mais informações.

    **Criar dacpac**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Aplicar dacpac**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Criar uma tabela SQL e um procedimento para armazenar e atualizar os níveis de marca-d'água

A tabela de marca-d'água é usada para armazenar o último carimbo de data/hora até o qual os dados foram sincronizados com o armazenamento do Azure. O procedimento armazenado Transact-SQL (T-SQL) é usado para atualizar a tabela de marca-d'água após cada sincronização. 

Execute os seguintes comandos na instância do Banco de Dados SQL no Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Criar um fluxo de trabalho de Data Factory

Nesta seção, você cria um pipeline do Azure Data Factory para sincronizar os dados de uma tabela em um Banco de Dados SQL do Azure no Edge para um Armazenamento de Blobs do Azure.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Criar um Data Factory usando a interface do usuário do Azure Data Factory

Crie um Data Factory usando as instruções fornecidas neste [tutorial](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Criar um pipeline do Data Factory

1. Na página **Introdução** da interface do usuário do Data Factory, clique no bloco **Criar Pipeline**.

    ![Data Factory - criar pipeline](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **Geral** da janela **Propriedades** para o pipeline, digite o nome **PeriodicSync**.

3. Adicione a atividade **Pesquisa** para obter o antigo valor da marca-d'água. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** à superfície do designer de pipeline. Altere o nome da atividade para *OldWatermark*.

    ![pesquisa de marca-d'água antiga](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Alterne para a guia **Configurações** e selecione **+ Novo** para o **Conjunto de Dados de Origem**. Nesta etapa, você cria um conjunto de dados para representar os dados na watermarktable. Esta tabela contém a marca d'água antiga que foi usada na operação de cópia anterior.

5. Na janela **Novo Conjunto de Dados**, selecione **SQL Server do Azure** e clique em **Continuar**.  

6. Na janela **Definir Propriedades** para o conjunto de dados, insira *WatermarkDataset* como Nome.

7. Para **Serviço Vinculado**, selecione **Novo** e execute as seguintes etapas:

    1. Insira *SQLDBEdgeLinkedService* como **Nome**.

    2. Insira os detalhes do servidor do Banco de Dados SQL no Edge para **Nome do Servidor**.

    3. Selecione o **Nome do Banco de Dados** na lista suspensa.

    4. Insira o **Nome de Usuário** e a **Senha**.

    5. Para testar a conexão à instância do Banco de Dados SQL no Edge, clique em **Testar Conectividade**.

    6. Selecione **Criar**.

    ![criar serviço vinculado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **Ok**

8. Na guia **Configurações**, selecione **Editar**.

9. Na guia **Conexão**, selecione *[dbo].[watermarktable]* para **Tabela**. Se você quiser visualizar os dados na tabela, selecione **Visualizar Dados**.

10. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline n modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de **Pesquisa**, confirme se **WatermarkDataset** está selecionado para o campo **Conjunto de Dados de Origem**.

11. Na caixa de ferramentas **Atividades**, expanda **Geral**, arraste e solte outra atividade de **Pesquisa** à superfície do designer de pipeline e defina o nome como **NewWatermark** na guia **Geral** da janela Propriedades. Esta atividade de Pesquisa obtém o novo valor de marca d'água da tabela com os dados de origem a serem copiados para o destino.

12. Na janela Propriedades da segunda atividade de **Pesquisa**, alterne para a guia **Configurações** e selecione **Novo** para criar um conjunto de dados que aponte para a tabela de origem que contém o novo valor de marca-d'água.

13. Na janela **Novo Conjunto de Dados**, selecione a instância do Banco de Dados SQL no Edge e selecione **Continuar**.

    1. Na janela **Definir propriedades**, insira **SourceDataset** como **Nome**. Selecione *SQLDBEdgeLinkedService* como Serviço vinculado.

    2. Selecione a ***tabela que você deseja sincronizar*** como Tabela. Você também pode especificar uma consulta para esse conjunto de dados, conforme mencionado mais adiante no tutorial. A consulta tem precedência sobre a tabela que você especificar nesta etapa.

    3. Selecione **OK**.

14. Alterne para o editor de pipeline selecionando a guia Pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de **Pesquisa**, confirme se **SourceDataset** está selecionado para o campo **Conjunto de dados de origem**.

15. Selecione **Consulta** para o campo **Usar Consulta** e digite a seguinte consulta depois de atualizar o nome da tabela na consulta: você está selecionando apenas o valor máximo de carimbo de data/hora para a tabela. Verifique se você também marcou **Somente a primeira linha**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![selecionar consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Na caixa de ferramentas **Atividades**, expanda **Mover e Transformar**, arraste e solte a atividade de **Cópia** da caixa de ferramentas Atividades e defina o nome para **IncrementalCopy**.

17. Conecte ambas as atividades de **Pesquisa** à atividade de **Cópia** arrastando o **botão verde** anexado às atividades de **Pesquisa** para a atividade de **Cópia**. Solte o botão do mouse quando a cor da borda da atividade de Cópia ficar azul.

18. Selecione a atividade de **Cópia** e confirme se você vê as propriedades da atividade na janela **Propriedades**.

19. Alterne para a guia **Fonte** na janela **Propriedades** e execute as seguintes etapas:

    1. Selecione **SourceDataset** para o campo **Conjunto de dados de origem**.

    2. Selecione a **Consulta** para o campo **Usar consulta**.

    3. Insira a consulta SQL no campo **Consulta**. Exemplo de consulta abaixo

    4. Consulta SQL:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Alterne para a guia **Coletor** e selecione **+ Novo** para o campo **Conjunto de Dados do Coletor**.

21. Neste tutorial, o armazenamento de dados do coletor é do tipo **Armazenamento de Blobs do Azure**. Selecione **Armazenamento de Blobs do Azure** e clique em **Continuar** na janela **Novo Conjunto de Dados**.

22. Na página **Selecionar Formato**, selecione o tipo de formato dos dados e clique em **Continuar**.

23. Na janela **Definir Propriedades**, insira **SinkDataset** como Nome. Para Serviço Vinculado, selecione **+ Novo**. Nesta etapa, você cria uma conexão (serviço vinculado) ao **Armazenamento de Blobs do Azure**.

24. Na janela **Novo Serviço Vinculado (Armazenamento de Blobs do Azure)** , execute as seguintes etapas:

    1. Insira *AzureStorageLinkedService* como Nome.

    2. Selecione sua conta de Armazenamento do Microsoft Azure como o **Nome da Conta de Armazenamento** com sua assinatura do Azure.

    3. Teste a **Conexão** e selecione **Concluir**.

25. Na janela **Definir Propriedades**, confirme se *AzureStorageLinkedService* está selecionado como **Serviço vinculado**. Selecione **Criar** e **OK**.

26. Na guia **Coletor**, selecione **Editar**.

27. Vá para a guia **Conexão** de *SinkDataset* e siga estas etapas:

    1. Para o campo **Caminho do Arquivo**, insira *asdedatasync/incrementalcopy*, em que **adftutorial** é o nome do contêiner de blob e **incrementalcopy** é o nome da pasta. Crie o contêiner caso ele não exista ou defina-o com o nome de um contêiner existente. O Azure Data Factory cria automaticamente a pasta de saída *incrementalcopy* se ela não existir. Você também pode usar o botão **Procurar** para o **Caminho de arquivo** para navegar até uma pasta em um contêiner de blob.

    2. Para a parte referente ao **Arquivo** do campo **Caminho de Arquivo**, selecione **Adicionar Conteúdo Dinâmico [Alt+P]** e, em seguida, *insira @CONCAT('Incremental-', pipeline().RunId, '.txt')* na janela aberta. Em seguida, selecione **Concluir**. Neste tutorial, o nome do arquivo é gerado dinamicamente pelo uso da expressão. Cada execução de pipeline possui uma ID exclusiva. A atividade de Cópia usa a ID de execução para gerar o nome do arquivo.

28. Alterne para o editor de **pipeline** selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda.

29. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Procedimento armazenado** da caixa de ferramentas **Atividades** para a superfície de designer do pipeline. **Conecte** a saída verde (Bem-sucedida) da atividade de **Cópia** à atividade de **Procedimento armazenado**.

30. Selecione **Atividade de Procedimento Armazenado** no designer de pipeline, altere seu nome para *SPtoUpdateWatermarkActivity*.

31. Alterne para a guia **Conta SQL** e selecione *SQLDBEdgeLinkedService* como **Serviço Vinculado**.

32. Alterne para a guia **Procedimento armazenado** e execute as etapas a seguir:

    1. Para o **Nome do Procedimento Armazenado**, selecione *[dbo].[usp_write_watermark]* .

    2. Para especificar valores para os parâmetros de procedimento armazenado, selecione Importar Parâmetros e insira os seguintes valores para os parâmetros:

    |NOME|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Cadeia de caracteres|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Para validar as configurações de pipeline, selecione **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar a janela **Relatório de Validação do Pipeline** selecione **>>** .

34. Publique as entidades (serviços vinculados, conjuntos de dados e pipelines) para o serviço de Azure Data Factory selecionando o botão **Publicar tudo**. Aguarde até que você veja a mensagem informando que a publicação foi bem-sucedida.

## <a name="trigger-a-pipeline-on-schedule"></a>Disparar um Pipeline em um cronograma

1. Na barra de ferramentas do pipeline, clique em **Adicionar Gatilho**, selecione **Novo/Editar** e **+ Novo**.

2. Nomeie o gatilho com *HourlySync*, escolha **Tipo** como Cronograma e defina **Recorrência** para a cada 1 hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **Disparar Agora**.

6. Alterne para a guia **Monitorar** à esquerda. Você pode ver o status da execução do pipeline disparado pelo gatilho manual. Selecione o botão **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Próximas etapas

O pipeline do Azure Data Factory neste tutorial copia os dados de uma tabela na instância do Banco de Dados SQL no Edge para um local no Armazenamento de Blobs do Azure a cada hora. Percorra os [tutoriais](../data-factory/tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários.
