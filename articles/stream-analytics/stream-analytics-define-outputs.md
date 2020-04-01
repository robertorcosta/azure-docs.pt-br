---
title: Entender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados da análise.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475235"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Entender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para um trabalho do Azure Stream Analytics. As saídas permitem armazenar e salvar os resultados do trabalho do Stream Analytics. Usando os dados de saída, você pode fazer mais análises de negócios e armazenamento de dados de seus dados.

Ao projetar sua consulta do Stream Analytics, consulte o nome da saída usando a [cláusula INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Você pode usar uma única saída por trabalho ou várias saídas por trabalho de streaming (se precisar) fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar as saídas de trabalho do Stream Analytics, você pode usar o [portal Azure,](stream-analytics-quick-create-portal.md#configure-job-output) [Azure PowerShell,](stream-analytics-quick-create-powershell.md#configure-output-to-the-job) [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e [Visual Studio.](stream-analytics-quick-create-vs.md)

Alguns tipos de saídas suportam [particionamento](#partitioning). [Os tamanhos dos lotes de saída](#output-batch-size) variam para otimizar o throughput.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics suporta [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). O Azure Data Lake Storage é um repositório de hiperescala para cargas de trabalho analíticas de big data. Você pode usar o Data Lake Storage para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. O Stream Analytics precisa ser autorizado a acessar o Data Lake Storage.

A saída do Azure Data Lake Storage da Stream Analytics não está disponível nas regiões do Azure China 21Vianet e Azure Germany (T-Systems International).

A tabela a seguir lista nomes de propriedades e suas descrições para configurar sua saída Data Lake Storage Gen 1.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para Data Lake Store. |
| Subscription | A assinatura que contém sua conta do Azure Data Lake Storage. |
| Nome da conta | O nome da conta data lake store para onde você está enviando sua saída. Você é apresentado com uma lista baixa de contas da Data Lake Store que estão disponíveis em sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo usado para gravar seus arquivos dentro da conta especificada data lake store. Você pode especificar uma ou mais instâncias das variáveis {date} e {time}:<br /><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br />O carimbo de hora da estrutura da pasta criada segue UTC e não hora local.<br /><br />Se o padrão de caminho do arquivo não contiver uma barra de arrasto (/), o último padrão no caminho do arquivo é tratado como um prefixo de nome de arquivo. <br /><br />Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinício externo ou interno de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.|
| Codificação | Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.|
| Delimitador | Aplicável apenas para serialização CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada,** o JSON será lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido.  **O Array** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, porque não requer nenhum manuseio especial enquanto o arquivo de saída ainda está sendo gravado.|
| Modo de autenticação | Você pode autorizar o acesso à sua conta data lake storage usando [identidade gerenciada](stream-analytics-managed-identities-adls.md) ou token de usuário. Depois de conceder acesso, você pode revogar o acesso alterando a senha da conta de usuário, excluindo a saída do Data Lake Storage para este trabalho ou excluindo o trabalho do Stream Analytics. |

## <a name="sql-database"></a>Banco de Dados SQL

Você pode usar [o Azure SQL Database](https://azure.microsoft.com/services/sql-database/) como uma saída para dados de natureza relacional ou para aplicativos que dependem do conteúdo hospedado em um banco de dados relacional. Os trabalhos do Stream Analytics são escrevem para uma tabela existente no Banco de Dados SQL. O esquema de tabela deve corresponder exatamente aos campos e seus tipos na saída do seu trabalho. Você também pode especificar [o Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da opção de saída do Banco de Dados SQL. Para saber como melhorar o throughput de gravação, consulte o [Stream Analytics com o Azure SQL Database como](stream-analytics-sql-output-perf.md) artigo de saída.

Você também pode usar [a instância gerenciada do banco de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como uma saída. Você tem que [configurar o ponto final público na instância gerenciada do banco de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, configurar manualmente as seguintes configurações no Azure Stream Analytics. A máquina virtual do Azure executando o SQL Server com um banco de dados conectado também é suportada configurando manualmente as configurações abaixo.

A tabela a seguir lista os nomes da propriedade e sua descrição para criar uma saída de banco de dados SQL.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados para onde você está enviando sua saída. |
| Nome do servidor | Nome do servidor do Banco de Dados SQL do Azure. Para a instância gerenciada do banco de dados Azure SQL, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de Usuário | O nome de usuário que tem acesso à gravação do banco de dados. O Stream Analytics suporta apenas autenticação SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da mesa é sensível a maiúsculas e minúsculas. O esquema desta tabela deve corresponder exatamente ao número de campos e seus tipos que sua saída de trabalho gera. |
|Herdar esquema de partição| Uma opção para herdar o esquema de particionamento da etapa de consulta anterior, para habilitar topologia totalmente paralela com vários escritores à mesa. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).|
|Contagem máxima do lote| O limite superior recomendado no número de registros enviados a cada transação de inserção em massa.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Armazenamento blob e Azure Data Lake Gen2

O Data Lake Storage Gen2 torna o armazenamento do Azure a fundação para a criação de data lakes empresariais no Azure. Projetado desde o início para atender vários petabytes de informações, mantendo centenas de gigabits de throughput, o Data Lake Storage Gen2 permite gerenciar facilmente grandes quantidades de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um namespace hierárquico ao armazenamento Blob.

O armazenamento Azure Blob oferece uma solução econômica e escalável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o armazenamento Blob e seu uso, consulte [Upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela a seguir lista os nomes da propriedade e suas descrições para criar uma saída blob ou ADLS Gen2.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento para onde você está enviando sua saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Recipiente de armazenamento   | Um agrupamento lógico para bolhas armazenadas no serviço Azure Blob. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão de caminho de arquivo usado para escrever suas bolhas dentro do recipiente especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das variáveis de data e hora para especificar a frequência que as bolhas são escritas: <br /> {data}, {hora} <br /><br />Você pode usar um particionamento de blob para especificar um nome de {field} personalizado dos dados do seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Nomes de campo não são sensíveis a casos. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "id".</li><li>Campos aninhados não são permitidos. Em vez disso, use um alias na consulta de trabalho para "achatar" o campo.</li><li>Expressões não podem ser usadas como nome de campo.</li></ul> <br />Esse recurso permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Os formatos personalizados de data e hora devem ser especificados um de cada vez, entre a palavra-chave {datetime:\<specifier>}. As entradas \<permitidas para> especificador são yyyy, MM, M, dd, d, hh, H, mm, m, ss ou s. A palavra-chave\<{datetime: specifier>} pode ser usada várias vezes no caminho para formar configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} onde a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo de hora da estrutura da pasta criada segue UTC e não hora local.<br /><br />A nomeação de arquivos usa a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br />Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esse recurso, consulte [particionamento personalizado de saída de bolha do Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída. JSON, CSV, Avro e Parquet são apoiados. |
|Linhas mínimas (somente Parquet)|O número de linhas mínimas por lote. Para o Parquet, cada lote criará um novo arquivo. O valor padrão atual é de 2.000 linhas e o máximo permitido é de 10.000 linhas.|
|Tempo máximo (somente Parquet)|O tempo máximo de espera por lote. Após esse período, o lote será escrito na saída mesmo que o requisito mínimo de linhas não seja atendido. O valor padrão atual é de 1 minuto e o máximo permitido é de 2 horas. Se a saída blob tiver freqüência de padrão de caminho, o tempo de espera não poderá ser maior do que o intervalo de tempo de partição.|
| Codificação    | Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador   | Aplicável apenas para serialização CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada,** o JSON será lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **O Array** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, porque não requer nenhum manuseio especial enquanto o arquivo de saída ainda está sendo gravado. |

Quando você estiver usando o armazenamento Blob como saída, um novo arquivo é criado na bolha nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). Você pode atingir o número máximo permitido de blocos sem atingir o tamanho máximo permitido de bolhas. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída exigir esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.
* Se a consulta estiver totalmente particionada, e um novo arquivo for criado para cada partição de saída.
* Se o usuário excluir um arquivo ou um contêiner da conta de armazenamento.
* Se a saída for particionada usando o padrão de prefixo de caminho, e uma nova bolha será usada quando a consulta se mover para a próxima hora.
* Se a saída for particionada por um campo personalizado, e uma nova bolha for criada por chave de partição, se ela não existir.
* Se a saída for particionada por um campo personalizado onde a cardinalidade da chave de partição excede 8.000, e uma nova bolha será criada por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando a saída de um trabalho do Stream Analytics se torna a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e a otimização do tamanho do lote, consulte a seção de tamanho do [lote de saída.](#output-batch-size)

Você precisa de alguns parâmetros para configurar fluxos de dados de hubs de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este hub de eventos. |
| Namespace do Hub de Eventos | Um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo hub de eventos, você também criou um namespace do hub de eventos. |
| Nome do Hub de Eventos | O nome da sua saída do hub de eventos. |
| Nome da política do hub de eventos | A política de acesso compartilhado, que você pode criar na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, permissões que você define e chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso compartilhada usada para autenticar o acesso ao namespace do hub de eventos. |
| Coluna de tecla partição | Opcional. Uma coluna que contém a chave de partição para saída do hub de eventos. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável apenas para serialização CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada,** o JSON será lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **O Array** especifica que a saída é formatada como uma matriz de objetos JSON.  |
| Colunas de propriedades | Opcional. Colunas separadas por comma que precisam ser anexadas como propriedades do usuário da mensagem de saída em vez da carga útil. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Você pode usar [o Power BI](https://powerbi.microsoft.com/) como uma saída para um trabalho do Stream Analytics para fornecer uma rica experiência de visualização de resultados de análise. Você pode usar esse recurso para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

A saída de POWER BI da Stream Analytics não está disponível atualmente nas regiões azure China 21Vianet e Azure Germany (T-Systems International).

A tabela a seguir lista nomes de propriedades e suas descrições para configurar sua saída de POWER BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Forneça um nome amigável usado em consultas para direcionar a saída de consulta para esta saída de POWER BI. |
| Agrupar o workspace |Para habilitar o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher **Meu Espaço de Trabalho** se você não quiser escrever para um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que você deseja que a saída power BI use. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |
| Autorizar conexão | Você precisa autorizar com o Power BI para configurar suas configurações de saída. Depois de conceder esse acesso à saída do painel do Power BI, você pode revogar o acesso alterando a senha da conta de usuário, excluindo a saída de trabalho ou excluindo o trabalho do Stream Analytics. | 

Para obter um passo a passo da configuração de uma saída e painel de controle de power bi, consulte o tutorial [Azure Stream Analytics e Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel Power BI. O conjunto de dados e a tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a bombear a saída para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Se o Power BI já tivesse um conjunto de dados e uma tabela com o mesmo nome do fornecido neste trabalho do Stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Azure Stream Analytics cria um conjunto de dados power bi e um esquema de tabela para o usuário se ele ainda não existir. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, apenas uma tabela pode existir dentro de um conjunto de dados. 

O Power BI usa a política de retenção FIFO (First-Out) first-in. Os dados serão coletados em uma tabela até atingir 200.000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converta um tipo de dados do Stream Analytics para power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no runtime se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões do tipo de dados de tipos de [dados do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para [os tipos edm (Power BI Entity Data Model, modelo de dados de entidade si,](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)se um conjunto de dados e tabela do Power BI não existirem).

Do Stream Analytics | Para o Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
DATETIME | Datetime
FLOAT | Double
Matriz de registro | Tipo de string, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar eventos de entrada que podem não se encaixar no esquema original.

Evite `SELECT *` a consulta para evitar a atualização dinâmica do esquema entre as linhas. Além de possíveis implicações de desempenho, pode resultar em incerteza do tempo necessário para os resultados. Selecione os campos exatos que precisam ser mostrados no painel Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>Armazenamento de tabela

[Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabela sao a chave/atributos NoSQL da Microsoft, que você pode usar para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes da propriedade e suas descrições para criar uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento para onde você está enviando sua saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. A mesa é criada se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de corda que pode ser de até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a tecla de linha. A tecla de linha é um identificador único para uma entidade dentro de uma partição. Ele forma a segunda parte da chave primária de uma entidade. A tecla de linha é um valor de string que pode ser de até 1 KB de tamanho. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte a [especificação Operação do Lote de Tabela](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) para obter mais detalhes sobre a modificação desta configuração. |

## <a name="service-bus-queues"></a>Filas do Barramento de Serviço

[As filas de ônibus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) de serviço oferecem uma entrega de mensagens FIFO para um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos receptores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por apenas uma mensagem do consumidor.

No [nível de compatibilidade 1.2,](stream-analytics-compatibility-level.md)o Azure Stream Analytics usa o protocolo de mensagens [AMQP (Advanced Message Queueing Protocol,](../service-bus-messaging/service-bus-amqp-overview.md) protocolo avançado de filamento de mensagens) para escrever em Filas e Tópicos de Ônibus de Serviço. O AMQP permite que você crie várias plataformas, aplicativos híbridos usando um protocolo de padrão aberto.

A tabela a seguir lista os nomes da propriedade e suas descrições para criar uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta fila de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Ônibus de Serviço. |
| Nome da política de fila |Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia **Configurar** da fila. Cada política de acesso compartilhado tem um nome, permissões que você define e chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável apenas para serialização CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável apenas para o tipo JSON. **A linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada,** o JSON será lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **O Array** especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas de propriedades | Opcional. Colunas separadas por comma que precisam ser anexadas como propriedades do usuário da mensagem de saída em vez da carga útil. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |
| Colunas de propriedade do sistema | Opcional. Principais pares de valores de Propriedades do Sistema e nomes de coluna correspondentes que precisam ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esse recurso estão na seção Propriedades do [sistema para saídas de linha de barra mento e tópico de serviço](#system-properties-for-service-bus-queue-and-topic-outputs)  |

O número de partições é [baseado no SKU e tamanho do Bus de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos do Service Bus
As filas de ônibus de serviço fornecem um método de comunicação um-para-um do remetente ao receptor. [Os tópicos de Ônibus](https://msdn.microsoft.com/library/azure/hh367516.aspx) de Serviço fornecem uma forma de comunicação única para muitos.

A tabela a seguir lista os nomes da propriedade e suas descrições para criar uma saída de tópico do Service Bus.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este tópico de Service Bus. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas a um tópico não estão disponíveis a menos que uma assinatura seja criada, então certifique-se de que há uma ou mais assinaturas sob o tópico. |
| Nome da política de tópico |Quando você cria um tópico de Barra de Serviço, você também pode criar políticas de acesso compartilhado na guia **Configurar** do tópico. Cada política de acesso compartilhado tem um nome, permissões que você define e chaves de acesso. |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável apenas para serialização CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Colunas de propriedades | Opcional. Colunas separadas por comma que precisam ser anexadas como propriedades do usuário da mensagem de saída em vez da carga útil. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |
| Colunas de propriedade do sistema | Opcional. Principais pares de valores de Propriedades do Sistema e nomes de coluna correspondentes que precisam ser anexados à mensagem de saída em vez da carga útil. Mais informações sobre esse recurso estão na seção Propriedades do [sistema para saídas de linha de barra mento e tópico de serviço](#system-properties-for-service-bus-queue-and-topic-outputs) |

O número de partições é [baseado no SKU e tamanho do Bus de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro único para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados distribuído globalmente que oferece escala elástica ilimitada em todo o mundo, consulta sofisticada e indexação automática sobre modelos de dados agnósticos de esquemas. Para saber mais sobre as opções de contêineres Do Azure Cosmos DB para Stream Analytics, consulte o [Stream Analytics com o Azure Cosmos DB como](stream-analytics-documentdb-output.md) artigo de saída.

A saída do Azure Cosmos DB da Stream Analytics não está disponível nas regiões do Azure China 21Vianet e Azure Germany (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics só suporta conexão com o Azure Cosmos DB usando a API SQL.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente.

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Azure Cosmos DB. |
| Importar opção | Escolha **selecionar o Cosmos DB na sua assinatura** ou fornecer **manualmente as configurações do Cosmos DB**.
| ID da Conta | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB. |
| Chave de conta | A chave de acesso compartilhado da conta do Azure Cosmos DB. |
| Banco de dados | O nome do banco de dados do Azure Cosmos DB. |
| Nome do contêiner | O nome do contêiner a ser usado, que deve existir no Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Um recipiente chamado "MyContainer" deve existir.</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída usado para especificar a chave principal na qual as operações de inserção ou atualização são baseadas.

## <a name="azure-functions"></a>Funções do Azure
O Azure Functions é um serviço de computação sem servidor que você pode usar para executar código sob demanda sem ter que prover ou gerenciar infra-estrutura explicitamente. Ele permite implementar código susceptíveis de eventos que ocorrem no Azure ou serviços de parceiros. Essa capacidade das funções do Azure de responder aos gatilhos torna-a uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite que os usuários conectem o Stream Analytics às funções do Azure e executem um script ou pedaço de código em resposta a uma variedade de eventos.

A saída de Funções do Azure da Stream Analytics não está disponível atualmente nas regiões azure China 21Vianet e Azure Germany (T-Systems International).

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O adaptador de saída Funções do Azure está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Aplicativo de funções |O nome do seu aplicativo Azure Functions. |
| Função |O nome da função no aplicativo Azure Functions. |
| Chave |Se você quiser usar uma função Azure de outra assinatura, você pode fazê-lo fornecendo a chave para acessar sua função. |
| Tamanho máximo do lote |Uma propriedade que permite definir o tamanho máximo para cada lote de saída enviado para sua função Azure. A unidade de entrada é em bytes. Por padrão, esse valor é de 262.144 bytes (256 KB). |
| Contagem máxima do lote  |Uma propriedade que permite especificar o número máximo de eventos em cada lote enviado para funções do Azure. O valor padrão é 100. |

O Azure Stream Analytics espera o status HTTP 200 do aplicativo Functions para lotes que foram processados com sucesso.

Quando o Azure Stream Analytics recebe uma exceção 413 ("http Request Entity Too Large") de uma função Azure, reduz o tamanho dos lotes que ele envia para funções do Azure. Em seu código de função Azure, use essa exceção para garantir que o Azure Stream Analytics não envie lotes superdimensionados. Além disso, certifique-se de que a contagem máxima de lotes e os valores de tamanho usados na função são consistentes com os valores inseridos no portal Stream Analytics.

> [!NOTE]
> Durante a conexão de teste, o Stream Analytics envia um lote vazio para funções do Azure para testar se a conexão entre os dois funciona. Certifique-se de que o aplicativo Functions lida com solicitações vazias em lote para garantir que a conexão de teste passe.

Além disso, em uma situação onde não há nenhum evento aterrissando em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função **computeResult** não é chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizadas para saída 

Você pode anexar colunas de consulta como propriedades do usuário às suas mensagens de saída. Essas colunas não entram na carga. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *A chave* é o nome da coluna e *o valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics são suportados, exceto Record e Array.  

Saídas suportadas: 
* Fila do Barramento de Serviço 
* Tópico de Ônibus de Serviço 
* Hub de Eventos 

No exemplo a seguir, adicionamos os dois campos `DeviceId` e `DeviceStatus` os metadados. 
* Consulta: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída:`DeviceId,DeviceStatus`

![Colunas de propriedades](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A captura de tela a seguir mostra as propriedades das mensagens de saída inspecionadas no EventHub através [do Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Propriedades do sistema para saídas de linha de barra de serviço e tópico 
Você pode anexar colunas de consulta como [propriedades do sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) às mensagens de barramento de serviço de saída. Essas colunas não entram na carga, em vez disso, a propriedade correspondente do [sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage está preenchida com os valores da coluna de consulta.
Essas propriedades do sistema `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`são suportadas - .
Os valores de seqüência dessas colunas são analisados como o tipo de valor de propriedade correspondente do sistema e quaisquer falhas de análise são tratadas como erros de dados.
Este campo é fornecido como um formato de objeto JSON. Detalhes sobre este formato são os seguintes -
* Cercado por {}chaves.
* Escrito em pares de tecla/valor.
* Chaves e valores devem ser strings.
* A chave é o nome e o valor da propriedade do sistema é o nome da coluna de consulta.
* Chaves e valores são separados por um cólon.
* Cada par de tecla/valor é separado por uma comuma.

Isso mostra como usar esta propriedade –

* Consulta: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas de propriedade do sistema:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Isso define `MessageId` as mensagens de `column1`fila de barramento de `column2`serviço com valores e PartitionKey é definido com valores.

## <a name="partitioning"></a>Particionamento

A tabela a seguir resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte ao particionamento | Chave de partição  | Número de gravadores de saída |
| --- | --- | --- | --- |
| Repositório Azure Data Lake | Sim | Use os tokens {date} e {time} no padrão de prefixo de caminho. Escolha o formato de data, como YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH é usado para o formato de tempo. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Banco de Dados SQL do Azure | Sim, precisa ser habilitado. | Com base na cláusula PARTIÇÃO POR na consulta. | Quando a opção Hericionamento estiver ativada, segue a particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como obter um melhor desempenho de throughput de gravação quando estiver carregando dados no Banco de Dados SQL do Azure, consulte [a saída do Azure Stream Analytics no Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Use os tokens {date} e {time} dos campos de eventos no padrão de caminho. Escolha o formato de data, como YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH é usado para o formato de tempo. A saída de blob pode ser particionada por um atributo de evento personalizado único {fieldname} ou {datetime:\<specifier>}. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Hubs de eventos do Azure | Sim | Sim | Varia dependendo do alinhamento da partição.<br /> Quando a chave de partição para saída do hub de eventos está igualmente alinhada com a etapa de consulta upstream (anterior), o número de escritores é o mesmo que o número de partições na saída do hub de eventos. Cada escritor usa a [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para saída do hub de eventos não está alinhada com a etapa de consulta upstream (anterior), o número de escritores é o mesmo que o número de partições nessa etapa anterior. Cada escritor usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) no **EventHubClient** para enviar eventos para todas as partições de saída. |
| Power BI | Não | Nenhum | Não aplicável. |
| Armazenamento da tabela do Azure | Sim | Qualquer coluna de saída.  | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Tópico do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro único para cada partição.| Mesmo que o número de partições no tópico de saída.  |
| Fila do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro único para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Com base na cláusula PARTIÇÃO POR na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Sim | Com base na cláusula PARTIÇÃO POR na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |

O número de escritores de `INTO <partition count>` saída também pode ser controlado usando a cláusula (ver [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) em sua consulta, o que pode ser útil para alcançar uma topologia de trabalho desejada. Se o seu adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até a quantidade de tempo de chegada tardia. Nesses casos, a saída é fundida a um único escritor, o que pode causar gargalos em seu pipeline. Para saber mais sobre a política de chegada tardia, consulte [considerações sobre a ordem de eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente, o mecanismo Stream Analytics não escreve uma mensagem de cada vez e usa lotes para eficiência. Quando a taxa de entrada e saída de eventos é alta, o Stream Analytics usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa.

A tabela a seguir explica algumas das considerações para o loteamento de saída:

| Tipo de saída |    Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Repositório Azure Data Lake | Consulte [os limites de armazenamento do lago de dados](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Use até 4 MB por operação de gravação. |
| Banco de Dados SQL do Azure | Configurável usando a contagem de lotes Max. 10.000 linhas máximas e 100 mínimas por inserção a granel por padrão.<br />Consulte [os limites do Azure SQL](../sql-database/sql-database-resource-limits.md). |  Cada lote é inicialmente inserido em massa com contagem máxima de lotes. O lote é dividido pela metade (até a contagem mínima de lotes) com base em erros retriáveis do SQL. |
| Armazenamento de Blobs do Azure | Consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do bloco blob é de 4 MB.<br />A contagem máxima de bock é de 50.000. |
| Hubs de eventos do Azure    | 256 KB ou 1 MB por mensagem. <br />Veja [os limites do Event Hubs](../event-hubs/event-hubs-quotas.md). |    Quando a particionamento de entrada/saída não está alinhada, cada evento é embalado individualmente `EventData` e enviado em um lote de até o tamanho máximo da mensagem. Isso também acontece se [as propriedades de metadados personalizadas](#custom-metadata-properties-for-output) forem usadas. <br /><br />  Quando a particionamento de entrada/saída é alinhada, vários eventos são embalados em uma única `EventData` instância, até o tamanho máximo da mensagem e enviados.    |
| Power BI | Consulte [os limites da API power bi rest](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento da tabela do Azure | Consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O padrão é de 100 entidades por transação única. Você pode configurá-lo para um valor menor, conforme necessário. |
| Fila do Barramento de Serviço do Azure    | 256 KB por mensagem para o nível Padrão, 1MB para o nível Premium.<br /> Veja [os limites do Service Bus](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Tópico do Barramento de Serviço do Azure | 256 KB por mensagem para o nível Padrão, 1MB para o nível Premium.<br /> Veja [os limites do Service Bus](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Azure Cosmos DB    | Veja [os limites do Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | O tamanho do lote e a freqüência de gravação são ajustados dinamicamente com base nas respostas do Azure Cosmos DB. <br /> Não há limitações predeterminadas do Stream Analytics. |
| Funções do Azure    | | O tamanho do lote padrão é de 262.144 bytes (256 KB). <br /> A contagem padrão de eventos por lote é de 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions) do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> 
> [Início Rápido: criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
