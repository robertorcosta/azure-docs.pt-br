---
title: Use a Azure Pipelines para construir & implantar soluções HPC - Azure Batch
description: Saiba como implantar um pipeline de compilação/lançamento para um aplicativo HPC em execução no Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533123"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Use o Azure Pipelines para construir e implantar soluções HPC

Os serviços do Azure DevOps fornecem uma série de ferramentas usadas pelas equipes de desenvolvimento ao criar um aplicativo personalizado. As ferramentas fornecidas pelo Azure DevOps podem se traduzir em construção automatizada e testes de soluções computacionais de alto desempenho. Este artigo demonstra como configurar uma integração contínua (CI) e uma implantação contínua (CD) usando o Azure Pipelines para uma solução de computação de alto desempenho implantada no Azure Batch.

O Azure Pipelines fornece uma gama de processos modernos de CI/CD para construção, implantação, teste e monitoramento de software. Esses processos aceleram sua entrega de software, permitindo que você se concentre em seu código em vez de suportar infra-estrutura e operações.

## <a name="create-an-azure-pipeline"></a>Criar um pipeline Azure

Neste exemplo, criaremos um pipeline de compilação e liberação para implantar uma infra-estrutura do Azure Batch e lançar um pacote de aplicativos. Supondo que o código seja desenvolvido localmente, este é o fluxo geral de implantação:

