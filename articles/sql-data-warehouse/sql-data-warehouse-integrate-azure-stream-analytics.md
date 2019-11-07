---
title: Usar Azure Stream Analytics
description: Dicas para usar o Stream Analytics do Azure com o Azure SQL Data Warehouse para desenvolver as soluções.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685725"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar o Stream Analytics do Azure com o SQL Data Warehouse
A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure][Introduction to Azure Stream Analytics]. Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure][Get started using Azure Stream Analytics] .

Neste artigo, você aprenderá a usar seu banco de dados do Azure SQL Data Warehouse como um coletor de saída para suas tarefas do Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, realize as etapas a seguir no tutorial [Introdução ao uso do Stream Analytics do Azure][Get started using Azure Stream Analytics] .  

1. Criar uma entrada de Hub de eventos
2. Configurar e iniciar o aplicativo gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada e a consulta do trabalho

Em seguida, crie um banco de dados do SQL Data Warehouse do Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique a saída do trabalho: banco de dados do SQL Data Warehouse do Azure
### <a name="step-1"></a>Etapa 1
Em seu trabalho de Stream Analytics clique em **saída** na parte superior da página e, em seguida, clique em **Adicionar**.

### <a name="step-2"></a>Etapa 2
Selecione banco de dados SQL.

### <a name="step-3"></a>Etapa 3
Insira os seguintes valores na próxima página:

* *Alias de saída*: insira um nome amigável para essa saída de trabalho.
* *Assinatura*:
  * se o seu banco de dados do SQL Data Warehouse estiver na mesma assinatura que o trabalho do Stream Analytics, selecione Usar Banco de Dados SQL da Assinatura Atual.
  * Se o seu banco de dados estiver em uma assinatura diferente, selecione Usar Banco de Dados SQL de Outra Assinatura.
* *Banco de dados*: especifique o nome de um banco de dados de destino.
* *Nome do servidor*: especifique o nome do servidor do banco de dados que você acabou de especificar. Você pode usar o portal do Azure para encontrá-lo.

![][server-name]

* *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: forneça a senha da conta de usuário especificada.
* *Tabela*: especifique o nome da tabela de destino no banco de dados.

![][add-database]

### <a name="step-4"></a>Etapa 4
Clique no botão de verificação para adicionar essa saída e para verificar se o Stream Analytics pode se conectar com êxito ao banco de dados.

Quando a conexão com o banco de dados for realizada com sucesso, você verá uma notificação no Portal. Você pode clicar em testar para testar a conexão com o banco de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral da integração, consulte [Visão geral de integração do SQL Data Warehouse][SQL Data Warehouse integration overview].

Para obter mais dicas de desenvolvimento, confira [Visão geral sobre o desenvolvimento no SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
