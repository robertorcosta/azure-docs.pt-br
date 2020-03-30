---
title: Fluxo de dados usando a integração do Stream Analytics (visualização)
description: Use o Azure Stream Analytics para transmitir dados em um banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820839"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Fluxo de dados usando a integração do Azure SQL Database Stream Analytics (visualização)

Agora, os usuários podem ingerir, processar, visualizar e analisar dados de streaming em tempo real em uma tabela diretamente de um banco de dados SQL no portal Azure usando [o Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Essa experiência permite uma grande variedade de cenários, como carro conectado, monitoramento remoto, detecção de fraudes e muito mais. No portal Azure, você pode selecionar uma fonte de eventos (Event Hub/IoT Hub), visualizar eventos em tempo real e selecionar uma tabela para armazenar eventos. Você também pode escrever consultas de consulta de stream analytics no portal para transformar eventos de entrada e armazená-los na tabela selecionada. Este novo ponto de entrada se soma às experiências de criação e configuração que já existem no Stream Analytics. Essa experiência começa a partir do contexto do seu banco de dados, permitindo que você configure rapidamente um trabalho de Stream Analytics e navegue perfeitamente entre as experiências do Banco de Dados SQL do Azure e do Stream Analytics.

![Fluxo de Stream Analytics](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Principais benefícios

- Comutação mínima de contexto: Você pode começar a partir de um banco de dados SQL no portal e começar a ingerir dados em tempo real em uma tabela sem mudar para qualquer outro serviço. 
- Número reduzido de etapas: O contexto do seu banco de dados e tabela é usado para pré-configurar um trabalho de Stream Analytics.
- Facilidade adicional de uso com dados de visualização: Visualizar dados de entrada da fonte de eventos (Event Hub/IoT Hub) no contexto da tabela selecionada 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/). 
- Um banco de dados SQL. Para obter detalhes, consulte [Criar um único banco de dados no Banco de Dados SQL do Azure](sql-database-single-database-get-started.md).
- Uma regra de firewall que permite que seu computador se conecte ao servidor SQL do Azure. Para obter detalhes, consulte [Criar uma regra de firewall no nível do servidor](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Configurar a integração de análise saqueada

1. Entre no portal do Azure. 
2. Navegue até o banco de dados SQL onde deseja ingerir seus dados de streaming. Selecione **análises de fluxo (visualização)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Para começar a ingerir seus dados de streaming neste banco de dados SQL, selecione **Criar** e dar um nome ao seu trabalho de streaming e, em seguida, selecione **Next: Input**. 

    ![criar trabalho stream analytics](media/sql-database-stream-analytics/create-job.png)

4. Digite os detalhes da origem dos eventos e selecione **Next: Output**.

   - **Tipo de entrada**: Hub de eventos/Hub IoT
   - **Alias de entrada**: Digite um nome para identificar sua fonte de eventos 
   - **Assinatura**: O mesmo que assinatura do Banco de Dados SQL 
   - **Namespace do Event Hub**: Nome para namespace 
   - **Nome do Event Hub**: Nome do hub de eventos dentro do namespace selecionado 
   - **Nome da política do Event Hub** (Padrão para criar novo): Dê um nome de política 
   - **Grupo de consumidores event Hub** (Padrão para criar novo): Dê um nome de grupo de consumidores  
     - Recomendamos que você crie um grupo de consumidores e uma política para cada novo trabalho do Azure Stream Analytics que você criar daqui. Os grupos de consumidores permitem apenas cinco leitores simultâneos, portanto, fornecer um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam surgir de ultrapassar esse limite. Uma política dedicada permite que você gire sua chave ou revogue permissões sem afetar outros recursos.

    ![criar trabalho stream analytics](media/sql-database-stream-analytics/create-job-output.png)

5. Selecione em qual tabela deseja ingerir seus dados de streaming. Uma vez feito, selecione **Criar**.
   - **Nome de usuário**, **Senha**: Digite suas credenciais para autenticação do servidor SQL. Selecione **Validar**.
   - **Tabela**: Selecione **Criar novo** ou Usar **o existente**. Neste fluxo, vamos selecionar **Criar**. Isso criará uma nova tabela quando você iniciar o trabalho do stream Analytics.

    ![criar trabalho stream analytics](media/sql-database-stream-analytics/create.png)

6. Uma página de consulta é aberta com os seguintes detalhes:

   - Sua **entrada** (fonte de eventos de entrada) a partir da qual você irá ingerir dados  
   - Sua **saída** (tabela de saída) que armazenará dados transformados 
   - Amostrar [a consulta SAQL com a](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) declaração SELECT. 
   - **Visualização de entrada**: Mostra instantâneo dos dados de entrada mais recentes da fonte de eventos de entrada.  
     - O tipo de serialização em seus dados é detectado automaticamente (JSON/CSV). Você pode alterá-lo manualmente também para JSON/CSV/AVRO. 
     - Você pode visualizar os dados de entrada no formato Tabela ou Formato Raw. 
     - Se os dados mostrados não forem atualizados, **selecione Atualizar** para ver os eventos mais recentes. 
     - Selecione **Selecionar intervalo de tempo** para testar sua consulta em um intervalo de tempo específico de eventos de entrada. 
     - Selecione **A entrada de amostra de upload** para testar sua consulta carregando um arquivo JSON/CSV de amostra. Para obter mais informações sobre como testar uma consulta SAQL, consulte [Teste um trabalho do Azure Stream Analytics com dados de amostra](../stream-analytics/stream-analytics-test-query.md). 

    ![consulta de teste](media/sql-database-stream-analytics/test-query.png)


   - **Resultados do teste**: Selecione **consulta de teste** e você pode ver os resultados da sua consulta de streaming 

    ![resultados de testes](media/sql-database-stream-analytics/test-results.png)

   - **Esquema de resultados de teste**: Mostra o esquema dos resultados da sua consulta de streaming após o teste. Certifique-se de que o esquema de resultados do teste corresponda ao seu esquema de saída. 

    ![esquema de resultados de teste](media/sql-database-stream-analytics/test-results-schema.png)


   - **Esquema de saída**: Este contém o esquema da tabela selecionada na etapa 5 (nova ou existente).
     - Criar novo: Se você selecionou essa opção na etapa 5, você não verá o esquema ainda até iniciar o trabalho de streaming. Ao criar uma nova tabela, selecione o índice de tabela apropriado. Para obter mais informações sobre indexação da tabela, consulte [Índices agrupados e não agrupados descritos](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Use a existente: Se você selecionou esta opção na etapa 5, você verá o esquema da tabela selecionada. 
 
7. Depois de terminar de escrever & testar a consulta, **selecione Salvar consulta**. Selecione **iniciar o trabalho de análise de fluxo** para começar a ingerir dados transformados na tabela SQL. Depois de finalizar os seguintes campos, **inicie** o trabalho. 
   - **Tempo de início da saída**: Isso define o tempo da primeira saída do trabalho.  
     - Agora: O trabalho começará agora e processará novos dados de entrada.
     - Personalizado: O trabalho começará agora, mas processará dados de um ponto específico no tempo (que pode ser no passado ou no futuro). Para obter mais informações, consulte [Como iniciar um trabalho no Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Unidades de streaming**: O Azure Stream Analytics é precificado pelo número de unidades de streaming necessárias para processar os dados no serviço. Para obter mais informações, consulte [os preços do Azure Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Manipulação de erros de dados de saída:**  
     - Nova tentativa: Quando ocorre um erro, o Azure Stream Analytics tenta novamente escrever o evento indefinidamente até que a gravação seja bem sucedida. Não há tempo limite para novas repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está tentando novamente. Essa opção é a política de tratamento de erro de saída padrão. 
     - Drop: O Azure Stream Analytics soltará qualquer evento de saída que resulte em um erro de conversão de dados. Os eventos removidos não poderão ser recuperados para reprocessamento posterior. Todos os erros transitórios (por exemplo, erros de rede) serão repetidos, independentemente da configuração da política de tratamento de erro de saída. 
   - **Configurações de saída do banco de dados SQL**: Uma opção para herdar o esquema de particionamento da etapa de consulta anterior, para habilitar a topologia totalmente paralela com vários escritores à mesa. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Contagem máxima de lotes**: O limite superior recomendado no número de registros enviados a cada transação de inserção em massa.  
    Para obter mais informações sobre o tratamento de erros de saída, consulte [políticas de erro de saída no Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![começar o trabalho](media/sql-database-stream-analytics/start-job.png)

8. Uma vez que você começa o trabalho, você verá o trabalho em execução na lista, e você pode tomar as seguintes ações: 
   - **Início/parada do trabalho**: Se o trabalho está funcionando, você pode parar o trabalho. Se o trabalho for interrompido, você pode começar o trabalho. 
   - **Editar trabalho**: Você pode editar a consulta. Se você quiser fazer mais alterações no job ex, adicione mais entradas/saídas e abra o trabalho no Stream Analytics. A opção de edição é desativada quando o trabalho está em execução. 
   - **Visualizar tabela de saída**: Você pode visualizar a tabela no editor de consulta SQL. 
   - **Abra no Stream Analytics**: Abra o trabalho no serviço Stream Analytics para visualizar o monitoramento, depurando detalhes do trabalho. 


    ![stream analytics empregos](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Próximas etapas

- [Documentação do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Padrões de solução do Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)
