---
title: Criar pipelines de dados preditivos
description: Saiba como criar um pipeline de previsão usando Azure Machine Learning – atividade de execução em lotes do Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 3587ee711864eb33fea9bc4e61fe226562e8f612
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418857"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines de previsão usando Azure Machine Learning e o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão atual](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, testar e implantar soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um experimento de treinamento.** Você faz esta etapa usando o Azure Machine Learning Studio (clássico). O Azure Machine Learning Studio (clássico) é um ambiente colaborativo de desenvolvimento visual que você usa para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Convertê-lo em um teste preditivo**. Quando o modelo foi treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, você preparará e simplificará seu teste para a pontuação.
3. **Implantá-lo como um serviço da Web**. Você pode publicar seu experimento de pontuação como um serviço Web do Azure. Você pode enviar dados ao seu modelo via esse ponto de extremidade de serviço Web e receber previsões de resultados do modelo.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning juntos
O Azure Data Factory permite que você crie facilmente pipelines que usam o serviço Web do [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) publicado para a análise preditiva. Usando a **Atividade de Execução** em lote em um pipeline da Fábrica de Dados Do Azure, você pode invocar um serviço web do Azure Machine Learning Studio (clássico) para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos nos experimentos de pontuação do Azure Machine Learning Studio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo em um pipeline do Data Factory executando as seguintes etapas:

1. Publique o experimento de treinamento (e não um experimento preditivo) como um serviço Web. Você faz essa etapa no Azure Machine Learning Studio (clássico) como fez para expor a experiência preditiva como um serviço web no cenário anterior.
2. Use o Azure Machine Learning Studio (clássico) Batch Execution Activity para invocar o serviço web para o experimento de treinamento. Basicamente, você pode usar a atividade de execução em lote do Azure Machine Learning Studio (clássico) para invocar o serviço web de treinamento e o serviço web de pontuação.

Depois de terminar com o retreinamento, atualize o serviço web de pontuação (experimento preditivo exposto como um serviço web) com o modelo recém-treinado usando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](update-machine-learning-models.md) para obter detalhes.

## <a name="azure-machine-learning-linked-service"></a>Serviço vinculado do Azure Machine Learning

Você cria um serviço vinculado do **Azure Machine Learning** para vincular um serviço Web do Azure Machine Learning a um Azure Data Factory. O serviço vinculado é usado pela atividade de execução em lotes do Azure Machine Learning e pela [atividade de atualização de recursos](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados) para obter descrições sobre as propriedades na definição JSON.

O Azure Machine Learning dá suporte a serviços Web clássicos e a novos serviços Web para sua experiência de previsão. Você pode escolher aquele que é mais adequado no Data Factory. Para obter as informações necessárias para criar o Serviço Vinculado do Azure Machine Learning, vá para https://services.azureml.net, onde todos os (novos) Serviços Web e Serviços Web Clássicos estão listados. Clique no serviço Web que deseja acessar e clique na página **Consumir**. Copie a **Chave Primária** da propriedade **apiKey** e **Solicitações de Lote** da propriedade **mlEndpoint**.

