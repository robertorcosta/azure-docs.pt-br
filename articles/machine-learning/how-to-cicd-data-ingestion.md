---
title: DevOps para um pipeline de ingestão de dados
titleSuffix: Azure Machine Learning
description: Saiba como aplicar práticas de DevOps a uma implementação de pipeline de ingestão de dados usada para preparar dados para um treinamento de modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247173"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps para um pipeline de ingestão de dados

Na maioria dos cenários, uma solução de ingestão de dados é uma composição de scripts, invocações de serviços e um pipeline orquestrando todas as atividades. Neste artigo, você aprende como aplicar práticas de DevOps ao ciclo de vida de desenvolvimento de um pipeline comum de ingestão de dados. O pipeline prepara os dados para o treinamento do modelo de Machine Learning.

## <a name="the-solution"></a>A solução

Considere o seguinte fluxo de trabalho de ingestão de dados:

![pipeline de ingestão de dados](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Nesta abordagem, os dados de treinamento são armazenados em um armazenamento de blob do Azure. Um pipeline da Azure Data Factory busca os dados de um contêiner de bolhas de entrada, transforma-os e salva os dados para o recipiente de blob de saída. Este contêiner serve como um [armazenamento de dados](concept-data.md) para o serviço de aprendizado de máquina do Azure. Tendo os dados preparados, o pipeline da Fábrica de Dados invoca um pipeline de aprendizado de máquina de treinamento para treinar um modelo. Neste exemplo específico, a transformação de dados é realizada por um notebook Python, sendo executado em um cluster Azure Databricks. 

## <a name="what-we-are-building"></a>O que estamos construindo

Como em qualquer solução de software, há uma equipe (por exemplo, Engenheiros de Dados) trabalhando nela. 

![cicd-data-ingestão](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Eles colaboram e compartilham os mesmos recursos do Azure, como Azure Data Factory, Azure Databricks, Azure Storage account e tal. A arrecadação desses recursos é um ambiente de desenvolvimento. Os engenheiros de dados contribuem para a mesma base de código fonte. O processo de Integração Contínua monta o código, verifica-o com os testes de qualidade de código, testes unitários e produz artefatos como código testado e modelos de Gerenciador de Recursos do Azure. O processo de Entrega Contínua implanta os artefatos nos ambientes a jusante. Este artigo demonstra como automatizar os processos de CI e CD com [o Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gerenciamento de controle de origem

Os membros da equipe trabalham de maneiras ligeiramente diferentes para colaborar no código-fonte do notebook Python e no código-fonte da Fábrica de Dados Do Azure. No entanto, em ambos os casos, o código é armazenado em um repositório de controle de origem (por exemplo, Azure DevOps, GitHub, GitLab) e a colaboração é normalmente baseada em algum modelo de ramificação (por exemplo, [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Código-fonte do notebook Python

Os engenheiros de dados trabalham com o código-fonte do notebook Python localmente em um IDE (por exemplo, [Visual Studio Code)](https://code.visualstudio.com)ou diretamente no espaço de trabalho Databricks. Este último dá a capacidade de depurar o código no ambiente de desenvolvimento. De qualquer forma, o código será mesclado ao repositório seguindo uma política de ramificação. É altamente recomendável armazenar o `.py` código em `.ipynb` arquivos em vez de em formato de notebook Jupyter. Melhora a legibilidade do código e permite verificações automáticas da qualidade do código no processo ci.

### <a name="azure-data-factory-source-code"></a>Código-fonte da fábrica de dados do Azure

O código fonte dos pipelines da Fábrica de Dados Do Azure é uma coleção de arquivos json gerados por um espaço de trabalho. Normalmente, os engenheiros de dados trabalham com um designer visual no espaço de trabalho da Fábrica de Dados Do Azure, em vez de com os arquivos de código-fonte diretamente. Configure o espaço de trabalho com um repositório de controle de origem como é descrito na documentação da [Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Com essa configuração em vigor, os engenheiros de dados são capazes de colaborar no código-fonte seguindo um fluxo de trabalho de ramificação preferencial.    

## <a name="continuous-integration-ci"></a>Integração contínua (IC)

O objetivo final do processo de Integração Contínua é reunir o trabalho da equipe conjunta a partir do código-fonte e prepará-lo para a implantação nos ambientes a jusante. Assim como no gerenciamento de código-fonte, esse processo é diferente para os notebooks Python e os pipelines da Fábrica de Dados Azure. 

### <a name="python-notebook-ci"></a>Python Notebook CI

O processo CI para os Notebooks Python obtém o código do ramo de colaboração (por exemplo, ***dominar*** ou ***desenvolver)*** e executa as seguintes atividades:
* Linting de código
* Teste de unidade
* Salvando o código como um artefato

O seguinte trecho de código demonstra a implementação dessas etapas em um pipeline ***de yaml*** Azure DevOps:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

O pipeline usa ***flake8*** para fazer o código Python. Ele executa os testes unitários definidos no código-fonte e publica os resultados de fiação e teste para que estejam disponíveis na tela de execução do Azure Pipeline:

![filinting-unit-testes](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se os testes de fiação e unidade forem bem-sucedidos, o pipeline copiará o código-fonte para o repositório de artefatos a ser usado pelas etapas de implantação subseqüentes.

### <a name="azure-data-factory-ci"></a>CI da fábrica de dados do Azure

O processo ci para um pipeline da Fábrica de Dados Azure é um gargalo em toda a história de CI/CD para um pipeline de ingestão de dados. Não há integração ***contínua.*** Um artefato implantável para a Fábrica de Dados Do Azure é uma coleção de modelos do Azure Resource Manager. A única maneira de produzir esses modelos é clicar no botão ***publicar*** no espaço de trabalho da Fábrica de Dados Do Azure. Não há automação aqui.
Os engenheiros de dados mesclam o código-fonte de seus ramos de recursos no ramo de colaboração, por exemplo, ***dominar*** ou ***desenvolver***. Em seguida, alguém com as permissões concedidas clica no botão ***publicar*** para gerar modelos do Azure Resource Manager a partir do código-fonte no ramo de colaboração. Quando o botão é clicado, o espaço de trabalho valida os pipelines (pense nele como de fiação e teste de unidade), gera modelos do Azure Resource Manager (pense nele como de construção) e salva os modelos gerados para um ramo técnico ***adf_publish*** no mesmo repositório de código (pense nele como de publicar artefatos). Este ramo é criado automaticamente pelo espaço de trabalho da Fábrica de Dados Do Azure. Este processo é descrito em detalhes na documentação da [Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

É importante ter certeza de que os modelos gerados do Azure Resource Manager são agnósticos de ambiente. Isso significa que todos os valores que podem diferir entre ambientes são parametrizados. A Fábrica de Dados Do Azure é inteligente o suficiente para expor a maioria desses valores como parâmetros. Por exemplo, no modelo a seguir, as propriedades de conexão a um espaço de trabalho do Azure Machine Learning são expostas como parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

No entanto, você pode querer expor suas propriedades personalizadas que não são tratadas pelo espaço de trabalho da Fábrica de Dados Do Azure por padrão. No cenário deste artigo, um pipeline da Azure Data Factory invoca um notebook Python processando os dados. O notebook aceita um parâmetro com o nome de um arquivo de dados de entrada.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Este nome é diferente para ambientes ***Dev,*** ***QA,*** ***UAT***e ***PROD.*** Em um pipeline complexo com múltiplas atividades, pode haver várias propriedades personalizadas. É uma boa prática coletar todos esses valores em um só lugar e defini-los como ***variáveis***de pipeline:

![adf-variáveis](media/how-to-cicd-data-ingestion/adf-variables.png)

As atividades do pipeline podem se referir às variáveis do pipeline enquanto as utilizam:

![adf-notebook-parâmetros](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

O espaço de trabalho da Fábrica de Dados do Azure ***não*** expõe variáveis de pipeline como parâmetros de modelos do Azure Resource Manager por padrão. O espaço de trabalho usa o [Modelo de Parametrização Padrão](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) ditando quais propriedades do pipeline devem ser expostas como parâmetros de modelo do Azure Resource Manager. Para adicionar variáveis de pipeline à lista, atualize a seção "Microsoft.DataFactory/factories/pipelines" do Modelo de [Parametrização Padrão](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) com o seguinte trecho e coloque o arquivo json de resultado na raiz da pasta de origem:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Isso forçará o espaço de trabalho da Fábrica de Dados do Azure a adicionar as variáveis à lista de parâmetros quando o botão ***de publicação*** for clicado:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Os valores no arquivo json são valores padrão configurados na definição do pipeline. Espera-se que eles sejam substituídos pelos valores do ambiente de destino quando o modelo do Azure Resource Manager for implantado.

## <a name="continuous-delivery-cd"></a>Entrega contínua (CD)

O processo de Entrega Contínua leva os artefatos e os implanta no primeiro ambiente de destino. Ele garante que a solução funcione fazendo testes. Se for bem sucedido, continua para o próximo ambiente. O CD Azure Pipeline consiste em múltiplos estágios representando os ambientes. Cada etapa contém [implantações](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) e [trabalhos](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que executam as seguintes etapas:
* Implantar um notebook Python no espaço de trabalho do Azure Databricks
* Implantar um pipeline azure Data Factory 
* Executar o pipeline
* Confira o resultado da ingestão de dados

As etapas do pipeline podem ser configuradas com [aprovações](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) e [portões](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) que fornecem controle adicional sobre como o processo de implantação evolui através da cadeia de ambientes.

### <a name="deploy-a-python-notebook"></a>Implantar um notebook Python

O seguinte trecho de código define uma [implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) do Azure Pipeline que copia um notebook Python para um cluster Databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Os artefatos produzidos pelo CI são automaticamente copiados para o agente de implantação e estão disponíveis na pasta ***$(Pipeline.Workspace).*** Neste caso, a tarefa de implantação refere-se ao artefato ***di-notebooks*** contendo o notebook Python. Essa [implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) usa a [extensão Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) para copiar os arquivos do notebook para o espaço de trabalho Databricks.
O ***estágio Deploy_to_QA*** contém uma referência ao grupo variável ***devops-ds-qa-vg*** definido no projeto Azure DevOps. As etapas nesta etapa referem-se às variáveis deste grupo variável (por exemplo, $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). A ideia é que a próxima etapa (por exemplo, ***Deploy_to_UAT***) funcione com os mesmos nomes de variável definidos em seu próprio grupo de variáveis com escopo UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Implantar um pipeline azure Data Factory

Um artefato implantável para a Fábrica de Dados Do Azure é um modelo do Azure Resource Manager. Portanto, ele será implantado com a tarefa de implantação do ***Grupo de Recursos Do Azure,*** como é demonstrado no seguinte trecho:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
O valor do parâmetro nome do arquivo de dados vem da variável $(DATA_FILE_NAME) definida em um grupo variável de estágio QA. Da mesma forma, todos os parâmetros definidos em ***ARMTemplateForFactory.json*** podem ser substituídos. Se não forem, os valores padrão serão usados.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Execute o pipeline e verifique o resultado da ingestão de dados

O próximo passo é garantir que a solução implantada esteja funcionando. A definição de trabalho a seguir executa um pipeline azure Data Factory com um [script PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) e executa um notebook Python em um cluster Azure Databricks. O notebook verifica se os dados foram ingeridos corretamente e valida o arquivo de dados de resultado com o nome de $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

A tarefa final no trabalho verifica o resultado da execução do caderno. Se ele retornar um erro, ele define o status da execução do pipeline como falha.

## <a name="putting-pieces-together"></a>Juntando peças

O resultado deste artigo é um Pipeline CI/CD Azure que consiste nas seguintes etapas:
* CI
* Implantar em QA
    * Implantar em Databricks + Implantar no ADF
    * Teste de integração

Ele contém um número de estágios ***de Implantação*** iguais ao número de ambientes de destino que você tem. Cada ***etapa de Implantação*** contém duas implantações que são [executadas](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) em paralelo e um [trabalho](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que é executado após implantações para testar a solução no ambiente.

Uma amostra de implementação do gasoduto é montada no seguinte trecho de ***yaml:***

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Próximas etapas

* [Controle do código-fonte no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Integração e entrega contínuas na Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps para Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
