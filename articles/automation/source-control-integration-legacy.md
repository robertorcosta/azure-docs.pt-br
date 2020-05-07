---
title: Integração do controle do código-fonte à Automação do Azure – Herdado
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: b990db39ffe0623b50a2cfc728da61bc51bdd4da
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855350"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integração do controle do código-fonte à Automação do Azure – Herdado

> [!NOTE]
> Há uma nova experiência para o controle do código-fonte. Para saber mais sobre a nova experiência, consulte [Controle do código-fonte (versão prévia)](source-control-integration.md).

A integração de controle do código-fonte permite associar runbooks em sua conta de automação a um repositório de controle do código-fonte do GitHub. O controle de origem permite que você colabore com facilidade com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle de origem permite a sincronização de ramificações diferentes no controle de origem para suas contas de desenvolvimento, teste ou produção da Automação, facilitando a promoção do código testado em seu ambiente de desenvolvimento para o ambiente de produção da conta de Automação.

O controle de origem permite enviar código por push da automação do Azure para o controle do código-fonte ou extrair seus runbooks do controle do código-fonte para a automação do Azure. Este artigo descreve como configurar o controle de origem no seu ambiente da Automação do Azure. Vamos começar configurando a Automação do Azure para acessar seu repositório do GitHub e analisar operações diferentes que podem ser feitas usando a integração de controle do código-fonte. 

