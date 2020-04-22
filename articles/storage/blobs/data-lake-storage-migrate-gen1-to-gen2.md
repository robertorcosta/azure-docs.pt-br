---
title: Migrar armazenamento do lago de dados do Azure de Gen1 para Gen2
description: Migrar o Azure Data Lake Storage de Gen1 para Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677140"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrar armazenamento do lago de dados do Azure de Gen1 para Gen2

Você pode migrar seus dados, cargas de trabalho e aplicativos do Data Lake Storage Gen1 para o Data Lake Storage Gen2.

O Azure Data Lake Storage Gen2 é construído no [armazenamento Azure Blob](storage-blobs-introduction.md) e fornece um conjunto de recursos dedicados à análise de big data. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combina recursos do [Azure Data Lake Storage Gen1,](https://docs.microsoft.com/azure/data-lake-store/index)como semântica do sistema de arquivos, segurança e escala de nível de arquivo com recursos de baixo custo, armazenamento hierárquico, alta disponibilidade/recuperação de desastres do [armazenamento Azure Blob.](storage-blobs-introduction.md)

> [!NOTE]
> Para facilitar a leitura, este artigo usa o termo *Gen1* para se referir ao Azure Data Lake Storage Gen1, e o termo *Gen2* para se referir ao Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Abordagem recomendada

Para migrar para gen2, recomendamos a seguinte abordagem.

:heavy_check_mark: Passo 1: Avaliar a prontidão

:heavy_check_mark: Passo 2: Prepare-se para migrar

:heavy_check_mark: Passo 3: Migrar dados e cargas de trabalho de aplicativos

:heavy_check_mark: Passo 4: Corte de Gen1 para Gen2

> [!NOTE]
> Gen1 e Gen2 são serviços diferentes, não há experiência de upgrade no local, esforço de migração intencional necessário. 

### <a name="step-1-assess-readiness"></a>Passo 1: Avaliar a prontidão

1. Conheça a [oferta do Data Lake Storage Gen2;](https://azure.microsoft.com/services/storage/data-lake-storage/) são benefícios, custos e arquitetura geral. 

2. [Compare as capacidades](#gen1-gen2-feature-comparison) do Gen1 com as do Gen2. 

3. Revise uma lista de [problemas conhecidos](data-lake-storage-known-issues.md) para avaliar quaisquer lacunas na funcionalidade.

4. O Gen2 suporta recursos de armazenamento Blob, como [registro de diagnóstico,](../common/storage-analytics-logging.md) [níveis de acesso](storage-blob-storage-tiers.md)e políticas de gerenciamento do ciclo de vida do armazenamento [Blob.](storage-lifecycle-management-concepts.md) Se você for interessante em usar qualquer um desses recursos, revise [o nível atual de suporte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features).

5. Revise o estado atual do [suporte ao ecossistema do Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) para garantir que o Gen2 suporte todos os serviços de que suas soluções dependem.

### <a name="step-2-prepare-to-migrate"></a>Passo 2: Prepare-se para migrar

1. Identifique os conjuntos de dados que você migrará.

   Aproveite esta oportunidade para limpar conjuntos de dados que você não usa mais. A menos que você planeje migrar todos os seus dados de uma só vez, aproveite esse tempo para identificar grupos lógicos de dados que você pode migrar em fases.
   
2. Determine o impacto que uma migração terá no seu negócio.

   Por exemplo, considere se você pode pagar qualquer tempo de inatividade enquanto a migração ocorre. Essas considerações podem ajudá-lo a identificar um padrão de migração adequado e a escolher as ferramentas mais adequadas.

3. Crie um plano de migração. 

   Recomendamos esses [padrões de migração.](#migration-patterns) Você pode escolher um desses padrões, combiná-los juntos ou projetar um padrão personalizado de sua autoria.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Passo 3: Migrar dados, cargas de trabalho e aplicativos

Migre dados, cargas de trabalho e aplicativos usando o padrão que você preferir. Recomendamos que você valide cenários incrementalmente.

1. [Crie uma conta de armazenamento](data-lake-storage-quickstart-create-account.md) e habilite o recurso hierárquico namespace. 

2. Migre seus dados. 

3. Configure [serviços em suas cargas de trabalho](data-lake-storage-integrate-with-azure-services.md) para apontar para o ponto final do Gen2. 
   
4. Atualize aplicativos para usar APIs Gen2. Consulte guias para [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Atualize scripts para usar [cmdlets Do](data-lake-storage-directory-file-acl-powershell.md)Data Lake Storage Gen2 PowerShell e [aazure CLI](data-lake-storage-directory-file-acl-cli.md).
   
6. Procure por referências uri `adl://` que contenham a seqüência de arquivos de código, ou em notebooks Databricks, arquivos Apache Hive HQL ou qualquer outro arquivo usado como parte de suas cargas de trabalho. Substitua essas referências pelo [URI formatado Gen2](data-lake-storage-introduction-abfs-uri.md) da sua nova conta de armazenamento. Por exemplo: o Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`URI: pode se tornar . 

7. Configure a segurança em sua conta para incluir [funções de controle de acesso baseado em função (RBAC),](../common/storage-auth-aad-rbac-portal.md)segurança de nível de [arquivo e pasta](data-lake-storage-access-control.md)e [firewalls de armazenamento Azure e redes virtuais.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Passo 4: Cutover de Gen1 para Gen2

Depois de estar confiante de que seus aplicativos e cargas de trabalho estão estáveis no Gen2, você pode começar a usar o Gen2 para satisfazer seus cenários de negócios. Desligue todos os gasodutos restantes que estejam sendo executados no Gen1 e descomissione sua conta Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Capacidades gen1 vs Gen2

Esta tabela compara as capacidades do Gen1 com as da Gen2.

|Área |Gen1   |Gen2 |
|---|---|---|
|Organização de dados|[Namespace hierárquico](data-lake-storage-namespace.md)<br>Suporte a arquivos e pastas|[Namespace hierárquico](data-lake-storage-namespace.md)<br>Suporte a contêineres, arquivos e pastas |
|Redundância geográfica| [Lrs](../common/storage-redundancy.md#locally-redundant-storage)| [RRs,](../common/storage-redundancy.md#locally-redundant-storage) [ZRS,](../common/storage-redundancy.md#zone-redundant-storage) [GRS,](../common/storage-redundancy.md#geo-redundant-storage) [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Autenticação|[Identidade gerenciada aAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Diretores de serviços](../../active-directory/develop/app-objects-and-service-principals.md)|[Identidade gerenciada aAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Diretores de serviços](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Chave de acesso compartilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorização|Gestão - [RBAC](../../role-based-access-control/overview.md)<br>Dados – [ACLs](data-lake-storage-access-control.md)|Gestão – [RBAC](../../role-based-access-control/overview.md)<br>Dados - [ACLs,](data-lake-storage-access-control.md) [RBAC](../../role-based-access-control/overview.md) |
|Criptografia – Dados em repouso|Lado do servidor – com chaves [gerenciadas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [gerenciadas pelo cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Lado do servidor – com chaves [gerenciadas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [gerenciadas pelo cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Suporte VNET|[Integração VNET](../../data-lake-store/data-lake-store-network-security.md)|[Pontos finais de serviço,](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [pontos finais privados](../common/storage-private-endpoints.md)|
|Experiência do desenvolvedor|[RESTO,](../../data-lake-store/data-lake-store-data-operations-rest-api.md) [.NET,](../../data-lake-store/data-lake-store-data-operations-net-sdk.md) [Java,](../../data-lake-store/data-lake-store-get-started-java-sdk.md) [Python,](../../data-lake-store/data-lake-store-data-operations-python.md) [PowerShell,](../../data-lake-store/data-lake-store-get-started-powershell.md) [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Geralmente disponível - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Pré-visualização pública - [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell,](data-lake-storage-directory-file-acl-powershell.md) [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Logs de diagnóstico|Troncos clássicos<br>[Monitor Azure integrado](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Registros clássicos](../common/storage-analytics-logging.md) - Geralmente disponíveis<br>Integração do monitor Azure – linha do tempo TBD|
|Ecossistema|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 e acima)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 e acima)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Padrões de Gen1 para Gen2

Escolha um padrão de migração e, em seguida, modifique esse padrão conforme necessário.

|||
|---|---|
|**Elevador e Mudança**|O padrão mais simples. Ideal se seus pipelines de dados podem pagar tempo de inatividade.|
|**Cópia incremental**|Semelhante ao *elevador e mudança,* mas com menos tempo de inatividade. Ideal para grandes quantidades de dados que levam mais tempo para serem copiados.|
|**Gasoduto duplo**|Ideal para oleodutos que não podem pagar qualquer tempo de inatividade.|
|**Sincronização bidirecional**|Semelhante ao *pipeline duplo,* mas com uma abordagem mais em fases que é adequada para gasodutos mais complicados.|

Vamos dar uma olhada mais de perto em cada padrão.
 
### <a name="lift-and-shift-pattern"></a>Padrão de elevação e mudança

Este é o padrão mais simples.

1. Pare todas as gravações para gen1.

2. Mova os dados do Gen1 para o Gen2. Recomendamos [a Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copiam com os dados.

3. Inseriu as operações e cargas de trabalho para a Gen2.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![padrão de elevação e mudança](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Considerações para usar o padrão de elevação e mudança

:heavy_check_mark: Corte de Gen1 para Gen2 para todas as cargas de trabalho ao mesmo tempo.

:heavy_check_mark: Espere tempo de inatividade durante a migração e o período de corte.

:heavy_check_mark: Ideal para pipelines que podem pagar tempo de inatividade e todos os aplicativos podem ser atualizados ao mesmo tempo.

### <a name="incremental-copy-pattern"></a>Padrão de cópia incremental

1. Comece a mover dados de Gen1 para Gen2. Recomendamos [a Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copiam com os dados.

2. Copie gradualmente novos dados do Gen1.

3. Depois que todos os dados forem copiados, pare todas as gravações para gen1 e aponte cargas de trabalho para gen2.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![Padrão de cópia incremental](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Considerações para usar o padrão de cópia incremental:

:heavy_check_mark: Corte de Gen1 para Gen2 para todas as cargas de trabalho ao mesmo tempo.

:heavy_check_mark: Espere tempo de inatividade apenas durante o período de corte.

:heavy_check_mark: Ideal para pipelines onde todos os aplicativos foram atualizados ao mesmo tempo, mas a cópia de dados requer mais tempo.

### <a name="dual-pipeline-pattern"></a>Padrão de pipeline duplo

1. Mova os dados do Gen1 para o Gen2. Recomendamos [a Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copiam com os dados.

2. Ingerir novos dados tanto para gen1 quanto para gen2.

3. Aponte cargas de trabalho para gen2.

4. Pare todas as gravações para gen1 e, em seguida, desativar Gen1.

> [!div class="mx-imgBorder"]
> ![Padrão de pipeline duplo](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Considerações para usar o padrão de pipeline duplo:

:heavy_check_mark: Os gasodutos Gen1 e Gen2 funcionam lado a lado.

:heavy_check_mark: Suporta zero tempo de inatividade.

:heavy_check_mark: Ideal em situações em que suas cargas de trabalho e aplicativos não podem pagar qualquer tempo de inatividade, e você pode ingerir em ambas as contas de armazenamento.

### <a name="bi-directional-sync-pattern"></a>Padrão de sincronização bidirecional

1. Configure a replicação bidirecional entre gen1 e Gen2. Recomendamos [wandisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Ele oferece um recurso de reparo para dados existentes.

3. Quando todos os movimentos estiverem completos, pare todas as gravações no Gen1 e desligue a replicação bidirecional.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![Padrão bidirecional](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Considerações para usar o padrão de sincronização bidirecional:

:heavy_check_mark: Ideal para cenários complexos que envolvem um grande número de gasodutos e dependências onde uma abordagem em fases pode fazer mais sentido.  

:heavy_check_mark: O esforço de migração é alto, mas fornece suporte lado a lado para gen1 e Gen2.

## <a name="next-steps"></a>Próximas etapas

- Conheça as várias partes da configuração de segurança para uma conta de armazenamento. Consulte [o guia de segurança do Azure Storage](../common/storage-security-guide.md).
- Otimize o desempenho do seu Data Lake Store. Consulte [Otimizar o Azure Data Lake Storage Gen2 para obter desempenho](data-lake-storage-performance-tuning-guidance.md)
- Revise as melhores práticas para gerenciar sua Data Lake Store. Veja [as melhores práticas para usar o Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

