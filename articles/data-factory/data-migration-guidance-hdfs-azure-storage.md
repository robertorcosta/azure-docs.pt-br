---
title: Migrar dados de um cluster Hadoop no local para o Azure Storage
description: Saiba como usar a Fábrica de Dados Do Azure para migrar dados do cluster Hadoop no local para o Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417126"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Use a Fábrica de Dados do Azure para migrar dados de um cluster Hadoop no local para o Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do HDFS local para o armazenamento Azure Blob ou o Azure Data Lake Storage Gen2. 

A Data Factory oferece duas abordagens básicas para migrar dados de HDFS para Azure no local. Você pode selecionar a abordagem com base no seu cenário. 

- **Modo DistCp de fábrica de dados** (recomendado): Na Fábrica de Dados, você pode usar [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (cópia distribuída) para copiar arquivos como está para o armazenamento Azure Blob (incluindo [cópia em estágio)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)ou Azure Data Lake Store Gen2. Use a Fábrica de Dados integrada ao DistCp para aproveitar um cluster poderoso existente para obter o melhor throughput de cópia. Você também tem o benefício de agendamento flexível e uma experiência de monitoramento unificada da Data Factory. Dependendo da configuração da fábrica de dados, a atividade de cópia constrói automaticamente um comando DistCp, envia os dados para o cluster Hadoop e, em seguida, monitora o status da cópia. Recomendamos o modo Data Factory DistCp para migrar dados de um cluster Hadoop no local para o Azure.
- **Data Factory native integration runtime mode**: DistCp não é uma opção em todos os cenários. Por exemplo, em um ambiente de Redes Virtuais Do Azure, a ferramenta DistCp não suporta peering privado do Azure ExpressRoute com um ponto final de rede virtual do Azure Storage. Além disso, em alguns casos, você não quer usar o cluster Hadoop existente como um motor para migrar dados para que você não coloque cargas pesadas em seu cluster, o que pode afetar o desempenho dos trabalhos de ETL existentes. Em vez disso, você pode usar o recurso nativo do tempo de execução de integração da Fábrica de Dados como o mecanismo que copia dados do HDFS no local para o Azure.

Este artigo fornece as seguintes informações sobre ambas as abordagens:
> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura de soluções de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

No modo Data Factory DistCp, o throughput é o mesmo que se você usar a ferramenta DistCp de forma independente. O modo DistCp da fábrica de dados maximiza a capacidade do cluster Hadoop existente. Você pode usar DistCp para grande cópia entre clusters ou intracluster. 

O DistCp usa o MapReduce para efetuar sua distribuição, manipulação e recuperação de erros e relatórios. Ele expande uma lista de arquivos e diretórios em entrada para mapeamento de tarefas. Cada tarefa copia uma partição de arquivo especificada na lista de origem. Você pode usar a Data Factory integrada com o DistCp para construir pipelines para utilizar totalmente a largura de banda da rede, o IOPS de armazenamento e a largura de banda para maximizar o throughput de movimentação de dados para o ambiente.  

O modo de tempo de execução de integração nativa da Fábrica de Dados também permite o paralelismo em diferentes níveis. Você pode usar o paralelismo para utilizar totalmente a largura de banda da rede, o IOPS de armazenamento e a largura de banda para maximizar a throughput de movimentação de dados:

- Uma única atividade de cópia pode tirar proveito de recursos de computação escaláveis. Com um tempo de execução de integração auto-hospedado, você pode escalar manualmente a máquina ou dimensionar para várias[máquinas (até quatro nós).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Uma única atividade de cópia particiona seu conjunto de arquivos em todos os nós. 
- Uma única atividade de cópia é lida e grava no armazenamento de dados usando vários segmentos. 
- O fluxo de controle da fábrica de dados pode iniciar várias atividades de cópia em paralelo. Por exemplo, você pode usar um [loop Para Cada .](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity) 

Para obter mais informações, consulte o guia de desempenho da [atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Resiliência

No modo Data Factory DistCp, você pode usar diferentes parâmetros `-i`de linha `-update`de comando DistCp (Por exemplo, ignorar falhas ou gravar dados quando o arquivo de origem e o arquivo de destino diferem em tamanho) para diferentes níveis de resiliência.

No modo de tempo de execução de integração nativa data factory, em uma única execução de atividade de cópia, a Data Factory tem um mecanismo de repetição incorporado. Ele pode lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer cópias binárias do HDFS no local para o armazenamento Blob e do HDFS no local para o Data Lake Store Gen2, a Data Factory executa automaticamente o checkpointing em grande parte. Se uma execução de atividade de cópia falhar ou se apagar, em uma repetição subseqüente (certifique-se de que a contagem de tentativas é > 1), a cópia será retomada a partir do último ponto de falha em vez de começar no início.

## <a name="network-security"></a>Segurança de rede 

Por padrão, a Fábrica de Dados transfere dados do HDFS local para o armazenamento Blob ou o Azure Data Lake Storage Gen2 usando uma conexão criptografada através do protocolo HTTPS. O HTTPS fornece criptografia de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Alternativamente, se você não quiser que os dados sejam transferidos pela internet pública, para maior segurança, você pode transferir dados através de um link de peering privado via ExpressRoute. 

## <a name="solution-architecture"></a>Arquitetura da solução

Esta imagem mostra dados migratórios pela internet pública:

![Diagrama que mostra a arquitetura de solução para migração de dados em uma rede pública](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos com segurança usando HTTPS pela internet pública. 
- Recomendamos o uso do modo Data Factory DistCp em um ambiente de rede pública. Você pode aproveitar um poderoso cluster existente para obter o melhor throughput de cópia. Você também tem o benefício de agendamento flexível e experiência de monitoramento unificada da Fábrica de Dados.
- Para esta arquitetura, você deve instalar o tempo de execução de integração auto-hospedado da Fábrica de Dados em uma máquina Windows atrás de um firewall corporativo para enviar o comando DistCp ao seu cluster Hadoop e monitorar o status da cópia. Como a máquina não é o motor que irá mover dados (apenas para fins de controle), a capacidade da máquina não afeta o throughput de movimentação de dados.
- Os parâmetros existentes do comando DistCp são suportados.

Esta imagem mostra dados migratórios em um link privado: 

![Diagrama que mostra a arquitetura da solução para migração de dados em uma rede privada](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, os dados são migrados através de um link de peering privado via Azure ExpressRoute. Os dados nunca atravessam a internet pública.
- A ferramenta DistCp não suporta peering privado ExpressRoute com um ponto final de rede virtual do Azure Storage. Recomendamos que você use a capacidade nativa da Data Factory através do tempo de execução de integração para migrar os dados.
- Para esta arquitetura, você deve instalar o tempo de execução de integração auto-hospedado da Fábrica de Dados em uma VM do Windows em sua rede virtual Azure. Você pode escalar manualmente sua VM ou dimensionar para várias VMs para utilizar totalmente sua rede e armazenamento IOPS ou largura de banda.
- A configuração recomendada para começar por cada Azure VM (com o tempo de execução de integração auto-hospedado da Fábrica de Dados instalado) é Standard_D32s_v3 com uma memória de 32 vCPU e 128 GB. Você pode monitorar o uso da CPU e da memória da VM durante a migração de dados para ver se você precisa escalar a VM para um melhor desempenho ou para reduzir a VM para reduzir o custo.
- Você também pode dimensionar associando até quatro nós VM com um único tempo de execução de integração auto-hospedado. Um único trabalho de cópia executando contra um tempo de execução de integração auto-hospedado particiona automaticamente o conjunto de arquivos e faz uso de todos os nós VM para copiar os arquivos em paralelo. Para alta disponibilidade, recomendamos que você comece com dois nós de VM para evitar um cenário de ponto único de falha durante a migração de dados.
- Quando você usa essa arquitetura, a migração inicial de dados de instantâneos e a migração de dados delta estão disponíveis para você.

## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação

Recomendamos que você siga essas práticas recomendadas ao implementar sua migração de dados.

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar o HDFS, você pode [usar o Windows (Kerberos) ou o Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Vários tipos de autenticação são suportados para se conectar ao armazenamento Azure Blob.  Recomendamos o uso de [identidades gerenciadas para recursos do Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Construídas em cima de uma identidade de fábrica de dados gerenciada automaticamente no Azure Active Directory (Azure AD), as identidades gerenciadas permitem configurar pipelines sem fornecer credenciais na definição de serviço vinculado. Alternativamente, você pode autenticar o armazenamento Blob usando um [principal de serviço,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)uma [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Vários tipos de autenticação também são suportados para se conectar ao Data Lake Storage Gen2.  Recomendamos usar [identidades gerenciadas para recursos do Azure,](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)mas você também pode usar um [diretor de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Quando você não está usando identidades gerenciadas para recursos do Azure, recomendamos armazenar [as credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar o gerenciamento central e a rotação de chaves sem modificar os serviços vinculados à Fábrica de Dados. Esta também é uma [prática recomendada para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados de instantâneo 

No modo DistCp da fábrica de dados, você pode criar uma atividade de cópia para enviar o comando DistCp e usar diferentes parâmetros para controlar o comportamento inicial de migração de dados. 

No modo de tempo de execução de integração nativa data Factory, recomendamos partição de dados, especialmente quando você migra mais de 10 TB de dados. Para particionar os dados, use os nomes das pastas no HDFS. Em seguida, cada trabalho de cópia da Fábrica de Dados pode copiar uma partição de pasta por vez. Você pode executar vários trabalhos de cópia da Fábrica de Dados simultaneamente para obter um melhor throughput.

Se algum dos trabalhos de cópia falhar devido a problemas transitórios de rede ou armazenamento de dados, você pode executar o trabalho de cópia com falha para recarregar essa partição específica do HDFS. Outros trabalhos de cópia que estão carregando outras partições não são afetados.

### <a name="delta-data-migration"></a>Migração de dados Delta 

No modo Data Factory DistCp, você pode usar o `-update`parâmetro de linha de comando DistCp, gravar dados quando o arquivo de origem e o arquivo de destino diferem em tamanho, para migração de dados delta.

No modo de integração nativa data factory, a maneira mais performática de identificar arquivos novos ou alterados do HDFS é usando uma convenção de nomeação particionada por tempo. Quando seus dados no HDFS foram particionados por tempo com informações de fatiade tempo no nome do arquivo ou da pasta (por exemplo, */yyyy/mm/dd/file.csv),* seu pipeline pode facilmente identificar quais arquivos e pastas copiar incrementalmente.

Alternativamente, se seus dados no HDFS não forem particionados por tempo, a Data Factory poderá identificar arquivos novos ou alterados usando o valor **LastModifiedDate.** Data Factory verifica todos os arquivos do HDFS e copia apenas arquivos novos e atualizados que têm um último carimbo de tempo modificado que é maior do que um valor definido. 

Se você tiver um grande número de arquivos no HDFS, a varredura inicial do arquivo pode levar muito tempo, independentemente de quantos arquivos correspondem à condição do filtro. Neste cenário, recomendamos que você primeiro particione os dados usando a mesma partição que você usou para a migração inicial de instantâneos. Em seguida, a varredura de arquivos pode ocorrer em paralelo.

### <a name="estimate-price"></a>Preço estimado 

Considere o seguinte pipeline para migração de dados do armazenamento HDFS para O Armazenamento Azure Blob: 

![Diagrama que mostra o pipeline de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vamos assumir as seguintes informações: 

- O volume total de dados é de 1 PB.
- Você migra dados usando o modo de tempo de execução de integração nativa data factory.
- 1 PB é dividido em 1.000 partições e cada cópia move uma partição.
- Cada atividade de cópia é configurada com um tempo de execução de integração auto-hospedado que está associado a quatro máquinas e que atinge o throughput de 500 MBps.
- ForEach sicurrency é definido como **4** e throughput agregado é de 2 GBps.
- No total, leva 146 horas para concluir a migração.

Aqui está o preço estimado baseado em nossas suposições: 

![Tabela que mostra cálculos de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Este é um exemplo hipotético de preços. Seu preço real depende do produto real em seu ambiente.
> O preço de uma VM do Azure Windows (com tempo de execução de integração auto-hospedado instalado) não está incluído.

### <a name="additional-references"></a>Referências adicionais

- [HDFS connector](https://docs.microsoft.com/azure/data-factory/connector-hdfs) (Conector de HDFS)
- [Conector de armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector do Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guia de ajuste de desempenho de atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criar e configurar um runtime de integração auto-hospedada](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Integração autohospedada alta disponibilidade e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança da movimentação de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copie um arquivo incrementalmente com base em um nome de arquivo com partição de tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiar arquivos novos e alterados com base no LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de preços da fábrica de dados](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres usando a Fábrica de Dados do Azure](solution-template-copy-files-multiple-containers.md)