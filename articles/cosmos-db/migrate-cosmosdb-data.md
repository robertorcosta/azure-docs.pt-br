---
title: Migrar centenas de terabytes de dados para o Azure Cosmos DB
description: Este doc descreve como você pode migrar 100 de terabytes de dados para o Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880213"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrar centenas de terabytes de dados para o Azure Cosmos DB 

O Azure Cosmos DB pode armazenar terabytes de dados. Você pode executar uma migração de dados em larga escala para mover sua carga de trabalho de produção para o Azure Cosmos DB. Este artigo descreve os desafios envolvidos na movimentação de dados em grande escala para o Azure Cosmos DB e apresenta a ferramenta que ajuda a enfrentá-los e migra os dados para o Azure Cosmos DB. Nesse estudo de caso, o cliente usou a API do SQL do Cosmos DB.  

Antes de migrar toda a carga de trabalho para o Azure Cosmos DB, você pode migrar um subconjunto de dados para validar alguns dos aspectos como escolha da chave de partição, desempenho da consulta e modelagem de dados. Depois de validar a prova de conceito, você pode mover toda a carga de trabalho para o Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Ferramentas para migração de dados 

As estratégias de migração do Azure Cosmos DB atualmente diferem com base na escolha da API e no tamanho dos dados. Para migrar conjuntos de dados menores – para validar a modelagem de dados, o desempenho da consulta, a escolha da chave de partição etc. – você pode escolher a [Ferramenta de Migração de Dados](import-data.md) ou o [conector Azure Cosmos DB da Azure Data Factory.](../data-factory/connector-azure-cosmos-db.md) Se você estiver familiarizado com o Spark, você também pode optar por usar o [conector Azure Cosmos DB Spark](spark-connector.md) para migrar dados.

## <a name="challenges-for-large-scale-migrations"></a>Desafios para migrações em larga escala 

As ferramentas existentes para migrar dados para o Azure Cosmos DB têm algumas limitações que se tornam especialmente aparentes em grandes escalas:

 * **Recursos de escala limitada**: A fim de migrar terabytes de dados para o Azure Cosmos DB o mais rápido possível e para consumir efetivamente todo o throughput provisionado, os clientes de migração devem ter a capacidade de escalar indefinidamente.  

* **Falta de rastreamento de progresso e check-pointing**: É importante acompanhar o progresso da migração e ter check-pointing enquanto migra grandes conjuntos de dados. Caso contrário, qualquer erro que ocorra durante a migração impedirá a migração, e você terá que iniciar o processo do zero. Não seria produtivo reiniciar todo o processo de migração quando 99% dele já tiver concluído.  

* **Falta de fila de letras mortas**: Dentro de grandes conjuntos de dados, em alguns casos pode haver problemas com partes dos dados de origem. Além disso, pode haver problemas transitórios com o cliente ou a rede. Qualquer um desses casos não deve causar o fracasso de toda a migração. Embora a maioria das ferramentas de migração tenha recursos robustos de repetição que protegem contra problemas intermitentes, nem sempre é suficiente. Por exemplo, se menos de 0,01% dos documentos de dados de origem forem maiores que 2 MB de tamanho, isso fará com que a gravação do documento falhe no Azure Cosmos DB. Idealmente, é útil para a ferramenta de migração persistir esses documentos 'falhos' para outra fila de letras mortas, que pode ser processada após a migração. 

Muitas dessas limitações estão sendo corrigidas para ferramentas como a fábrica de dados do Azure, os serviços de migração de dados do Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Ferramenta personalizada com biblioteca executora a granel 

Os desafios descritos na seção acima podem ser resolvidos usando uma ferramenta personalizada que pode ser facilmente dimensionada em várias instâncias e é resistente a falhas transitórias. Além disso, a ferramenta personalizada pode pausar e retomar a migração em vários pontos de verificação. O Azure Cosmos DB já fornece a [biblioteca de executores em massa](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) que incorpora alguns desses recursos. Por exemplo, a biblioteca de executores em massa já tem a funcionalidade para lidar com erros transitórios e pode dimensionar threads em um único nó para consumir cerca de 500 RUs por nó. A biblioteca executora em massa também particiona o conjunto de dados de origem em microlotes que são operados independentemente como uma forma de verificação.  

