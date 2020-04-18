---
title: Integração e entrega contínuas na Fábrica de Dados do Azure
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
ms.date: 02/12/2020
ms.openlocfilehash: 3e6612b30dd8ae6716c0f87687e77c5961275ea5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606560"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas na Fábrica de Dados do Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

Integração contínua é a prática de testar cada alteração feita em sua base de código automaticamente e o mais cedo possível.A entrega contínua segue os testes que acontecem durante a integração contínua e empurra as alterações para um sistema de estadiamento ou produção.

Na Fábrica de Dados Azure, integração e entrega contínua (CI/CD) significa mover os pipelines da Fábrica de Dados de um ambiente (desenvolvimento, teste, produção) para outro. Você pode usar a integração data factory UX com modelos do Azure Resource Manager para fazer CI/CD.

No UX da fábrica de dados, você pode gerar um modelo de Gerenciador de recursos a partir do menu suspenso **do Modelo ARM.** Quando você seleciona **Exportar modelo DE BRAÇO,** o portal gera o modelo Gerenciador de recursos para a fábrica de dados e um arquivo de configuração que inclui todas as suas strings de conexão e outros parâmetros. Em seguida, você cria um arquivo de configuração para cada ambiente (desenvolvimento, teste, produção). O arquivo de modelo do Resource Manager principal permanece o mesmo em todos os ambientes.

Para uma introdução de nove minutos a este recurso e uma demonstração, assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida do CI/CD

Abaixo está uma visão geral da amostra do ciclo de vida do CI/CD em uma fábrica de dados do Azure que está configurada com o Azure Repos Git. Para obter mais informações sobre como configurar um repositório do Git, consulte [O controle de origem na Fábrica de Dados Do Azure](source-control.md).

1.  Uma fábrica de dados de desenvolvimento é criada e configurada com o Azure Repos Git. Todos os desenvolvedores devem ter permissão para criar recursos da Fábrica de Dados, como pipelines e conjuntos de dados.

1.  À medida que os desenvolvedores fazem alterações em seus ramos de recursos, eles depuram suas operações de pipeline com suas mudanças mais recentes. Para obter mais informações sobre como depurar uma execução de pipeline, consulte [desenvolvimento iterativo e depuração com a Fábrica de Dados Azure](iterative-development-debugging.md).

1.  Depois que os desenvolvedores estão satisfeitos com suas alterações, eles criam uma solicitação de atração de seu ramo de recursos para o ramo mestre ou de colaboração para obter suas alterações revisadas pelos pares.

1.  Após a aprovação de uma solicitação de retirada e a fusão de alterações no ramo principal, as alterações podem ser publicadas na fábrica de desenvolvimento.

1.  Quando a equipe está pronta para implantar as alterações na fábrica de testes e, em seguida, para a fábrica de produção, a equipe exporta o modelo Gerenciador de recursos do ramo mestre.

1.  O modelo gerenciador de recursos exportado é implantado com diferentes arquivos de parâmetros para a fábrica de testes e a fábrica de produção.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Criar um modelo do Resource Manager para cada ambiente

