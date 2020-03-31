---
title: Integração de controle de origem na Automação do Azure
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132922"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controle de origem na Automação do Azure

 A integração de controle de origem no Azure Automation suporta sincronização de direção única do repositório de controle de origem. O controle de origem permite que você mantenha seus livros de execução em sua conta de Automação atualizados com scripts em seu repositório de controle de origem GitHub ou Azure Repos. Esse recurso facilita a promoção de códigos que foram testados em seu ambiente de desenvolvimento para sua conta de automação de produção.
 
 A integração de controle de origem permite que você colabore facilmente com sua equipe, acompanhe as alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle de origem permite sincronizar diferentes ramos no controle de origem com suas contas de desenvolvimento, teste e automação de produção. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Tipos de controle de origem

O Azure Automation suporta três tipos de controle de origem:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de controle de origem (GitHub ou Azure Repos)
* Uma [conta run como](manage-runas-account.md)
* Os [módulos Azure mais recentes](automation-update-azure-modules.md) em `Az.Accounts` sua conta de `AzureRM.Profile`Automação, incluindo o módulo (módulo Az equivalente a )

> [!NOTE]
> Os trabalhos de sincronização de controle de origem são executados a conta de Automação do usuário e são cobrados na mesma taxa que outros trabalhos de Automação.

## <a name="configuring-source-control"></a>Configuração do controle de origem

Esta seção diz como configurar o controle de origem para sua conta de Automação. Você pode usar o portal Azure ou o PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configure o controle de origem no portal Azure

Use este procedimento para configurar o controle de origem usando o portal Azure.