A ferramenta personalizada usa a biblioteca de executores em massa e suporta a dimensionamento entre vários clientes e para rastrear erros durante o processo de ingestão. Para usar esta ferramenta, os dados de origem devem ser particionados em arquivos distintos no AdLS (ADLS) para que diferentes trabalhadores de migração possam pegar cada arquivo e ingeri-los no Azure Cosmos DB. A ferramenta personalizada faz uso de uma coleção separada, que armazena metadados sobre o progresso da migração para cada arquivo de origem individual no ADLS e rastreia quaisquer erros associados a eles.  

A imagem a seguir descreve o processo de migração usando esta ferramenta personalizada. A ferramenta está sendo executado em um conjunto de máquinas virtuais, e cada máquina virtual consulta a coleção de rastreamento no Azure Cosmos DB para adquirir um contrato de locação em uma das partições de dados de origem. Feito isso, a partição de dados de origem é lida pela ferramenta e ingerida no Azure Cosmos DB usando a biblioteca de executores em massa. Em seguida, a coleta de rastreamento é atualizada para registrar o progresso da ingestão de dados e quaisquer erros encontrados. Depois que uma partição de dados é processada, a ferramenta tenta consultar a próxima partição de origem disponível. Ele continua a processar a próxima partição de origem até que todos os dados sejam migrados. O código fonte da ferramenta está disponível [aqui.](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)  

 
![Configuração da ferramenta de migração](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

A coleção de rastreamento contém documentos como mostrado no exemplo a seguir. Você verá esses documentos um para cada partição nos dados de origem.  Cada documento contém os metadados para a partição de dados de origem, como sua localização, status de migração e erros (se houver):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Pré-requisitos para migração de dados 

Antes do início da migração de dados, existem alguns pré-requisitos a considerar:  

#### <a name="estimate-the-data-size"></a>Estimar o tamanho dos dados:  

O tamanho dos dados de origem pode não ser exatamente o mapa do tamanho dos dados no Azure Cosmos DB. Alguns documentos de amostra da fonte podem ser inseridos para verificar o tamanho dos dados no Azure Cosmos DB. Dependendo do tamanho do documento de amostra, o tamanho total dos dados no Azure Cosmos DB pós-migração pode ser estimado. 

Por exemplo, se cada documento após a migração no Azure Cosmos DB for em torno de 1 KB e se houver cerca de 60 bilhões de documentos no conjunto de dados de origem, isso significaria que o tamanho estimado no Azure Cosmos DB seria próximo de 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Pré-criar contêineres com RUs suficientes: 

Embora o Azure Cosmos DB dimensione o armazenamento automaticamente, não é aconselhável começar do menor tamanho do contêiner. Os contêineres menores têm menor disponibilidade de throughput, o que significa que a migração levaria muito mais tempo para ser concluída. Em vez disso, é útil criar os contêineres com o tamanho final dos dados (conforme estimado na etapa anterior) e certificar-se de que a carga de trabalho de migração está consumindo totalmente o throughput provisionado.  

Na etapa anterior. uma vez que o tamanho dos dados foi estimado em torno de 60 TB, um contêiner de pelo menos 2,4 M RUs é necessário para acomodar todo o conjunto de dados.  

 

#### <a name="estimate-the-migration-speed"></a>Estimar a velocidade de migração: 

Supondo que a carga de trabalho de migração possa consumir todo o throughput provisionado, o provisionamento ao longo forneceria uma estimativa da velocidade de migração. Continuando o exemplo anterior, 5 RUs são necessárias para escrever um documento de 1 KB na conta Azure Cosmos DB SQL API.  2,4 milhões de RUs permitiriam a transferência de 480.000 documentos por segundo (ou 480 MB/s). Isso significa que a migração completa de 60 TB levará 125.000 segundos ou cerca de 34 horas.  

Caso você queira que a migração seja concluída dentro de um dia, você deve aumentar o throughput provisionado para 5 milhões de RUs. 

 

#### <a name="turn-off-the-indexing"></a>Desligue a indexação:  

Uma vez que a migração deve ser concluída o mais rápido possível, é aconselhável minimizar o tempo e as RUs gastos na criação de índices para cada um dos documentos ingeridos.  O Azure Cosmos DB indexa automaticamente todas as propriedades, vale a pena minimizar a indexação para alguns termos selecionados ou desligá-lo completamente para o curso da migração. Você pode desativar a política de indexação do contêiner alterando o modo de indexação para nenhum como mostrado abaixo:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Depois que a migração estiver concluída, você pode atualizar a indexação.  

## <a name="migration-process"></a>Processo de migração 

Depois que os pré-requisitos forem concluídos, você pode migrar dados com as seguintes etapas:  

1. Primeiro importe os dados da fonte para o Azure Blob Storage. Para aumentar a velocidade de migração, é útil paraparalizar entre partições de origem distintas. Antes de iniciar a migração, o conjunto de dados de origem deve ser particionado em arquivos com tamanho em torno de 200 MB.   

2. A biblioteca de executores em massa pode aumentar, para consumir 500.000 RUs em um único cliente VM. Como o throughput disponível é de 5 milhões de RUs, 10 VMs Ubuntu 16.04 (Standard_D32_v3) devem ser provisionados na mesma região onde está localizado o banco de dados Do Azure Cosmos. Você deve preparar essas VMs com a ferramenta de migração e seu arquivo de configurações.  

3. Execute o passo da fila em uma das máquinas virtuais do cliente. Essa etapa cria a coleta de rastreamento, que verifica o contêiner ADLS e cria um documento de rastreamento de progresso para cada um dos arquivos de partição do conjunto de dados de origem.  

4. Em seguida, execute o passo de importação em todas as VMs do cliente. Cada um dos clientes pode tomar posse de uma partição de origem e ingerir seus dados no Azure Cosmos DB. Uma vez concluído e seu status atualizado na coleção de rastreamento, os clientes podem então consultar a próxima partição de origem disponível na coleção de rastreamento.  

5. Esse processo continua até que todo o conjunto de partições de origem fosse ingerido. Uma vez que todas as partições de origem sejam processadas, a ferramenta deve ser reexecutada no modo de correção de erros na mesma coleção de rastreamento. Esta etapa é necessária para identificar as partições de origem que devem ser reprocessadas devido a erros.  

6. Alguns desses erros podem ser devido a documentos incorretos nos dados de origem. Estes devem ser identificados e consertados. Em seguida, você deve executar a etapa de importação nas partições com falha para retolá-las. 

Uma vez concluída a migração, você pode validar que a contagem de documentos no Azure Cosmos DB é a mesma que a contagem de documentos no banco de dados de origem. Neste exemplo, o tamanho total no Azure Cosmos DB foi de 65 terabytes. Após a migração, a indexação pode ser ligada seletivamente e as RUs podem ser reduzidas ao nível exigido pelas operações da carga de trabalho.

## <a name="contact-the-azure-cosmos-db-team"></a>Entre em contato com a equipe do Azure Cosmos DB
Embora você possa seguir este guia para migrar com sucesso grandes conjuntos de dados para o Azure Cosmos DB, para migrações em larga escala, é recomendável que você entre em contato com a equipe de produtos do Azure Cosmos DB para validar a modelagem de dados e uma revisão geral da arquitetura. Com base no seu conjunto de dados e carga de trabalho, a equipe do produto também pode sugerir outras otimizações de desempenho e custos que poderiam ser aplicáveis a você. Para entrar em contato com a equipe do Azure Cosmos DB para obter assistência com migrações em larga escala, você pode abrir um ticket de suporte o tipo de problema General Advisory e subtipo de problema Grandes (TB+) como mostrado abaixo.

![Tópico de suporte à migração](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Próximas etapas

* Saiba mais experimentando os aplicativos de amostra que consomem a biblioteca de executores em massa em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* A biblioteca executora em massa é integrada ao conector Cosmos DB Spark, para saber mais, consulte o artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* Entre em contato com a equipe de produtos do Azure Cosmos DB abrindo um ticket de suporte o tipo de problema General Advisory e subtipo de problemas Grandes (TB+) para obter ajuda adicional com migrações em larga escala. 
