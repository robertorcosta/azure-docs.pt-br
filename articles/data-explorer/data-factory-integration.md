---
title: Integração do Azure Data Explorer com a Fábrica de Dados do Azure
description: Neste tópico, integre o Azure Data Explorer com a Fábrica de Dados Do Azure para usar as atividades de cópia, pesquisa e comando
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293616"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integre o Azure Data Explorer com a fábrica de dados do Azure

[O Azure Data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado em nuvem que permite integrar diferentes armazenamentos de dados e executar atividades sobre os dados. O ADF permite criar fluxos de trabalho orientados a dados para orquestrar e automatizar a movimentação de dados e a transformação de dados. O Azure Data Explorer é um dos armazenamentos de [dados suportados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) na Fábrica de Dados Azure. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Atividades da Fábrica de Dados do Azure para o Azure Data Explorer

Várias integrações com a Fábrica de Dados do Azure estão disponíveis para usuários do Azure Data Explorer:

### <a name="copy-activity"></a>Atividade de cópia
 
A atividade do Azure Data Factory Copy é usada para transferir dados entre armazenamentos de dados. O Azure Data Explorer é suportado como uma fonte, onde os dados são copiados do Azure Data Explorer para qualquer armazenamento de dados suportado e um sink, onde os dados são copiados de qualquer armazenamento de dados suportado para o Azure Data Explorer. Para obter mais informações, consulte [os dados de cópia para ou do Azure Data Explorer usando o Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). e para um passo-through detalhado ver [dados de carga da Fábrica de Dados Do Azure no Azure Data Explorer](data-factory-load-data.md).
O Azure Data Explorer é suportado pelo Azure IR (Integration Runtime), usado quando os dados são copiados no Azure, e o IR auto-hospedado, usado ao copiar dados de/para data stores localizadas no local ou em uma rede com controle de acesso, como uma Rede Virtual Do Azure. Para obter mais informações, veja [qual IR usar](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Ao usar a atividade de cópia e criar um **Serviço Vinculado** ou um Conjunto **de Dados,** selecione o armazenamento de dados **Azure Data Explorer (Kusto)** e não o antigo armazenamento de dados **Kusto**.  

### <a name="lookup-activity"></a>Atividade de procurar
 
A atividade 'Procurar' é usada para executar consultas no Azure Data Explorer. O resultado da consulta será devolvido como a saída da atividade de Pesquisa, podendo ser usado na próxima atividade no pipeline, conforme descrito na [documentação aDF Lookup](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Além do limite de tamanho de resposta de 5.000 linhas e 2 MB, a atividade também tem um limite de tempo limite de consulta de 1 hora.

### <a name="command-activity"></a>Atividade de comando

A atividade Command permite a execução de [comandos](/azure/kusto/concepts/#control-commands)de controle do Azure Data Explorer . Ao contrário das consultas, os comandos de controle podem potencialmente modificar dados ou metadados. Alguns dos comandos de controle são direcionados para ingerir dados no `.ingest` `.set-or-append`Azure Data Explorer, usando comandos como ou ) `.export`ou copiar dados do Azure Data Explorer para armazenamentos de dados externos usando comandos como .
Para obter um passo a passo detalhado da atividade de comando, consulte usar a atividade de [comando azure Data Factory para executar os comandos de controle do Azure Data Explorer](data-factory-command-activity.md).  Usar um comando de controle para copiar dados pode, às vezes, ser uma opção mais rápida e barata do que a atividade Copiar. Para determinar quando usar a atividade Comando versus a atividade Copiar, consulte [selecionar entre as atividades Copiar e Comandar ao copiar dados](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Copiar em massa a partir de um modelo de banco de dados

O [Copy em massa de um banco de dados para o Azure Data Explorer usando o modelo Azure Data Factory](data-factory-template.md) é um pipeline predefinido da Fábrica de Dados Do Azure. O modelo é usado para criar muitos pipelines por banco de dados ou por tabela para uma cópia de dados mais rápida. 

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento 

[Os fluxos de dados da Fábrica de Dados do Azure](/azure/data-factory/concepts-data-flow-overview) são transformações de dados projetadas visualmente que permitem aos engenheiros de dados desenvolver a lógica de transformação de dados gráficos sem escrever código. Para criar um fluxo de dados e ingerir dados no Azure Data Explorer, use o seguinte método:

1. Crie o [fluxo de dados de mapeamento](/azure/data-factory/data-flow-create).
1. [Exporte os dados para o Azure Blob](/azure/data-factory/data-flow-sink). 
1. Defina [a grade de eventos](/azure/data-explorer/ingest-data-event-grid) ou a atividade de cópia do [ADF](/azure/data-explorer/data-factory-load-data) para ingerir os dados no Azure Data Explorer.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Selecione entre as atividades do Comando Copy e do Azure Data Explorer ao copiar dados 

Esta seção irá ajudá-lo a selecionar a atividade correta para suas necessidades de cópia de dados.

Ao copiar dados do Azure Data Explorer, existem duas opções disponíveis na Fábrica de Dados do Azure:
* Copiar atividade.
* A atividade do Azure Data Explorer Command, que executa um dos comandos de controle que transferem dados no Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Copiar dados do Azure Data Explorer
  
Você pode copiar dados do Azure Data [`.export`](/azure/kusto/management/data-export/) Explorer usando a atividade de cópia ou o comando. O `.export` comando executa uma consulta e, em seguida, exporta os resultados da consulta. 

Veja a tabela a seguir para `.export` comparar a atividade e o comando Copiar para copiar dados do Azure Data Explorer.

| | Atividade de cópia | .comando de exportação |
|---|---|---|
| **Descrição do fluxo** | O ADF executa uma consulta no Kusto, processa o resultado e envia-o para o armazenamento de dados de destino. <br>**(ADX > ADF > armazenamento de dados da pia)** | O ADF `.export` envia um comando de controle para o Azure Data Explorer, que executa o comando, e envia os dados diretamente para o armazenamento de dados de destino. <br>**(ADX > sink data store**) |
| **Armazenamentos de dados de destino suportados** | Uma grande variedade de armazenamentos de [dados suportados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, Banco de Dados SQL |
| **Desempenho** | Centralizado | <ul><li>Distribuídos (padrão), exportando dados de vários lontoas simultaneamente</li><li>Mais rápido e eficiente em COGS.</li></ul> |
| **Limites do servidor** | [Os limites de consulta](/azure/kusto/concepts/querylimits) podem ser estendidos/desativados. Por padrão, as consultas ADF contêm: <ul><li>Limite de tamanho de 500.000 registros ou 64 MB.</li><li>Limite de tempo de 10 minutos.</li><li>`noTruncation`definido para falso.</li></ul> | Por padrão, amplia ou desativa os limites de consulta: <ul><li>Os limites de tamanho estão desativados.</li><li>O tempo de intervalo do servidor é estendido para 1 hora.</li><li>`MaxMemoryConsumptionPerIterator`e `MaxMemoryConsumptionPerQueryPerNode` são estendidos ao máximo (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Se o destino da sua cópia for `.export` um dos armazenamentos de dados suportados pelo comando `.export` e se nenhum dos recursos de atividade do Copiar for crucial para suas necessidades, selecione o comando.

### <a name="copying-data-to-azure-data-explorer"></a>Copiando dados para o Azure Data Explorer

Você pode copiar dados para o Azure Data Explorer usando os comandos de`.set-or-append` `.set-or-replace`atividade `.set` `.replace)`de cópia ou de ingestão, como [ingerir de consulta](/azure/kusto/management/data-ingestion/ingest-from-query) ( , , e [ingerir do armazenamento](/azure/kusto/management/data-ingestion/ingest-from-storage) ).`.ingest` 

Veja a tabela a seguir para comparar a atividade do Copy e os comandos de ingestão para copiar dados no Azure Data Explorer.

| | Atividade de cópia | Ingestão de consulta<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestão de armazenamento <br> `.ingest` |
|---|---|---|---|
| **Descrição do fluxo** | O ADF obtém os dados do armazenamento de dados de origem, converte-os em um formato tabular e faz as alterações necessárias de mapeamento de esquemas. O ADF então envia os dados para blobs do Azure, divide-os em pedaços e, em seguida, baixa as bolhas para ingeri-los na tabela ADX. <br> (**Fonte data store > ADF > Blobs Azure > ADX**) | Esses comandos podem executar uma `.show` consulta ou um comando e ingerr os resultados da consulta em uma tabela **(ADX > ADX**). | Este comando ingere dados em uma tabela "puxando" os dados de um ou mais artefatos de armazenamento em nuvem. |
| **Armazenamentos de dados de origem suportados** |  [variedade de opções](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (usando o plugin sql_request), Cosmos (usando o plugin cosmosdb_sql_request) e qualquer outro armazenamento de dados que forneça APIs HTTP ou Python. | Sistema de arquivos, armazenamento azure blob, ADLS Gen 1, ADLS Gen 2 |
| **Desempenho** | As ingesações são enfileiradas e gerenciadas, o que garante ingestão de tamanho pequeno e garante alta disponibilidade, fornecendo balanceamento de carga, repetições e manipulação de erros. | <ul><li>Esses comandos não foram projetados para importação de dados de alto volume.</li><li>Funciona como esperado e mais barato. Mas para cenários de produção e quando as taxas de tráfego e tamanhos de dados forem grandes, use a atividade Copiar.</li></ul> |
| **Limites do servidor** | <ul><li>Sem limite de tamanho.</li><li>Limite máximo de tempo: 1 hora por bolha ingerida. |<ul><li>Há apenas um limite de tamanho na parte de consulta, `noTruncation=true`que pode ser ignorado especificando .</li><li>Limite máximo de tempo: 1 hora.</li></ul> | <ul><li>Sem limite de tamanho.</li><li>Limite máximo de tempo: 1 hora.</li></ul>|

> [!TIP]
> * Ao copiar dados do ADF para o `ingest from query` Azure Data Explorer, use os comandos.  
> * Para grandes conjuntos de dados (>1GB), use a atividade Copiar.  

## <a name="required-permissions"></a>Permissões necessárias

A tabela a seguir lista as permissões necessárias para várias etapas na integração com a Fábrica de Dados Do Azure.

| Etapa | Operação | Nível mínimo de permissões | Observações |
|---|---|---|---|
| **Criar um serviço vinculado** | Navegação de banco de dados | *visualizador de banco de dados* <br>O usuário logado usando ADF deve ser autorizado a ler metadados de banco de dados. | O usuário pode fornecer o nome do banco de dados manualmente. |
| | Teste a conexão | *monitor de banco de dados* ou tabela *ingestor* <br>O principal de serviço deve `.show` ser autorizado a executar comandos de nível de banco de dados ou ingestão de nível de tabela. | <ul><li>TestConnection verifica a conexão com o cluster, e não com o banco de dados. Pode ter sucesso mesmo que o banco de dados não exista.</li><li>As permissões de admin de mesa não são suficientes.</li></ul>|
| **Criando um conjunto de dados** | Navegação de mesa | *monitor de banco de dados* <br>O usuário logado usando ADF, deve ser `.show` autorizado a executar comandos de nível de banco de dados. | O usuário pode fornecer o nome da tabela manualmente.|
| **Criando um conjunto de dados** ou atividade de **cópia** | Visualizar dados | *visualizador de banco de dados* <br>O diretor de serviço deve ser autorizado a ler metadados do banco de dados. | | 
|   | Esquema de importação | *visualizador de banco de dados* <br>O diretor de serviço deve ser autorizado a ler metadados do banco de dados. | Quando o ADX é a fonte de uma cópia tabular-tabular, o ADF importará esquema automaticamente, mesmo que o usuário não importe o esquema explicitamente. |
| **ADX como Sink** | Crie um mapeamento de coluna de nome | *monitor de banco de dados* <br>O diretor de serviço deve `.show` ser autorizado a executar comandos de nível de banco de dados. | <ul><li>Todas as operações obrigatórias funcionarão com *tabela ingestor.*</li><li> Algumas operações opcionais podem falhar.</li></ul> |
|   | <ul><li>Crie um mapeamento CSV na tabela</li><li>Solte o mapeamento</li></ul>| *ingestor de tabela* ou *administrador de banco de dados* <br>O diretor de serviço deve ser autorizado a fazer alterações em uma tabela. | |
|   | Ingestão de dados | *ingestor de tabela* ou *administrador de banco de dados* <br>O diretor de serviço deve ser autorizado a fazer alterações em uma tabela. | | 
| **ADX como fonte** | Executar consulta | *visualizador de banco de dados* <br>O diretor de serviço deve ser autorizado a ler metadados do banco de dados. | |
| **Comando kusto** | | De acordo com o nível de permissões de cada comando. |

## <a name="performance"></a>Desempenho 

Se o Azure Data Explorer for a fonte e você usar a atividade de pesquisa, cópia ou comando que contém uma consulta onde, consulte [as práticas recomendadas de consulta](/azure/kusto/query/best-practices) para informações de desempenho e [documentação ADF para atividade de cópia](/azure/data-factory/copy-activity-performance).
  
Esta seção aborda o uso da atividade de cópia onde o Azure Data Explorer é o dissipador. O throughput estimado para o afundamento do Azure Data Explorer é de 11-13 MBps. A tabela a seguir detalha os parâmetros que influenciam o desempenho do dissipador do Azure Data Explorer.

| Parâmetro | Observações |
|---|---|
| **Proximidade geográfica dos componentes** | Coloque todos os componentes na mesma região:<ul><li>fontes e armazenamentos de dados de pia.</li><li>Tempo de execução da integração do ADF.</li><li>Seu cluster ADX.</li></ul>Certifique-se de que pelo menos o tempo de execução da integração esteja na mesma região do seu cluster ADX. |
| **Número de DIUs** | 1 VM para cada 4 DIUs usados pela ADF. <br>O aumento das DIUs só ajudará se sua fonte for uma loja baseada em arquivos com vários arquivos. Cada VM então processará um arquivo diferente em paralelo. Portanto, copiar um único arquivo grande terá uma latência maior do que copiar vários arquivos menores.|
|**Quantidade e SKU do seu cluster ADX** | O alto número de nodos ADX aumentará o tempo de processamento da ingestão.|
| **Paralelismo** | Para copiar uma quantidade muito grande de dados de um banco de dados, particione seus dados e use um loop ForEach que copia cada partição em paralelo ou use a [Cópia em massa do Banco de Dados para o Modelo do Explorador de Dados do Azure](data-factory-template.md). Nota: O grau de **configuração** > **do paralelismo** na atividade Copiar não é relevante para o ADX. |
| **Complexidade do processamento de dados** | A latência varia de acordo com o formato do arquivo de origem, mapeamento da coluna e compactação.|
| **A VM executando seu tempo de execução de integração** | <ul><li>Para cópia do Azure, as VMs adf e as SKUs da máquina não podem ser alteradas.</li><li> Para a cópia on-prem to Azure, determine que a VM que hospeda seu IR auto-hospedado é forte o suficiente.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Dicas e armadilhas comuns

### <a name="monitor-activity-progress"></a>Monitorar o progresso da atividade

* Ao monitorar o progresso da atividade, a propriedade *data written* pode ser muito maior do que a propriedade *Data read* porque a leitura *de dados* é calculada de acordo com o tamanho do arquivo binário, enquanto os *dados gravados* são calculados de acordo com o tamanho da memória, depois que os dados são desserializados e descompactados.

* Ao monitorar o progresso da atividade, você pode ver que os dados estão gravados no dissipador do Azure Data Explorer. Ao consultar a tabela Azure Data Explorer, você verá que os dados ainda não chegaram. Isso porque há dois estágios ao copiar para o Azure Data Explorer. 
    * O primeiro estágio lê os dados de origem, divide-os em pedaços de 900 MB e envia cada pedaço para um Azure Blob. A primeira etapa é vista pela visão de progresso da atividade da ADF. 
    * A segunda etapa começa assim que todos os dados são carregados para o Azure Blobs. Os nós do mecanismo do Azure Data Explorer baixam as bolhas e ingerem os dados na tabela do sumidouro. Os dados são então vistos na tabela Azure Data Explorer.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Falha na ingestão de arquivos CSV devido à fuga inadequada

O Azure Data Explorer espera que os arquivos CSV se alinhem com [o RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Ele espera:
* Campos que contêm caracteres que requerem fuga (como " e novas linhas) devem começar e terminar com um **"** caractere, sem espaço em branco. Todos os **"** caracteres *dentro* do campo são escapados usando um caractere duplo **(** **").** Por exemplo, _"Hello, ""World""_ é um arquivo CSV válido com um único registro com uma única coluna ou campo com o conteúdo _Hello, "World"_.
* Todos os registros no arquivo devem ter o mesmo número de colunas e campos.

A Azure Data Factory permite o caractere barra invertida (escape). Se você gerar um arquivo CSV com um caractere de barra invertida usando a Fábrica de Dados do Azure, a ingestão do arquivo no Azure Data Explorer falhará.

#### <a name="example"></a>Exemplo

Os seguintes valores de texto: Olá, "Mundo"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Deve aparecer em um arquivo CSV adequado da seguinte forma: "Olá, "Mundo"""<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
""ABC\D"EF"<br/>

Usando o caractere de fuga padrão (barra invertida), o CSV a seguir \"\"não funcionará com o Azure Data Explorer: "Hello, World"<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
\""ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Objetos JSON aninhados

Ao copiar um arquivo JSON para o Azure Data Explorer, observe que:
* Os arrays não são suportados.
* Se sua estrutura JSON contiver tipos de dados de objeto, a Fábrica de Dados do Azure achatará os itens filho do objeto e tentará mapear cada item filho para uma coluna diferente na tabela Do Azure Data Explorer. Se você quiser que todo o item do objeto seja mapeado para uma única coluna no Azure Data Explorer:
    * Ingera toda a linha JSON em uma única coluna dinâmica no Azure Data Explorer.
    * Edite manualmente a definição do pipeline usando o editor JSON da Azure Data Factory. Em **Mapeamentos**
       * Remova os vários mapeamentos criados para cada item filho e adicione um único mapeamento que mapeie seu tipo de objeto na coluna da tabela.
       * Após o suporte quadrado de fechamento, adicione uma comuma seguida por:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Especificar propriedades adicionais ao copiar para o Azure Data Explorer

> [!NOTE]
> Este recurso está disponível atualmente editando manualmente a carga útil JSON. 

Adicione uma única linha na seção "sink" da atividade de cópia da seguinte forma:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Escapar do valor pode ser complicado. Use o seguinte trecho de código como referência:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

O valor impresso:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como [copiar dados para o Azure Data Explorer usando o Azure Data Factory](data-factory-load-data.md).
* Saiba mais sobre o uso do [modelo Azure Data Factory para cópia em massa do banco de dados para o Azure Data Explorer](data-factory-template.md).
* Saiba como usar [a atividade de comando da Fábrica de Dados do Azure para executar os comandos de controle do Azure Data Explorer](data-factory-command-activity.md).
* Saiba mais sobre [as consultas do Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.



