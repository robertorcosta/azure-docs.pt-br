---
title: Entender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados da análise.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: d1eda3671b52a1e4bbae9af2d97010657880c383
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585395"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Entender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para um trabalho do Azure Stream Analytics. As saídas permitem armazenar e salvar os resultados do trabalho do Stream Analytics. Ao usar os dados de saída, você pode fazer mais análise de negócios e data warehouse de seus dados.

Quando você criar sua consulta do Stream Analytics, consulte o nome de saída usando a [cláusula INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Você pode usar uma única saída por trabalho ou várias saídas por trabalho de streaming (caso precise delas) fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar as saídas de trabalho do Stream Analytics, você pode usar o [Porta do Azure](stream-analytics-quick-create-portal.md#configure-job-output), o [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), o [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), o [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas têm suporte para o [particionamento](#partitioning). [Os tamanhos dos lotes de saída](#output-batch-size) variam para otimizar a taxa de transferência.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

O Stream Analytics tem suporte para o [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). O Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para cargas de trabalho de análise de Big Data. Você pode usar o Data Lake Storage para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. O Stream Analytics deve estar autorizado a acessar o Data Lake Storage.

A saída do Azure Data Lake Storage do Stream Analytics não está disponível no Azure China 21Vianet e regiões do Azure Alemanha (T-Systems internacional).

A tabela a seguir lista nomes de propriedade e suas descrições para configurar a saída do Data Lake Storage Gen 1.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para o Data Lake Store. |
| Subscription | A assinatura que contém sua conta do Azure Data Lake Storage. |
| Nome da conta | O nome da conta do Data Lake Store para a qual você está enviando sua saída. Você verá uma lista suspensa de contas do Data Lake Store que estão disponíveis na sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo que é usado para gravar seus arquivos na conta do Data Lake Store. Você pode especificar uma ou mais instâncias de variáveis de {data} e {hora}:<br /><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br />O registro de data e hora da estrutura de pastas criada segue o UTC (Tempo Universal Coordenado) e não o horário local.<br /><br />Se o padrão de caminho do arquivo não contiver uma barra (/) à direita, o último padrão no caminho do arquivo é tratado como um prefixo de nome de arquivo. <br /><br />Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinicialização externa ou interna de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.|
| Codificação | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento.|
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido.  A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar o JSON separado por linha, porque ele não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado.|
| Modo de autenticação | Você pode autorizar o acesso à sua conta do Data Lake Storage usando a [Identidade Gerenciada](stream-analytics-managed-identities-adls.md) ou Token de usuário. Depois de conceder acesso, você pode revogar o acesso alterando a senha da conta do usuário, excluindo a saída do Data Lake Storage para esse trabalho ou excluindo o trabalho do Stream Analytics. |

## <a name="sql-database"></a>Banco de Dados SQL

Você pode usar o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) como uma saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam da hospedagem do conteúdo em um banco de dados relacional. Os trabalhos do Stream Analytics gravam em uma tabela existente em um Banco de Dados SQL. O esquema da tabela deve corresponder exatamente aos campos e seus tipos na saída de trabalho. Você também pode especificar um [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída por meio da opção de saída do Banco de Dados SQL. Para saber mais sobre as maneiras de melhorar a taxa de transferência de gravação, consulte o artigo [Stream Analytics com o Banco de Dados SQL do Azure como saída](stream-analytics-sql-output-perf.md).

Também é possível usar uma [Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como uma saída. Você precisa [configurar o ponto de extremidade público na Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, configurar manualmente as configurações a seguir no Azure Stream Analytics. A máquina virtual do Azure que executando o SQL Server com um banco de dados anexado também tem suporte da definição manual das configurações abaixo.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de Banco de Dados SQL.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados para o qual você está enviando sua saída. |
| Nome do servidor | Nome do servidor do Banco de Dados SQL do Azure. Para a Instância Gerenciada do Banco de Dados SQL do Azure, é preciso especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de Usuário | O nome de usuário que tem acesso de gravação ao banco de dados. O Stream Analytics tem suporte apenas à autenticação do SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e tipos gerados pela sua saída de trabalho. |
|Herdar esquema de partição| Uma opção para herdar o esquema de partição da etapa da sua consulta anterior para possibilitar uma topologia totalmente paralela com vários gravadores na tabela. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).|
|Contagem máxima do lote| O limite máximo recomendado do número de registros enviados com cada transação de inserção em massa.|

Há dois adaptadores que permitem a saída do Azure Stream Analytics para o Azure Synapse Analytics (anteriormente SQL Data Warehouse): Banco de Dados SQL e Azure Synapse. Recomendamos que você escolha o adaptador do Azure Synapse Analytics em vez do adaptador do Banco de Dados SQL se alguma das condições a seguir for verdadeira:

* **Taxa de transferência**: se a taxa de transferência esperada agora ou no futuro for maior que 10 MB/s, use a opção de saída do Azure Synapse para melhorar o desempenho.

* **Partições de entrada**: caso tenha oito ou mais partições de entrada, use a opção de saída do Azure Synapse para uma expansão melhor.

## <a name="azure-synapse-analytics-preview"></a>Azure Synapse Analytics (versão prévia)

O [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (anteriormente, SQL Data Warehouse) é um serviço de análise ilimitado que reúne o data warehouse empresarial e a análise de Big Data. 

Os trabalhos do Azure Stream Analytics podem gerar uma saída para uma tabela do pool de SQL no Azure Synapse Analytics e podem processar taxas de transferência de até 200 MB/s, o que dá suporte às mais exigentes análises em tempo real e necessidades de processamento de dados de caminho crítico para cargas de trabalho, como relatórios e painéis.  

A tabela do pool de SQL deve existir antes que você possa adicioná-la como uma saída ao seu trabalho do Stream Analytics. O esquema da tabela deve corresponder aos campos e seus tipos na saída de trabalho. 

Para usar o Azure Synapse como saída, é preciso garantir que a conta de armazenamento esteja configurada. Navegue até as configurações da Conta de armazenamento para configurá-la. São permitidos apenas os tipos de conta de armazenamento com suporte para tabelas: Uso geral V2 e Uso geral V1.   

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída do Azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
|Banco de dados |Nome do pool de SQL ao qual você está enviando sua saída. |
|Nome do servidor |Nome do servidor do Azure Synapse.  |
|Nome de Usuário |O nome de usuário que tem acesso de gravação ao banco de dados. O Stream Analytics tem suporte apenas à autenticação do SQL. |
|Senha |A senha para se conectar ao banco de dados. |
|Tabela  | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e tipos gerados pela sua saída de trabalho.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Armazenamento de blobs e Azure Data Lake Gen2

O Data Lake Storage Gen2 torna o armazenamento do Azure a fundação para a criação de data lakes empresariais no Azure. Projetado desde o início para atender a vários petabytes de informações enquanto mantém centenas de gigabits de taxa de transferência, o Data Lake Storage Gen2 permite que você gerencie de uma maneira fácil grandes quantidades de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um namespace hierárquico ao armazenamento de blobs.

O Armazenamento de blobs do Azure oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o Armazenamento de blobs e seu uso, consulte [Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de um blob ou de uma saída do ADLS Gen2.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento para a qual você está enviando a saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contêiner de armazenamento   | Um agrupamento lógico para blobs armazenados no serviço Blob do Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão do caminho do arquivo que é usado para gravar seus blobs no contêiner especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das duas variáveis de hora e data para especificar a frequência com a qual os blobs são gravados: <br /> {data}, {hora} <br /><br />Você pode usar um particionamento de blob para especificar um nome de {field} personalizado dos dados do seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Nomes de campo não diferenciam maiúsculas de minúsculas. Por exemplo, o serviço não consegue diferenciar entre "ID" e "id" da coluna.</li><li>Campos aninhados não são permitidos. Em vez disso, use um alias na consulta de trabalho para “nivelar” o campo.</li><li>As expressões não podem ser usadas como um nome de campo.</li></ul> <br />Esse recurso permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Os formatos personalizados de data e hora devem ser especificados um de cada vez, entre a palavra-chave {datetime:\<specifier>}. As entradas permitidas para o \<specifier> são aaaa, MM, M, dd, d, HH, H, mm, m, ss ou s. A palavra-chave {datetime:\<specifier>} pode ser usada várias vezes no caminho para formar as configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O registro de data e hora da estrutura de pastas criada segue o UTC (Tempo Universal Coordenado) e não o horário local.<br /><br />A nomenclatura de arquivo usa a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br />Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esse recurso, consulte [Particionamento de saída de blobs personalizados do Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV, Avro e Parquet. |
|Mínimo de linhas (somente Parquet)|O número mínimo de linhas por lote. Para o Parquet, cada lote criará um novo arquivo. O valor padrão atual é de 2.000 linhas, e o máximo permitido é de 10.000 linhas.|
|Tempo máximo (somente Parquet)|O tempo máximo de espera por lote. Após esse período, o lote será gravado na saída mesmo que o requisito de linhas mínimas não seja atendido. O valor padrão atual é de 1 minuto, e o máximo permitido é de 2 horas. Se a saída de blobs tiver a frequência de padrão do caminho, o tempo de espera não poderá ser maior que o intervalo de tempo da partição.|
| Codificação    | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador   | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido. A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar o JSON separado por linha, porque ele não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado. |

Ao usar o Armazenamento de blobs como saída, um novo arquivo será criado no blob nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). Você pode alcançar o número máximo permitido de blocos sem atingir o tamanho máximo permitido do blob. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.
* Se a consulta for totalmente particionada, e um novo arquivo for criado para cada partição de saída.
* Se o usuário excluir um arquivo ou um contêiner da conta de armazenamento.
* Se a saída for particionada por tempo usando o padrão de prefixo do caminho, e um novo blob for usado quando a consulta se mover para a próxima hora.
* Se a saída for particionada por um campo personalizado, e um novo blob for criado por chave de partição caso não exista.
* Se a saída for particionada por um campo personalizado, em que a cardinalidade da chave de partição exceda 8.000, e um novo blob for criado por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando a saída de um trabalho do Stream Analytics torna-se a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e a otimização do tamanho do lote, consulte a seção [Tamanho do lote de saída](#output-batch-size).

São necessários alguns parâmetros para configurar os fluxos de dados dos hubs de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse hub de eventos. |
| Namespace do Hub de Eventos | Um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo hub de eventos, também criou um namespace do hub de eventos. |
| Nome do Hub de Eventos | O nome da sua saída de hub de eventos. |
| Nome da política do hub de eventos | A política de acesso compartilhado, que você pode criar na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso compartilhado que é usada para autenticar o acesso ao namespace do hub de eventos. |
| Coluna de chave da partição | Opcional. Uma coluna contém a chave de partição para a saída do hub de eventos. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido. A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON.  |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso na seção [Propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Você pode usar o [Power BI](https://powerbi.microsoft.com/) como saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Você pode usar essa funcionalidade para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

A saída do Power BI do Stream Analytics atualmente não está disponível nas regiões Azure China 21Vianet e Azure Alemanha (T-Systems International).

A tabela a seguir lista nomes de propriedade e suas descrições para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Fornece é um nome amigável que é usado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Agrupar o workspace |Para permitir o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher **Meu Workspace** caso não queira gravar em um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Fornece um conjunto de dados que você deseja que a saída do Power BI use. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |
| Autorizar conexão | Você precisa autorizar com o Power BI para definir as configurações de saída. Depois de conceder esse acesso de saída ao seu painel do Power BI, você pode revogar o acesso alterando a senha da conta de usuário, excluindo a saída do trabalho ou excluindo o trabalho do Stream Analytics. | 

Para obter um passo a passo de configuração de uma saída e de um painel do Power BI, consulte o tutorial [Azure Stream Analytics e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Se a consulta do trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados. Se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Stream Analytics do Azure cria um conjunto de dados do Power BI e um esquema de tabela para o usuário caso eles ainda não existam. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, só pode existir uma tabela em um conjunto de dados. 

O Power BI usa a política de retenção de PEPS (primeiro a entrar, primeiro a sair). Os dados serão coletados em uma tabela até que ela atinja 200.000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para o Power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no runtime se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões de tipo de dados dos [Tipos de dados do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para [Tipos de EDM (Modelo de Dados de Entidade)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) do Power BI caso não existam um conjunto de dados e uma tabela do Power BI.

Do Stream Analytics | Para o Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
DATETIME | Datetime
FLOAT | Double
Matriz de registro | Tipo de cadeia de caracteres, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que não se encaixam no esquema original.

Evite a consulta `SELECT *` para impedir que o esquema dinâmico atualize as linhas. Além das possíveis implicações de desempenho, ela pode resultar na incerteza do tempo necessário para obter os resultados. Selecione os campos exatos que precisam ser mostrados no painel do Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>Armazenamento de tabela

[Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabelas é um repositório de chave/atributo NoSQL da Microsoft, que você pode usar para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento para a qual você está enviando a saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. Caso ainda não exista, a tabela será criada. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição em uma tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter um tamanho de até 1 KB. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo de uma entidade em uma partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter um tamanho de até 1 KB. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte [Especificação da operação de lote da tabela](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) para obter mais detalhes sobre como modificar essa configuração. |

## <a name="service-bus-queues"></a>Filas do Barramento de Serviço

As [filas do Barramento de Serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferecem entrega de mensagem do tipo PEPS para um ou mais consumidores concorrentes. Geralmente, as mensagens são recebidas e processadas pelos receptores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por somente um consumidor de mensagem.

No [nível de compatibilidade 1.2](stream-analytics-compatibility-level.md), o Azure Stream Analytics usa o protocolo de sistema de mensagens [AMQP (Protocolo de Enfileiramento de Mensagens Avançado)](../service-bus-messaging/service-bus-amqp-overview.md) para gravar em filas e tópicos do barramento de serviço. O AMQP permite que você crie várias plataformas, aplicativos híbridos usando um protocolo de padrão aberto.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para essa fila de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Barramento de Serviço. |
| Nome da política de fila |Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia **Configurar** da fila. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para o tipo JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido. A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso na seção [Propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |
| Colunas da Propriedade do Sistema | Opcional. Pares de valor-chave das Propriedades do Sistema e nomes de colunas correspondentes que precisam ser anexados à mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso estão na seção [Propriedades do sistema para saídas de fila e tópico do Barramento de Serviço](#system-properties-for-service-bus-queue-and-topic-outputs)  |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos do Service Bus
As filas do Barramento de Serviço fornecem um método de comunicação um-para-um do remetente para o destinatário. [Os tópicos do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um-para-muitos.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tópico do Barramento de Serviço.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse tópico de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estão disponíveis a menos que uma assinatura seja criada, portanto, verifique se há uma ou mais assinaturas sob o tópico. |
| Nome da política de tópico |Ao criar um tópico do Barramento de Serviço, você também pode criar políticas de acesso compartilhado na guia **Configurar** do tópico. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso na seção [Propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |
| Colunas da Propriedade do Sistema | Opcional. Pares de valor-chave das Propriedades do Sistema e nomes de colunas correspondentes que precisam ser anexados à mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso estão na seção [Propriedades do sistema para saídas de fila e tópico do Barramento de Serviço](#system-properties-for-service-bus-queue-and-topic-outputs) |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
O [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados distribuído globalmente que oferece dimensionamento elástico ilimitado em todo o mundo, consulta avançada e indexação automática em modelos de dados independentes de esquemas. Para saber mais sobre opções de contêiner do Azure Cosmos DB para Stream Analytics, consulte o artigo [Stream Analytics com o Cosmos DB como saída](stream-analytics-documentdb-output.md).

A saída do Azure Cosmos DB do Stream Analytics não está disponível atualmente nas regiões Azure China 21Vianet e Azure Alemanha (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics oferece suporte apenas à conexão ao Microsoft Azure Cosmos DB usando a API do SQL.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente.

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Azure Cosmos DB. |
| Importar opção | Escolha **Selecionar o Cosmos DB de sua assinatura** ou **Fornecer configurações do Cosmos DB manualmente**.
| ID da Conta | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB. |
| Chave de conta | A chave de acesso compartilhado da conta do Azure Cosmos DB. |
| Banco de dados | O nome do banco de dados do Azure Cosmos DB. |
| Nome do contêiner | O nome do contêiner a ser usado, o qual deve existir no Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Já deve existir um contêiner chamado "MyContainer".</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída que é usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.

## <a name="azure-functions"></a>Funções do Azure
O Azure Functions é um serviço de computação sem servidor que você pode usar para executar o código sob demanda sem precisar provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente o código que é disparado por eventos que ocorrem no Azure ou por serviços de parceiros. Essa capacidade do Azure Functions de responder a gatilhos o torna uma saída natural para o Azure Stream Analytics. Esse adaptador de saída habilita aos usuários se conectar o Stream Analytics ao Azure Functions e executar um script ou trecho de código em resposta a vários eventos.

A saída do Azure Functions do Stream Analytics não está disponível atualmente nas regiões Azure China 21Vianet e Azure Alemanha (T-Systems International).

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O adaptador de saída do Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Aplicativo de funções |O nome de seu aplicativo do Azure Functions. |
| Função |O nome da função em seu aplicativo do Azure Functions. |
| Chave |Se você quiser usar um Azure Function de outra assinatura, você pode fazer isso fornecendo a chave para acessar sua função. |
| Tamanho máximo do lote |Uma propriedade que permite que você defina o tamanho máximo de cada lote de saída que é enviado para seu Azure Functions. A unidade de entrada é em bytes. Por padrão, esse valor é 262,144 bytes (256 KB). |
| Contagem máxima do lote  |Uma propriedade que permite que você especifique o número máximo de eventos em cada que é enviado ao Azure Functions. O valor padrão é 100. |

O Azure Stream Analytics espera o status 200 do HTTP do aplicativo Functions para lotes que foram processados com êxito.

Quando o Azure Stream Analytics recebe a exceção 413 (“Entidade de solicitação http muito grande”) de uma função do Azure, ele reduz o tamanho dos lotes que envia para o Azure Functions. Em seu código de função do Azure, use essa exceção para verificar se o Azure Stream Analytics não enviará lotes grandes demais. Também verifique se os valores de contagem e o tamanho máximo do lote usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics.

> [!NOTE]
> Durante a conexão de teste, Stream Analytics envia um lote vazio para o Azure Functions para testar se a conexão entre os dois funciona. Verifique se seu aplicativo Functions manipula as solicitações em lote vazias para garantir que os testes de conexão sejam aprovados.

Além disso, em uma situação em que não há nenhum evento caindo em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função **computeResult** não é chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="custom-metadata-properties-for-output"></a>Personalizar propriedades de metadados para a saída 

Você pode anexar colunas de consulta às suas mensagens de saída como propriedades de usuário. Essas colunas não entram no conteúdo. As propriedades estão presentes no formato de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics têm suporte, exceto Registro e Matriz.  

Saídas com suporte: 
* Fila do Barramento de Serviço 
* Tópico do Barramento de Serviço 
* Hub de Eventos 

No exemplo a seguir, adicionamos os campos `DeviceId` e `DeviceStatus` aos metadados. 
* Consulta: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída: `DeviceId,DeviceStatus`

![Colunas da propriedade](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A captura de tela a seguir mostra as propriedades da mensagem de saída inspecionadas no EventHub por meio do [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Propriedades do sistema para saídas de fila e tópico do Barramento de Serviço 
Você pode anexar colunas de consulta como [propriedades do sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) às suas mensagens da fila ou do tópico do Barramento de Serviço de saída. Essas colunas não entram no conteúdo, em vez disso, a [propriedade do sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage correspondente é preenchida com os valores da coluna de consulta.
Estas propriedades do sistema têm suporte: `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Os valores de cadeia de caracteres dessas colunas são analisados como o tipo de valor de propriedade do sistema correspondente, e quaisquer falhas de análise são tratadas como erros de dados.
Esse campo é fornecido como um formato de objeto JSON. Os detalhes sobre esse formato são os seguintes:
* Entre chaves {}.
* Escritos em pares chave/valor.
* Chaves e valores devem ser cadeias de caracteres.
* A chave é o nome da propriedade do sistema, e o valor é o nome da coluna de consulta.
* Chaves e valores são separados por dois pontos.
* Cada par chave/valor é separado por uma vírgula.

Eis como usar essa propriedade:

* Consulta: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas da Propriedade do Sistema: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Isso define a `MessageId` nas mensagens de fila do barramento de serviço com os valores de `column1`, e a PartitionKey é definida com os valores de `column2`.

## <a name="partitioning"></a>Particionamento

A tabela a seguir resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte ao particionamento | Chave de partição  | Número de gravadores de saída |
| --- | --- | --- | --- |
| Repositório Azure Data Lake | Sim | Use tokens de {data} e {hora} no padrão de prefixo de caminho. Escolha o formato de data, como AAAA/MM/DD, DD/MM/AAAA ou MM-DD-AAAA. HH é usado para o formato de hora. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Banco de Dados SQL do Azure | Sim, precisa ser habilitada. | Com base na cláusula PARTITION BY na consulta. | Quando a opção Herdar Particionamento estiver habilitada, ela seguirá o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como obter melhor desempenho de taxa de transferência de gravação ao carregar dados no Banco de Dados SQL do Azure, consulte a [saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Use os tokens de {data} e {hora} dos seus campos de evento no padrão do caminho. Escolha o formato de data, como AAAA/MM/DD, DD/MM/AAAA ou MM-DD-AAAA. HH é usado para o formato de hora. A saída de blob pode ser particionada por um atributo de evento personalizado único {fieldname} ou {datetime:\<specifier>}. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Hubs de eventos do Azure | Sim | Sim | Varia dependendo do alinhamento da partição.<br /> Quando a chave de partição de saída do hub de eventos estiver alinhada de forma igual com a etapa de consulta (anterior) de upstream, o número de gravadores será o mesmo que o de partições na saída do hub de eventos. Cada gravador usa [a classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição de saída do hub de eventos não é alinhada com a etapa de consulta (anterior) de upstream, o número de gravadores será o mesmo que o de partições nessa etapa anterior. Cada gravador usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) no **EventHubClient** para enviar eventos para todas as partições de saída. |
| Power BI | Não | Nenhum | Não aplicável. |
| Armazenamento da tabela do Azure | Sim | Qualquer coluna de saída.  | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Tópico do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições no tópico de saída.  |
| Fila do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Com base na cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Sim | Com base na cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |

O número de gravadores de saída também pode ser controlado usando a cláusula `INTO <partition count>` (consulte [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) em sua consulta, o que pode ser útil para alcançar uma topologia de trabalho desejada. Se o seu adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até a quantidade de tempo de chegada tardia. Nesses casos, a saída é mesclada em um único gravador, o que pode causar gargalos no pipeline. Para saber mais sobre a política de chegada tardia, consulte [Considerações sobre a ordem de evento do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente, o mecanismo do Stream Analytics não grava uma mensagem por vez e usa lotes para maior eficiência. Quando a taxa de eventos de entrada e saída estiver alta, o Stream Analytics usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa.

A tabela a seguir explica algumas considerações para envio em lote de saída:

| Tipo de saída |    Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Repositório Azure Data Lake | Consulte [limites do Data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Até 4 MB por operação de gravação. |
| Banco de Dados SQL do Azure | Configurável usando a contagem máxima de lotes. Um máximo de 10.000 e um mínimo de 100 linhas por única inserção em massa por padrão.<br />Consulte [limites do SQL do Azure](../sql-database/sql-database-resource-limits.md). |  Inicialmente, cada lote é inserido em massa com a contagem máxima de lotes. O lote é dividido na metade (até a contagem de lote mínima) com base em erros de nova tentativa do SQL. |
| Armazenamento de Blobs do Azure | Consulte os [limites de Armazenamento do Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do bloco do blob é de 4 MB.<br />A contagem máxima do bloco do blob é de 50.000. |
| Hubs de eventos do Azure    | São 256 KB ou 1 MB por mensagem. <br />Consulte [Limites de Hubs de Eventos](../event-hubs/event-hubs-quotas.md). |    Quando o particionamento de entrada/saída não estiver alinhado, cada evento é compactado individualmente em um `EventData` e enviado em um lote com até o tamanho máximo da mensagem. Isso também ocorrerá se forem usadas [propriedades de metadados personalizadas](#custom-metadata-properties-for-output). <br /><br />  Quando o particionamento de entrada/saída estiver alinhado, vários eventos serão compactados em uma única instância `EventData` com até o tamanho máximo da mensagem e serão enviados.    |
| Power BI | Consulte [limites da API Rest do Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento da tabela do Azure | Consulte os [limites de Armazenamento do Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | O padrão é 100 entidades por cada transação. É possível configurá-lo para um valor menor, conforme necessário. |
| Fila do Barramento de Serviço do Azure    | São 256 KB por mensagem para a camada Standard e 1 MB para a camada Premium.<br /> Consulte [Limites do Barramento de Serviço](../service-bus-messaging/service-bus-quotas.md). | Use um evento único por mensagem. |
| Tópico do Barramento de Serviço do Azure | São 256 KB por mensagem para a camada Standard e 1 MB para a camada Premium.<br /> Consulte [Limites do Barramento de Serviço](../service-bus-messaging/service-bus-quotas.md). | Use um evento único por mensagem. |
| Azure Cosmos DB    | Consulte [Limites do Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | O tamanho do lote e a frequência de gravação são ajustados dinamicamente com base nas respostas do Cosmos DB. <br /> Não há limitações predeterminadas do Stream Analytics. |
| Funções do Azure    | | O tamanho de lote padrão é 262.144 bytes (256 KB). <br /> A contagem de eventos padrão por lote é de 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions) do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> 
> [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
