---
title: Crie ambientes multiVM e recursos PaaS com modelos
description: Saiba como criar ambientes de várias VMs e recursos de PaaS no Azure DevTest Labs com base em um modelo do Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169644"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager

Os ambientes do Azure DevTest Labs permitem que os usuários implantem prontamente infra-estruturas complexas de forma consistente dentro dos limites do laboratório. Você pode usar [modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para criar ambientes com conjuntos de recursos no DevTest Labs. Esses ambientes podem conter qualquer recurso do Azure que os modelos do Resource Manager possam criar.

Você pode facilmente [adicionar uma máquina virtual (VM) de cada vez](devtest-lab-add-vm.md) a um laboratório usando o portal [Azure](https://portal.azure.com). No entanto, cenários como aplicativos web de vários níveis ou uma fazenda SharePoint precisam de um mecanismo para criar várias VMs em um único passo. Ao usar os modelos do Azure Resource Manager, você pode definir a infra-estrutura e a configuração da sua solução Azure e implantar repetidamente várias VMs em um estado consistente.

Os modelos do Azure Resource Manager também fornecem os seguintes benefícios:

- Os modelos do Azure Resource Manager são carregados diretamente do repositório de controle de origem do GitHub ou do Azure Repos.
- Seus usuários podem criar um ambiente escolhendo um modelo configurado do Azure Resource Manager no portal Azure, assim como fazem com outros tipos de bases de [VM](devtest-lab-comparing-vm-base-image-types.md).
- Você pode provisionar recursos do Azure PaaS, bem como VMs IaaS em um ambiente a partir de um modelo de Gerenciador de Recursos do Azure.
- Você pode acompanhar o custo dos ambientes no laboratório, além de VMs individuais criadas por outros tipos de bases. Os recursos paaS são criados e aparecerão no rastreamento de custos. No entanto, o desligamento automático de VM não se aplica aos recursos de PaaS.

Para saber mais sobre os benefícios de usar modelos do Gerenciador de Recursos para implantar, atualizar ou excluir muitos recursos de laboratório em uma única operação, consulte [Benefícios de usar modelos do Gerenciador de recursos](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Quando você usa um modelo de Gerenciador de recursos como base para criar VMs de laboratório, existem algumas diferenças entre a criação de várias VMs ou uma única VM. Para obter mais informações, consulte [Use o modelo Azure Resource Manager de uma máquina virtual](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Use ambientes públicos do DevTest Labs
O Azure DevTest Labs tem um [repositório público de modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que você pode usar para criar ambientes sem ter que se conectar a uma fonte externa do GitHub. Este repositório público é semelhante ao repositório público de artefatos que está disponível no portal Azure para cada laboratório que você cria. O repositório de ambiente permite que você comece rapidamente com modelos de ambiente pré-autores que têm poucos parâmetros de entrada. Esses modelos fornecem uma experiência suave para começar os recursos paaS dentro dos laboratórios.

No repositório público, a equipe do DevTest Labs e outros criaram e compartilharam modelos frequentemente usados, como aplicativos web do Azure, service fabric cluster e um ambiente de desenvolvimento sharePoint Farm. Você pode usar esses modelos diretamente ou personalizá-los para atender às suas necessidades. Para obter mais informações, veja [Configurar e usar ambientes públicos no DevTest Labs](devtest-lab-configure-use-public-environments.md). Depois de criar seus próprios modelos, você pode armazená-los neste repositório para compartilhá-los com outros ou configurar seu próprio repositório git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Crie seus próprios repositórios de modelos

Como uma das práticas recomendadas com infra-estrutura como código e configuração como código, você deve gerenciar modelos de ambiente no controle de origem. O Azure DevTest Labs segue essa prática e carrega todos os modelos do Azure Resource Manager diretamente dos repositórios do GitHub ou do Azure Repos. Como resultado, você pode usar modelos do Gerenciador de recursos em todo o ciclo de lançamento, desde o ambiente de teste até o ambiente de produção.

Existem várias regras a seguir para organizar seus modelos do Azure Resource Manager em um repositório:

- Você deve nomear o arquivo de modelo mestre *azuredeploy.json*.

- Se você quiser usar valores de parâmetro definidos em um arquivo de parâmetro, o arquivo parâmetro deve ser nomeado *azuredeploy.parameters.json*.

  Você pode usar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` para construir o valor do URI parametersLink, permitindo ao DevTest Labs gerenciar automaticamente os modelos aninhados. Para obter mais informações, consulte [Implantar modelos aninhados do Azure Resource Manager para testar ambientes](deploy-nested-template-environments.md).

- Você pode definir metadados para especificar o nome e a descrição do modelo em um arquivo chamado *metadata.json,* da seguinte forma:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Principais arquivos de modelo do Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Adicionar repositórios de modelos ao laboratório

Depois de criar e configurar seu repositório, você pode adicioná-lo ao seu laboratório usando o portal Azure:

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório que você quer.
1. No painel **Visão Geral** do laboratório, selecione **Configuração e políticas**.

   ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Na **lista de configurações e configurações** de configuração, selecione **Repositórios**. O **repositório public artifact repo** é gerado automaticamente para todos os laboratórios e se conecta ao [repositório público Do DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Para adicionar o repositório de modelo do Azure Resource Manager, selecione **Adicionar**.

   ![Repositório público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. No **painel Repositórios,** digite as seguintes informações:

   - **Nome**: Digite um nome de repositório para usar no laboratório.
   - **URL clone do Git**: Digite a URL de clone do GitHttps do GitHub ou do Azure Repos.
   - **Ramo** (opcional): Digite o nome da filial para acessar as definições do modelo do Azure Resource Manager.
   - **Token de acesso pessoal**: Digite o token de acesso pessoal que é usado para acessar seu repositório com segurança.
     - Para obter seu token no Azure Repos, em seu perfil, selecione**Security** > **Tokens de acesso de segurança pessoais de** **configurações** > do usuário .
     - Para obter seu token no GitHub, em seu perfil, selecione **Configurações** > **do desenvolvedor Configurações** > de acesso pessoal**tokens de acesso**.
   - **Caminhos da pasta**: Insira o caminho da pasta em relação ao uri clone do Git para as definições do artefato ou as definições do modelo do Azure Resource Manager.

1. Selecione **Salvar**.

   ![Adicionar novo repositório](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Depois de adicionar um modelo do Azure Resource Manager ao laboratório, os usuários do laboratório podem criar ambientes usando o modelo.

## <a name="configure-access-rights-for-lab-users"></a>Configure os direitos de acesso para usuários de laboratório

Os usuários de laboratório têm a função **Reader** por padrão, então eles não podem alterar os recursos em um grupo de recursos ambientais. Por exemplo, eles não podem parar ou iniciar seus recursos.

Para dar aos seus usuários de laboratório a função **contribuinte** para que eles possam editar os recursos em seus ambientes, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)no painel **Visão Geral** do seu laboratório, selecione **Configuração e políticas**e selecione **configurações de laboratório**.

1. No **painel de configurações** do Laboratório, selecione **Contribuinte**e selecione **Salvar** para conceder permissões de gravação aos usuários do laboratório.

   ![Configurar direitos de acesso de usuário de laboratório](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A próxima seção passa pela criação de ambientes a partir de um modelo do Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Crie ambientes a partir de modelos no portal Azure

Depois de adicionar um modelo do Azure Resource Manager ao laboratório, os usuários do laboratório podem criar ambientes no portal Dozure seguindo estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório que você quer.

1. Na página do laboratório, selecione **Adicionar**.

1. O **Painel Escolher um** painel base exibe as imagens base que você pode usar, com os modelos do Azure Resource Manager listados primeiro. Selecione o modelo do Azure Resource Manager deseja.

   ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. No **painel Adicionar,** digite um valor **de nome de ambiente** para exibir aos usuários do ambiente.

   O modelo do Azure Resource Manager define o resto dos campos de entrada. Se o arquivo *modelo azuredeploy.parameter.json* definir valores padrão, os campos de entrada mostrarão esses valores.

   Para parâmetros de *string tipo seguro,* você pode usar segredos do seu Cofre de Chaves Do Azure. Para aprender sobre armazenar segredos em um cofre de chaves e usá-los ao criar recursos de laboratório, consulte [Os segredos da loja no Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Adicionar painel](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Os seguintes valores de parâmetro não aparecem nos campos de entrada, mesmo que o modelo os especifique. Em vez disso, o formulário mostra campos de entrada em branco onde os usuários de laboratório devem inserir valores ao criar o ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selecione **Adicionar** para criar o ambiente.

   O ambiente começa a provisionar imediatamente, com o status exibido na lista **Minhas máquinas virtuais.** O laboratório cria automaticamente um novo grupo de recursos para provisionar todos os recursos definidos no modelo do Azure Resource Manager.

1. Uma vez criado o ambiente, selecione o ambiente na lista **Minhas máquinas virtuais** para abrir o painel do grupo de recursos e navegar por todos os recursos do ambiente provisionado.

   ![Recursos ambientais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Você também pode expandir o ambiente para visualizar apenas a lista de VMs do ambiente provisionado.

   ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecione qualquer um dos ambientes para visualizar as ações disponíveis, como aplicar artefatos, anexar discos de dados, alterar o tempo de desligamento automático e muito mais.

   ![Ações do ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatize a criação de ambientes com o PowerShell

É viável usar o portal Azure para adicionar um único ambiente a um laboratório, mas quando um cenário de desenvolvimento ou teste deve criar vários ambientes, a implantação automatizada é uma experiência melhor.

Antes de prosseguir, certifique-se de ter um modelo de Gerenciador de Recursos do Azure que defina os recursos a serem criados. [Adicione e configure o modelo em um repositório Git](#configure-your-own-template-repositories)e [adicione o repositório ao laboratório](#add-template-repositories-to-the-lab).

O script de amostra a seguir cria um ambiente em seu laboratório. Os comentários ajudam você a entender melhor o roteiro.

1. Salve o script powershell de amostra a seguir no seu disco rígido como *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Execute o script da seguinte forma, usando seus valores específicos para SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (pasta no repo do Git) e EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Você também pode usar o Azure CLI para implantar recursos com modelos do Gerenciador de Recursos. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Somente um usuário com permissões de proprietário de laboratório pode criar VMs de um modelo do Resource Manager usando o Azure PowerShell. Se você quiser automatizar a criação de VM usando um modelo de Gerenciador de recursos e você só tiver permissões de usuário, você pode usar o comando CLI [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitações de modelo do Gerenciador de recursos em DevTest Labs

Considere essas limitações ao usar modelos do Gerenciador de recursos no DevTest Labs:

- Os modelos do Gerenciador de Recursos não podem se referir à maioria dos recursos existentes. Eles só podem criar novos recursos. Se você tiver modelos de gerenciador de recursos que você usa fora do DevTest Labs que se referem aos recursos existentes, você não poderá usá-los no DevTest Labs. A única exceção é que você pode referenciar uma rede virtual existente.

- Você não pode criar fórmulas ou imagens personalizadas de VMs de laboratório que foram criadas a partir de um modelo de Gerenciador de recursos.

- A maioria das políticas não são avaliadas quando você implanta modelos do Gerenciador de recursos.

  Por exemplo, você pode ter uma política de laboratório que um usuário pode criar apenas cinco VMs. No entanto, um usuário pode implantar um modelo do Resource Manager que crie dezenas de VMs. As políticas que não são avaliadas incluem:

  - Número de VMs por usuário

  - Número de VMs premium por usuário do laboratório

  - Número de discos premium por usuário do laboratório

## <a name="next-steps"></a>Próximas etapas
- Depois de criar uma VM, você pode se conectar à VM selecionando **Conectar** no painel de gerenciamento da VM.
- Exiba e gerencie recursos em um ambiente selecionando-o na lista **Minhas máquinas virtuais** no seu laboratório.
- Explorar os [modelos do Azure Resource Manager na galeria de modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