![Serviços Web do Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução em lotes do Azure Machine Learning

O snippet JSON a seguir define uma atividade de execução em lotes do Azure Machine Learning. A definição da atividade tem uma referência ao serviço vinculado do Azure Machine Learning que você criou anteriormente.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nome da atividade no pipeline     | Sim      |
| descrição       | Texto que descreve o que a atividade faz.  | Não       |
| type              | Para a atividade do U-SQL do Data Lake Analytics, o tipo de atividade é **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviços vinculados ao serviço vinculado do Azure Machine Learning. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| webServiceInputs  | Pares chave-valor, mapeando os nomes das entradas do serviço Web do Azure Machine Learning. A chave deve corresponder aos parâmetros de entrada definidos no serviço Web do Azure Machine Learning publicado. Valor é um par de propriedades FilePath e serviços vinculados do Armazenamento do Azure especificando os locais de Blob de entrada. | Não       |
| webServiceOutputs | Pares chave-valor, mapeando os nomes das saídas do serviço Web do Azure Machine Learning. A chave deve corresponder aos parâmetros de saída definidos no serviço Web do Azure Machine Learning publicado. Valor é um par de propriedades FilePath e serviços vinculados do Armazenamento do Azure especificando os locais de Blob de saída. | Não       |
| globalParameters  | Chave, pares de valor a serem passados para o ponto final do Serviço de Execução em Lote do Azure Machine Learning Studio (clássico). As teclas devem corresponder aos nomes dos parâmetros de serviço web definidos no serviço web publicado no Azure Machine Learning Studio (clássico). Os valores são passados na propriedade GlobalParameters do Azure Machine Learning Studio (clássico) pedido de execução em lote | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: testes usando entradas/saídas de serviço Web que se referem ao Armazenamento de Blobs do Azure

Nesse cenário, o serviço Web de Azure Machine Learning faz previsões usando dados de um arquivo em um armazenamento de blob do Azure e armazena os resultados de previsão no armazenamento de blob. O JSON a seguir define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. Os dados de entrada e saída no Armazenamento de Blobs do Azure são referenciados usando um par de LinkedName e FilePath. Na amostra Serviço vinculado de entradas e saídas são diferentes, você pode usar diferentes Serviços Vinculados para cada uma de suas entradas/saídas para data factory para poder pegar os arquivos certos e enviar para o Azure Machine Learning Studio (clássico) Web Service.

> [!IMPORTANT]
> Em seu experimento Azure Machine Learning Studio (clássico), portas de entrada e saída de serviçoweb e parâmetros globais têm nomes padrão ("input1", "input2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode visualizar a carga de solicitação de amostra na página Ajuda de execução em lote para o ponto final do Azure Machine Learning Studio (clássico) para verificar o mapeamento esperado.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: testes usando módulos de leitura/gravação para referenciar dados em vários armazenamentos
Outro cenário comum ao criar experimentos do Azure Machine Learning Studio (clássico) é usar módulos de dados de importação e de saída. O módulo de Importar Dados é usado para carregar dados em um teste e o módulo Exportar Dados é usado para salvar os dados dos testes. Para obter detalhes sobre os módulos Importar Dados e Exportar Dados, consulte os tópicos [Importar Dados](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Exportar Dados](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.

Ao usar os módulos Importar Dados e Exportar Dados, recomenda-se usar um parâmetro de serviço Web para cada propriedade desses módulos. Esses parâmetros da Web permitem que você configure os valores durante o runtime. Por exemplo, você pode criar um teste com um módulo Importar Dados que use um Banco de Dados SQL do Azure: XXX.database.windows.net. Depois que o serviço Web for implantado, você precisará habilitar os consumidores do serviço Web para especificar outro SQL Server do Azure chamado `YYY.database.windows.net`. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A saída e entrada de serviço Web são diferentes dos parâmetros de serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificadas para um serviço web (clássico) do Azure Machine Learning Studio. Nesse cenário, você pode passar parâmetros para um serviço Web que correspondam às propriedades dos módulos Importar Dados e Exportar Dados.
>
>

Vejamos um cenário para o uso de parâmetros de serviço Web. Você tem um serviço Web implantado de Azure Machine Learning que usa um módulo de leitor para ler dados de uma das fontes de dados compatíveis com o Azure Machine Learning (por exemplo: Banco de Dados SQL do Azure). Após a execução do lote, os resultados são gravados usando um módulo Gravador (banco de dados SQL do Azure).  Não há entradas e saídas de serviço Web definidas nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos de leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalParameters** no JSON da atividade da seguinte maneira:

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>

Depois de terminar com o retreinamento, atualize o serviço web de pontuação (experimento preditivo exposto como um serviço web) com o modelo recém-treinado usando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](update-machine-learning-models.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Atividade suína](transform-data-using-hadoop-pig.md)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade do procedimento armazenado](transform-data-using-stored-procedure.md)
