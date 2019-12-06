---
title: Integração do controle do código-fonte na Automação do Azure
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fcb3885446e534a2a6a5153545fd39fb14a02776
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850152"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração do controle de origem na Automação do Azure

O controle do código-fonte permite que você mantenha seus runbooks em sua conta de automação atualizados com seus scripts em seu GitHub ou Azure Repos repositório de controle do código-fonte. O controle de origem permite que você colabore com facilidade com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite sincronizar diferentes branches no controle do código-fonte com suas contas de Automação de desenvolvimento, teste ou produção. Isso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de Automação de produção. A integração de controle do código-fonte com automação dá suporte à sincronização de direção única no repositório do controle do código-fonte.

A automação do Azure dá suporte a três tipos de controle do código-fonte:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Pré-requisitos

* Um repositório de controle do código-fonte (GitHub ou Azure Repos)
* Uma [conta Executar como](manage-runas-account.md)
* Verifique se você tem os [módulos do Azure mais recentes](automation-update-azure-modules.md) em sua conta de automação

> [!NOTE]
> Trabalhos de sincronização de controle do código-fonte são executados na Conta de Automação do usuário e são cobrados conforme a mesma taxa que outros trabalhos de Automação.

## <a name="configure-source-control---azure-portal"></a>Configurar o controle do código-fonte-portal do Azure

Em sua conta de automação, selecione **controle do código-fonte** e clique em **+ Adicionar**

![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

Escolha **Tipo de controle do código-fonte** e clique em **Autenticar**. Uma janela do navegador é aberta e solicita que você entre, siga os prompts para concluir a autenticação.

Na página **Resumo de controle do código-fonte**, preencha as informações e clique em **Salvar**. A tabela a seguir fornece uma descrição dos campos disponíveis.

|Propriedade  |Descrição  |
|---------|---------|
|Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte. *Esse nome deve conter apenas letras e números.*        |
|Tipo de controle do código-fonte     | O tipo de origem do controle do código-fonte. As opções disponíveis são:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
|Repositório     | O nome do repositório ou projeto. Os primeiros 200 repositórios são retornados. Para pesquisar um repositório, digite o nome no campo e clique em **Pesquisar no GitHub**.|
|Filial     | O branch do qual o pull dos arquivos de origem é efetuado. O direcionamento de Branch não está disponível para o tipo de controle do código-fonte TFVC.          |
|Caminho da pasta     | A pasta que contém os runbooks a serem sincronizados. Exemplo:/Runbooks </br>*Somente runbooks na pasta especificada são sincronizados. Não há suporte para recursão.*        |
|Sincronização automática<sup>1</sup>     | Ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório de controle do código-fonte         |
|Publicar runbook     | Se definido como **on**, após os runbooks serem sincronizados do controle do código-fonte, eles serão publicados automaticamente.         |
|Descrição     | Um campo de texto para fornecer detalhes adicionais        |

<sup>1</sup> para habilitar a sincronização automática ao configurar a integração do controle do código-fonte com o Azure Repos, você deve ser um administrador do projeto.

![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Seu logon para o repositório do controle do código-fonte pode ser diferente do seu logon para o portal do Azure. Verifique se você está conectado com a conta correta para seu repositório de controle do código-fonte ao configurar o controle do código-fonte. Se houver alguma dúvida, abra uma nova guia no navegador e faça logoff do visualstudio.com ou do github.com e tente conectar o controle do código-fonte novamente.

## <a name="configure-source-control---powershell"></a>Configurar controle do código-fonte-PowerShell

Você também pode usar o PowerShell para configurar o controle do código-fonte na automação do Azure. Para configurar o controle do código-fonte com os cmdlets do PowerShell, é necessário um PAT (token de acesso pessoal). Use o [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) para criar a conexão de controle do código-fonte. O cmdlet requer uma cadeia de caracteres segura do token de acesso pessoal, para saber como criar uma cadeia de caracteres segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Permissões do token de acesso pessoal

O controle do código-fonte requer algumas permissões mínimas para tokens de acesso pessoal. As tabelas a seguir contêm as permissões mínimas necessárias para o GitHub e o Azure Repos.

#### <a name="github"></a>GitHub

Para obter mais informações sobre como criar um token de acesso pessoal no GitHub, visite [criando um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Escopo  |Descrição  |
|---------|---------|
|**repositório**     |         |
|repo:status     | Acessar status de confirmação         |
|repo_deployment      | Acessar status de implantação         |
|public_repo     | Repositórios públicos de acesso         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Escrever ganchos de repositório         |
|read:repo_hook|Ler ganchos de repositório|

#### <a name="azure-repos"></a>Azure Repos

Para obter mais informações sobre como criar um token de acesso pessoal no Azure Repos, visite [autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Escopo  |
|---------|
|Código (leitura)     |
|Projeto e equipe (leitura)|
|Identidade (leitura)      |
|Perfil do usuário (leitura)     |
|Itens de trabalho (leitura)    |
|Conexões de serviço (ler, consultar e gerenciar)<sup>1</sup>    |

<sup>1</sup> a permissão de conexões de serviço só será necessária se você tiver habilitado o AutoSync.

## <a name="syncing"></a>Sincronização

Selecione a origem da tabela na página **controle do código-fonte** . Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

Você pode exibir o status do trabalho de sincronização atual ou os anteriores clicando na guia **trabalhos de sincronização** . Na lista suspensa **controle do código-fonte** , selecione um controle do código-fonte.

![Status da sincronização](./media/source-control-integration/sync-status.png)

Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

```output
========================================================================================================

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



========================================================================================================
```

O registro em log adicional está disponível selecionando **todos os logs** na página Resumo do trabalho de sincronização de **controle do código-fonte** . Essas entradas de log adicionais podem ajudá-lo a solucionar problemas que podem surgir ao usar o controle do código-fonte.

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para se desconectar de um repositório de controle do código-fonte, **controle do código-fonte** aberto em **configurações de conta** em sua conta de automação.

Selecione o controle do código-fonte que você deseja remover. Na página **Resumo do controle do código-fonte**, clique em **Excluir**.

## <a name="encoding"></a>Codificação

Se várias pessoas estiverem editando runbooks em seu repositório de controle do código-fonte com editores diferentes, haverá a chance de executar problemas de codificação. Essa situação pode levar a caracteres incorretos em seu runbook. Para saber mais sobre isso, confira [causas comuns de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Atualizando o token de acesso

Atualmente, não há nenhuma maneira de atualizar o token de acesso no controle do código-fonte no Portal. Depois que o token de acesso pessoal expirar ou for revogado, você poderá atualizar o controle do código-fonte com um novo token de acesso das seguintes maneiras:

* Por meio da [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Usando o cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
