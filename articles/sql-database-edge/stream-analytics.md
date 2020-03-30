---
title: Usando pacotes DaC de banco de dados SQL e trabalhos de Stream Analytics com a zure SQL Database Edge | Microsoft Docs
description: Saiba mais sobre o uso de trabalhos do Stream Analytics no SQL Database Edge
keywords: sql database edge, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384169"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Usando pacotes DaC do banco de dados SQL e trabalhos de Análise de Fluxo com vantagem de banco de dados SQL

O Azure SQL Database Edge Preview é um mecanismo de banco de dados relacional otimizado voltado para implantações de IoT e borda. Ele é baseado nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece recursos de processamento de desempenho, segurança e consulta líderes do setor. Juntamente com os recursos de gerenciamento de banco de dados relacionais líderes do setor do SQL Server, o Azure SQL Database Edge fornece recursos de streaming incorporados para análise em tempo real e processamento de eventos complexos.

O Azure SQL Database Edge também fornece uma implementação nativa do SqlPackage.exe que permite que você implante um pacote [DAC do Banco de Dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implantação do SQL Database Edge.

A Zure SQL Database Edge expõe `module twin's desired properties` dois parâmetros opcionais através da opção do módulo IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrição |
|------|-------------|
| SqlPackage | Uri de armazenamento Azure Blob para o arquivo *.zip que contém o pacote DAC do Banco de Dados SQL.
| ASAJobInfo | Uri de armazenamento Azure Blob para o trabalho ASA Edge. Para obter mais informações, consulte [Publicando um trabalho ASA Edge para SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Usando pacotes DaC do banco de dados SQL com borda de banco de dados SQL

Para usar um pacote DAC do Banco de Dados SQL (*.dacpac) com o SQL Database Edge, tome estas etapas:

1. Criar ou extrair um pacote DAC do Banco de Dados SQL. Consulte [Extrair um DAC de um banco de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote DAC para um banco de dados SQL Server existente.

2. Feche o *.dacpac e carregue-o em uma conta de armazenamento Do Zure Blob. Para obter mais informações sobre o upload de arquivos para o armazenamento Do Azure Blob, consulte [Upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso compartilhado para o arquivo zip usando o portal Azure. Para obter mais informações, consulte [O acesso do Delegado com assinaturas de acesso compartilhado (SAS)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo SQL Database Edge para incluir o URI de acesso compartilhado para o pacote DAC. Para atualizar o módulo SQL Database Edge, tome estas etapas:

    1. No portal Azure, vá para a sua implantação do IoT Hub.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página **IoT Edge,** encontre e selecione a borda IoT onde o módulo SQL Database Edge é implantado.

    4. Na página do **dispositivo ioT edge device,** selecione **Definir módulo**.

    5. Na página **Definir módulos,** **selecione Configurar** no módulo SQL Database Edge.

    6. No painel **Módulos personalizados de borda IoT,** selecione **Definir as propriedades desejadas do módulo twin**. Atualize as propriedades desejadas para `SQLPackage` incluir o URI para a opção, conforme mostrado no exemplo a seguir.

        > [!NOTE]
        > O Uri SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selecione **Salvar**.

    8. Na página **Definir módulos,** selecione **Next**.

    9. Na página **Definir módulos,** selecione **'Avançar'** e, em seguida, **Enviar**.

5. Após a atualização do módulo, o arquivo do pacote DAC é baixado, descompactado e implantado na instância SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Usando trabalhos de streaming com sql database edge

O Azure SQL Database Edge tem uma implementação nativa do tempo de execução da análise de fluxo. Essa implementação permite que você crie um trabalho de borda do Azure Stream Analytics e implante esse trabalho como um trabalho de streaming sql database Edge. Para criar um trabalho de borda do Stream Analytics, complete essas etapas:

1. Vá para o portal Azure usando a [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)de visualização . Essa URL de visualização permite configurar a saída do Banco de Dados SQL para um trabalho de borda do Stream Analytics.

2. Crie um novo **azure Stream Analytics no trabalho ioT Edge.** Escolha o ambiente de hospedagem que tem como alvo **o Edge**.

3. Defina uma entrada e saída para o trabalho do Azure Stream Analytics. Cada saída SQL, que você vai configurar aqui, está vinculada a uma única tabela no banco de dados. Se você precisar transmitir dados para várias tabelas, você precisará criar várias saídas de banco de dados SQL. Você pode configurar as saídas SQL para apontar para diferentes bancos de dados.

    **Entrada**. Escolha o EdgeHub como a entrada para o trabalho de borda e forneça as informações de recurso.

    **Saída**. Selecione SQL Database como saída. Selecione **Fornecer configurações de banco de dados SQL manualmente**. Forneça os detalhes de configuração para o banco de dados e tabela.

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do alias de saída.|
    |Banco de dados | Nome do banco de dados SQL. Ele precisa ser um nome válido de um banco de dados que existe na instância SQL Database Edge.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância SQL. Para uma implantação sql database edge, você pode usar **tcp:.,1433** para o nome do servidor.|
    |Nome de Usuário | Conta de login SQL que tem acesso ao leitor de dados e ao leitor de dados ao banco de dados que você especificou anteriormente.|
    |Senha | Senha para a conta de login SQL que você especificou anteriormente.|
    |Tabela | Nome da tabela que será saída para o trabalho de streaming.|
    |Particionamento herdar| Habilita herdar o esquema de particionamento da etapa ou entrada de consulta anterior. Quando essa opção estiver ativada, você pode esperar ver um melhor throughput quando você gravar em uma tabela baseada em disco e ter uma topologia totalmente paralela para o seu trabalho.|
    |Tamanho do lote| O número máximo de registros enviados a cada transação de inserção em massa.|

    Aqui está uma configuração de entrada/saída de amostra:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Para obter mais informações sobre o adaptador de saída SQL para O Azure Stream Analytics, consulte [a saída do Azure Stream Analytics para o Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Defina a consulta de trabalho ASA para o trabalho de borda. Esta consulta deve usar os aliases de entrada/saída definidos como os nomes de entrada e saída na consulta. Para obter mais informações, consulte [a referência do idioma de consulta de stream analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Defina as configurações da conta de armazenamento para o trabalho de borda. A conta de armazenamento é usada como alvo de publicação para o trabalho de borda.

6. Em **Configure,** **selecione Publicar**e selecione o botão **Publicar.** Salve o Uri SAS para uso com o módulo SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Implantar o trabalho de borda do Stream Analytics na borda do banco de dados SQL

Para implantar o trabalho de streaming no módulo SQL Database Edge, atualize a configuração do módulo SQL Database Edge para incluir o Uri SAS para o trabalho de streaming da etapa anterior. Para atualizar o módulo SQL Database Edge:

1. No portal Azure, vá para a sua implantação do IoT Hub.

2. No painel esquerdo, selecione **IoT Edge**.

3. Na página **IoT Edge,** encontre e selecione a borda IoT onde o módulo SQL Database Edge é implantado.

4. Na página do **dispositivo ioT edge device,** selecione **Definir módulo**.

5. Na página **Definir módulos,** **selecione Configurar** no módulo SQL Database Edge.

6. No painel **Módulos personalizados de borda IoT,** selecione **Definir as propriedades desejadas do módulo twin**. Atualize as propriedades desejadas para `ASAJobInfo` incluir o URI para a opção, conforme mostrado no exemplo a seguir.

    > [!NOTE]
    > O Uri SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selecione **Salvar**.

8. Na página **Definir módulos,** selecione **Next**.

9. Na página **Definir módulos,** selecione **'Avançar'** e, em seguida, **Enviar**.

10. Após a atualização do módulo, o arquivo de trabalho de análise de fluxo é baixado, descompactado e implantado na instância SQL Database Edge.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre preços e disponibilidade, consulte [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Solicite ativar o Azure SQL Database Edge para sua assinatura.
- Para começar, consulte [Implantar o SQL Database Edge através do portal Azure](deploy-portal.md).