1. Na lista **ARM Template,** selecione **Exportar modelo DE BRAÇO** para exportar o modelo gerenciador de recursos para sua fábrica de dados no ambiente de desenvolvimento.

   ![Exportar um modelo de gerenciador de recursos](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Em suas fábricas de dados de teste e produção, selecione **Importar modelo ARM**. Essa ação leva você até o Portal do Azure, onde você pode importar o modelo exportado. Selecione **Construir seu próprio modelo no editor** para abrir o editor de modelos do Gerenciador de recursos.

   ![Construa seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **'Carregar arquivo'** e, em seguida, selecione o modelo de gerenciador de recursos gerado. Este é o arquivo **arm_template.json** localizado no arquivo .zip exportado na etapa 1.

   ![Editar modelo](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na seção configurações, digite os valores de configuração, como credenciais de serviço vinculadas. Quando terminar, selecione **Comprar** para implantar o modelo Gerenciador de recursos.

   ![Seção de configurações](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Cadeias de conexão

Para obter informações sobre como configurar as strings de conexão, consulte o artigo do conector. Por exemplo, para o Banco de Dados SQL do Azure, consulte [Copiar dados para ou do Banco de Dados SQL do Azure usando o Azure Data Factory](connector-azure-sql-database.md). Para verificar uma seqüência de conexões, você pode abrir a exibição de código para o recurso no UX da fábrica de dados. Na exibição de código, a parte de senha ou chave da conta da seqüência de conexões é removida. Para abrir a exibição de código, selecione o ícone destacado aqui:

![Visão de código aberta para ver a seqüência de conexões](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatize a integração contínua usando os lançamentos do Azure Pipelines

A seguir está um guia para configurar uma versão do Azure Pipelines, que automatiza a implantação de uma fábrica de dados em vários ambientes.

![Diagrama de integração contínua com os pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada ao Visual Studio Team Foundation Server ou a Azure Repos que usa o ponto final do [serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Uma fábrica de dados configurada com a integração Do Azure Repos Git.

-   Um [cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento do Azure Pipelines

1.  No [Azure DevOps,](https://dev.azure.com/)abra o projeto configurado com sua fábrica de dados.

1.  No lado esquerdo da página, selecione **Pipelines**e selecione **Releases**.

    ![Selecione Pipelines, Lançamentos](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **Novo pipeline**ou, se você tiver pipelines existentes, selecione **Novo** e, em seguida, Novo pipeline **de liberação**.

1.  Selecione o modelo **de trabalho Vazio.**

    ![Selecione Trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa **Nome do palco,** digite o nome do seu ambiente.

1.  Selecione **Adicionar artefato**e selecione o repositório configurado com a fábrica de dados. Selecione **adf_publish** para a **ramificação Padrão**. Para a **versão Padrão,** selecione **Mais recente no ramo padrão**.

    ![Adicionar um artefato](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de Implantação do Azure Resource Manager:

    a.  Na exibição de etapas, **selecione Exibir tarefas de estágio**.

    ![Vista do palco](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Crie uma nova tarefa. Procure a **implantação do grupo de recursos do Azure**e selecione **Adicionar**.

    c.  Na tarefa Implantação, selecione a assinatura, o grupo de recursos e a localização da fábrica de dados de destino. Forneça credenciais, se necessário.

    d.  Na lista **Ação,** selecione **Criar ou atualizar grupo de recursos**.

    e.  Selecione o botão ellipsis **(...**) ao lado da **caixa Template.** Procure o modelo do Azure Resource Manager que você criou usando **o Modelo DE IMPORTAR ARM** no modelo Criar um [gerenciador](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) de recursos para cada seção de ambiente deste artigo. Procure por este <FactoryName> arquivo na pasta do ramo adf_publish.

    f.  **Selecione...** ao lado da caixa **Parâmetros Modelo** para escolher o arquivo parâmetros. O arquivo escolhido dependerá se você criou uma cópia ou está usando o arquivo padrão, ARMTemplateParametersForFactory.json.

    g.  **Selecione...** ao lado da caixa **parâmetros de modelo De substituição** e digite as informações para a fábrica de dados de destino. Para obter credenciais provenientes do Azure Key Vault, digite o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, digite **"$(cred1)"** para este valor.

    h. Selecione **Incremental** para o **modo Deimplantação**.

    > [!WARNING]
    > Se você selecionar **Concluir** para o **modo Implantação,** os recursos existentes poderão ser excluídos, incluindo todos os recursos do grupo de recursos de destino que não estão definidos no modelo Gerenciador de recursos.

    ![Implantação do Prod da fábrica de dados](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o pipeline de lançamento.

1. Para ativar uma versão, selecione **Criar versão**.

   ![Selecione Criar versão](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Nos cenários de CI/CD, o tipo de tempo de execução de integração (IR) em diferentes ambientes deve ser o mesmo. Por exemplo, se você tem um IR auto-hospedado no ambiente de desenvolvimento, o mesmo RI também deve ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, você tem que configurar os tempos de execução de integração como conectados auto-hospedados em todos os ambientes, como desenvolvimento, teste e produção.

### <a name="get-secrets-from-azure-key-vault"></a>Obtenha segredos do Azure Key Vault

Se você tem segredos para passar em um modelo do Azure Resource Manager, recomendamos que você use o Azure Key Vault com a versão do Azure Pipelines.

Há duas maneiras de lidar com segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do arquivo de parâmetros que é carregado para o ramo de publicação. Defina os valores dos parâmetros que deseja obter do Key Vault usando este formato:

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

1. Adicione uma [tarefa do Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implantação do Gerenciador de recursos do Azure descrita na seção anterior:

    1.  Na guia **Tarefas,** crie uma nova tarefa. Procure **o Azure Key Vault** e adicione-o.

    1.  Na tarefa Key Vault, selecione a assinatura na qual você criou o cofre de chaves. Forneça credenciais, se necessário, e selecione o cofre-chave.

    ![Adicione uma tarefa do Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente do Azure Pipelines

A tarefa do Azure Key Vault pode falhar com um erro de acesso negado se as permissões corretas não forem definidas. Baixe os logs para a versão e localize o arquivo .ps1 que contém o comando para dar permissões ao agente Azure Pipelines. Você pode executar o comando diretamente. Ou você pode copiar o ID principal do arquivo e adicionar a política de acesso manualmente no portal Azure. `Get`e `List` são as permissões mínimas necessárias.

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos

A implantação poderá falhar se você tentar atualizar gatilhos ativos. Para atualizar os gatilhos ativos, você precisa pará-los manualmente e, em seguida, reiniciá-los após a implantação. Você pode fazer isso usando uma tarefa do Azure PowerShell:

1.  Na guia **Tarefas** da versão, adicione uma tarefa **do Azure PowerShell.** Escolha a versão da tarefa 4.*. 

1.  Selecione a assinatura em que sua fábrica está.

1.  Selecione **Caminho de arquivo de script** como o tipo de script. Isso exige que você salve seu script PowerShell em seu repositório. O seguinte script powerShell pode ser usado para parar gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Você pode completar etapas `Start-AzDataFactoryV2Trigger` semelhantes (com a função) para reiniciar os gatilhos após a implantação.

### <a name="sample-pre--and-post-deployment-script"></a>Exemplo de script pré e pós-implantação

O script de exemplo a seguir pode ser usado para parar os gatilhos antes da implantação e reiniciá-los posteriormente. O script também inclui código para excluir recursos que foram removidos. Salve o script em um repositório git do Azure DevOps e faça referência a ele através de uma tarefa do Azure PowerShell usando a versão 4.*.

Ao executar um script de pré-implantação, você precisará especificar uma variação dos seguintes parâmetros no campo **Argumentos** de script.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Ao executar um script pós-implantação, você precisará especificar uma variação dos seguintes parâmetros no campo **Argumentos** de script.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Tarefa Do Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Aqui está o script que pode ser usado para pré e pós-implantação. Ele contabiliza recursos excluídos e referências de recursos.

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
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
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

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se você estiver no modo GIT, você pode substituir as propriedades padrão no modelo do Gerenciador de recursos para definir propriedades parametrizadas no modelo e propriedades codificadas. Você pode querer substituir o modelo de parametrização padrão nesses cenários:

* Você usa CI/CD automatizado e deseja alterar algumas propriedades durante a implantação do Gerenciador de recursos, mas as propriedades não são parametrizadas por padrão.
* Sua fábrica é tão grande que o modelo padrão do Gerenciador de Recursos é inválido porque tem mais do que os parâmetros máximos permitidos (256).

Nessas condições, para substituir o modelo de parametrização padrão, crie um arquivo chamado **arm-template-parameters-definition.json** na pasta especificada como a pasta raiz para a integração git da fábrica de dados. Você deve usar esse nome de arquivo exato. A Data Factory lê este arquivo de qualquer ramo que você esteja atualmente no portal Azure Data Factory, não apenas do ramo de colaboração. Você pode criar ou editar o arquivo a partir de um ramo privado, onde você pode testar suas alterações selecionando **Exportar modelo de braço** na ui. Em seguida, você pode mesclar o arquivo no ramo de colaboração. Se nenhum arquivo for encontrado, o modelo padrão será usado.

> [!NOTE]
> Um modelo de parametrização personalizado não altera o limite de parâmetro do modelo ARM de 256. Ele permite que você escolha e diminua o número de propriedades parametrizadas.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizados

A seguir estão algumas diretrizes a seguir quando você criar o arquivo de parâmetros personalizados. O arquivo consiste em uma seção para cada tipo de entidade: gatilho, pipeline, serviço vinculado, conjunto de dados, tempo de execução de integração e assim por diante.
* Digite o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome `*` de propriedade para indicar que você deseja parametrizar todas as propriedades sob ele (apenas até o primeiro nível, não recursivamente). Você também pode fornecer exceções a essa configuração.
* Definir o valor de uma propriedade como uma string indica que você deseja parametrizar a propriedade. Use o `<action>:<name>:<stype>`formato .
   *  `<action>` pode ser um desses caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Azure Key Vault para seqüências de conexão ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, leva o nome da propriedade. Se o valor `-` começar com um caractere, o nome será encurtado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria encurtado para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, `string`o tipo padrão é . `string`Valores suportados: , `bool`, `number`, `object`e `securestring`.
* Especificar uma matriz no arquivo de definição indica que a propriedade correspondente no modelo é uma matriz. A fábrica de dados itera todos os objetos da matriz usando a definição especificada no objeto de tempo de execução de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usada como o nome do parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as strings seguras, como segredos do Key Vault, e strings seguras, como strings de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização de amostra

Aqui está um exemplo de como um modelo de parametrização pode parecer:

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
    
* Qualquer propriedade no `activities/typeProperties/waitTimeInSeconds` caminho é parametrizada. Qualquer atividade em um pipeline que tenha `waitTimeInSeconds` uma propriedade `Wait` de nível de código nomeada (por exemplo, a atividade) é parametrizada como um número, com um nome padrão. Mas ele não terá um valor padrão no modelo Gerenciador de recursos. Será uma entrada obrigatória durante a implantação do Gerenciador de Recursos.
* Da mesma forma, `headers` uma propriedade chamada `Web` (por exemplo, em `object` uma atividade) é parametrizada com o tipo (JObject). Ele tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades `typeProperties` sob o caminho são parametrizadas com seus respectivos valores padrão. Por exemplo, existem `IntegrationRuntimes` duas propriedades `computeProperties` `ssisProperties`em propriedades de tipo: e . Ambos os tipos de propriedades são criados com seus respectivos valores e tipos padrão (Object).

#### <a name="triggers"></a>Gatilhos

* Em `typeProperties`, duas propriedades são parametrizadas. O primeiro `maxConcurrency`é, que é especificado para ter`string`um valor padrão e é do tipo . Ele tem o nome `<entityName>_properties_typeProperties_maxConcurrency`do parâmetro padrão .
* A `recurrence` propriedade também está parametrizada. Sob ele, todas as propriedades nesse nível são especificadas para serem parametrizadas como strings, com valores padrão e nomes de parâmetros. Uma exceção `interval` é a propriedade, que `number`é parametrizada como tipo . O nome do parâmetro é `<entityName>_properties_typeProperties_recurrence_triggerSuffix`sufixo com . Da mesma `freq` forma, a propriedade é uma string e é parametrizada como uma string. No entanto, a `freq` propriedade é parametrizada sem um valor padrão. O nome é encurtado e sufixo. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços vinculados são únicos. Como os serviços e conjuntos de dados vinculados têm uma ampla gama de tipos, você pode fornecer personalização específica do tipo. Neste exemplo, para todos os `AzureDataLakeStore`serviços vinculados de tipo, um modelo específico será aplicado. Para todos os `*`outros (via), um modelo diferente será aplicado.
* A `connectionString` propriedade será parametrizada como um `securestring` valor. Não terá um valor padrão. Ele terá um nome de parâmetro abreviado que `connectionString`é sufixo com .
* A `secretAccessKey` propriedade passa `AzureKeyVaultSecret` a ser um (por exemplo, em um serviço vinculado ao Amazon S3). É automaticamente parametrizado como um segredo do Cofre chave Azure e buscado do cofre de chaves configurado. Você também pode parametrizar o cofre da chave em si.

#### <a name="datasets"></a>Conjunto de dados

* Embora a personalização específica do tipo esteja disponível para conjuntos de dados, você pode fornecer configuração sem ter explicitamente uma \*configuração de nível. No exemplo anterior, todas as `typeProperties` propriedades do conjunto de dados abaixo são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

A seguir está o modelo de parametrização padrão atual. Se você precisar adicionar apenas alguns parâmetros, editar este modelo diretamente pode ser uma boa ideia, porque você não perderá a estrutura de parametrização existente.

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

O exemplo a seguir mostra como adicionar um único valor ao modelo de parametrização padrão. Só queremos adicionar um ID de cluster interativo azure Databricks existente para um serviço vinculado ao Databricks ao arquivo parâmetros. Observe que este arquivo é o mesmo que `existingClusterId` o arquivo anterior, exceto para a adição de sob o campo de propriedades de `Microsoft.DataFactory/factories/linkedServices`.

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

Se você configurar ci/cd para suas fábricas de dados, você pode exceder os limites de modelo do Azure Resource Manager à medida que sua fábrica cresce. Por exemplo, um limite é o número máximo de recursos em um modelo de Gerenciador de recursos. Para acomodar grandes fábricas enquanto gera o modelo completo de Gerenciador de Recursos para uma fábrica, a Fábrica de Dados agora gera modelos de Gerenciador de Recursos vinculados. Com este recurso, toda a carga útil de fábrica é dividida em vários arquivos para que você não fique limitado pelos limites.

Se você configurou o Git, os modelos vinculados serão gerados e salvos ao lado dos modelos completos do Gerenciador de recursos no ramo adf_publish em uma nova pasta chamada linkedTemplates:

![Pasta de modelos vinculados do Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos do Gerenciador de recursos vinculados geralmente consistem em um modelo mestre e um conjunto de modelos filho que estão vinculados ao mestre. O modelo pai é chamado ArmTemplate_master.json, e os modelos de filho são nomeados com o padrão ArmTemplate_0.json, ArmTemplate_1.json, e assim por diante. 

Para usar modelos vinculados em vez do modelo completo do Gerenciador de Recursos, atualize sua tarefa de CI/CD para apontar para ArmTemplate_master.json em vez de ArmTemplateForFactory.json (o modelo completo do Gerenciador de Recursos). O Gerenciador de recursos também exige que você carregue os modelos vinculados em uma conta de armazenamento para que o Azure possa acessá-los durante a implantação. Para obter mais informações, consulte [Implantando modelos de gerenciador de recursos vinculados com VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts do Data Factory no pipeline de CI/CD antes e depois a tarefa de implantação.

Se você não tiver o Git configurado, poderá acessar os modelos vinculados via **Exportar modelo DE BRAÇO** na lista ARM **Template.**

## <a name="hotfix-production-branch"></a>Ramo de produção hotfix

Se você implantar uma fábrica para produção e perceber que há um bug que precisa ser corrigido imediatamente, mas você não pode implantar o ramo de colaboração atual, você pode precisar implantar um hotfix. Esta abordagem é conhecida como engenharia de correção rápida ou QFE.

1.    No Azure DevOps, vá para o lançamento que foi implantado para a produção. Encontre o último compromisso que foi implantado.

2.    A partir da mensagem de confirmação, obtenha o ID de confirmação do ramo de colaboração.

3.    Crie um novo ramo hotfix a partir desse compromisso.

4.    Vá para o Ux da Fábrica de Dados Do Azure e mude para o ramo hotfix.

5.    Usando o UX da Fábrica de Dados Do Azure, corrija o bug. Teste suas mudanças.

6.    Depois que a correção for verificada, selecione **Exportar modelo de BRAÇO** para obter o modelo hotfix Resource Manager.

7.    Verifique manualmente esta compilação no ramo adf_publish.

8.    Se você configurou seu pipeline de liberação para acionar automaticamente com base em adf_publish check-ins, uma nova versão será iniciada automaticamente. Caso contrário, enfileirar manualmente uma liberação.

9.    Implantar a liberação hotfix para as fábricas de teste e produção. Esta versão contém a carga útil da produção anterior mais a correção que você fez na etapa 5.

10.    Adicione as alterações do hotfix ao ramo de desenvolvimento para que as versões posteriores não incluam o mesmo bug.

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você está usando a integração do Git com sua fábrica de dados e tem um pipeline de CI/CD que move suas mudanças do desenvolvimento para o teste e, em seguida, para a produção, recomendamos essas práticas recomendadas:

-   **Integração git**. Você precisa configurar apenas sua fábrica de dados de desenvolvimento com a integração git. As alterações no teste e na produção são implantadas via CI/CD e não precisam de integração git.

-   **Script de CI/CD do Data Factory**. Antes da etapa de implantação do Gerenciador de recursos em CI/CD, você precisa concluir certas tarefas, como parar e reiniciar gatilhos e realizar a limpeza. Recomendamos que você use scripts PowerShell antes e depois da implantação. Para obter mais informações, consulte [Atualizar gatilhos ativos](#update-active-triggers).

-   **Tempos de execução de integração e compartilhamento**. Os tempos de execução de integração não mudam com freqüência e são semelhantes em todos os estágios do seu CI/CD. Assim, a Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todas as etapas do CI/CD. Se você quiser compartilhar tempos de execução de integração em todas as etapas, considere usar uma fábrica de ternários apenas para conter os tempos de execução de integração compartilhada. Você pode usar esta fábrica compartilhada em todos os seus ambientes como um tipo de execução de integração vinculada.

-   **Cofre de Chaves**. Quando você usa serviços vinculados com base no Azure Key Vault, você pode aproveitá-los ainda mais mantendo cofres-chave separados para diferentes ambientes. Você também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, você pode não querer que os membros da sua equipe tenham permissões para obter segredos de produção. Se você seguir esta abordagem, recomendamos que você mantenha os mesmos nomes secretos em todas as etapas. Se você mantiver os mesmos nomes, não será necessário alterar os modelos do Gerenciador de Recursos em ambientes de CI/CD, porque a única coisa que muda é o nome do cofre chave, que é um dos parâmetros do modelo do Gerenciador de recursos.

## <a name="unsupported-features"></a>Recursos sem suporte

- Por design, a Data Factory não permite a coleta de erros ou a publicação seletiva de recursos. As publicações incluirão todas as alterações feitas na fábrica de dados.

    - Entidades de fábrica de dados dependem umas das outras. Por exemplo, os gatilhos dependem de dutos, e os gasodutos dependem de conjuntos de dados e outros pipelines. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que você precisa de publicação seletiva, considere usar um hotfix. Para obter mais informações, consulte [o ramo de produção hotfix](#hotfix-production-branch).

-   Não é possível publicar de branches particulares.

-   Você não pode atualmente hospedar projetos no Bitbucket.
