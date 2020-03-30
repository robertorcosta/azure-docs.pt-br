---
title: Meu primeiro runbook de Fluxo de Trabalho do PowerShell na Automação do Azure
description: Tutorial que orienta você pela criação, teste e publicação de um runbook textual simples usando o Fluxo de Trabalho do PowerShell.
keywords: fluxo de trabalho do powershell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367254"
---
# <a name="my-first-powershell-workflow-runbook"></a>Meu primeiro runbook de Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [runbook de Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automação do Azure. Comece com um manual simples que você testa e publica enquanto aprende a acompanhar o status do trabalho de runbook. Em seguida, modifique o manual para realmente gerenciar os recursos do Azure, ilustrado por iniciar uma máquina virtual Do Zure. Por último, torne o runbook mais robusto adicionando parâmetros de runbook.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Desde que você parar e iniciar esta máquina, não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Etapa 1: criar o novo runbook

Comece criando um manual simples que `Hello World`produz o texto .

1. No portal do Azure, abra sua conta da Automação.

   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos são os módulos automaticamente incluídos em uma nova conta de Automação. Você também deve ter o recurso credencial associado à sua assinatura.
 
1. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
1. Crie um novo runbook selecionando **Criar um runbook**.
1. Atribua o nome **MyFirstRunbook-Workflow**ao runbook.
1. Neste caso, você vai criar um [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Selecione **o fluxo de trabalho PowerShell** para o tipo **Runbook**.
1. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar código diretamente no livro de execução ou selecionar cmdlets, runbooks e ativos do controle da Biblioteca e adicioná-los ao livro de execução com quaisquer parâmetros relacionados. Para este tutorial, digite código diretamente no runbook.

1. No momento, seu runbook está `Workflow` vazio apenas com a palavra-chave necessária, o nome do runbook e as chaves que envolvem todo o fluxo de trabalho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digite `Write-Output "Hello World"` entre os aparelhos.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o manual para torná-lo disponível na produção, você deve testá-lo para ter certeza de que ele funciona corretamente. Testar um runbook executa sua versão Draft e permite que você visualize sua saída de forma interativa.

1. Selecione **O painel Teste** para abrir o painel teste.
1. Clique **em Iniciar** para iniciar o teste, testando a única opção habilitada.
1. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do `Queued`trabalho começa como , indicando que o trabalho está esperando um trabalhador de runbook na nuvem para se tornar disponível. O status `Starting` muda para quando um trabalhador reivindica o emprego. Finalmente, o `Running` status se torna quando o runbook realmente começa a ser executado.

1. Quando o trabalho do runbook é concluído, o painel Teste exibe sua saída. Neste caso, você `Hello World`vê .

   ![Olá, Mundo](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo Draft. Você deve publicá-lo antes de executá-lo em produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role à esquerda para ver o runbook na página **Runbooks** e observe que o campo **Status de autoria** está definido como **Publicado**.
1. Role de volta para a direita para exibir a página de **MyFirstRunbook-Workflow**.

   As opções em toda a parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado através de uma chamada HTTP.

1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.

   ![Iniciar runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Um painel de emprego é aberto para o trabalho de runbook que foi criado. Neste caso, deixe o painel aberto para que você possa assistir ao progresso do trabalho.

1. Observe que o status do trabalho é mostrado no **Resumo do Trabalho**. Este status corresponde aos status que você viu ao testar o runbook.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Uma vez que `Completed`o status do runbook seja mostrado, clique **em Saída**. A página Saída é aberta, `Hello World` onde você pode ver sua mensagem.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Feche a página de Saída.

1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só `Hello World` deve ver no fluxo de saída. Observe que o painel Streams pode mostrar outros fluxos para um trabalho de runbook, como verbose e fluxos de erro, se o runbook escrever para eles.

   ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Feche o painel Streams e o painel de trabalho para retornar à página **MyFirstRunbook.**
1. Clique em **'Empregos** em **Recursos'** para abrir a página Empregos para este manual. Esta página lista todos os trabalhos criados pelo seu manual. Você só deve ver um trabalho listado, já que você executou o trabalho apenas uma vez.

   ![Trabalhos](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que você visualizou quando começou o runbook. Use este painel para ver os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Ele não pode fazer isso a menos que ele autenticausando as credenciais para a assinatura. A autenticação `Connect-AzAccount` usa o cmdlet.

>[!NOTE]
>Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Você pode usar esses cmdlets ou atualizar [seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Você pode precisar atualizar seus módulos mesmo se você acabou de criar uma nova conta de Automação.

1. Navegue até a página **MyFirstRunbook-Workflow** e abra o editor textual clicando **em Editar**.
2. Apague `Write-Output` a linha.
3. Posicione o cursor em uma linha em branco entre as chaves.
4. Digite ou copie e cole o seguinte código, que lida com a autenticação com sua conta Automation Run As.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Clique **em Testar o painel** para que você possa testar o manual.
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluído, você deve ver saída semelhante à seguinte, exibindo informações básicas da sua conta. Esta ação confirma que a credencial é válida.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que seu runbook está autenticando a assinatura do Azure, você pode gerenciar recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure, e por enquanto você está codificando esse nome no runbook. Se você estiver gerenciando recursos em várias `AzContext` assinaturas, você precisa usar o parâmetro com o cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Forneça o nome e o nome do grupo de recursos da VM para iniciar inserindo uma chamada para o cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) conforme mostrado abaixo. 

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
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluído, verifique se a VM foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Etapa 7: adicionar um parâmetro de entrada ao runbook

Seu runbook atualmente inicia a VM que você codificou no manual. Será mais útil se você puder especificar a VM quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao manual para fornecer essa funcionalidade.

1. Adicione variáveis para `VMName` `ResourceGroupName` o e parâmetros ao livro de `Start-AzVM` execução e use as variáveis com o cmdlet como mostrado abaixo.

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
5. Pare a VM que você começou.
6. Clique em **Iniciar** para iniciar o runbook. 
7. Digite os valores de **VMNAME** e **RESOURCEGROUPNAME** para a VM que você vai iniciar.

   ![Iniciar Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Quando o manual estiver concluído, verifique se a VM foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar com os runbooks do PowerShell, consulte [Meu primeiro runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Para saber mais sobre os tipos de runbook e suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para obter mais informações sobre os recursos de suporte a script do PowerShell, consulte [o suporte ao script Native PowerShell no Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
