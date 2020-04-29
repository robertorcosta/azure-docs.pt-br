---
title: Usar Azure Stream Analytics
description: Dicas para usar Azure Stream Analytics com seus data warehouse no Azure Synapse para desenvolver soluções em tempo real.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633291"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Usar Azure Stream Analytics com o Azure Synapse Analytics

A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Neste artigo, você aprenderá a usar seu data warehouse como um coletor de saída para seus trabalhos de Azure Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Azure Stream Analytics trabalho – para criar um trabalho de Azure Stream Analytics, siga as etapas no tutorial introdução ao [uso de Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para:  

    1. Criar uma entrada de Hub de eventos
    2. Configurar e iniciar o aplicativo gerador de evento
    3. Provisionar um trabalho de análise de fluxo
    4. Especifique a entrada e a consulta do trabalho
* Data warehouse do pool SQL do Azure Synapse – para criar um novo data warehouse, siga as etapas no guia de [início rápido para criar um novo data warehouse](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Especifique a saída de streaming para apontar para seu data warehouse

### <a name="step-1"></a>Etapa 1

No portal do Azure, vá para seu trabalho de Stream Analytics e clique em **saídas** no menu **topologia de trabalho** .

### <a name="step-2"></a>Etapa 2

Clique no botão **Adicionar** e escolha **banco de dados SQL** no menu suspenso.

![Escolher banco de dados SQL](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Etapa 3

Insira os valores a seguir:

* *Alias de saída*: Insira um nome amigável para esta saída de trabalho.
* *Assinatura*:
  * Se o data warehouse estiver na mesma assinatura que o trabalho de Stream Analytics, clique em ***Selecionar Banco de dados SQL em suas assinaturas***.
  * Se o banco de dados estiver em uma assinatura diferente, clique em fornecer configurações do banco de dados SQL manualmente.
* *Banco de dados*: selecione o banco de dados de destino na lista suspensa.
* *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: forneça a senha da conta de usuário especificada.
* *Tabela*: especifique o nome da tabela de destino no banco de dados.
* Clique no botão **salvar**

![Formulário do banco de dados SQL concluído](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Etapa 4

Antes de executar um teste, você precisará criar a tabela em seu data warehouse.  Execute o seguinte script de criação de tabela usando SQL Server Management Studio (SSMS) ou sua opção de ferramenta de consulta.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Etapa 5

No portal do Azure para o trabalho de Stream Analytics, clique no nome do trabalho.  Clique no botão ***testar*** no painel ***detalhes de saída*** .

![Botão testar em detalhes](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) do outpout quando a conexão com o banco de dados for realizada com sucesso, você verá uma notificação no Portal.

### <a name="step-6"></a>Etapa 6

Clique no menu ***consulta*** em ***topologia do trabalho*** e altere a consulta para inserir dados na saída do fluxo que você criou.  Clique no botão ***testar consulta selecionada*** para testar sua consulta.  Clique no botão ***Salvar consulta*** quando o teste de consulta for bem-sucedido.

![Salvar consulta](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Etapa 7

Inicie o trabalho de Azure Stream Analytics.  Clique no botão ***Iniciar*** no menu ***visão geral*** .

![Iniciar trabalho do Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Clique no botão ***Iniciar*** no painel Iniciar trabalho.

![Clique em Iniciar](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da integração, consulte [integrar outros serviços](sql-data-warehouse-overview-integrate.md).
Para obter mais dicas de desenvolvimento, consulte [decisões de design e técnicas de codificação para data warehouses](sql-data-warehouse-overview-develop.md).
