---
title: Meu primeiro runbook do PowerShell na Automação do Azure
description: Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell.
keywords: azure powershell, tutorial de script do powershell, automação do powershell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367305"
---
# <a name="my-first-powershell-runbook"></a>Meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Comece com um runbook simples que você pode testar e publicar, enquanto aprende como acompanhar o status do trabalho de runbook. Em seguida, modifique o runbook para realmente gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Conclua o tutorial para tornar o runbook mais robusto adicionando parâmetros de runbook.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-quickstart-create-account.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Como você interrompe e inicia esse computador, ele não deve ser uma VM de produção.
* Se necessário, [importe módulos do Azure](shared-resources/modules.md) ou [atualize os módulos](automation-update-azure-modules.md) com base nos cmdlets que você usa.

## <a name="differences-from-powershell-workflow-runbooks"></a>Diferenças de runbooks de fluxo de trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, recursos e gerenciamento como runbooks de fluxo de trabalho do PowerShell. No entanto, há algumas diferenças e limitações.

| Característica  | Runbooks do PowerShell | Runbooks de fluxo de trabalho do PowerShell |
| ------ | ----- | ----- |
| Velocidade | Execute rapidamente, pois eles não usam uma etapa de compilação. | Execute mais lentamente. |
| Pontos de verificação | Não há suporte para pontos de verificação. Um runbook do PowerShell só pode retomar a operação desde o início. | Use pontos de verificação, que permitem que uma pasta de trabalho retome a operação de qualquer ponto. |
| Execução do comando | Suporte somente à execução serial. | Suporte a execução serial e paralela.|
| Runspace | Um único runspace executa tudo em um script. | Um runspace separado pode ser usado para uma atividade, um comando ou um bloco de script. |

Além dessas diferenças, os runbooks do PowerShell têm algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) dos Runbooks de fluxo de trabalho do PowerShell.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um runbook simples que produza o texto `Hello World`.

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **automação de processo** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **criar um runbook**.
4. Atribua o nome **MyFirstRunbook-PowerShell**ao runbook.
5. Nesse caso, você vai criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks). Selecione **PowerShell** para **tipo de runbook**.
6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, você vai digitar o código diretamente no runbook.

1. Seu runbook está vazio no momento. Digite `Write-Output "Hello World"` no corpo do script.

   ![Olá, Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a> Etapa 3 – testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para certificar-se de que ele funciona corretamente. O teste de um runbook executa sua versão de rascunho e permite que você exiba sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do trabalho é iniciado como `Queued`, indicando que o trabalho está aguardando que um runbook Worker na nuvem fique disponível. O status muda para `Starting` quando um trabalho alega o trabalho. Por fim, o status se torna `Running` quando o runbook realmente começa a ser executado.

4. Quando o trabalho de runbook for concluído, o painel de teste exibirá sua saída. Nesse caso, você verá `Hello World`.

   ![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo de rascunho. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role para a esquerda para exibir o runbook na página Runbooks e observe que o valor de **status de criação** é definido como **publicado**.
1. Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**.
   
   As opções na parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado por meio de uma chamada http.
1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook. 
1. Um painel de trabalho é aberto para o trabalho de runbook que foi criado. Embora você possa fechar esse painel, deixe-o aberto no momento para que você possa observar o progresso do trabalho. O status do trabalho é mostrado no **Resumo do trabalho**e os status possíveis são descritos para testar o runbook.

   ![Resumo do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Quando o status do runbook mostrar `Completed`, clique em **saída** para abrir a página saída, onde é possível ver `Hello World` exibida.

   ![Saída do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver `Hello World` no fluxo de saída.

    Observe que o painel fluxos pode mostrar outros fluxos para um trabalho de runbook, como os fluxos de erro e detalhados, se o runbook gravar neles.

   ![Todos os Logs](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche o painel fluxos e o painel trabalho para retornar à página **MyFirstRunbook-PowerShell** .
1. Em **detalhes**, clique em **trabalhos** para abrir a página trabalhos para este runbook. Esta página lista todos os trabalhos criados pelo seu runbook. Você só deve ver um trabalho listado, pois você só executou o trabalho uma vez.

   ![Lista de trabalhos](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho exibido quando você iniciou o runbook. Use este painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Para fazer isso, o runbook deve ser capaz de autenticar usando a conta Executar como que foi criada automaticamente quando você criou sua conta de automação.

Conforme mostrado no exemplo abaixo, a conexão executar como é feita com o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) . Se você estiver gerenciando recursos em várias assinaturas, será necessário usar o parâmetro `AzContext` com [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de automação para as versões mais recentes. Talvez seja necessário atualizar seus módulos mesmo se você acabou de criar uma nova conta de automação.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Abra o editor de texto clicando em **Editar** na página **MyFirstRunbook-PowerShell** .
1. Você não precisa mais do `Write-Output` linha. Vamos continuar e excluí-lo.
1. Digite ou copie e cole o código a seguir, que manipula a autenticação com sua conta Executar como de automação.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Clique em **Painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Após a conclusão, você deverá ver uma saída semelhante à seguinte, exibindo as informações básicas da sua conta. Essa saída confirma que a conta Executar como é válida.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e simplesmente codificar esse nome no runbook por enquanto.

1. Para o script de runbook, adicione o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) para iniciar a máquina virtual. Como mostrado abaixo, o cmdlet inicia uma máquina virtual com o nome `VMName` e com um grupo de recursos denominado `ResourceGroupName`.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.
1. Clique em **Iniciar** para iniciar o teste. Após a conclusão, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter"></a>Etapa 7 – adicionar um parâmetro de entrada

O runbook inicia atualmente a máquina virtual que você codificou no runbook. O runbook será mais útil se você especificar a máquina virtual quando o runbook for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. No editor de texto, modifique o cmdlet `Start-AzVM` para usar variáveis para os parâmetros `VMName` e `ResourceGroupName`. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.
1. Feche o Painel de teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que você iniciou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. 
1. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** para a máquina virtual que você vai iniciar e clique em **OK**.<br><br> ![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](/powershell/scripting/overview).
* Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para saber mais sobre tipos de runbook e suas vantagens e limitações, confira [tipos de runbook de automação do Azure](automation-runbook-types.md).
* Para obter mais informações sobre o recurso de suporte de script do PowerShell, consulte [Native PowerShell script support na automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
