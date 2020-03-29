---
title: Use comandos e recursos de notebook incorporados no Azure Cosmos DB (visualização)
description: Aprenda a usar comandos e recursos incorporados para fazer operações comuns usando os notebooks embutidos do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513392"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Use comandos e recursos de notebook incorporados no Azure Cosmos DB (visualização)

Os notebooks Jupyter incorporados no Azure Cosmos DB permitem analisar e visualizar seus dados a partir do portal Azure. Este artigo descreve como usar os recursos e comandos do notebook integrado para realizar operações comuns.

## <a name="install-a-new-package"></a>Instale um novo pacote
Depois de habilitar o suporte a notebooks para suas contas do Azure Cosmos, você pode abrir um novo notebook e instalar um pacote.

Em uma nova célula de código, insira e execute o seguinte código, substituindo-o ``PackageToBeInstalled`` pelo pacote Python desejado.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Este pacote estará disponível para uso a partir de qualquer notebook no espaço de trabalho da conta Azure Cosmos. 

> [!TIP]
> Se o seu notebook precisar de um pacote personalizado, recomendamos que você adicione um celular em seu notebook para instalar o pacote, pois os pacotes são removidos se você [redefinir o espaço de trabalho](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Execute uma consulta SQL

Você pode ``%%sql`` usar o comando magic para executar uma [consulta SQL](sql-query-getting-started.md) contra qualquer contêiner em sua conta. Use a sintaxe:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` Substitua ``{container_id}`` e com o nome do banco de dados e contêiner em sua conta Cosmos. Se ``--database`` os ``--container`` argumentos e argumentos não forem fornecidos, a consulta será executada no [banco de dados e contêiner padrão](#set-default-database-for-queries).
- Você pode executar qualquer consulta SQL válida no Azure Cosmos DB. O texto de consulta deve estar em uma nova linha.

Por exemplo:  
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Corra ```%%sql?``` em uma cela para ver a documentação de ajuda para o comando sql magic no notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Execute uma consulta SQL e saída para um DataFrame do Pandas

Você pode produzir os ``%%sql`` resultados de uma consulta em um [DataFrame do Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Use a sintaxe: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` Substitua ``{container_id}`` e com o nome do banco de dados e contêiner em sua conta Cosmos. Se ``--database`` os ``--container`` argumentos e argumentos não forem fornecidos, a consulta será executada no [banco de dados e contêiner padrão](#set-default-database-for-queries).
- Substitua pelo ``{outputDataFrameVar}`` nome da variável DataFrame que conterá os resultados.
- Você pode executar qualquer consulta SQL válida no Azure Cosmos DB. O texto de consulta deve estar em uma nova linha. 

Por exemplo: 

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>Carregue itens JSON em um contêiner
Você pode ``%%upload`` usar o comando mágico para carregar dados de um arquivo JSON para um contêiner Azure Cosmos especificado. Use o seguinte comando para carregar os itens:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` Substitua ``{container_id}`` e com o nome do banco de dados e contêiner em sua conta do Azure Cosmos. Se ``--database`` os ``--container`` argumentos e argumentos não forem fornecidos, a consulta será executada no [banco de dados e contêiner padrão](#set-default-database-for-queries).
- Substitua pela ``{url_location_of_file}`` localização do seu arquivo JSON. O arquivo deve ser uma matriz de objetos JSON válidos e deve ser acessível pela Internet pública.

Por exemplo: 

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Com as estatísticas de saída, você pode calcular o RU/s efetivo usado para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos ao longo de 38 segundos, o RU/s efetivo é de 25.000 RUs / 38 segundos = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Definir banco de dados padrão para consultas
Você pode definir ```%%sql``` os comandos de banco de dados padrão usados para o notebook. Substitua ```{database_id}``` pelo nome do seu banco de dados.

```bash
%database {database_id}
```
Corra ```%database?``` em uma cela para ver a documentação no caderno.

## <a name="set-default-container-for-queries"></a>Definir o contêiner padrão para consultas
Você pode definir ```%%sql``` os comandos de contêiner padrão usados para o notebook. Substitua pelo ```{container_id}``` nome do seu recipiente.

```bash
%container {container_id}
```
Corra ```%container?``` em uma cela para ver a documentação no caderno.

## <a name="use-built-in-nteract-data-explorer"></a>Use o explorador de dados nteract incorporado
Você pode usar o explorador de [dados nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporado para filtrar e visualizar um DataFrame. Para habilitar esse recurso, defina a opção ``pd.options.display.html.table_schema`` para ``pd.options.display.max_rows`` e ``None`` ``True`` ``pd.options.display.max_rows`` para o valor desejado (você pode definir para mostrar todos os resultados).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract data explorer](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Use o Python SDK incorporado
A versão 4 do [Azure Cosmos DB Python SDK para SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) está instalada e incluída no ambiente do notebook para a conta do Azure Cosmos.

Use a ``cosmos_client`` instância incorporada para executar qualquer operação SDK. 

Por exemplo: 

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Veja [as amostras python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> O SDK Python incorporado só é suportado para contas API SQL (Core). Para outras APIs, você precisará [instalar o driver Python relevante](#install-a-new-package) que corresponde à API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância personalizada de``cosmos_client``
Para obter mais flexibilidade, você ``cosmos_client`` pode criar uma instância personalizada de:

- Personalize a [política de conexão](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Executar operações contra uma conta Azure Cosmos diferente da que você está em

Você pode acessar a seqüência de conexões e a chave principal da conta corrente através das variáveis de [ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Acesse o ponto final da conta e as variáveis de env principal
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> As ``COSMOS_ENDPOINT`` ``COSMOS_KEY`` variáveis de ambiente e ambiente são aplicáveis apenas para a API SQL. Para outras APIs, encontre o ponto final e a chave na lâmina Strings ou **Keys** de **conexão** na sua conta Do Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Redefinir espaços de trabalho de notebooks
Para redefinir o espaço de trabalho dos notebooks para as configurações padrão, selecione **Redefinir espaço de trabalho** na barra de comando. Isso removerá quaisquer pacotes instalados personalizados e reiniciará o servidor Jupyter. Seus notebooks, arquivos e recursos do Azure Cosmos não serão afetados.  

![Redefinir espaços de trabalho de notebooks](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Próximas etapas

- Conheça os benefícios dos [notebooks Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Conheça o [Azure Cosmos DB Python SDK para API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