![Diagrama mostrando o fluxo de implantação em nosso Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Instalação

Para seguir os passos deste artigo, você precisa de uma organização Do Azure DevOps e um projeto de equipe.

* [Criar uma organização Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Crie um projeto no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controle de origem para o seu ambiente

O controle de origem permite que as equipes rastreiem as alterações feitas na base de código e inspecionem versões anteriores do código.

Normalmente, o controle de origem é pensado de mão na mão com código de software. E a infra-estrutura subjacente? Isso nos leva à infra-estrutura como código, onde usaremos modelos do Azure Resource Manager ou outras alternativas de código aberto para definir declarativamente nossa infra-estrutura subjacente.

Essa amostra depende fortemente de vários modelos de gerenciadores de recursos (Documentos JSON) e binários existentes. Você pode copiar esses exemplos em seu repositório e empurrá-los para DevOps Do Zure.

A estrutura de base de código utilizada nesta amostra se assemelha ao seguinte;

* Uma pasta **de modelos de braço,** contendo vários modelos do Azure Resource Manager. Os modelos são explicados neste artigo.
* Uma pasta **de aplicativo de cliente,** que é uma cópia do [Azure Batch .NET File Processing com amostra ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Isso não é necessário para este artigo.
* Uma pasta **de aplicativo hpc,** que é a versão do Windows de 64 bits do [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Uma pasta **de oleodutos.** Isso contém um arquivo YAML delineando nosso processo de compilação. Isso é discutido no artigo.

Esta seção pressupõe que você esteja familiarizado com o controle de versão e o design de modelos do Gerenciador de Recursos. Se você não estiver familiarizado com esses conceitos, consulte as páginas a seguir para obter mais informações.

* [O que é controle do código-fonte?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo aproveita vários modelos do Gerenciador de Recursos para implantar nossa solução. Para isso, usamos uma série de modelos de capacidade (semelhantes a unidades ou módulos) que implementam uma peça específica de funcionalidade. Também usamos um modelo de solução completo que é responsável por reunir esses recursos subjacentes. Há alguns benefícios para esta abordagem:

* Os modelos de capacidade subjacentes podem ser testados individualmente.
* Os modelos de capacidade subjacentes podem ser definidos como um padrão dentro de uma organização e serem reutilizados em múltiplas soluções.

Para este exemplo, há um modelo de solução de ponta a ponta (deployment.json) que implanta três modelos. Os modelos subjacentes são modelos de capacidade, responsáveis pela implantação de um aspecto específico da solução.

![Exemplo de estrutura de modelos vinculados usando modelos do Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que vamos olhar é para uma conta de armazenamento do Azure. Nossa solução requer uma conta de armazenamento para implantar o aplicativo em nossa Conta de Lote. Vale a pena estar ciente do guia de referência do modelo do Gerenciador de [recursos para os tipos de recursos do Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) ao criar modelos do Gerenciador de recursos para contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Em seguida, veremos o modelo de Conta de Lote do Azure. A Conta de Lote do Azure atua como uma plataforma para executar inúmeros aplicativos em pools (agrupamentos de máquinas). Vale a pena estar ciente do guia de referência do modelo do Gerenciador de [recursos para os tipos de recursos Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Gerenciador de recursos para contas em lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

O modelo seguinte mostra um exemplo criando um Pool de Lotes Azure (as máquinas de back-end para processar nossas aplicações). Vale a pena estar ciente do guia de referência do modelo do Gerenciador de [recursos para os tipos de recursos microsoft.batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Gerenciador de recursos para pools de contas em lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Finalmente, temos um modelo que age semelhante a um orquestrador. Este modelo é responsável pela implantação dos modelos de capacidade.

Você também pode saber mais sobre [a criação de modelos vinculados do Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) em um artigo separado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A solução HPC

A infra-estrutura e o software podem ser definidos como código e localizados no mesmo repositório.

Para esta solução, o ffmpeg é usado como o pacote do aplicativo. O pacote ffmpeg pode ser baixado [aqui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exemplo de Estrutura de Repositório Git](media/batch-ci-cd/git-repository.jpg)

Existem quatro seções principais para este repositório:

* A pasta **de modelos de braço** que armazena nossa infra-estrutura como Código
* A pasta **hpc-application** que contém os binários para ffmpeg
* A pasta **de pipelines** que contém a definição para o nosso pipeline de construção.
* **Opcional**: A pasta **cliente-aplicativo** que armazenaria código para o aplicativo .NET. Não usamos isso na amostra, mas em seu próprio projeto, você pode desejar executar as execuçãos do Aplicativo de Lote HPC através de um aplicativo cliente.

> [!NOTE]
> Este é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é usada para demonstrar que o código de aplicação, infra-estrutura e pipeline são armazenados no mesmo repositório.

Agora que o código fonte está configurado, podemos começar a primeira compilação.

## <a name="continuous-integration"></a>Integração contínua

[O Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), dentro dos Serviços Azure DevOps, ajuda você a implementar um pipeline de compilação, teste e implantação para seus aplicativos.

Nesta etapa do seu pipeline, os testes são normalmente executados para validar o código e construir as peças apropriadas do software. O número e os tipos de testes e quaisquer tarefas adicionais que você executar dependerão de sua estratégia de construção e lançamento mais ampla.

## <a name="preparing-the-hpc-application"></a>Preparando o aplicativo HPC

Neste exemplo, vamos focar na pasta **hpc-application.** A pasta **hpc-application** é o software ffmpeg que será executado dentro da conta Azure Batch.

1. Navegue até a seção Builds do Azure Pipelines em sua organização Azure DevOps. Crie um **novo pipeline**.

    ![Criar um novo pipeline de construção](media/batch-ci-cd/new-build-pipeline.jpg)

1. Você tem duas opções para criar um pipeline build:

    a. [Usando o Visual Designer](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Para usá-lo, clique em "Use o designer visual" na página **Novo pipeline.**

    b. [Usando compilações YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Você pode criar um novo pipeline YAML clicando na opção Azure Repos ou GitHub na página Novo pipeline. Alternativamente, você pode armazenar o exemplo abaixo no controle de origem e referenciar um arquivo YAML existente clicando no Visual Designer e, em seguida, usando o modelo YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Uma vez que a compilação esteja configurada conforme necessário, **selecione Salvar & Fila**. Se você tiver a integração contínua ativada (na seção **Gatilhos),** a compilação será acionada automaticamente quando um novo compromisso no repositório for feito, atendendo às condições definidas na compilação.

    ![Exemplo de um pipeline de construção existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Veja atualizações ao vivo sobre o progresso de sua compilação no Azure DevOps navegando para a seção **Build** do Azure Pipelines. Selecione a compilação apropriada a partir da definição de compilação.

    ![Exibir saídas ao vivo da sua compilação](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se você usar um aplicativo cliente para executar seu aplicativo de lote HPC, você precisará criar uma definição de compilação separada para esse aplicativo. Você pode encontrar uma série de guias de como fazer na documentação do [Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)

## <a name="continuous-deployment"></a>Implantação contínua

O Azure Pipelines também foi usado para implantar sua infra-estrutura de aplicativos e subjacentes. [Os pipelines de liberação](https://docs.microsoft.com/azure/devops/pipelines/release) são o componente que permite a implantação contínua e automatiza o processo de liberação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantando seu aplicativo e infra-estrutura subjacente

Há uma série de etapas envolvidas na implantação da infra-estrutura. Como usamos [modelos vinculados,](../azure-resource-manager/templates/linked-templates.md)esses modelos precisarão ser acessíveis a partir de um ponto final público (HTTP ou HTTPS). Isso pode ser um repositório no GitHub, ou uma conta de armazenamento Do Azure Blob, ou outro local de armazenamento. Os artefatos de modelo enviados podem permanecer seguros, pois podem ser mantidos em um modo privado, mas acessados usando algum tipo de token de assinatura de acesso compartilhado (SAS). O exemplo a seguir demonstra como implantar uma infra-estrutura com modelos de uma bolha de armazenamento Azure.

1. Crie uma **nova definição de versão**e selecione uma definição vazia. Em seguida, precisamos renomear o ambiente recém-criado para algo relevante para o nosso pipeline.

    ![Pipeline de liberação inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência do Build Pipeline para obter a saída para o nosso aplicativo HPC.

    > [!NOTE]
    > Mais uma vez, observe o **Alias de origem,** pois isso será necessário quando as tarefas forem criadas dentro da Definição de Versão.

    ![Crie um link de artefato para o HPCApplicationPackage no pipeline de compilação apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie um link para outro artefato, desta vez, um Azure Repo. Isso é necessário para acessar os modelos do Gerenciador de recursos armazenados em seu repositório. Como os modelos do Gerenciador de recursos não exigem compilação, você não precisa empurrá-los através de um pipeline de compilação.

    > [!NOTE]
    > Mais uma vez, observe o **Alias de origem,** pois isso será necessário quando as tarefas forem criadas dentro da Definição de Versão.

    ![Crie um link de artefato para os Repos do Azure](media/batch-ci-cd/Release-2.jpg)

1. Navegue até a seção **de variáveis.** Recomenda-se criar uma série de variáveis em seu pipeline, para que você não esteja inserindo as mesmas informações em várias tarefas. Estas são as variáveis utilizadas neste exemplo, e como elas impactam a implantação.

    * **aplicativoArmazenamentoNome**: Nome da conta de armazenamento para manter binários de aplicativos HPC
    * **batchAccountAppName**: Nome do aplicativo na conta do lote do Azure
    * **batchAccountName**: Nome da conta de lote do Azure
    * **batchAccountPoolName**: Nome do pool de VMs fazendo o processamento
    * **batchApplicationId**: ID exclusivo para o aplicativo Azure Batch
    * **batchApplicationVersion**: Versão semântica do seu aplicativo em lote (ou seja, os binários ffmpeg)
    * **localização**: Localização para os Recursos do Azure a serem implantados
    * **resourceGroupName**: Nome do grupo de recursos a ser criado e onde seus recursos serão implantados
    * **armazenamentoNome da conta**: Nome da conta de armazenamento para manter modelos de gerenciador de recursos vinculados

    ![Exemplo de variáveis definidas para a versão do Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Navegue até as tarefas para o ambiente Dev. No instantâneo abaixo, você pode ver seis tarefas. Essas tarefas: baixar os arquivos ffmpeg com zíper, implantar uma conta de armazenamento para hospedar os modelos aninhados do Gerenciador de Recursos, copiar esses modelos do Gerenciador de recursos para a conta de armazenamento, implantar a conta de lote e as dependências necessárias, criar um aplicativo em a Conta de Lote do Azure e faça upload do pacote do aplicativo para a Conta de Lote do Azure.

    ![Exemplo das tarefas usadas para liberar o aplicativo HPC para o Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **Download Pipeline Artifact (Preview)** e defina as seguintes propriedades:
    * **Nome do display:** Baixar aplicativopacote para agente
    * **O nome do artefato para baixar:** hpc-application
    * **Caminho para download para**: $(System.DefaultWorkingDirectory)

1. Crie uma conta de armazenamento para armazenar seus artefatos. Uma conta de armazenamento existente da solução poderia ser usada, mas para a amostra independente e o isolamento do conteúdo, estamos fazendo uma conta de armazenamento dedicada para nossos artefatos (especificamente os modelos do Gerenciador de Recursos).

    Adicione a tarefa **de implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome do display:** Implantar conta de armazenamento para modelos do Gerenciador de recursos
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Ação**: Criar ou atualizar grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Localização**: $(localização)
    * **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Anular parâmetros do modelo:**-accountName $(storageAccountName)

1. Carregue os artefatos do Controle de Origem para a Conta de Armazenamento. Há uma tarefa do Azure Pipeline para realizar isso. Como parte dessa tarefa, a URL do contêiner de conta de armazenamento e o Token SAS podem ser desproduzidos para uma variável no Azure Pipelines. Isso significa que pode ser reutilizado durante esta fase do agente.

    Adicione a tarefa **Azure File Copy** e defina as seguintes propriedades:
    * **Fonte:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Tipo de conexão Azure**: Gerenciador de recursos do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Tipo de destino**: Azure Blob
    * **Conta de armazenamento RM**: $(armazenamentoAccountName)
    * **Nome do contêiner:** modelos
    * **Uri do contêiner de armazenamento:** templateContainerUri
    * **Token SAS do contêiner**de armazenamento: modeloContainerSToken

1. Implante o modelo de orquestrador. Relembre o modelo de orquestrador de anteriormente, você notará que havia parâmetros para a URL do contêiner da conta de armazenamento, além do token SAS. Você deve notar que as variáveis exigidas no modelo do Gerenciador de recursos são mantidas na seção de variáveis da definição de versão ou foram definidas a partir de outra tarefa do Azure Pipelines (por exemplo, parte da tarefa Azure Blob Copy).

    Adicione a tarefa **de implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome do display:** Implantar lote Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Ação**: Criar ou atualizar grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Localização**: $(localização)
    * **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Parâmetros do modelo de substituição:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar tarefas do Azure Key Vault. Se o Service Principal (conexão com sua Assinatura Azure) tiver um conjunto de políticas de acesso apropriado, ele poderá baixar segredos de um Cofre de Chaves do Azure e ser usado como variáveis em seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword pode ser referenciado com $(sshPassword) na definição de versão.

1. Os próximos passos chamam a CLI do Azure. O primeiro é usado para criar um aplicativo no Azure Batch. e carregar pacotes associados.

    Adicione a tarefa CLI do **Azure** e defina as seguintes propriedades:
    * **Nome do display:** Criar aplicativo na conta de lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Localização do script**: Script inline
    * **Script inline**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A segunda etapa é usada para carregar pacotes associados para o aplicativo. No nosso caso, os arquivos ffmpeg.

    Adicione a tarefa CLI do **Azure** e defina as seguintes propriedades:
    * **Nome do display:** Carregar pacote para a conta de lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Localização do script**: Script inline
    * **Script inline**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número da versão do pacote de aplicativos é definido como uma variável. Isso é conveniente se a substituição de versões anteriores do pacote funcionar para você, e se você quiser controlar manualmente o número da versão do pacote empurrado para o Azure Batch.

1. Crie uma nova versão selecionando **> de lançamento Criar uma nova versão**. Uma vez acionado, selecione o link para sua nova versão para visualizar o status.

1. Você pode visualizar a saída ao vivo do agente selecionando o botão **Logs** embaixo do ambiente.

    ![Veja o status de sua versão](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testando o ambiente

Uma vez configurado o ambiente, confirme que os seguintes testes podem ser concluídos com sucesso.

Conecte-se à nova Conta de Lote do Azure, usando o Azure CLI a partir de um prompt de comando PowerShell.

* Faça login na sua conta `az login` do Azure e siga as instruções para autenticar.
* Agora, autentique a conta Batch:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Liste os aplicativos disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verifique se o pool é válido

```azurecli
az batch pool list
```

Observe o `currentDedicatedNodes` valor da saída deste comando. Este valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensionar a piscina

Redimensione o pool para que haja nós de computação disponíveis para testes de trabalho e tarefas, verifique com o comando pool list para ver o status atual até que o redimensionamento tenha sido concluído e haja nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Próximas etapas

Além deste artigo, existem dois tutoriais que utilizam ffmpeg, usando .NET e Python. Veja estes tutoriais para obter mais informações sobre como interagir com uma conta Batch através de um aplicativo simples.

* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do .NET](tutorial-parallel-dotnet.md)
