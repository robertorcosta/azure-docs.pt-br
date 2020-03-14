---
title: Meu primeiro runbook de Fluxo de Trabalho do PowerShell na Automação do Azure
description: Tutorial que orienta você pela criação, teste e publicação de um runbook textual simples usando o Fluxo de Trabalho do PowerShell.
keywords: fluxo de trabalho do powershell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367254"
---
# <a name="my-first-powershell-workflow-runbook"></a>Meu primeiro runbook de Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [runbook de Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automação do Azure. Comece com um runbook simples que você testa e publica enquanto aprende como acompanhar o status do trabalho de runbook. Em seguida, modifique o runbook para realmente gerenciar recursos do Azure, ilustrado iniciando uma máquina virtual do Azure. Por fim, torne o runbook mais robusto adicionando parâmetros de runbook.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Como você interrompe e inicia esse computador, ele não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Etapa 1: criar o novo runbook

Comece criando um runbook simples que produza o texto `Hello World`.

1. No portal do Azure, abra sua conta da Automação.

   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos são os módulos incluídos automaticamente em uma nova conta de automação. Você também deve ter o ativo de credencial associado à sua assinatura.
 
1. Selecione **Runbooks** em **automação de processo** para abrir a lista de runbooks.
1. Crie um novo runbook selecionando **criar um runbook**.
1. Atribua o nome **MyFirstRunbook-Workflow**ao runbook.
1. Nesse caso, você vai criar um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Selecione **fluxo de trabalho do PowerShell** para o **tipo de runbook**.
1. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook ou pode selecionar cmdlets, runbooks e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, você digita o código diretamente no runbook.

1. No momento, o runbook está vazio com apenas a palavra-chave Required `Workflow`, o nome do runbook e as chaves que encerrarãom todo o fluxo de trabalho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digite `Write-Output "Hello World"` entre as chaves.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para certificar-se de que ele funciona corretamente. O teste de um runbook executa sua versão de rascunho e permite que você exiba sua saída interativamente.

1. Selecione o **painel de teste** para abrir o painel de teste.
1. Clique em **Iniciar** para iniciar o teste, com o teste da única opção habilitada.
1. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do trabalho é iniciado como `Queued`, indicando que o trabalho está aguardando que um runbook Worker na nuvem fique disponível. O status muda para `Starting` quando um trabalho alega o trabalho. Por fim, o status se torna `Running` quando o runbook realmente começa a ser executado.

1. Quando o trabalho de runbook for concluído, o painel de teste exibirá sua saída. Nesse caso, você verá `Hello World`.

   ![Olá, Mundo](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo de rascunho. Você deve publicá-lo antes de poder executá-lo em produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role para a esquerda para exibir o runbook na página **Runbooks** e observe que o campo **status de criação** está definido como **publicado**.
1. Role de volta à direita para exibir a página de **MyFirstRunbook-Workflow**.

   As opções na parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado por meio de uma chamada http.

1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.

   ![Iniciar runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Um painel de trabalho é aberto para o trabalho de runbook que foi criado. Nesse caso, deixe o painel aberto para que você possa ver o progresso do trabalho.

1. Observe que o status do trabalho é mostrado em **Resumo do trabalho**. Esse status corresponde aos status que você viu ao testar o runbook.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando o status do runbook mostrar `Completed`, clique em **saída**. A página saída é aberta, onde você pode ver sua mensagem de `Hello World`.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Feche a página de Saída.

1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver `Hello World` no fluxo de saída. Observe que o painel fluxos pode mostrar outros fluxos para um trabalho de runbook, como os fluxos de erro e detalhados, se o runbook gravar neles.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Feche o painel fluxos e o painel trabalho para retornar à página **MyFirstRunbook** .
1. Clique em **trabalhos** em **recursos** para abrir a página trabalhos para este runbook. Esta página lista todos os trabalhos criados pelo seu runbook. Você só deve ver um trabalho listado, pois você executou o trabalho apenas uma vez.

   ![Trabalhos](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho exibido quando você iniciou o runbook. Use este painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Ele não pode fazer isso, a menos que seja autenticado usando as credenciais da assinatura. A autenticação usa o cmdlet `Connect-AzAccount`.

>[!NOTE]
>Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de automação para as versões mais recentes. Talvez seja necessário atualizar seus módulos mesmo se você acabou de criar uma nova conta de automação.

1. Navegue até a página **MyFirstRunbook-Workflow** e abra o editor de texto clicando em **Editar**.
2. Exclua a linha de `Write-Output`.
3. Posicione o cursor em uma linha em branco entre as chaves.
4. Digite ou copie e cole o código a seguir, que manipula a autenticação com sua conta Executar como de automação.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Clique em **Painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Após a conclusão, você deverá ver uma saída semelhante à seguinte, exibindo as informações básicas da sua conta. Essa ação confirma que a credencial é válida.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que seu runbook está Autenticando para a assinatura do Azure, você pode gerenciar recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure e, por enquanto, você está codificando esse nome no runbook. Se você estiver gerenciando recursos em várias assinaturas, será necessário usar o parâmetro `AzContext` com o cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Forneça o nome e o nome do grupo de recursos da VM para iniciar inserindo uma chamada para o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) , conforme mostrado abaixo. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.
1. Clique em **Iniciar** para iniciar o teste. Após a conclusão, verifique se a VM foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Etapa 7: adicionar um parâmetro de entrada ao runbook

O runbook inicia atualmente a VM que você codificou no runbook. Ele será mais útil se você puder especificar a VM quando o runbook for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione variáveis para os parâmetros `VMName` e `ResourceGroupName` ao runbook e use as variáveis com o cmdlet `Start-AzVM`, conforme mostrado abaixo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada que estão no teste.
3. Feche o Painel de teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a VM que você iniciou.
6. Clique em **Iniciar** para iniciar o runbook. 
7. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** para a VM que você vai iniciar.

   ![Iniciar Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Quando o runbook for concluído, verifique se a VM foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para saber mais sobre tipos de runbook e suas vantagens e limitações, confira [tipos de runbook de automação do Azure](automation-runbook-types.md).
* Para obter mais informações sobre os recursos de suporte de script do PowerShell, consulte [Native PowerShell script support na automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
