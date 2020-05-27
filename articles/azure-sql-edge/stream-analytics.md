---
title: Usar trabalhos do Edge do Azure Stream Analytics com o SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre como usar trabalhos do Stream Analytics no SQL do Azure no Edge (versão prévia)
keywords: SQL no Edge, Stream Analytics
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594515"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Usar trabalhos do Azure Stream Analytics com o SQL no Edge

O SQL do Azure no Edge (versão prévia) é um mecanismo de banco de dados relacional otimizado projetado para implantações de IoT e de borda. Ele foi criado com base nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server, que fornece funcionalidades de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com as funcionalidades de gerenciamento de banco de dados relacional líderes do setor do SQL Server, o SQL do Azure no Edge fornece a funcionalidade de streaming interno para análise em tempo real e processamento de eventos complexos.

O SQL do Azure no Edge tem uma implementação nativa do runtime do Stream Analytics. Essa implementação permite que você crie um trabalho do Edge do Azure Stream Analytics e implante esse trabalho como um trabalho de streaming do SQL no Edge. Os trabalhos do Azure Stream Analytics podem ser implantados no SQL no Edge usando o parâmetro ASAJobInfo exposto por meio da opção `module twin's desired properties` do módulo do SQL no Edge:

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
| SqlPackage | O URI de armazenamento de blobs do Azure para o arquivo *.zip que contém o pacote de DAC do Banco de Dados SQL.
| ASAJobInfo | URI de armazenamento de blobs do Azure para o trabalho do ASA no IoT Edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Criar um trabalho do Edge do Azure Stream Analytics

1. Vá para o portal do Azure.

2. Crie um trabalho do **Azure Stream Analytics no IoT Edge**. Escolha o ambiente de hospedagem que direciona o **Edge**.

3. Defina uma entrada e uma saída para o trabalho do Azure Stream Analytics. Cada saída do SQL, que você vai configurar aqui, está vinculada a uma única tabela no banco de dados. Se precisar transmitir os dados para várias tabelas, você precisará criar várias saídas do Banco de Dados SQL. Você pode configurar as saídas do SQL para apontar para bancos de dados diferentes.

    **Entrada**. Escolha o EdgeHub como a entrada para o trabalho de borda e forneça as informações do recurso.

    **Saída**. Selecione o Banco de Dados SQL como a saída. Selecione **Fornecer configurações do Banco de Dados SQL manualmente**. Forneça os detalhes de configuração para o banco de dados e a tabela.

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do alias da saída.|
    |Banco de dados | Nome do banco de dados SQL. Precisa ser um nome válido de um banco de dados que exista na instância do SQL no Edge.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância do SQL. Para uma implantação do SQL no Edge, você pode usar **tcp:.,1433** para o nome do servidor.|
    |Nome de Usuário | Conta de entrada do SQL que tem o acesso de leitor e gravador de dados ao banco de dados especificado anteriormente.|
    |Senha | Senha para a conta de entrada do SQL especificada anteriormente.|
    |Tabela | Nome da tabela que será a saída do trabalho de streaming.|
    |Herdar particionamento| Permite herdar o esquema de particionamento da entrada ou etapa de consulta anterior. Quando essa opção está habilitada, você pode esperar ver uma melhor taxa de transferência quando grava uma tabela baseada em disco e tem uma topologia totalmente paralela para seu trabalho.|
    |Tamanho do lote| O número máximo de registros enviados com cada transação de inserção em massa.|

    Esta é uma configuração de exemplo de entrada/saída:

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
    > Para obter mais informações sobre o adaptador de saída do SQL para Azure Stream Analytics, confira [Saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Defina a consulta do trabalho do ASA para o trabalho de borda. Essa consulta deve usar os aliases de entrada/saída definidos como os nomes de entrada e saída na consulta. Para obter mais informações, confira a [Referência da linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Defina as configurações da conta de armazenamento para o trabalho de borda. A conta de armazenamento é usada como o destino de publicação do trabalho de borda.

6. Em **Configurar**, selecione **Publicar** e o botão **Publicar**. Salve o URI do SAS para uso com o módulo do SQL no Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Implantar um trabalho do Edge do Azure Stream Analytics no SQL no Edge

Para implantar o trabalho de streaming no módulo do SQL no Edge, atualize a configuração do módulo do SQL no Edge para incluir o URI do SAS para o trabalho de streaming da etapa anterior. Para atualizar o módulo do SQL no Edge:

1. No portal do Azure, acesse sua implantação do Hub IoT.

2. No painel esquerdo, selecione **IoT Edge**.

3. Na página **IoT Edge**, localize e selecione o IoT Edge em que o módulo SQL no Edge é implantado.

4. Na página de dispositivos **Dispositivo do IoT Edge**, selecione **Definir Módulo**.

5. Na página **Definir módulos**, selecione **Configurar** no módulo do SQL no Edge.

6. No painel **Módulos Personalizados do IoT Edge**, selecione **Definir propriedades desejadas do módulo gêmeo**. Atualize as propriedades desejadas para incluir o URI da opção `ASAJobInfo`, conforme mostrado no exemplo a seguir.

    > [!NOTE]
    > O URI do SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Clique em **Salvar**.

8. Na página **Definir módulos**, selecione **Avançar**.

9. Na página **Definir módulos**, selecione **Avançar** e **Enviar**.

10. Após a atualização do módulo, o arquivo de trabalho do Stream Analytics será baixado, descompactado e implantado na instância do SQL no Edge.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL no Edge por meio do portal do Azure](deploy-portal.md).

- [Transmitir dados](stream-data.md)

- [Machine learning e IA com o ONNX no SQL no Edge (versão prévia)](onnx-overview.md)