1. Em sua conta de Automação, selecione **Controle de origem** e clique em **Adicionar**.

    ![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

2. Escolha **o tipo Controle de origem**e clique em **Autenticar**. 

3. Uma janela do navegador abre e solicita que você faça login. Siga as instruções para concluir a autenticação.

4. Na página Resumo de controle de origem, use os campos para preencher as propriedades de controle de origem definidas abaixo. Clique em **Salvar** ao terminar. 

    |Propriedade  |Descrição  |
    |---------|---------|
    |Nome do controle do código-fonte     | Um nome amigável para o controle de origem. Este nome deve conter apenas letras e números.        |
    |Tipo de controle do código-fonte     | Tipo de mecanismo de controle de fonte. As opções disponíveis são:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repositório     | Nome do repositório ou projeto. Os primeiros 200 repositórios são recuperados. Para procurar um repositório, digite o nome no campo e clique **em Pesquisar no GitHub**.|
    |Branch     | Ramifique-se a partir do qual puxar os arquivos de origem. A segmentação de ramificações não está disponível para o tipo de controle de origem TFVC.          |
    |Caminho da pasta     | Pasta que contém os runbooks para sincronizar, por exemplo, **/Runbooks**. Apenas os runbooks na pasta especificada são sincronizados. A recursão não é suportada.        |
    |Sincronização<sup>automática 1</sup>     | Configuração que liga ou desativa a sincronização automática quando um compromisso é feito no repositório de controle de origem.        |
    |Publicar runbook     | Configuração de On se os runbooks forem publicados automaticamente após a sincronização do controle de origem e desapartir em outra.           |
    |Descrição     | Texto especificando detalhes adicionais sobre o controle de origem.        |

    <sup>1</sup> Para ativar o Sincronização Automática ao configurar a integração de controle de origem com os Azure Repos, você deve ser um administrador de projetos.

   ![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> O login do seu repositório de controle de origem pode ser diferente do seu login para o portal Azure. Certifique-se de que você está logado com a conta correta para o repositório de controle de origem ao configurar o controle de origem. Em caso de dúvida, abra uma nova guia no seu navegador, faça login em **dev.azure.com**, **visualstudio.com**ou **github.com,** e tente se reconectar ao controle de origem.

### <a name="configure-source-control-in-powershell"></a>Configure o controle de origem no PowerShell

Você também pode usar o PowerShell para configurar o controle de origem no Azure Automation. Para usar os cmdlets PowerShell para esta operação, você precisa de um token de acesso pessoal (PAT). Use o [cmdlet New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) para criar a conexão de controle de origem. Este cmdlet requer uma corda segura para o PAT. Para aprender como criar uma string segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

As subseções a seguir ilustram a criação do PowerShell da conexão de controle de origem para GitHub, Azure Repos (Git) e Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Criar conexão de controle de origem para o GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Criar conexão de controle de origem para Ozure Repos (Git)

> [!NOTE]
> O Azure Repos (Git) usa uma URL que acessa **dev.azure.com** em vez de **visualstudio.com,** usada em formatos anteriores. O formato `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` de URL mais antigo é preterido, mas ainda suportado. O novo formato é preferido.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Criar conexão de controle de origem para Os Repos do Azure (TFVC)

> [!NOTE]
> O Azure Repos (TFVC) usa uma URL que acessa **dev.azure.com** em vez de **visualstudio.com,** usada em formatos anteriores. O formato `https://<accountname>.visualstudio.com/<projectname>/_versionControl` de URL mais antigo é preterido, mas ainda suportado. O novo formato é preferido.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Permissões de token de acesso pessoal (PAT)

O controle de origem requer algumas permissões mínimas para PATs. As subseções a seguir contêm as permissões mínimas necessárias para os Repos GitHub e Azure.

##### <a name="minimum-pat-permissions-for-github"></a>Permissões mínimas de PAT para GitHub

A tabela a seguir define as permissões mínimas de PAT necessárias para o GitHub. Para obter mais informações sobre como criar um PAT no GitHub, consulte [Criando um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Escopo  |Descrição  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Acessar status de confirmação         |
|`repo_deployment`      | Acessar status de implantação         |
|`public_repo`     | Repositórios públicos de acesso         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Escrever ganchos de repositório         |
|`read:repo_hook`|Ler ganchos de repositório|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Permissões mínimas de PAT para Azure Repos

A lista a seguir define as permissões mínimas de PAT necessárias para o Azure Repos. Para obter mais informações sobre a criação de um PAT no Azure Repos, consulte [Authenticate access with personal access tokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Escopo  |  Tipo de acesso  |
|---------| ----------|
| `Code`      | Ler  |
| `Project and team` | Ler |
| `Identity` | Ler     |
| `User profile` | Ler     |
| `Work items` | Ler    |
| `Service connections` | Ler, consultar, gerenciar<sup>1</sup>    |

<sup>1</sup> `Service connections` A permissão só é necessária se você tiver ativado a sincronização automática.

## <a name="synchronizing"></a>Sincronizando

Siga essas etapas para sincronizar com o controle de origem. 

1. Selecione a fonte da tabela na página de controle Origem. 

2. Clique em **Iniciar sincronização** para iniciar o processo de sincronização. 

3. Exibir o status do trabalho de sincronização atual ou os anteriores clicando na guia **Sincronizar empregos.** 

4. No menu suspenso **controle de origem,** selecione um mecanismo de controle de origem.

    ![Status da sincronização](./media/source-control-integration/sync-status.png)

5. Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. O registro adicional está disponível selecionando **Todos os logs** na página Sumário de trabalho de sincronização de controle de fonte. Essas entradas de log adicionais podem ajudá-lo a solucionar problemas que podem surgir ao usar o controle de origem.

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para desconectar-se de um repositório de controle de origem:

1. Controle **de código aberto** em **Configurações de conta** em sua conta de automação.

2. Selecione o mecanismo de controle de origem para remover. 

3. Na página Resumo do controle do código-fonte, clique em **Excluir**.

## <a name="handling-encoding-issues"></a>Lidar com problemas de codificação

Se várias pessoas estiverem editando runbooks em seu repositório de controle de origem usando diferentes editores, problemas de codificação podem ocorrer. Para saber mais sobre essa situação, consulte [as causas comuns de problemas de codificação.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Atualização do PAT

Atualmente, você não pode usar o portal Azure para atualizar o PAT no controle de origem. Quando seu PAT é expirado ou revogado, você pode atualizar o controle de origem com um novo token de acesso de uma dessas maneiras:

* Use a [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Use o [cmdlet Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os tipos de runbook e suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
