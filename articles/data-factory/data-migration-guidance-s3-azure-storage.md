---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Use Azure Data Factory para migrar dados do Amazon S3 para o armazenamento do Azure.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122637"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Use Azure Data Factory para migrar dados do Amazon S3 para o armazenamento do Azure 

O Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do Amazon S3 para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para desenvolvedores e engenheiros de dados: 

> [!div class="checklist"]
> * Performance 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura de solução de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Performance

O ADF oferece uma arquitetura sem servidor que permite paralelismo em diferentes níveis, o que permite aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados para o seu ambiente. 

Os clientes migraram com êxito petabytes de dados consistindo em centenas de milhões de arquivos do Amazon S3 para o armazenamento de BLOBs do Azure, com uma taxa de transferência sustentada de 2 GBps e mais. 

![desempenho](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A figura acima ilustra como você pode obter grandes velocidades de movimentação de dados por meio de diferentes níveis de paralelismo:
 
- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar Azure Integration Runtime, você pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de maneira sem servidor; ao usar o Integration Runtime auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até 4 nós](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e uma única atividade de cópia particionará seu conjunto de arquivos em todos os nós. 
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 
- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [loop for each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resiliência

Dentro de uma única execução da atividade de cópia, o ADF tem um mecanismo de repetição interno para que possa lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer cópias binárias de S3 para BLOB e de S3 para ADLS Gen2, o ADF executa automaticamente o ponto de verificação.  Se uma execução da atividade de cópia tiver falhado ou expirar, em uma nova tentativa subsequente, a cópia será retomada do último ponto de falha em vez de começar do início. 

## <a name="network-security"></a>Segurança de rede 

Por padrão, o ADF transfere dados do Amazon S3 para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 usando a conexão criptografada via protocolo HTTPS.  O HTTPS fornece criptografia de dados em trânsito e impede ataques de interceptação e Man-in-the-Middle. 

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, poderá obter maior segurança transferindo dados por um link de emparelhamento privado entre o AWS Direct Connect e o Azure Express Route.  Consulte a arquitetura da solução abaixo sobre como isso pode ser feito. 

## <a name="solution-architecture"></a>Arquitetura da solução

Migrar dados pela Internet pública:

![solução-arquitetura-pública-rede](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública. 
- O Amazon S3 de origem, bem como o armazenamento de BLOBs do Azure de destino ou Azure Data Lake Storage Gen2 estão configurados para permitir o tráfego de todos os endereços IP de rede.  Consulte a segunda arquitetura abaixo sobre como você pode restringir o acesso à rede para um intervalo de IP específico. 
- Você pode escalar verticalmente facilmente a quantidade de potência em maneira sem servidor para utilizar totalmente sua rede e largura de banda de armazenamento para que você possa obter a melhor taxa de transferência para seu ambiente. 
- A migração de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura. 

Migrar dados por link privado: 

![solução-arquitetura-privada-rede](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nessa arquitetura, a migração de dados é feita por meio de um link de emparelhamento privado entre o AWS Direct Connect e o Azure Express Route, de modo que os dados nunca percorram pela Internet pública.  Ele requer o uso do VPC AWS e da rede virtual do Azure. 
- Você precisa instalar o tempo de execução de integração auto-hospedado do ADF em uma VM do Windows em sua rede virtual do Azure para obter essa arquitetura.  Você pode escalar verticalmente suas VMs de IR hospedadas automaticamente ou escalar horizontalmente para várias VMs (até 4 nós) para utilizar totalmente sua rede e IOPS/largura de banda de armazenamento. 
- Se for aceitável transferir dados por HTTPS, mas você quiser bloquear o acesso à rede S3 para um intervalo de IP específico, você pode adotar uma variação dessa arquitetura removendo o VPC AWS e substituindo o link privado por HTTPS.  Convém manter o IR do Azure virtual e auto-Hosted na VM do Azure para que você possa ter um IP roteável publicamente estático para fins de lista de permissões. 
- A migração de dados de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura. 

## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar a conta do Amazon S3, você deve usar a [chave de acesso para a conta iam](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Há suporte para vários tipos de autenticação para se conectar ao armazenamento de BLOBs do Azure.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendável: criado com base em um ADF gerenciado automaticamente no Azure AD, ele permite que você configure pipelines sem fornecer credenciais na definição de serviço vinculado.  Como alternativa, você pode autenticar no armazenamento de BLOBs do Azure usando a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou a [chave da conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendado, embora a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a chave de conta de [armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) também possa ser usada. 
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, [o armazenamento das credenciais em Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) é altamente recomendável para facilitar o gerenciamento e a rotação centralizado das chaves sem modificar os serviços vinculados do ADF.  Essa também é uma das [práticas recomendadas para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

A partição de dados é recomendada especialmente ao migrar mais de 100 TB de dados.  Para particionar os dados, aproveite a configuração ' prefix ' para filtrar as pastas e os arquivos no Amazon S3 por nome e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de cada vez.  You can run multiple ADF copy jobs concurrently for better throughput. 

If any of the copy jobs fail due to network or data store transient issue, you can rerun the failed copy job to reload that specific partition again from AWS S3.  All other copy jobs loading other partitions will not be impacted. 

### <a name="delta-data-migration"></a>Delta data migration 

The most performant way to identify new or changed files from AWS S3 is by using time-partitioned naming convention – when your data in AWS S3 has been time partitioned with time slice information in the file or folder name (for example, /yyyy/mm/dd/file.csv), then your pipeline can easily identify which files/folders to copy incrementally. 

Alternatively, If your data in AWS S3 is not time partitioned, ADF can identify new or changed files by their LastModifiedDate.   The way it works is that ADF will scan all the files from AWS S3, and only copy the new and updated file whose last modified timestamp is greater than a certain value.  Be aware that if you have a large number of files in S3, the initial file scanning could take a long time regardless of how many files match the filter condition.  In this case you are suggested to partition the data first, using the same ‘prefix’ setting for initial snapshot migration, so that the file scanning can happen in parallel.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>For scenarios that require self-hosted Integration runtime on Azure VM 

Whether you are migrating data over private link or you want to allow specific IP range on Amazon S3 firewall, you need to install self-hosted Integration runtime on Azure Windows VM. 

- The recommend configuration to start with for each Azure VM is Standard_D32s_v3 with 32 vCPU and 128-GB memory.  You can keep monitoring CPU and memory utilization of the IR VM during the data migration to see if you need to further scale up the VM for better performance or scale down the VM to save cost. 
- You can also scale out by associating up to 4 VM nodes with a single self-hosted IR.  A single copy job running against a self-hosted IR will automatically partition the file set and leverage all VM nodes to copy the files in parallel.  For high availability, you are recommended to start with 2 VM nodes to avoid single point of failure during the data migration. 

### <a name="rate-limiting"></a>Limitação de taxa 

As a best practice, conduct a performance POC with a representative sample dataset, so that you can determine an appropriate partition size. 

Start with a single partition and a single copy activity with default DIU setting.  Gradually increase the DIU setting until you reach the bandwidth limit of your network or IOPS/bandwidth limit of the data stores, or you have reached the max 256 DIU allowed on a single copy activity. 

Next, gradually increase the number of concurrent copy activities until you reach limits of your environment. 

When you encounter throttling errors reported by ADF copy activity, either reduce the concurrency or DIU setting in ADF, or consider increasing the bandwidth/IOPS limits of the network and data stores.  

### <a name="estimating-price"></a>Estimating Price 

> [!NOTE]
> This is a hypothetical pricing example.  Your actual pricing depends on the actual throughput in your environment.

Consider the following pipeline constructed for migrating data from S3 to Azure Blob Storage: 

![pricing-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Let us assume the following: 

- Total data volume is 2 PB 
- Migrating data over HTTPS using first solution architecture 
- 2 PB is divided into 1 K partitions and each copy moves one partition 
- Each copy is configured with DIU=256 and achieves 1 GBps throughput 
- ForEach concurrency is set to 2 and aggregate throughput is 2 GBps 
- In total, it takes 292 hours to complete the migration 

Here is the estimated price based on the above assumptions: 

![pricing-table](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Amazon Simple Storage Service connector](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage) (Conector do Armazenamento de Blobs do Azure)
- [Conector do Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Copy activity performance tuning guide](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creating and configuring self-hosted Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integration runtime HA and scalability](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Data movement security considerations](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Store credentials in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copy file incrementally based on time partitioned file name](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copy new and changed files based on LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF pricing page](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modelo

Here is the [template](solution-template-migration-s3-azure.md) to start with to migrate petabytes of data consisting of hundreds of millions of files from Amazon S3 to Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Próximos passos

- [Copy files from multiple containers with Azure Data Factory](solution-template-copy-files-multiple-containers.md)
