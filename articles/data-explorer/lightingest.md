---
title: LightIngest é um utilitário de linha de comando para ingestão no Azure Data Explorer.
description: Aprenda sobre lightingest, um utilitário de linha de comando para ingestão de dados ad-hoc no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109557"
---
# <a name="install-and-use-lightingest"></a>Instale e use o LightIngest

LightIngest é um utilitário de linha de comando para ingestão de dados ad-hoc no Azure Data Explorer.
O utilitário pode extrair dados de origem de uma pasta local ou de um contêiner de armazenamento blob do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* LightIngest - baixe-o como parte do [pacote Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)
* WinRAR - [baixe-o](http://www.win-rar.com/download.html) de www.win-rar.com/download.html

## <a name="install-lightingest"></a>Instale o lightingest

1. Navegue até o local do seu computador onde você baixou lightingest. 
1. Usando o WinRAR, extraia o diretório de *ferramentas* para o seu computador.

## <a name="run-lightingest"></a>Executar mais iluminado

1. Navegue até o diretório de *ferramentas* extraídas em seu computador.
1. Exclua as informações de localização existentes da barra de localização.
    
      ![Excluir informações de localização](media/lightingest/lightingest-location-bar.png)

1. Digite `cmd` e **pressione Enter**.
1. No prompt de `LightIngest.exe` comando, entre seguido do argumento relevante da linha de comando.

    > [!Tip]
    > Para obter uma lista de argumentos `LightIngest.exe /help`suportados da linha de comando, digite .
    >
    >![Ajuda da linha de comando](media/lightingest/lightingest-cmd-line-help.png)

1. Digite `LightIngest` seguido pela seqüência de conexões no cluster Azure Data Explorer que gerenciará a ingestão.
    Ensine a seqüência de conexões em aspas duplas e siga a [especificação de strings de conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Por exemplo: 
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* O método recomendado `LightIngest` é trabalhar com o ponto `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`final da ingestão em . Desta forma, o serviço Azure Data Explorer pode gerenciar a carga de ingestão, e você pode facilmente se recuperar de erros transitórios. No entanto, você `LightIngest` também pode configurar para`https://{yourClusterNameAndRegion}.kusto.windows.net`trabalhar diretamente com o ponto final do motor ( ).
* Para um ótimo desempenho de ingestão, é importante que o LightIngest saiba o tamanho dos dados brutos e assim `LightIngest` estimar o tamanho não compactado dos arquivos locais. No `LightIngest` entanto, pode não ser capaz de estimar corretamente o tamanho bruto de bolhas compactadas sem primeiro baixá-los. Portanto, ao ingerir bolhas compactadas, `rawSizeBytes` defina a propriedade nos metadados blob para tamanho de dados descompactado em bytes.

## <a name="general-command-line-arguments"></a>Argumentos gerais de linha de comando

|Nome do argumento         |Nome curto   |Type    |Obrigatório |Descrição                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |string  |Obrigatório |[Azure Data Explorer Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) especificando o ponto final do Kusto que lidará com a ingestão. Deve ser fechado em aspas duplas |
|-banco de dados             |-db          |string  |Opcional  |Nome do banco de dados do Azure Data Explorer |
|-mesa                |             |string  |Obrigatório |Nome da tabela Target Azure Data Explorer |
|-sourcePath           |-source      |string  |Obrigatório |Caminho para arquivos de origem ou URI raiz do recipiente blob. Se os dados estão em blobs, deve conter a chave da conta de armazenamento ou SAS. Recomendado para fechar em aspas duplas |
|-prefixo               |             |string  |Opcional  |Quando os dados de origem para ingerir residem no armazenamento blob, este prefixo url é compartilhado por todos os blobs, excluindo o nome do contêiner. <br>Por exemplo, se os `MyContainer/Dir1/Dir2`dados estão dentro, então o prefixo deve ser `Dir1/Dir2`. Recomenda-se a fechamento em cotações duplas |
|-padrão              |             |string  |Opcional  |Padrão pelo qual os arquivos/blobs de origem são escolhidos. Suporta curingas. Por exemplo, `"*.csv"`. Recomendado para fechar em aspas duplas |
|-zipPattern           |             |string  |Opcional  |Expressão regular para usar ao selecionar quais arquivos em um arquivo ZIP para ingerir.<br>Todos os outros arquivos no arquivo serão ignorados. Por exemplo, `"*.csv"`. É recomendável cercá-lo em aspas duplas |
|-formato               |-f           |string  |Opcional  |Formato de dados de origem. Deve ser um dos [formatos suportados](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats) |
|-ingestionMappingPath |-mapeamentoPath |string  |Opcional  |Caminho para o arquivo de mapeamento de colunas de ingestão (obrigatório para formatos Json e Avro). Ver [mapeamentos de dados](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-mapeamentoRef  |string  |Opcional  |Nome de um mapeamento de coluna de ingestão pré-criado (obrigatório para formatos Json e Avro). Ver [mapeamentos de dados](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-criaçãoTimePattern  |             |string  |Opcional  |Quando definido, é usado para extrair a propriedade CreationTime do caminho do arquivo ou blob. Consulte [Usando o argumento CreationTimePattern](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreprimeiro |bool    |Opcional  |Se definido, o primeiro registro de cada arquivo/bolha é ignorado (por exemplo, se os dados de origem tiver cabeçalhos) |
|-tag                  |             |string  |Opcional  |[Tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) para associar com os dados ingeridos. Múltiplas ocorrências são permitidas |
|-dontWait             |             |bool    |Opcional  |Se definido como 'verdadeiro', não espere a conclusão da ingestão. Útil ao ingerer grandes quantidades de arquivos/blobs |

### <a name="using-creationtimepattern-argument"></a>Usando o argumento CreationTimePattern

O `-creationTimePattern` argumento extrai a propriedade CreationTime do caminho do arquivo ou blob. O padrão não precisa refletir todo o caminho do item, apenas a seção que fecha o carimbo de data e hora que você deseja usar.
O valor do argumento deve conter três seções:
* Teste constante imediatamente precedendo o carimbo de data e hora, incluído em aspas únicas
* O formato de carimbo de tempo, na notação padrão [.NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Texto constante imediatamente após o carimbo de data e hora Por exemplo, se os nomes blob terminarem com 'valores históricos19840101.parquet' (o carimbo de `-creationTimePattern` tempo é de quatro dígitos para o ano, dois dígitos para o mês e dois dígitos para o dia do mês), o valor correspondente para o argumento é 'valores históricos'yyyyMMdd'.parquet'.

### <a name="command-line-arguments-for-advanced-scenarios"></a>Argumentos de linha de comando para cenários avançados

|Nome do argumento         |Nome curto   |Type    |Obrigatório |Descrição                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compressão          |-cr          |double  |Opcional  |Dica de razão de compressão. Útil ao ingerir arquivos/blobs compactados para ajudar o Azure Data Explorer a avaliar o tamanho dos dados brutos. Calculado como tamanho original dividido por tamanho comprimido |
|-limite                |-l           |inteiro |Opcional  |Se definido, limita a ingestão aos primeiros arquivos N |
|-listOnly             |-lista        |bool    |Opcional  |Se definido, só exibirá os itens que teriam sido selecionados para ingestão| 
|-ingestTimeout        |             |inteiro |Opcional  |Tempo máximo em minutos para a conclusão de todas as operações. Usa `60` como padrão|
|-forceSync            |             |bool    |Opcional  |Se estiver definido, força a ingestão síncrona. Usa `false` como padrão |
|-dataBatchSize        |             |inteiro |Opcional  |Define o limite total de tamanho (MB, descompactado) de cada operação ingerida |
|-arquivosInBatch         |             |inteiro |Opcional  |Define o limite de contagem de arquivos/bolhas de cada operação ingerida |
|-devTracing           |-traço       |string  |Opcional  |Se definido, os registros de diagnóstico são gravados `RollingLogs` em um diretório local (por padrão, no diretório atual ou podem ser modificados definindo o valor do switch) |

## <a name="blob-metadata-properties"></a>Propriedades de metadados blob
Quando usado com blobs `LightIngest` Azure, usará certas propriedades de metadados blob para aumentar o processo de ingestão.

|Propriedade de metadados                            | Uso                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Se definido, será interpretado como o tamanho de dados não compactado                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interpretado como carimbo de data e hora UTC. Se definido, será usado para substituir o tempo de criação em Kusto. Útil para cenários de enchimento de recarga |

## <a name="usage-examples"></a>Exemplos de uso

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Ingerir bolhas usando uma chave de conta de armazenamento ou um token SAS

* Ingera 10 bolhas em conta `ACCOUNT`de armazenamento `DIR`especificada, em pasta, em recipiente, `CONT`e correspondendo ao padrão`*.csv.gz`
* Destino é `DB`banco `TABLE`de dados, tabela, e o mapeamento `MAPPING` de ingestão é pré-criado no destino
* A ferramenta vai esperar até que as operações de ingestão sejam concluídas
* Observe as diferentes opções para especificar a chave de banco de dados e armazenamento de armazenamento versus token SAS

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Ingerindo todas as bolhas em um recipiente, sem incluir linhas de cabeçalho

* Ingera todas as bolhas `ACCOUNT`a conta `DIR1/DIR2`de `CONT`armazenamento especificada, em pasta, em recipiente, e correspondendo ao padrão`*.csv.gz`
* Destino é `DB`banco `TABLE`de dados, tabela, e o mapeamento `MAPPING` de ingestão é pré-criado no destino
* Blobs de origem contêm linha de cabeçalho, de modo que a ferramenta é instruída a soltar o primeiro registro de cada bolha
* A ferramenta publicará os dados para ingestão e não esperará que as operações de ingestão sejam concluídas

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Ingerindo todos os arquivos JSON de um caminho

* Ingera todos os `PATH`arquivos no caminho, correspondendo ao padrão`*.json`
* Destino é `DB`banco `TABLE`de dados, tabela, e o mapeamento de ingestão é definido em arquivo local`MAPPING_FILE_PATH`
* A ferramenta publicará os dados para ingestão e não esperará que as operações de ingestão sejam concluídas

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Ingerir arquivos e escrever arquivos de rastreamento de diagnóstico

* Ingera todos os `PATH`arquivos no caminho, correspondendo ao padrão`*.json`
* Destino é `DB`banco `TABLE`de dados, tabela, e o mapeamento de ingestão é definido em arquivo local`MAPPING_FILE_PATH`
* A ferramenta publicará os dados para ingestão e não esperará que as operações de ingestão sejam concluídas
* Os arquivos de rastreamento de diagnóstico serão gravados localmente em pasta`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Log de alteração
|Versão        |Alterações                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>O argumento `-zipPattern` foi adicionado a </li><li>O argumento `-listOnly` foi adicionado a </li><li>O resumo dos argumentos é exibido antes do início da execução</li><li>CreationTime é lido a partir de propriedades de metadados blob ou de blob ou nome de arquivo, de acordo com o `-creationTimePattern` argumento</li></ul>|
