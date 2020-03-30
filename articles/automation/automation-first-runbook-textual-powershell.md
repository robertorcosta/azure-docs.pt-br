---
title: Meu primeiro runbook do PowerShell na Automação do Azure
description: Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell.
keywords: azure powershell, tutorial de script do powershell, automação do powershell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367305"
---
# <a name="my-first-powershell-runbook"></a>Meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Comece com um manual simples que você pode testar e publicar, enquanto aprende a acompanhar o status do trabalho de runbook. Em seguida, modifique o manual para gerenciar os recursos do Azure, neste caso iniciando uma máquina virtual Do Zure. Complete o tutorial para tornar o runbook mais robusto adicionando parâmetros de runbook.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-quickstart-create-account.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Desde que você parar e iniciar esta máquina, não deve ser uma VM de produção.
* Se necessário, [importe módulos Azure](shared-resources/modules.md) ou [atualize módulos](automation-update-azure-modules.md) com base nos cmdlets que você usa.

## <a name="differences-from-powershell-workflow-runbooks"></a>Diferenças dos runbooks do PowerShell Workflow

Os runbooks do PowerShell têm o mesmo ciclo de vida, recursos e gerenciamento que os runbooks do PowerShell Workflow. No entanto, existem algumas diferenças e limitações.

| Característica  | PowerShell Runbooks | Runbooks de fluxo de trabalho PowerShell |
| ------ | ----- | ----- |
| Velocidade | Corra rápido, pois eles não usam uma etapa de compilação. | Corra mais devagar. |
| Pontos de verificação | Não apoie postos de controle. Um runbook PowerShell só pode retomar a operação desde o início. | Use pontos de verificação, que permitem que uma carteira de trabalho retome a operação a partir de qualquer ponto. |
| Execução de Comando | Apoie apenas a execução em série. | Apoie execução em série e paralela.|
| Runspace | Um único runspace executa tudo em um script. | Um runspace separado pode ser usado para uma atividade, um comando ou um bloco de script. |

Além dessas diferenças, os runbooks do PowerShell têm algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) dos runbooks do PowerShell Workflow.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um manual simples que `Hello World`produz o texto .

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **Criar um runbook**.
4. Atribua o nome **MyFirstRunbook-PowerShell**ao runbook.
5. Neste caso, você vai criar um [runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Selecione **PowerShell** para **o tipo Runbook**.
6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, você vai digitar código diretamente no runbook.

1. Seu manual está vazio no momento. Digite `Write-Output "Hello World"` o corpo do roteiro.

   ![Olá, Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Passo 3 - Teste o manual

Antes de publicar o manual para torná-lo disponível na produção, você deve testá-lo para ter certeza de que ele funciona corretamente. Testar um runbook executa sua versão Draft e permite que você visualize sua saída de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do `Queued`trabalho começa como , indicando que o trabalho está esperando um trabalhador de runbook na nuvem para se tornar disponível. O status `Starting` muda para quando um trabalhador reivindica o emprego. Finalmente, o `Running` status se torna quando o runbook realmente começa a ser executado.

4. Quando o trabalho do runbook é concluído, o painel Teste exibe sua saída. Neste caso, você `Hello World`vê .

   ![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo Draft. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role à esquerda para ver o runbook na página Runbooks e observe que o valor **de Status de criação** está definido como **Publicado**.
1. Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**.
   
   As opções em toda a parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado através de uma chamada HTTP.
1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook. 
1. Um painel de emprego é aberto para o trabalho de runbook que foi criado. Embora você possa fechar este painel, deixe-o aberto agora para que você possa assistir o progresso do trabalho. O status do trabalho é mostrado no **Resumo do Trabalho**, e os possíveis status são descritos para testar o manual.

   ![Resumo do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Uma vez que `Completed`o status do runbook seja exibido, `Hello World` clique **em 'Sair'** para abrir a página Saída, onde você pode ver exibido.

   ![Saída do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só `Hello World` deve ver no fluxo de saída.

    Observe que o painel Streams pode mostrar outros fluxos para um trabalho de runbook, como verbose e fluxos error, se o runbook escrever para eles.

   ![Todos os Logs](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche o painel Streams e o painel de trabalho para retornar à página **MyFirstRunbook-PowerShell.**
1. Em **Detalhes,** clique **em Empregos** para abrir a página Empregos para este livro de execução. Esta página lista todos os trabalhos criados pelo seu manual. Você só deve ver um trabalho listado, já que você só executou o trabalho uma vez.

   ![Lista de trabalhos](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que você visualizou quando começou o runbook. Use este painel para ver os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Para fazer isso, o runbook deve ser capaz de autenticar usando a conta Run As que foi criada automaticamente quando você criou sua conta de Automação.

Como mostrado no exemplo abaixo, a conexão Run As é feita com o [cmdlet Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Se você estiver gerenciando recursos em várias `AzContext` assinaturas, você precisa usar o parâmetro com [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Você pode usar esses cmdlets ou atualizar [seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Você pode precisar atualizar seus módulos mesmo se você acabou de criar uma nova conta de Automação.

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
1. Abra o editor textual clicando em **Editar** na página **MyFirstRunbook-PowerShell.**
1. Você não precisa `Write-Output` mais da linha. Vá em frente e exclua.
1. Digite ou copie e cole o seguinte código, que lida com a autenticação com sua conta Automation Run As.

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

1. Clique **em Testar o painel** para que você possa testar o manual.
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluído, você deve ver saída semelhante à seguinte, exibindo informações básicas da sua conta. Essa saída confirma que a conta Executar como é válida.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure, e apenas codifique esse nome no manual por enquanto.

1. Ao seu script de runbook, adicione o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) para iniciar a máquina virtual. Como mostrado abaixo, o cmdlet inicia `VMName` uma máquina virtual `ResourceGroupName`com o nome e com um grupo de recursos chamado .

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
1. Clique **em Começar** para iniciar o teste. Uma vez concluído, certifique-se de que a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter"></a>Passo 7 - Adicionar um parâmetro de entrada

Seu runbook atualmente inicia a máquina virtual que você codifica no manual. O runbook será mais útil se você especificar a máquina virtual quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao manual para fornecer essa funcionalidade.

1. No editor textual, `Start-AzVM` modifique o cmdlet para `VMName` `ResourceGroupName`usar variáveis para os parâmetros e . 

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
1. Pare a máquina virtual que você começou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. 
1. Digite os valores de **VMNAME** e **RESOURCEGROUPNAME** para a máquina virtual que você vai iniciar e, em seguida, clique em **OK**.<br><br> ![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Quando o manual estiver concluído, certifique-se de que a máquina virtual foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](/powershell/scripting/overview).
* Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar com os runbooks do PowerShell Workflow, consulte [Meu primeiro runbook powershell workflow](automation-first-runbook-textual.md).
* Para saber mais sobre os tipos de runbook e suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para obter mais informações sobre o recurso de suporte a script powerShell, consulte [o suporte ao script Native PowerShell no Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
