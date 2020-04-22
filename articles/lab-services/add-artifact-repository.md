---
title: Adicione um repositório de artefatos ao seu laboratório no Azure DevTest Labs | Microsoft Docs
description: Aprenda a adicionar um repositório de artefatos ao seu laboratório nos laboratórios Azure DevTest.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770251"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Adicione um repositório de artefatos ao seu laboratório em DevTest Labs
O DevTest Labs permite que você especifique um artefato a ser adicionado a uma VM no momento da criação da VM ou após a criação da VM. Este artefato pode ser uma ferramenta ou um aplicativo que você deseja instalar na VM. Os artefatos são definidos em um arquivo JSON carregado de um repositório GitHub ou Azure DevOps Git.

O [repositório de artefatos públicos,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)mantido pelo DevTest Labs, fornece muitas ferramentas comuns para Windows e Linux. Um link para este repositório é automaticamente adicionado ao seu laboratório. Você pode criar seu próprio repositório de artefatos com ferramentas específicas que não estão disponíveis no repositório de artefatos públicos. Para aprender sobre a criação de artefatos personalizados, consulte [Criar artefatos personalizados.](devtest-lab-artifact-author.md)

Este artigo fornece informações sobre como adicionar seu repositório de artefatos personalizados usando o portal Azure, modelos de gerenciamento de recursos do Azure e o PowerShell do Azure. Você pode automatizar a adição de um repositório de artefatos a um laboratório escrevendo scripts PowerShell ou CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar um repositório de artefatos ao laboratório, você deve primeiro obter as informações de chave do seu repositório. As seções a seguir descrevem como obter as informações necessárias para repositórios hospedados no **GitHub** ou **no Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório GitHub e token de acesso pessoal

1. Vá para a página inicial do repositório GitHub que contém o artefato ou definições de modelo do Resource Manager.
2. Selecione **Clonar ou baixar**.
3. Para copiar a URL para a área de transferência, selecione o botão **URL de clone HTTPS**. Salve a URL para uso posterior.
4. Selecione a imagem de perfil no canto superior direito do GitHub e, em seguida, selecione **Configurações**.
5. No **menu Configurações Pessoais** à esquerda, selecione **Configurações do desenvolvedor**.
6. Selecione **Tokens de acesso pessoal** no menu esquerdo.
7. Selecione **Gerar novo token**.
8. No **Novo token de acesso pessoal**, insira uma **Descrição do token**. Aceite os itens padrão em **Selecionar escopos** e, em seguida, selecione **Gerar Token**.
9. Salve o token gerado. Você usará o token posteriormente.
10. Feche o GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obter a URL de clone do Azure Repos e o token de acesso pessoal
1. Vá para a página inicial da coleção `https://contoso-web-team.visualstudio.com`de sua equipe (por exemplo, ) e, em seguida, selecione seu projeto.
2. Na home page do projeto, selecione **Código**.
3. Para exibir a URL de clone, na página **Código** do projeto, selecione **Clone**.
4. Salve a URL. Você usará a URL posteriormente.
5. Para criar um token de acesso pessoal, selecione **Meu perfil** no menu suspenso da conta de usuário.
6. Na página de informações de perfil, selecione **Segurança**.
7. Na guia **De tokens de acesso > Segurança Pessoal,** selecione **+ Novo Token**.
8. Na **página Criar um novo token de acesso pessoal:**
   1. Digite um **nome** para o token.
   2. Na lista **Organização,** selecione **Todas as organizações acessíveis**.
   3. Na lista **Expiração (UTC),** selecione **90 dias**ou um período de expiração personalizado definido.
   4. Selecione a **opção de acesso completo** para Escopos.
   5. Selecione **Criar**.
