---
title: Atividade de cópia na fábrica de dados do Azure
description: Saiba mais sobre a atividade do Copy na Fábrica de Dados do Azure. Você pode usá-los para copiar dados de um armazenamento de dados de origem suportado para um armazenamento de dados da pia suportado.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 2557ce7be44f0505b96df06cd2b44a2fa3ce3fdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414231"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia na fábrica de dados do Azure

> [!div class="op_single_selector" title1="Selecione a versão da Fábrica de Dados que você está usando:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Na Fábrica de Dados do Azure, você pode usar a atividade Copiar para copiar dados entre os armazenamentos de dados localizados no local e na nuvem. Depois de copiar os dados, você pode usar outras atividades para transformá-los e analisá-los. Você também pode usar a atividade Copiar para publicar resultados de transformação e análise para inteligência de negócios (BI) e consumo de aplicativos.

![O papel da atividade Copiar](media/copy-activity-overview/copy-activity.png)

A atividade Copiar é executada em um [tempo de execução de integração](concepts-integration-runtime.md). Você pode usar diferentes tipos de tempos de execução de integração para diferentes cenários de cópia de dados:

* Quando você está copiando dados entre dois armazenamentos de dados que são acessíveis publicamente através da internet a partir de qualquer IP, você pode usar o tempo de execução de integração do Azure para a atividade de cópia. Este tempo de execução de integração é seguro, confiável, escalável e [globalmente disponível](concepts-integration-runtime.md#integration-runtime-location).
* Quando você está copiando dados para e a partir de armazenamentos de dados localizados no local ou em uma rede com controle de acesso (por exemplo, uma rede virtual Do Azure), você precisa configurar um tempo de execução de integração auto-hospedado.

Um tempo de execução de integração precisa ser associado a cada fonte e armazenamento de dados do sink. Para obter informações sobre como a atividade Copiar determina qual tempo de execução de integração usar, consulte [Determinar qual IR usar](concepts-integration-runtime.md#determining-which-ir-to-use).

Para copiar dados de uma fonte para um dissipador, o serviço que executa a atividade Copiar executa essas etapas:

1. Lê dados de um armazenamento de dados de origem.
2. Executa serialização/desserialização, compactação/descompressão, mapeamento de colunas e assim por diante. Ele executa essas operações com base na configuração do conjunto de dados de entrada, conjunto de dados de saída e atividade copiar.
3. Grava dados no armazenamento de dados de coletor/destino.

![Copiar a visão geral da atividade](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Fontes de dados e formatos com suporte

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a atividade Copiar para copiar arquivos como está entre dois armazenamentos de dados baseados em arquivos, nesse caso os dados são copiados eficientemente sem qualquer serialização ou desserialização. Além disso, você também pode analisar ou gerar arquivos de um determinado formato, por exemplo, você pode executar o seguinte:

* Copie os dados de um banco de dados SQL Server no local e escreva para o Azure Data Lake Storage Gen2 no formato Parquet.
* Copiar arquivos no formato CSV (text) de um sistema de arquivos no local e gravar para o armazenamento Azure Blob no formato Avro.
* Copie arquivos fechados de um sistema de arquivos no local, descompacte-os em tempo real e escreva arquivos extraídos para o Azure Data Lake Storage Gen2.
* Copie os dados no formato Gzip compactpressed-text (CSV) do armazenamento Azure Blob e escreva-os no Azure SQL Database.
* Muitas outras atividades que requerem serialização/desserialização ou compressão/descompressão.

## <a name="supported-regions"></a>Regiões com suporte

O serviço que permite a atividade copy está disponível globalmente nas regiões e geografias listadas nos [locais de tempo de execução de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante a movimentação de dados eficiente, o que geralmente evita saltos entre regiões. Consulte [Produtos por região](https://azure.microsoft.com/regions/#services) para verificar a disponibilidade da Fábrica de Dados e a movimentação de dados em uma região específica.

## <a name="configuration"></a>Configuração

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Em geral, para usar a atividade Copiar na Fábrica de Dados do Azure, você precisa:

1. **Crie serviços vinculados para o armazenamento de dados de origem e o armazenamento de dados do dissipador.** Você pode encontrar a lista de conectores suportados na seção [de armazenamentos e formatos suportados](#supported-data-stores-and-formats) deste artigo. Consulte a seção "Propriedades de serviço vinculadas" do artigo do conector para obter informações de configuração e propriedades suportadas. 
2. **Crie conjuntos de dados para a origem e afunde.** Consulte as seções "Propriedades do conjunto de dados" dos artigos de conector de origem e sumidouro para obter informações de configuração e propriedades suportadas.
3. **Crie um pipeline com a atividade Copiar.** A próxima seção fornece um exemplo.

### <a name="syntax"></a>Sintaxe

O modelo a seguir de uma atividade Copiar contém uma lista completa de propriedades suportadas. Especifique as adequadas para o seu cenário.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Obrigatório? |
|:--- |:--- |:--- |
| type | Para uma atividade Copiar, definido como`Copy` | Sim |
| inputs | Especifique o conjunto de dados que você criou que aponta para os dados de origem. A atividade Copiar suporta apenas uma única entrada. | Sim |
| outputs | Especifique o conjunto de dados que você criou que aponta para os dados do sink. A atividade Copiar suporta apenas uma única saída. | Sim |
| typeProperties | Especificar propriedades para configurar a atividade Copiar. | Sim |
| source | Especifique o tipo de origem da cópia e as propriedades correspondentes para recuperar dados.<br/>Para obter mais informações, consulte a seção "Copiar propriedades de atividade" no artigo do conector listado em [armazenamentos e formatos de dados suportados](#supported-data-stores-and-formats). | Sim |
| coletor | Especifique o tipo de soca de cópia e as propriedades correspondentes para a gravação de dados.<br/>Para obter mais informações, consulte a seção "Copiar propriedades de atividade" no artigo do conector listado em [armazenamentos e formatos de dados suportados](#supported-data-stores-and-formats). | Sim |
| tradutor | Especifique mapeamentos de coluna explícita da origem para o coletor. Essa propriedade se aplica quando o comportamento de cópia padrão não atende às suas necessidades.<br/>Para obter mais informações, consulte [o mapeamento de Esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md). | Não |
| dataIntegrationUnits | Especifique uma medida que represente a quantidade de energia que o tempo de execução de [integração do Azure](concepts-integration-runtime.md) usa para cópia de dados. Essas unidades eram anteriormente conhecidas como Unidades de Movimentação de Dados em Nuvem (DMU). <br/>Para obter mais informações, consulte [Unidades de Integração de Dados](copy-activity-performance-features.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que deseja que a atividade Copiar use ao ler dados da fonte e escrever dados para o dissipador.<br/>Para obter mais informações, consulte [Cópia paralela](copy-activity-performance-features.md#parallel-copy). | Não |
| preservar | Especifique se deve preservar metadados/ACLs durante a cópia de dados. <br/>Para obter mais informações, consulte [Preservar metadados](copy-activity-preserve-metadata.md). |Não |
| enableStaging<br/>stagingSettings | Especifique se deve encenar os dados provisórios no armazenamento Blob em vez de copiar diretamente dados da fonte para afundar.<br/>Para obter informações sobre cenários úteis e detalhes de configuração, consulte [Staged copy](copy-activity-performance-features.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como lidar com linhas incompatíveis ao copiar dados da fonte para afunidar.<br/>Para obter mais informações, consulte [Tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitoramento

Você pode monitorar a atividade do Copy executada na Fábrica de Dados do Azure tanto visual quanto programáticamente. Para obter detalhes, consulte [A atividade de copiar do Monitor](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Cópia incremental

A Fábrica de Dados permite copiar gradualmente dados delta de um armazenamento de dados de origem para um armazenamento de dados da pia. Para obter detalhes, consulte [Tutorial: Copie gradualmente os dados](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste

A experiência [de monitoramento de atividades](copy-activity-monitoring.md) de cópia mostra as estatísticas de desempenho de cópia para cada uma de suas atividades executadas. O [guia de desempenho e escalabilidade da atividade do Copy](copy-activity-performance.md) descreve os principais fatores que afetam o desempenho da movimentação de dados através da atividade Copiar na Fábrica de Dados do Azure. Ele também lista os valores de desempenho observados durante os testes e discute como otimizar o desempenho da atividade Copiar.

## <a name="resume-from-last-failed-run"></a>Retomar da última corrida fracassada

Copiar o suporte de atividade saem da última execução com falha quando você copia o grande tamanho dos arquivos como está com o formato binário entre lojas baseadas em arquivos e opta por preservar a hierarquia de pasta/arquivo da origem para o sumidouro, por exemplo, para migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2. Aplica-se aos seguintes conectores baseados em arquivos: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md)e [SFTP.](connector-sftp.md)

Você pode aproveitar o currículo da atividade de cópia das duas maneiras a seguir:

- **Retente de nível de atividade:** Você pode definir a contagem de repetição na atividade de cópia. Durante a execução do pipeline, se essa execução de atividade de cópia falhar, a próxima repetição automática começará a partir do ponto de falha do último teste.
- **Reexecutar da atividade com falha:** Após a conclusão da execução do pipeline, você também pode desencadear uma reprise da atividade com falha na exibição de monitoramento da ADF UI ou programática. Se a atividade com falha for uma atividade de cópia, o pipeline não só será reexecutado a partir desta atividade, mas também será retomado a partir do ponto de falha da execução anterior.

    ![Cópia do currículo](media/copy-activity-overview/resume-copy.png)

Poucos pontos a notar:

- O currículo acontece no nível do arquivo. Se a atividade de cópia falhar ao copiar um arquivo, na próxima execução, este arquivo específico será recopiado.
- Para que o currículo funcione corretamente, não altere as configurações de atividade de cópia entre as reprises.
- Quando você copia dados do Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 e Google Cloud Storage, a atividade de cópia pode ser retomada a partir de um número arbitrário de arquivos copiados. Enquanto para o resto dos conectores baseados em arquivos como fonte, atualmente a atividade de cópia suporta currículo a partir de um número limitado de arquivos, geralmente na faixa de dezenas de milhares e varia dependendo do comprimento dos caminhos de arquivo; arquivos além desse número serão copiados de re-cópia durante as reprises.

Para outros cenários além da cópia de arquivo binário, a reprise de atividade de cópia começa desde o início.

## <a name="preserve-metadata-along-with-data"></a>Preserve metadados junto com os dados

Ao copiar dados de origem para afundar, em cenários como a migração de data lake, você também pode optar por preservar os metadados e AsCLs, juntamente com os dados usando a atividade de cópia. Consulte [Preservar metadados](copy-activity-preserve-metadata.md) para obter detalhes.

## <a name="schema-and-data-type-mapping"></a>Mapeamento de tipo de dados e de esquema

Consulte [esquema e mapeamento do tipo de dados](copy-activity-schema-and-type-mapping.md) para obter informações sobre como a atividade Copiar mapeia seus dados de origem para o seu dissipador.

## <a name="add-additional-columns-during-copy"></a>Adicionar colunas adicionais durante a cópia

Além de copiar dados do armazenamento de dados de origem para afundar, você também pode configurar para adicionar colunas de dados adicionais para copiar junto para afundar. Por exemplo:

- Ao copiar da fonte baseada em arquivo, armazene o caminho relativo do arquivo como uma coluna adicional para rastrear de qual arquivo os dados vêm.
- Adicione uma coluna com a expressão ADF, para anexar variáveis do sistema ADF, como nome do pipeline/id do pipeline, ou armazenar outro valor dinâmico da saída da atividade upstream.
- Adicione uma coluna com valor estático para atender à necessidade de consumo a jusante.

Você pode encontrar a seguinte configuração na guia de origem da atividade de cópia: 

![Adicionar colunas adicionais na atividade de cópia](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Este recurso funciona com o modelo mais recente de conjunto de dados. Se você não ver essa opção da ui, tente criar um novo conjunto de dados.

Para configurá-lo programáticamente, adicione a `additionalColumns` propriedade na sua fonte de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| colunas adicionais | Adicione colunas de dados adicionais para copiar para afundar.<br><br>Cada objeto `additionalColumns` sob a matriz representa uma coluna extra. O `name` define o nome da `value` coluna e indica o valor dos dados dessa coluna.<br><br>Os valores de dados permitidos são:<br>- **`$$FILEPATH`**- uma variável reservada indica para armazenar o caminho relativo dos arquivos de origem para o caminho da pasta especificado no conjunto de dados. Inscreva-se na fonte baseada em arquivos.<br>- **Expressão**<br>- **Valor estático** | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade Copiar pára de copiar dados e retorna uma falha quando as linhas de dados de origem são incompatíveis com as linhas de dados do sink. Para fazer a cópia ter sucesso, você pode configurar a atividade Copiar para pular e registrar as linhas incompatíveis e copiar apenas os dados compatíveis. Consulte [Copiar tolerância à falha de atividade](copy-activity-fault-tolerance.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes guias de início rápido, tutoriais e exemplos:

- [Copiar dados de um local para outro na mesma conta de armazenamento Azure Blob](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do armazenamento Azure Blob para o Banco de Dados SQL do Azure](tutorial-copy-data-dot-net.md)
- [Copiar dados de um banco de dados SQL Server no local para o Azure](tutorial-hybrid-copy-powershell.md)
