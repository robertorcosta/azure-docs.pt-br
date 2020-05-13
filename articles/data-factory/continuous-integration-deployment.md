---
title: Integração e entrega contínuas no Azure Data Factory
description: Aprenda a usar integração e entrega contínuas para mover os pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 54ff58735b6831bb45a9477360ffca3439d2f6b4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124713"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

A integração contínua é a prática de testar cada alteração feita na base de código automaticamente e o mais cedo possível.A entrega contínua segue os testes que ocorrem durante a integração contínua e envia por push as alterações para um sistema de preparo ou de produção.

Em Azure Data Factory, a integração contínua e a entrega (CI/CD) significam mover Data Factory pipelines de um ambiente (desenvolvimento, teste, produção) para outro. O Azure Data Factory utiliza [modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) para armazenar a configuração de suas várias entidades do ADF (pipelines, conjuntos de dados, fluxos e assim por diante). Há dois métodos sugeridos para promover um data factory para outro ambiente:

-    Implantação automatizada usando a integração do Data Factory com o [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Carregue manualmente um modelo do Resource Manager usando a integração do Data Factory UX com o Azure Resource Manager.

Para uma introdução de nove minutos a esse recurso e uma demonstração, Assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida de CI/CD

Abaixo está um exemplo de visão geral do ciclo de vida de CI/CD em uma data factory do Azure configurada com Azure Repos git. Para obter mais informações sobre como configurar um repositório git, consulte [controle do código-fonte em Azure data Factory](source-control.md).

1.  Um data factory de desenvolvimento é criado e configurado com Azure Repos git. Todos os desenvolvedores devem ter permissão para criar Data Factory recursos como pipelines e conjuntos de valores.

1.  Um desenvolvedor [cria uma ramificação de recursos](source-control.md#creating-feature-branches) para fazer uma alteração. Eles depuram suas execuções de pipeline com suas alterações mais recentes. Para obter mais informações sobre como depurar uma execução de pipeline, consulte [desenvolvimento iterativo e depuração com Azure data Factory](iterative-development-debugging.md).

1.  Depois que um desenvolvedor estiver satisfeito com suas alterações, ele criará uma solicitação de pull de sua ramificação de recursos para a ramificação mestre ou de colaboração para que as alterações sejam revisadas por colegas.

1.  Depois que uma solicitação de pull é aprovada e as alterações são mescladas no Branch mestre, as alterações são publicadas na fábrica de desenvolvimento.

1.  Quando a equipe estiver pronta para implantar as alterações em uma fábrica de teste ou UAT, a equipe vai para o Azure Pipelines versão e implanta a versão desejada da fábrica de desenvolvimento em UAT. Essa implantação ocorre como parte de uma tarefa de Azure Pipelines e usa parâmetros de modelo do Resource Manager para aplicar a configuração apropriada.

1.  Depois que as alterações tiverem sido verificadas na fábrica de testes, implante na fábrica de produção usando a próxima tarefa da versão de pipelines.

> [!NOTE]
> Somente a fábrica de desenvolvimento é associada a um repositório git. As fábricas de teste e produção não devem ter um repositório git associado a elas e só deverão ser atualizadas por meio de um pipeline DevOps do Azure ou por meio de um modelo de gerenciamento de recursos.

A imagem abaixo realça as diferentes etapas desse ciclo de vida.

![Diagrama de integração contínua com os pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua usando versões Azure Pipelines

Veja a seguir um guia para configurar uma versão Azure Pipelines que automatiza a implantação de um data factory em vários ambientes.

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada ao Visual Studio Team Foundation Server ou Azure Repos que usa o [ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Um data factory configurado com Azure Repos integração com o git.

-   Um [cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento do Azure Pipelines

1.  No [Azure DevOps](https://dev.azure.com/), abra o projeto que está configurado com seu data Factory.

1.  No lado esquerdo da página, selecione **pipelines**e, em seguida, selecione **versões**.

    ![Selecionar pipelines, versões](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **novo pipeline**ou, se você tiver pipelines existentes, selecione **novo** e, em seguida, **novo pipeline de liberação**.

1.  Selecione o modelo de **trabalho vazio** .

    ![Selecionar trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa **nome do estágio** , digite o nome do seu ambiente.

1.  Selecione **Adicionar artefato**e, em seguida, selecione o repositório git configurado com seu data Factory de desenvolvimento. Selecione a [ramificação de publicação](source-control.md#configure-publishing-settings) do repositório para a **ramificação padrão**. Por padrão, esse Branch de publicação é `adf_publish` . Para a **versão padrão**, selecione **mais recente do Branch padrão**.

    ![Adicionar um artefato](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de Implantação do Azure Resource Manager:

    a.  No modo de exibição de estágio, selecione **Exibir tarefas de estágio**.

    ![Exibição de estágio](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Crie uma nova tarefa. Pesquise **implantação do grupo de recursos do Azure**e, em seguida, selecione **Adicionar**.

    c.  Na tarefa de implantação, selecione a assinatura, o grupo de recursos e o local para o data factory de destino. Forneça as credenciais, se necessário.

    d.  Na lista **ação** , selecione **criar ou atualizar grupo de recursos**.

    e.  Selecione o botão de reticências (**...**) ao lado da caixa **modelo** . Procure o modelo de Azure Resource Manager que é gerado no Branch de publicação do repositório git configurado. Procure o arquivo `ARMTemplateForFactory.json` na <FactoryName> pasta da ramificação adf_publish.

    f.  Selecionar **...** ao lado da caixa **parâmetros de modelo** para escolher o arquivo de parâmetros. Procure o arquivo `ARMTemplateParametersForFactory.json` na <FactoryName> pasta da ramificação adf_publish.

    g.  Selecionar **...** ao lado da caixa **Substituir parâmetros de modelo** e insira os valores de parâmetro desejados para o data Factory de destino. Para as credenciais provenientes de Azure Key Vault, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$ (cred1)"** para esse valor.

    h. Selecione **incremental** para o **modo de implantação**.

    > [!WARNING]
    > Se você selecionar **concluir** para o **modo de implantação**, os recursos existentes poderão ser excluídos, incluindo todos os recursos no grupo de recursos de destino que não estão definidos no modelo do Resource Manager.

    ![Implantação do Data Factory prod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o pipeline de lançamento.

1. Para disparar uma versão, selecione **criar versão**. Para automatizar a criação de versões, consulte [gatilhos de versão do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![Selecionar criar versão](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Em cenários de CI/CD, o tipo de IR (Integration Runtime) em ambientes diferentes deve ser o mesmo. Por exemplo, se você tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deverá ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, precisará configurar os tempos de execução de integração como vinculados internamente em todos os ambientes, como desenvolvimento, teste e produção.

### <a name="get-secrets-from-azure-key-vault"></a>Obter segredos de Azure Key Vault

Se você tiver segredos para passar um modelo de Azure Resource Manager, recomendamos que você use Azure Key Vault com a versão Azure Pipelines.

Há duas maneiras de lidar com os segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do arquivo de parâmetros que é carregado para a ramificação de publicação. Defina os valores dos parâmetros que você deseja obter de Key Vault usando este formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando você usa esse método, o segredo é extraído do cofre de chaves automaticamente.

    O arquivo de parâmetros também deve estar no branch de publicação.

1. Adicione uma [tarefa de Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implantação de Azure Resource Manager descrita na seção anterior:

    1.  Na guia **tarefas** , crie uma nova tarefa. Pesquise **Azure Key Vault** e adicione-o.

    1.  Na tarefa Key Vault, selecione a assinatura na qual você criou o cofre de chaves. Forneça as credenciais, se necessário, e selecione o cofre de chaves.

    ![Adicionar uma tarefa de Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente do Azure Pipelines

A tarefa Azure Key Vault poderá falhar com um erro de acesso negado se as permissões corretas não estiverem definidas. Baixe os logs da versão e localize o arquivo. ps1 que contém o comando para conceder permissões ao agente de Azure Pipelines. Você pode executar o comando diretamente. Ou você pode copiar a ID da entidade de segurança do arquivo e adicionar a política de acesso manualmente no portal do Azure. `Get`e `List` são as permissões mínimas necessárias.

### <a name="updating-active-triggers"></a>Atualizando gatilhos ativos

A implantação poderá falhar se você tentar atualizar gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e reiniciá-los após a implantação. Você pode fazer isso usando uma tarefa de Azure PowerShell:

1.  Na guia **tarefas** da versão, adicione uma tarefa de **Azure PowerShell** . Escolha a tarefa versão 4. *. 

1.  Selecione a assinatura em que sua fábrica está.

1.  Selecione **caminho do arquivo de script** como o tipo de script. Isso exige que você salve o script do PowerShell em seu repositório. O seguinte script do PowerShell pode ser usado para parar gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Você pode concluir etapas semelhantes (com a `Start-AzDataFactoryV2Trigger` função) para reiniciar os gatilhos após a implantação.

A equipe de data factory forneceu um [exemplo de script de pré e pós-implantação](#script) localizado na parte inferior deste artigo. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Promover manualmente um modelo do Resource Manager para cada ambiente

1. Na lista **modelo do ARM** , selecione **Exportar modelo do ARM** para exportar o modelo do Resource Manager para seu data Factory no ambiente de desenvolvimento.

   ![Exportar um modelo do Resource Manager](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Em suas fábricas de dados de teste e de produção, selecione **Importar modelo de ARM**. Essa ação leva você até o Portal do Azure, onde você pode importar o modelo exportado. Selecione **criar seu próprio modelo no editor** para abrir o editor de modelos do Resource Manager.

   ![Crie seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **carregar arquivo**e, em seguida, selecione o modelo do Resource Manager gerado. Esse é o arquivo **arm_template. JSON** localizado no arquivo. zip exportado na etapa 1.

   ![Editar modelo](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na seção Configurações, insira os valores de configuração, como credenciais de serviço vinculado. Quando terminar, selecione **comprar** para implantar o modelo do Resource Manager.

   ![Seção de configurações](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se sua fábrica de desenvolvimento tiver um repositório git associado, você poderá substituir os parâmetros de modelo padrão do Resource Manager do modelo do Resource Manager gerado pela publicação ou exportação do modelo. Talvez você queira substituir o modelo de parametrização padrão nesses cenários:

* Você usa CI/CD automatizado e deseja alterar algumas propriedades durante a implantação do Resource Manager, mas as propriedades não são parametrizadas por padrão.
* Sua fábrica é tão grande que o modelo padrão do Resource Manager é inválido porque ele tem mais do que os parâmetros máximos permitidos (256).

Para substituir o modelo de parametrização padrão, crie um arquivo chamado **ARM-template-Parameters-Definition. JSON** na pasta raiz do seu Branch git. Você deve usar esse nome de arquivo exato.

   ![Arquivo de parâmetros personalizados](media/continuous-integration-deployment/custom-parameters.png)

Ao publicar a partir da ramificação de colaboração, Data Factory lerá esse arquivo e usará sua configuração para gerar quais propriedades são parametrizadas. Se nenhum arquivo for encontrado, o modelo padrão será usado.

Ao exportar um modelo do Resource Manager, o Data Factory lê esse arquivo de qualquer ramificação na qual você está trabalhando no momento, não apenas da ramificação de colaboração. Você pode criar ou editar o arquivo de um Branch privado, no qual você pode testar suas alterações selecionando **Exportar modelo ARM** na interface do usuário. Em seguida, você pode mesclar o arquivo no Branch de colaboração.

> [!NOTE]
> Um modelo de parametrização personalizado não altera o limite de parâmetro de modelo ARM de 256. Ele permite que você escolha e diminua o número de propriedades parametrizadas.

### <a name="custom-parameter-syntax"></a>Sintaxe de parâmetro personalizado

Veja a seguir algumas diretrizes a serem seguidas ao criar o arquivo de parâmetros personalizados, **ARM-template-Parameters-Definition. JSON**. O arquivo consiste em uma seção para cada tipo de entidade: gatilho, pipeline, serviço vinculado, conjunto de dados, tempo de execução de integração e fluxo de arquivos.

* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade para  `*` indicar que você deseja parametrizar todas as propriedades abaixo dele (somente até o primeiro nível, não recursivamente). Você também pode fornecer exceções a essa configuração.
* Definir o valor de uma propriedade como uma cadeia de caracteres indica que você deseja parametrizar a propriedade. Use o formato  `<action>:<name>:<stype>` .
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa que não mantenha o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos de Azure Key Vault para cadeias de conexão ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, ele usará o nome da propriedade. Se o valor começar com um `-` caractere, o nome será reduzido. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria reduzido para `AzureStorage1_connectionString` .
   * `<stype>` é o tipo de parâmetro. Se  `<stype>`   estiver em branco, o tipo padrão será `string` . Valores com suporte:,,, `string` `bool` `number` `object` e `securestring` .
* A especificação de uma matriz no arquivo de definição indica que a propriedade correspondente no modelo é uma matriz. Data Factory itera por todos os objetos na matriz usando a definição especificada no objeto de tempo de execução de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usada como o nome do parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as cadeias de caracteres seguras, como segredos de Key Vault e cadeias de caracteres seguras, como cadeias de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização de exemplo

Veja um exemplo de como seria um modelo de parametrização:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Aqui está uma explicação de como o modelo anterior é construído, dividido por tipo de recurso.

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade em um pipeline que tenha uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, a `Wait` atividade) é parametrizada como um número, com um nome padrão. Mas ele não terá um valor padrão no modelo do Resource Manager. Será uma entrada obrigatória durante a implantação do Gerenciador de recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, em uma `Web` atividade) é parametrizada com tipo `object` (JObject). Ele tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades no caminho `typeProperties` são parametrizadas com seus respectivos valores padrão. Por exemplo, há duas propriedades em `IntegrationRuntimes` Propriedades de tipo: `computeProperties` e `ssisProperties` . Ambos os tipos de propriedade são criados com seus respectivos valores e tipos padrão (objeto).

#### <a name="triggers"></a>Gatilhos

* Em `typeProperties` , duas propriedades são parametrizadas. O primeiro é `maxConcurrency` , que é especificado para ter um valor padrão e é do tipo `string` . Ele tem o nome de parâmetro padrão `<entityName>_properties_typeProperties_maxConcurrency` .
* A `recurrence` propriedade também é parametrizada. Sob ele, todas as propriedades nesse nível são especificadas para serem parametrizadas como cadeias de caracteres, com valores padrão e nomes de parâmetro. Uma exceção é a `interval` propriedade, que é parametrizada como tipo `number` . O nome do parâmetro é sufixado com `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Da mesma forma, a `freq` propriedade é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, a `freq` propriedade é parametrizada sem um valor padrão. O nome é reduzido e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços vinculados são exclusivos. Como os serviços vinculados e os conjuntos de linhas têm uma ampla gama de tipos, você pode fornecer personalização específica de tipo. Neste exemplo, para todos os serviços vinculados do tipo `AzureDataLakeStore` , um modelo específico será aplicado. Para todos os outros (via `*` ), um modelo diferente será aplicado.
* A `connectionString` propriedade será parametrizada como um `securestring` valor. Ele não terá um valor padrão. Ele terá um nome de parâmetro abreviado com sufixo `connectionString` .
* A propriedade `secretAccessKey` é um `AzureKeyVaultSecret` (por exemplo, em um serviço vinculado do Amazon S3). Ele é parametrizado automaticamente como um Azure Key Vault segredo e buscado a partir do cofre de chaves configurado. Você também pode parametrizar o cofre de chaves em si.

#### <a name="datasets"></a>Conjunto de dados

* Embora a personalização específica de tipo esteja disponível para conjuntos de informações, você pode fornecer a configuração sem ter explicitamente uma \* configuração de nível. No exemplo anterior, todas as propriedades de DataSet em `typeProperties` são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

Veja abaixo o modelo de parametrização padrão atual. Se você precisar adicionar apenas alguns parâmetros, editar esse modelo diretamente pode ser uma boa ideia, pois você não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Exemplo: parametrizando uma ID de cluster interativa de Azure Databricks existente

O exemplo a seguir mostra como adicionar um único valor ao modelo de parametrização padrão. Queremos apenas adicionar uma ID de cluster interativa de Azure Databricks existente para um serviço vinculado do databricks ao arquivo de parâmetros. Observe que esse arquivo é o mesmo que o arquivo anterior, exceto para a adição de `existingClusterId` sob o campo Propriedades de `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelos Vinculados do Resource Manager

Se você tiver configurado o CI/CD para suas fábricas de dados, você poderá exceder os limites do modelo de Azure Resource Manager à medida que sua fábrica aumentar. Por exemplo, um limite é o número máximo de recursos em um modelo do Resource Manager. Para acomodar grandes fábricas ao gerar o modelo completo do Resource Manager para uma fábrica, Data Factory agora gera modelos vinculados do Resource Manager. Com esse recurso, toda a carga de fábrica é dividida em vários arquivos para que você não seja restrito pelos limites.

Se você tiver configurado o Git, os modelos vinculados serão gerados e salvos junto com os modelos completos do Resource Manager na ramificação adf_publish em uma nova pasta chamada linkedTemplates:

![Pasta de modelos vinculados do Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente consistem em um modelo mestre e um conjunto de modelos filho que estão vinculados ao mestre. O modelo pai é chamado de ArmTemplate_master. JSON, e os modelos filho são nomeados com o padrão ArmTemplate_0. JSON, ArmTemplate_1. JSON e assim por diante. 

Para usar modelos vinculados em vez do modelo completo do Resource Manager, atualize sua tarefa de CI/CD para apontar para ArmTemplate_master. JSON em vez de ArmTemplateForFactory. JSON (o modelo completo do Resource Manager). O Gerenciador de recursos também exige que você carregue os modelos vinculados em uma conta de armazenamento para que o Azure possa acessá-los durante a implantação. Para obter mais informações, consulte [implantando modelos vinculados do Resource Manager com o VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts do Data Factory no pipeline de CI/CD antes e depois a tarefa de implantação.

Se você não tiver o Git configurado, poderá acessar os modelos vinculados por meio do **modelo de ARM de exportação** na lista de modelos do **ARM** .

## <a name="exclude-azure-ssis-integration-runtimes-from-cicd"></a>Excluir tempos de execução de integração do Azure-SSIS de CI/CD

Se sua fábrica de desenvolvimento tiver o tempo de execução de integração do Azure-SSIS, você poderá excluir todos os tempos de execução de integração do Azure-SSIS do processo de CI/CD no cenário abaixo:

- Azure-SSIS IR infraestrutura é complexa e varia em cada ambiente.  
- Azure-SSIS IR é configurado manualmente para cada ambiente com o mesmo nome. Caso contrário, a publicação falhará se houver atividade, dependendo do Azure-SSIS IR.

Para excluir o tempo de execução de integração do Azure-SSIS:

1. Adicione um arquivo publish_config. JSON à pasta raiz no Branch de colaboração, se não existir.
1. Adicione a configuração abaixo a publish_config. JSON: 

```json
{
    " excludeIRs": "true"
}
```

Ao publicar da ramificação de colaboração, os tempos de execução de integração do Azure-SSIS serão excluídos do modelo do Resource Manager gerado.

## <a name="hotfix-production-branch"></a>Branch de produção de hotfix

Se você implantar uma fábrica na produção e perceber que há um bug que precisa ser corrigido imediatamente, mas não é possível implantar o Branch de colaboração atual, talvez seja necessário implantar um hotfix. Essa abordagem é conhecida como engenharia de correção rápida ou QFE.

1.    No Azure DevOps, vá para a versão que foi implantada para produção. Localize a última confirmação implantada.

2.    Na mensagem de confirmação, obtenha a ID de confirmação da ramificação de colaboração.

3.    Crie uma nova ramificação de hotfix a partir dessa confirmação.

4.    Vá para o Azure Data Factory UX e alterne para o Branch de hotfix.

5.    Usando o Azure Data Factory UX, corrija o bug. Teste suas alterações.

6.    Depois que a correção for verificada, selecione **Exportar modelo ARM** para obter o modelo do Gerenciador de recursos de hotfix.

7.    Verifique manualmente essa compilação na ramificação adf_publish.

8.    Se você configurou o pipeline de liberação para disparar automaticamente com base em adf_publish check-ins, uma nova versão será iniciada automaticamente. Caso contrário, enfileirar manualmente uma versão.

9.    Implante a versão do hotfix nas fábricas de teste e produção. Esta versão contém a carga de produção anterior mais a correção que você fez na etapa 5.

10.   Adicione as alterações do hotfix para a ramificação de desenvolvimento para que as versões posteriores não incluam o mesmo bug.

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você estiver usando a integração do git com seu data factory e tiver um pipeline de CI/CD que move as alterações do desenvolvimento para o teste e, em seguida, para a produção, recomendamos estas práticas recomendadas:

-   **Integração com o Git**. Configure somente seu data factory de desenvolvimento com a integração do git. As alterações no teste e na produção são implantadas por meio de CI/CD e não precisam de integração com o git.

-   **Script pré e pós-implantação**. Antes da etapa de implantação do Gerenciador de recursos no CI/CD, você precisa concluir determinadas tarefas, como parar e reiniciar gatilhos e executar a limpeza. Recomendamos que você use scripts do PowerShell antes e depois da tarefa de implantação. Para obter mais informações, consulte [Atualizar gatilhos ativos](#updating-active-triggers). A equipe de data factory [forneceu um script](#script) a ser usado na parte inferior desta página.

-   **Tempos de execução de integração e compartilhamento**. Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD. Portanto Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todos os estágios de CI/CD. Se você quiser compartilhar tempos de execução de integração em todos os estágios, considere o uso de uma fábrica ternário apenas para conter os tempos de execução de integração compartilhados. Você pode usar essa fábrica compartilhada em todos os seus ambientes como um tipo de tempo de execução de integração vinculado.

-   **Key Vault**. Quando você usa serviços vinculados cujas informações de conexão são armazenadas em Azure Key Vault, é recomendável manter cofres de chaves separados para ambientes diferentes. Você também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, talvez você não queira que os membros da equipe tenham permissões para os segredos de produção. Se você seguir essa abordagem, recomendamos que você mantenha os mesmos nomes de segredo em todos os estágios. Se você mantiver os mesmos nomes de segredo, não precisará parametrizar cada cadeia de conexão em ambientes de CI/CD porque a única coisa que muda é o nome do cofre de chaves, que é um parâmetro separado.

## <a name="unsupported-features"></a>Recursos sem suporte

- Por design, Data Factory não permite a seleção de Cherry de confirmações ou publicação seletiva de recursos. As publicações incluirão todas as alterações feitas no data factory.

    - As entidades do data Factory dependem umas das outras. Por exemplo, os gatilhos dependem de pipelines e os pipelines dependem de conjuntos de valores e outros pipelines. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que você precisa de publicação seletiva, considere o uso de um hotfix. Para saber mais, confira [Branch de produção de hotfix](#hotfix-production-branch).

-   Não é possível publicar de branches particulares.

-   Atualmente, não é possível hospedar projetos no bitbucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a>Script pré e pós-implantação de exemplo

O script de exemplo a seguir pode ser usado para parar os gatilhos antes da implantação e reiniciá-los depois. O script também inclui código para excluir recursos que foram removidos. Salve o script em um repositório Git do Azure DevOps e faça referência a ele por meio de uma tarefa Azure PowerShell usando a versão 4. *.

Ao executar um script de pré-implantação, você precisará especificar uma variação dos parâmetros a seguir no campo **argumentos de script** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Ao executar um script pós-implantação, será necessário especificar uma variação dos parâmetros a seguir no campo **argumentos de script** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell tarefa](media/continuous-integration-deployment/continuous-integration-image11.png)

Este é o script que pode ser usado para pré e pós-implantação. Ele conta para recursos excluídos e referências de recursos.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```
