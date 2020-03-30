---
title: Recursos de otimização do desempenho da atividade de cópia
description: Conheça os principais recursos que ajudam a otimizar o desempenho da atividade de cópia na Fábrica de Dados do Azure.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257879"
---
# <a name="copy-activity-performance-optimization-features"></a>Recursos de otimização do desempenho da atividade de cópia

Este artigo descreve os recursos de otimização de desempenho da atividade de cópia que você pode aproveitar na Fábrica de Dados do Azure.

## <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa a potência (uma combinação de alocação de recursos de CPU, memória e rede) de uma única unidade na Fábrica de Dados Do Azure. A Unidade de Integração de Dados só se aplica ao [tempo de execução de integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não no [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

As DIUs permitidas para capacitar uma atividade de cópia são **entre 2 e 256**. Se não for especificado ou você escolher "Auto" na ui, a Fábrica de Dados aplica dinamicamente a configuração DIU ideal com base no seu par de dissipadores de origem e no padrão de dados. A tabela a seguir lista as faixas de DIU suportadas e o comportamento padrão em diferentes cenários de cópia:

| Copiar cenário | Faixa DIU suportada | DIUs padrão determinadas pelo serviço |
|:--- |:--- |---- |
| Entre lojas de arquivos |- **Copiar de ou para arquivo único**: 2-4 <br>- **Copiar de e para vários arquivos**: 2-256, dependendo do número e tamanho dos arquivos <br><br>Por exemplo, se você copiar dados de uma pasta com 4 arquivos grandes e optar por preservar a hierarquia, o DIU máximo efetivo é de 16; quando você optar por mesclar arquivo, o DIU máximo efetivo é 4. |Entre 4 e 32, dependendo do número e tamanho dos arquivos |
| Da loja de arquivos para a loja de arquivos |- **Cópia de arquivo único:** 2-4 <br/>- **Copiar de vários arquivos**: 2-256, dependendo do número e tamanho dos arquivos <br/><br/>Por exemplo, se você copiar dados de uma pasta com 4 arquivos grandes, o DIU máximo efetivo é 16. |- **Copie no Banco de Dados SQL do Azure ou no Azure Cosmos DB**: entre 4 e 16, dependendo do nível do sink (DTUs/RUs) e do padrão do arquivo de origem<br>- **Copiar no Azure Synapse Analytics** usando a declaração PolyBase ou COPY: 2<br>- Outro cenário: 4 |
| De loja de arquivos a armazenamento de arquivos |- **Copiar de armazenamentos de dados habilitados para opção de partição** (incluindo [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao escrever para uma pasta e 2-4 ao escrever em um único arquivo. Nota por partição de dados de origem pode usar até 4 DIUs.<br>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP:** 1<br/>- **Cópia do Amazon Redshift** usando UNLOAD: 2<br>- **Outro cenário**: 4 |
| Entre lojas não-arquivo |- **Copiar de armazenamentos de dados habilitados para opção de partição** (incluindo [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao escrever para uma pasta e 2-4 ao escrever em um único arquivo. Nota por partição de dados de origem pode usar até 4 DIUs.<br/>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP:** 1<br>- **Outro cenário**: 4 |

Você pode ver os DIUs usados para cada cópia executado na exibição de monitoramento de atividade de cópia ou saída de atividade. Para obter mais informações, consulte [Copiar monitoramento de atividades](copy-activity-monitoring.md). Para substituir esse padrão, especifique um valor para a `dataIntegrationUnits` propriedade da seguinte forma. O *número real de DIUs* que a operação de cópia usa na execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados.

Será cobrado **# de DIUs \* \* usado preço unitário de duração/DIU-hora**. Veja os preços atuais [aqui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Moeda local e descontos separados podem ser aplicados por tipo de assinatura.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade de tempo de execução de integração auto-hospedada

Se você quiser obter um rendimento maior, você pode escalar ou dimensionar o IR auto-hospedado:

- Se a CPU e a memória disponível no nó IR auto-hospedado não forem totalmente utilizadas, mas a execução de trabalhos simultâneos estiver chegando ao limite, você deve aumentar o número de trabalhos simultâneos que podem ser executados em um nó.  Veja [aqui](create-self-hosted-integration-runtime.md#scale-up) as instruções.
- Se, por outro lado, a CPU estiver alta no nó IR auto-hospedado ou a memória disponível estiver baixa, você poderá adicionar um novo nó para ajudar a dimensionar a carga entre os vários nós.  Veja [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) as instruções.

Observe nos seguintes cenários, a execução de atividade de cópia única pode aproveitar vários nós IR auto-hospedados:

- Copiar dados de lojas baseadas em arquivos, dependendo do número e tamanho dos arquivos.
- Copiar dados do armazenamento de dados habilitado para opção de partição (incluindo [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)dependendo do número de partições de dados.

## <a name="parallel-copy"></a>Cópia paralela

Você pode definir`parallelCopies` a cópia paralela (propriedade) na atividade de cópia para indicar o paralelismo que deseja que a atividade de cópia use. Você pode pensar nesta propriedade como o número máximo de threads dentro da atividade de cópia que lê de sua fonte ou gravar para seus armazenamentos de dados da pia em paralelo.

A cópia paralela é ortogonal para [Unidades de Integração de Dados](#data-integration-units) ou [nós IR auto-hospedados](#self-hosted-integration-runtime-scalability). Ele é contado em todos os NÓS DIUs ou IR auto-hospedados.

Para cada atividade de cópia executada, por padrão, a Fábrica de Dados do Azure aplica dinamicamente a configuração ideal de cópia paralela com base no seu par de sumidouros de origem e padrão de dados. 

> [!TIP]
> O comportamento padrão da cópia paralela geralmente lhe dá o melhor throughput, que é automaticamente determinado pelo ADF com base no seu par de dissipadores de origem, padrão de dados e número de DIUs ou a contagem de CPU/memória/nó do IR auto-hospedado. Consulte o [desempenho da atividade de cópia de solução de problemas](copy-activity-performance-troubleshooting.md) quando sintonizar a cópia paralela.

A tabela a seguir lista o comportamento da cópia paralela:

| Copiar cenário | Comportamento de cópia paralela |
| --- | --- |
| Entre lojas de arquivos | `parallelCopies`determina o paralelismo **no nível do arquivo**. O aparamento dentro de cada arquivo acontece por baixo de forma automática e transparente. Ele foi projetado para usar o tamanho de pedaço mais adequado para um determinado tipo de armazenamento de dados para carregar dados em paralelo. <br/><br/>O número real de cópias paralelas que a atividade de cópia usa em tempo de execução não é mais do que o número de arquivos que você tem. Se o comportamento de cópia for **mesclarArquivo** no dissipador de arquivos, a atividade de cópia não poderá tirar proveito do paralelismo em nível de arquivo. |
| Da loja de arquivos para a loja de arquivos | - Ao copiar dados no Banco de Dados SQL do Azure ou no Azure Cosmos DB, a cópia paralela padrão também depende do nível do sink (número de DTUs/RUs).<br>- Ao copiar dados na Tabela Azure, a cópia paralela padrão é 4. |
| De loja de arquivos a armazenamento de arquivos | - Ao copiar dados do armazenamento de dados habilitado para opção de partição (incluindo [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)a cópia paralela padrão é 4. O número real de cópias paralelas que a atividade de cópia usa em tempo de execução não é mais do que o número de partições de dados que você tem. Ao usar o Executetime de integração auto-hospedado e copie para o Azure Blob/ADLS Gen2, observe que a cópia paralela máxima eficaz é de 4 ou 5 por nó IR.<br>- Para outros cenários, a cópia paralela não faz efeito. Mesmo que o paralelismo seja especificado, não é aplicado. |
| Entre lojas não-arquivo | - Ao copiar dados no Banco de Dados SQL do Azure ou no Azure Cosmos DB, a cópia paralela padrão também depende do nível do sink (número de DTUs/RUs).<br/>- Ao copiar dados do armazenamento de dados habilitado para opção de partição (incluindo [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)a cópia paralela padrão é 4.<br>- Ao copiar dados na Tabela Azure, a cópia paralela padrão é 4. |

Para controlar a carga em máquinas que hospedam seus armazenamentos de dados ou para `parallelCopies` ajustar o desempenho da cópia, você pode substituir o valor padrão e especificar um valor para a propriedade. O valor deve ser um inteiro maior ou igual a 1. No tempo de execução, para o melhor desempenho, a atividade de cópia usa um valor menor ou igual ao valor que você define.

Quando você especificar `parallelCopies` um valor para a propriedade, leve em conta o aumento de carga na sua fonte e afunde os armazenamentos de dados. Considere também o aumento de carga para o tempo de execução de integração auto-hospedado se a atividade de cópia for habilitada por ela. Esse aumento de carga acontece especialmente quando você tem várias atividades ou corridas simultâneas das mesmas atividades que funcionam contra o mesmo armazenamento de dados. Se você notar que o armazenamento de dados ou o tempo de execução `parallelCopies` de integração auto-hospedado são sobrecarregados com a carga, diminua o valor para aliviar a carga.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. Esse preparo é especialmente útil nos seguintes casos:

- **Você deseja ingerir dados de vários armazenamentos de dados no SQL Data Warehouse via PolyBase.** O SQL Data Warehouse usa o PolyBase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados no SQL Data Warehouse. Os dados de origem devem estar no armazenamento Blob ou no Azure Data Lake Store, e devem atender a critérios adicionais. Quando você carrega dados de um armazenamento de dados diferente do armazenamento de Blobs ou do Azure Data Lake Store, pode ativar a cópia dos dados por meio do armazenamento de Blobs de preparo provisório. Nesse caso, a Azure Data Factory realiza as transformações de dados necessárias para garantir que ele atenda aos requisitos do PolyBase. Em seguida, ele usa o PolyBase para carregar os dados no SQL Data Warehouse de forma eficaz. Para ver mais informações, confira [Usar o PolyBase para carregar dados para o SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Às vezes, leva um tempo para realizar um movimento de dados híbridos (ou seja, copiar de um armazenamento de dados local para um armazenamento de dados na nuvem) sobre uma conexão de rede lenta.** Para melhorar o desempenho, você pode usar a cópia em estágio para compactar os dados no local, de modo que leve menos tempo para mover dados para o armazenamento de dados de preparação na nuvem. Em seguida, você pode descompactar os dados na loja de estadiamento antes de carregar no armazenamento de dados de destino.
- **Você não quer abrir portas além da porta 80 e porta 443 em seu firewall por causa de políticas corporativas de TI.** Por exemplo, quando você copia dados de um armazenamento de dados local para um coletor do Banco de Dados SQL do Azureou um coletor do SQL Data Warehouse, precisa ativar a comunicação de saída TCP na porta 1433 para o firewall do Windows e o firewall corporativo. Neste cenário, a cópia em etapas pode aproveitar o tempo de execução de integração auto-hospedado para primeiro copiar dados para uma instância de estágio de armazenamento Blob em HTTP ou HTTPS na porta 443. Em seguida, ele pode carregar os dados no Banco de Dados SQL ou no SQL Data Warehouse a partir do armazenamento Blob. Nesse fluxo, você não precisa habilitar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas

Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de blobs de preparo (traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. A Azure Data Factory gerencia automaticamente o fluxo de dois estágios para você. A Azure Data Factory também limpa dados temporários do armazenamento de estadiamento após a movimentação de dados ser concluída.

![Cópia em etapas](media/copy-activity-performance/staged-copy.png)

Quando você ativa a movimentação de dados usando um armazenamento de estadiamento, você pode especificar se deseja que os dados sejam compactados antes de mover dados do armazenamento de dados de origem para um armazenamento de dados provisório ou de sino e, em seguida, descompactado antes de mover dados de um interino ou encenação armazenamento de dados para o armazenamento de dados da pia.

Atualmente, você não pode copiar dados entre dois armazenamentos de dados que estão conectados através de diferentes IRs auto-hospedados, nem com nem sem cópia encenada. Para tal cenário, você pode configurar duas atividades de cópia explicitamente acorrentadas para copiar da fonte para a encenação e, em seguida, de encenação para afundamento.

### <a name="configuration"></a>Configuração

Configure a configuração **enableStaging** na atividade de cópia para especificar se você deseja que os dados sejam encenados no armazenamento Blob antes de carregá-los em um armazenamento de dados de destino. Quando você definir `TRUE` **enableStaging** para, especifique as propriedades adicionais listadas na tabela a seguir. Você também precisa criar um serviço de acesso compartilhado de acesso compartilhado do Azure para ser encenado se você não tiver um.

| Propriedade | Descrição | Valor padrão | Obrigatório |
| --- | --- | --- | --- |
| enableStaging |Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço vinculado [AzureStorage](connector-azure-blob-storage.md#linked-service-properties), que se refere à instância do Armazenamento que você usa como um repositório de preparo provisório. <br/><br/> Você não pode usar o armazenamento com uma assinatura de acesso compartilhada para carregar dados no SQL Data Warehouse via PolyBase. Pode usar em todos os outros cenários. |N/D |Sim, quando **enableStaging** está definido para TRUE |
| caminho |Especifique o caminho do armazenamento de Blobs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço cria um contêiner para armazenar dados temporários. <br/><br/>  Especifique um caminho somente se você usar o Armazenamento com uma assinatura de acesso compartilhado ou precisar que os dados temporários fiquem em um local específico. |N/D |Não |
| Enablecompression |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que são transferidos. |Falso |Não |

>[!NOTE]
> Se você usar cópia em estágio com compressão ativada, a autenticação principal do serviço ou MSI para o serviço vinculado ao blob não será suportada.

Aqui está uma definição de amostra de uma atividade de cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto de cobrança de cópia em etapas

Você é cobrado com base em duas etapas: duração da cópia e tipo de cópia.

* Quando você usa o staging durante uma cópia em nuvem, que está copiando dados de um armazenamento de dados na nuvem para outro armazenamento de dados em nuvem, ambos os estágios habilitados pelo tempo de execução da integração do Azure, você é cobrado a [soma da duração da cópia para a etapa 1 e a etapa 2] x [preço unitário de cópia em nuvem].
* Quando você usa o staging durante uma cópia híbrida, que está copiando dados de um armazenamento de dados on-premises para um armazenamento de dados na nuvem, um estágio habilitado por um tempo de execução de integração auto-hospedado, você é cobrado por [duração da cópia híbrida] x [preço da unidade de cópia híbrida] + [duração da cópia em nuvem] x [preço unitário de cópia em nuvem].

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Copiar o desempenho da atividade e o guia de escalabilidade](copy-activity-performance.md)
- [Solucionar problemas no desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md)
- [Use a Fábrica de Dados do Azure para migrar dados do seu lago de dados ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)