> [!NOTE]
> O controle do código-fonte dá suporte à obtenção [e ao envio](automation-runbook-types.md#powershell-runbooks)de Runbooks de fluxo de trabalho do [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , bem como [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) ainda não têm suporte.

## <a name="configuring-source-control"></a>Configurando controle do código-fonte

Há duas etapas simples necessárias para configurar o controle de origem para sua conta de Automação, e somente uma se você já tiver uma conta do GitHub. 

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se você já tiver uma conta do GitHub e um repositório que deseja vincular à Automação do Azure, entre em sua conta existente e comece da etapa 2 abaixo. Caso contrário, navegue até o [GitHub](https://github.com/), Inscreva-se para uma nova conta e [crie um novo repositório](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Instalar o controle de origem

1. Na página Conta de Automação no Portal do Azure, em **Configurações de Conta**, clique em **Controle do Código-Fonte.**

2. A página Controle do Código-Fonte será aberta e você poderá configurar os detalhes da conta GitHub. A seguir, a lista de parâmetros a serem configurados:  

   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolher Origem |Selecione a origem. No momento, apenas **GitHub** tem suporte. |
   | Autorização |Clique no botão **Autorizar** para conceder acesso da Automação do Azure a seu repositório do GitHub. Se você já estiver conectado à sua conta do GitHub em uma janela diferente, as credenciais da conta serão usadas. Depois que a autorização for bem-sucedida, a página mostrará o nome de usuário do GitHub em **propriedade de autorização**. |
   | Escolher o repositório |Selecione um repositório do GitHub na lista de repositórios disponíveis. |
   | Escolher a ramificação |Selecione uma ramificação na lista de ramificações disponíveis. Somente a ramificação **mestre** será mostrada se você ainda não tiver criado quaisquer ramificações. |
   | Caminho da pasta do runbook |O caminho da pasta do runbook especifica o caminho no repositório do GitHub do qual você deseja enviar seu código por push ou por pull. Ele deve ser inserido no formato **/nomedapasta/nomedasubpasta**. Somente os runbooks no caminho da pasta do runbook serão sincronizados com sua conta da Automação. Os runbooks nas subpastas do caminho da pasta do runbook serão **NÃO** serão sincronizados. Use **/** para sincronizar todos os runbooks no repositório. |
3. Por exemplo, se você tiver um repositório chamado **PowerShellScripts** com uma pasta chamada **RootFolder** que contenha uma pasta chamada **SubFolder**. Você pode usar as seguintes cadeias de caracteres para sincronizar cada nível de pasta:

   1. Para sincronizar runbooks do **repositório**, o caminho da pasta **/** do runbook é.
   2. Para sincronizar runbooks de **RootFolder**, o caminho da pasta do runbook é **/RootFolder**.
   3. Para sincronizar os runbooks de **SubFolder**, o caminho da pasta do runbook será **/RootFolder/SubFolder**.
4. Depois de configurar os parâmetros, eles serão exibidos na página Configurar Controle do Código-Fonte.  

    ![A página de controle do código-fonte mostrando as configurações](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Após você clicar em **OK**, a integração do controle do código-fonte estará configurada para sua conta de automação e deverá ser atualizada com as informações do GitHub. Agora você pode clicar nesta parte para exibir todo o histórico do trabalho de sincronização de controle do código-fonte.  

    ![Valores para a configuração de controle do código-fonte configurada atual](media/source-control-integration-legacy/automation-RepoValues.png)
6. Depois de configurar o controle do código-fonte, dois [ativos variáveis](automation-variables.md) são criados na sua conta de automação. Além disso, um aplicativo autorizado é adicionado à sua conta do GitHub.

   * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores da cadeia de conexão, como mostrado abaixo.  

     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Cadeia de caracteres |
     | `Value` |{"Branch":\<*Nome da sua ramificação*>,"RunbookFolderPath":\<*Caminho da pasta do runbook*>,"ProviderType":\<*tem um valor 1 para o GitHub*>,"Repository":\<*Nome do seu repositório*>,"Username":\<*O nome do seu usuário no GitHub*>} |

   * A variável **Microsoft.Azure.Automation.SourceControl.OAuthToken**contém o valor criptografado seguro do OAuthToken.  

     |**Parâmetro**            |**Valor** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Uma janela mostrando variáveis de controle do código-fonte](media/source-control-integration-legacy/automation-Variables.png)  

   * **Controle de Origem de Automação** é adicionado como um aplicativo autorizado à sua conta do GitHub. Para exibir o aplicativo, em seu home page do GitHub, navegue até**configurações** > de **perfil** > **aplicativos**. Esse aplicativo permite que a Automação do Azure sincronize seu repositório do GitHub para uma conta da Automação.  

     ![Configurações do aplicativo no GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Usando o controle do código-fonte na automação

O check-in de runbook permite que você envie por push as alterações feitas em um runbook na Automação do Azure para o repositório do controle do código-fonte. A seguir, as etapas para fazer check-in de um runbook:

1. Na sua conta de automação, [crie um novo runbook textual](automation-first-runbook-textual.md)ou [edite um runbook textual existente](automation-edit-textual-runbook.md). Esse runbook pode ser um Fluxo de Trabalho do PowerShell ou um runbook de script do PowerShell.  
2. Depois de editar o runbook, salve-o e clique em **check-in** na página Editar.  

    ![Uma janela que mostra o check-in para o botão do GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > O check-in da Automação do Azure substituirá o código que existe atualmente no controle do código-fonte. A instrução de linha de comando equivalente do Git para fazer check-in é **git add + git commit + git push**  

3. Quando você clicar em **check-in**, uma mensagem de confirmação será exibida. Clique em **Sim** para continuar.  

    ![Uma caixa de diálogo confirmando o check-in no controle do código-fonte](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. O check-in inicia o runbook do controle de origem: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Esse runbook se conecta ao GitHub e envia por push alterações da Automação do Azure para seu repositório. Para exibir o histórico de trabalho de check-in, volte para a guia **Integração de Controle do Código-fonte** e clique para abrir a página Sincronização de Repositório. Esta página mostra todos os seus trabalhos de controle do código-fonte. Selecione o trabalho que você deseja exibir e clique para exibir os detalhes.  

    ![Uma janela mostrando os resultados de um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Runbooks de controle de origem são runbooks especiais de Automação que você não pode exibir nem editar. Embora eles não apareçam em sua lista de runbooks, você verá os trabalhos de sincronização em sua lista de trabalhos.

5. O nome do runbook modificado é enviado como um parâmetro de entrada para o runbook de check-in. Você pode [exibir os detalhes do trabalho](automation-runbook-execution.md#job-statuses) expandindo o runbook na página sincronização do repositório.  

    ![Uma janela mostrando a entrada para um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Atualize seu repositório do GitHub depois que o trabalho for concluído para exibir as alterações.  Deve haver uma confirmação em seu repositório com uma mensagem de confirmação: ** *nome do runbook* atualizado na automação do Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sincronizar runbooks do controle de origem para a Automação do Azure

O botão de sincronização na página Sincronização do Repositório permite efetuar pull em todos os runbooks no caminho da pasta do runbook do repositório para sua conta de Automação. O mesmo repositório pode ser sincronizado em mais de uma conta de Automação. A seguir, as etapas para sincronizar um runbook:

1. Na conta de automação em que você configurou o controle do código-fonte, abra a página de sincronização de controle do código-fonte/repositório e clique em **sincronizar**.  Você receberá uma mensagem de confirmação, clique em **Sim** para continuar.  

    ![O botão Sincronizar com a mensagem confirmando todos os runbooks será sincronizado](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. A sincronização inicia o runbook **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** , que se conecta ao GitHub e efetua pull das alterações de seu repositório para a automação do Azure. Você verá um novo trabalho na página Sincronização de Repositório para esta ação. Para exibir detalhes sobre o trabalho de sincronização, clique para abrir a página de detalhes do trabalho.  

    ![Uma janela mostrando os resultados da sincronização de um trabalho de sincronização em um repositório GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Uma sincronização do controle de origem substitui a versão de rascunho dos runbooks que existem atualmente em sua conta da Automação para **TODOS** os runbooks atualmente no controle de origem. A instrução de linha de comando equivalente do Git para sincronizar é **git pull**

![Uma janela mostrando todos os logs de um trabalho de sincronização de controle do código-fonte suspenso](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para se desconectar de sua conta do GitHub, abra a página Sincronização de Repositórios e clique em **Desconectar**. Depois de se desconectar do controle do código-fonte, os runbooks sincronizados anteriormente ainda permanecerão em sua conta da Automação, mas a página Sincronização de Repositórios não será habilitada.  

  ![Uma janela que mostra o botão desconectar para desconectar o controle do código-fonte](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de controle do código-fonte, veja os seguintes recursos:  

* [Automação do Azure: integração de controle do código-fonte na Automação do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Automação do Azure: integrando o controle do código-fonte do runbook usando o Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