9. O novo token será exibido na lista de **Tokens de Acesso Pessoal**. Selecione **Copiar Token**e salve o valor do token para uso posterior.
10. Continue para a seção Conectar seu laboratório ao repositório.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório no portal Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione o laboratório.
4. Selecione **Configuração e políticas** no menu esquerdo.
5. Selecione **Repositórios** na seção **Recursos externos** no menu esquerdo.
6. Selecione **+ Adicione** na barra de ferramentas.

    ![O botão Adicionar repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na página **Repositories,** especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **URL de clone do Git**. Insira a URL HTTPS de clone de Git que você copiou anteriormente do GitHub ou do Azure DevOps Services.
   3. **Ramo.** Insira a ramificação para obter as suas definições.
   4. **Token de acesso pessoal**. Insira o token de acesso pessoal obtido anteriormente do GitHub ou do Azure DevOps Services.
   5. **Caminhos de pasta**. Insira, pelo menos, um caminho de pasta em relação a URL de clone que contém o artefato ou definições de modelo do Resource Manager. Ao especificar um subdiretório, verifique se você incluiu a barra "/" no caminho da pasta.

        ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Clique em **Salvar**.

## <a name="use-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
Os modelos do Azure Resource Management (Azure Resource Manager) são arquivos JSON que descrevem os recursos no Azure que você deseja criar. Para obter mais informações sobre esses modelos, consulte [Os modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório usando um modelo de Gerenciador de Recursos do Azure.  O modelo cria o laboratório se ele ainda não existe.

### <a name="template"></a>Modelo
O modelo de amostra utilizado neste artigo reúne as seguintes informações através de parâmetros. A maioria dos parâmetros tem padrões inteligentes, mas há alguns valores que devem ser especificados. Você deve especificar o nome do laboratório, URI para o repositório de artefatos e o token de segurança para o repositório.

- Nome do laboratório.
- Nome de exibição do repositório de artefatos na interface de usuário do DevTest Labs (UI). O valor padrão `Team Repository`é: .
- URI para o repositório `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`(Exemplo: ou .
- Ramo no repositório que contém artefatos. O valor padrão `master`é: .
- Nome da pasta que contém artefatos. O valor padrão `/Artifacts`é: .
- Tipo de repositório. Os valores permitidos são `VsoGit` ou `GitHub`.
- Token de acesso para o repositório.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Implantar o modelo
Existem algumas maneiras de implantar o modelo no Azure e ter o recurso criado, se ele não existir, ou atualizado, se ele existir. Para obter detalhes, confira os seguintes artigos:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Implantar recursos com modelos do Resource Manager e o portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)

Vamos em frente e ver como implantar o modelo no PowerShell. Os cmdlets usados para implantar o modelo são específicos do contexto, de modo que o inquilino atual e a assinatura atual são usados. Use [Set-AzContext](/powershell/module/az.accounts/set-azcontext) antes de implantar o modelo, se necessário, para alterar o contexto.

Primeiro, crie um grupo de recursos usando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se o grupo de recursos que você deseja usar já existe, pule esta etapa.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Em seguida, crie uma implantação para o grupo de recursos usando [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Este cmdlet aplica as alterações de recursos ao Azure. Várias implantações de recursos podem ser feitas em qualquer grupo de recursos. Se você estiver implantando várias vezes no mesmo grupo de recursos, certifique-se de que o nome de cada implantação é único.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Após a execução do New-AzResourceGroupDeployment com sucesso, o comando produz informações importantes como o estado de provisionamento (deve ser bem sucedido) e quaisquer saídas para o modelo.

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Esta seção fornece um script PowerShell de amostra que pode ser usado para adicionar um repositório de artefatos a um laboratório. Se você não tiver o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) para obter instruções detalhadas para instalá-lo.

### <a name="full-script"></a>Script completo
Aqui está o roteiro completo, incluindo algumas mensagens e comentários verbosos:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell
O exemplo a seguir mostra como executar o script:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parâmetros
O script de amostra PowerShell neste artigo toma os seguintes parâmetros:

| Parâmetro | Descrição |
| --------- | ----------- |
| Nome do Laboratório | O nome do laboratório. |
| ArtefatoRepositoryNome | Nome do novo repositório de artefatos. O script cria um nome aleatório para o respositório se não for especificado. |
| ArtefatoRepositoryDisplayName | Nome de exibição do repositório de artefatos. Este é o nome que aparece nohttps://portal.azure.com) portal Azure ( ao visualizar todos os repositórios de artefatos para um laboratório. |
| RepositoryUri | Uri para o repositório. Exemplos: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.|
| RepositoryBranch | Ramificação em que os arquivos de artefatos podem ser encontrados. Padrão para 'mestre'. |
| FolderPath | Pasta sob a qual os artefatos podem ser encontrados. Padrões para '/Artefatos' |
| PersonalAccessToken | Token de segurança para acessar o repositório GitHub ou VSOGit. Consulte a seção de pré-requisitos para obter instruções para obter token de acesso pessoal |
| SourceType | Se o artefato é o REPOsitório VSOGit ou GitHub. |

O próprio repositório precisa de um nome interno para identificação, o que é diferente do nome de exibição que é visto no portal Azure. Você não vê o nome interno usando o portal Azure, mas você vê-lo ao usar APIs Azure REST ou Azure PowerShell. O script fornece um nome, se não for especificado pelo usuário do nosso script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandos PowerShell usados no script

| Comando do PowerShell | Observações |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Este comando é usado para obter detalhes sobre o laboratório, como sua localização. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Não há um comando específico para adicionar repositórios de artefatos. O cmdlet genérico [New-AzResource](/powershell/module/az.resources/new-azresource) faz o trabalho. Este cmdlet precisa do **resourceId** ou do par **ResourceName** e **ResourceType** para saber o tipo de recurso a ser criado. Este script de amostra usa o nome do recurso e o par de tipos de recursos. <br/><br/>Observe que você está criando a fonte do repositório de artefatos no mesmo local e sob o mesmo grupo de recursos que o laboratório.|

O script adiciona um novo recurso à assinatura atual. Use [get-AzContext](/powershell/module/az.accounts/get-azcontext) para ver essas informações. Use [Set-AzContext](/powershell/module/az.accounts/set-azcontext) para definir o inquilino e a assinatura atuais.

A melhor maneira de descobrir o nome dos recursos e informações do tipo de recurso é usar o site [de APIs do Test Drive Azure REST.](https://azure.github.io/projects/apis/) Confira o provedor [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) para ver as APIs DE REST disponíveis para o provedor de DevTest Labs. Os usuários do script o seguinte ID de recurso.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

O tipo de recurso é tudo listado após 'provedores' no URI, exceto para itens listados nos suportes encaracolados. O nome do recurso é tudo visto nos suportes encaracolados. Se mais de um item é esperado para o nome do recurso, separe cada item com uma barra como fizemos.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Próximas etapas
- [Especifique artefatos obrigatórios para o seu laboratório no Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Criar artefatos personalizados para sua máquina virtual do DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefato no laboratório](devtest-lab-troubleshoot-artifact-failure.md)
