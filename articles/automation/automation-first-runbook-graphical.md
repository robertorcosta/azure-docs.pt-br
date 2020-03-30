---
title: Meu primeiro runbook gráfico na Automação do Azure
description: Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples.
keywords: runbook, modelo de runbook, automação de runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6bd360b2075c337e3ed3d69d84368d16571a9335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536047"
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automação do Azure. Comece com um manual simples que você pode testar e publicar, enquanto aprende a acompanhar o status do trabalho de runbook. Em seguida, modifique o manual para gerenciar os recursos do Azure, neste caso iniciando uma máquina virtual Do Zure. Complete o tutorial para tornar o runbook mais robusto adicionando parâmetros de runbook e links condicionais.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Desde que você parar e iniciar esta máquina, não deve ser uma VM de produção.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um manual simples que `Hello World`produz o texto .

1. No portal do Azure, abra sua conta da Automação. 

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos são os módulos automaticamente incluídos em uma nova conta de Automação. Você também deve ter o recurso credencial associado à sua assinatura.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **Criar um runbook**.
4. Atribua o nome **MyFirstRunbook-Graphical**ao runbook.
5. Neste caso, você vai criar um [runbook gráfico](automation-graphical-authoring-intro.md). Selecione **Gráfico** para **o tipo Runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Passo 2 - Adicionar atividades

O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook. Você vai adicionar um `Write-Output` cmdlet para output text do runbook.

1. No controle biblioteca, clique no campo `write-output`de pesquisa e digite . Os resultados da pesquisa são mostrados na imagem a seguir. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Role até o final da lista. Clique com o botão direito do mouse **'Gravar-Saída'** e **selecione Adicionar à tela**. Alternativamente, você pode clicar na elipse (...) ao lado do nome do cmdlet e, em seguida, selecionar **Adicionar à tela**.
1. Clique na atividade **Write-Output** na tela. Esta ação abrirá a página Controle da configuração, que permite configurar a atividade.
1. O **campo Rótulo** é padrão para o nome do cmdlet, mas você pode alterá-lo para algo mais amigável. Mude para. `Write Hello World to output`
1. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros, e você precisa selecionar qual usar. Neste caso, `Write-Output` tem apenas um parâmetro definido.

1. Selecione `InputObject` o parâmetro. Este é o parâmetro que você usa para especificar o texto a ser enviado para o fluxo de saída.
1. O menu suspenso **da fonte de dados** fornece fontes que você pode usar para preencher um valor de parâmetro. Neste menu, selecione **a expressão PowerShell**. 

   Você pode usar a saída de fontes como outra atividade, um ativo de Automação ou uma expressão PowerShell. Neste caso, a saída `Hello World`é apenas . Você pode usar uma expressão do PowerShell e especificar uma cadeia de caracteres.<br>

1. No campo **Expressão,** digite `Hello World` e clique em **OK** duas vezes para retornar à tela.
1. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o manual para torná-lo disponível na produção, você deve testá-lo para ter certeza de que ele funciona corretamente. Testar um runbook executa sua versão Draft e permite que você visualize sua saída de forma interativa.

1. Selecione **O painel Teste** para abrir o painel teste.
1. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
1. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do `Queued`trabalho começa como , indicando que o trabalho está esperando um trabalhador de runbook na nuvem para se tornar disponível. O status `Starting` muda para quando um trabalhador reivindica o emprego. Finalmente, o `Running` status se torna quando o runbook realmente começa a ser executado.

1. Quando o trabalho do runbook é concluído, o painel Teste exibe sua saída. Neste caso, você `Hello World`vê .

    ![Olá, Mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo Draft. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Selecione **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role à esquerda para ver o runbook na página Runbooks e observe que o valor **de Status de criação** está definido como **Publicado**.
1. Role de volta para a direita para visualizar a página para **MyFirstRunbook-Graphical**.

   As opções em toda a parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado através de uma chamada HTTP.

1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.
1. Um painel de emprego é aberto para o trabalho de runbook que foi criado. Verifique se o **campo de status do trabalho** mostra **concluído**.
1. Clique **em Sair** para abrir a `Hello World` página Saída, onde você pode ver exibido.
1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só `Hello World` deve ver no fluxo de saída. 

    Observe que o painel Streams pode mostrar outros fluxos para um trabalho de runbook, como verbose e fluxos error, se o runbook escrever para eles.
1. Feche o painel Streams e o painel de trabalho para retornar à página **MyFirstRunbook-Graphical.**
1. Para ver todos os trabalhos do manual, selecione **Empregos** em **Recursos**. A página Jobs lista todos os trabalhos criados pelo seu manual. Você deve ver apenas um trabalho listado, uma vez que você só executou o trabalho uma vez.
1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que você visualizou quando começou o runbook. Use este painel para ver os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---create-variable-assets"></a>Etapa 5: criar ativos de variável

Você testou e publicou seu manual, mas até agora não faz nada útil para gerenciar os recursos do Azure. Antes de configurar o manual para autenticar, você deve criar uma variável para segurar o ID de assinatura, configurar uma atividade para autenticar e, em seguida, referenciar a variável. Incluir uma referência ao contexto de assinatura permite que você trabalhe facilmente com várias assinaturas.

1. Copie seu ID de assinatura da opção **Assinaturas** no painel Navegação.
1. Na página Contas de Automação, selecione Variáveis em **Recursos Compartilhados**. **Variables**
1. Selecione **Adicionar uma variável**.
1. Na página Nova variável, faça as seguintes configurações nos campos fornecidos.

    * **Nome** - `AzureSubscriptionId`digite .
    * **Valor** - digite seu ID de assinatura. 
    * **Tipo** - manter string selecionada.
    * **Criptografia-** use o valor padrão.
1. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Passo 6 - Adicionar autenticação

Agora que você tem uma variável para segurar o ID de assinatura, você pode configurar o manual para autenticar com as credenciais Run As para sua assinatura. Faça isso adicionando a conexão Azure Run As como um ativo. Você também deve adicionar o [cmdlet Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e o [cmdlet Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) à tela.

>[!NOTE]
>Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Observe que esses pseudônimos não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só `Connect-AzAccount`pode usar a si mesmo.

1. Navegue até o seu runbook e selecione **Editar** na página **MyFirstRunbook-Graphical.**
1. Você não precisa `Write Hello World to output` mais da entrada. Basta clicar na elipse e selecionar **Excluir**.
1. No controle da Biblioteca, expanda **ativos**e, em seguida, **conexões**. Adicione `AzureRunAsConnection` à tela selecionando **Adicionar à tela**.
1. Renomeie `AzureRunAsConnection` para `Get Run As Connection`.
1. No controle da `Connect-AzAccount` Biblioteca, digite no campo de pesquisa.
1. Adicione `Connect-AzAccount` à tela.
1. Paire `Get Run As Connection` sobre até que um círculo apareça na parte inferior da forma. Clique no círculo e `Connect-AzAccount` arraste a seta para formar um link. O runbook `Get Run As Connection` começa com `Connect-AzAccount`e depois é executado.<br> ![Criar link entre as atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, `Connect-AzAccount`selecione . No painel de controle de configuração, digite **login no Azure** no campo **Rótulo.**
1. Clique **em Parâmetros**e a página Configuração do Parâmetro de Atividade será exibida.
1. O `Connect-AzAccount` cmdlet tem vários conjuntos de parâmetros, e você precisa selecionar um antes de fornecer valores de parâmetros. Clique **em Definir parâmetro** e, em seguida, selecione **ServicePrincipalCertificateWithSubscriptionId**.
1. Os parâmetros para este conjunto de parâmetros são exibidos na página Configuração do Parâmetro de Atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros de conta do Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página Valor do parâmetro, faça as seguintes configurações e clique em **OK**.

   * **Origem de dados** - selecione **Saída de atividade**.
   * Lista de origem de dados -- selecione **Obter conexão de automação**.
   * **Caminho de** campo `ApplicationId`- tipo . Você está especificando o nome da propriedade para o caminho de campo porque a atividade produz um objeto com várias propriedades.

1. Clique **em CERTIFICATETHUMBPRINT**e na página Valor do parâmetro, faça as seguintes configurações e clique em **OK**.

    * **Origem de dados** - selecione **Saída de atividade**.
    * Lista de origem de dados -- selecione **Obter conexão de automação**.
    * **Caminho de** campo `CertificateThumbprint`- tipo .
1. Clique **em SERVICEPRINCIPAL**e, na página Valor do parâmetro, selecione **ConstantValue** para o campo **Origem de dados;** clique na opção **True;** e, em seguida, clique **em OK**.
1. Clique **em TENANTID**e faça as seguintes configurações na página Valor do parâmetro. Quando terminar, clique **em OK** duas vezes.

    * **Origem de dados** - selecione **Saída de atividade**. 
    * Lista de origem de dados -- selecione **Obter conexão de automação**.
    * **Caminho de** campo `TenantId`- tipo . 
1. No controle da `Set-AzContext` Biblioteca, digite no campo de pesquisa.
1. Adicione `Set-AzContext` à tela.
1. Selecione `Set-AzContext` na tela. No painel de controle `Specify Subscription Id` de configuração, insira no **campo Rótulo.**
1. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.
1. O `Set-AzContext` cmdlet tem vários conjuntos de parâmetros, e você precisa selecionar um antes de fornecer valores de parâmetros. Clique **em Definir parâmetros** e, em seguida, **selecione SubscriptionId**.
1. Os parâmetros para este conjunto de parâmetros são exibidos na página Configuração do Parâmetro de Atividade. Clique **em SubscriptionID**.
1. Na página Valor do parâmetro, selecione **Ativo Variável** para o campo Origem **de dados** e selecione **AzureSubscriptionId** na lista de origem. Quando terminar, clique **em OK** duas vezes.
1. Paire `Login to Azure` sobre até que um círculo apareça na parte inferior da forma. Clique no círculo e `Specify Subscription Id`arraste a seta para . Seu manual deve parecer o seguinte neste momento.

    ![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Etapa 7: adicionar a atividade para iniciar uma máquina virtual

Agora você deve `Start-AzVM` adicionar uma atividade para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure, e por enquanto você está codificando seu nome no cmdlet [Start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. No controle da `Start-Az` Biblioteca, digite no campo de pesquisa.
2. Adicione `Start-AzVM` à tela e clique e `Specify Subscription Id`arraste-a por baixo .
1. Paire `Specify Subscription Id` sobre até que um círculo apareça na parte inferior da forma. Clique no círculo e `Start-AzVM`arraste a seta para .
1. Selecione `Start-AzVM`. Clique **em Parâmetros** e, em seguida, **parâmetro definido** para exibir os conjuntos para a atividade.
1. Selecione **ResourceGroupNameParameterSetNome** para o conjunto de parâmetros. Os campos **ResourceGroupName** e **Name** têm pontos de exclamação ao lado deles para indicar que são parâmetros necessários. Observe que ambos os campos esperam valores de seqüência.
1. Selecione **Nome**. Escolha **a expressão PowerShell** para o campo **Origem** de dados. Para a VM que você usa para iniciar este runbook, digite o nome da máquina cercado de aspas duplas. Clique em **OK**.
1. Selecione **ResourceGroupName**. Use a **expressão PowerShell** de valor para o campo **Origem de dados** e digite o nome do grupo de recursos cercado de cotações duplas. Clique em **OK**.
1. Clique **em Testar o painel** para que você possa testar o manual.
1. Clique **em Começar** para iniciar o teste. Uma vez concluído, certifique-se de que a VM tenha começado. Seu manual deve parecer o seguinte neste momento.

    ![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passo 8 - Adicionar parâmetros adicionais de entrada

Seu runbook atualmente inicia a VM no grupo `Start-AzVM` de recursos que você especificou para o cmdlet. O runbook será mais útil se você especificar o nome e o grupo de recursos quando o runbook for iniciado. Vamos adicionar parâmetros de entrada ao manual para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook-Graphical**.
1. Selecione **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.
1. Faça as seguintes configurações nos campos fornecidos e clique em **OK**.
   * **Nome** - `VMName`especificar .
   * **Digite** - mantenha a configuração de seqüência.
   * **Obrigatório** - alterar o valor para **Sim**.
1. Crie um segundo parâmetro de `ResourceGroupName` entrada obrigatório chamado e clique em **OK** para fechar o painel Entrada e Saída.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecione `Start-AzVM` a atividade e clique **em Parâmetros**.
1. Alterar o **campo 'Origem de** dados' para **entrada Nome** para **Runbook**. Em seguida, selecione **VMName**.
1. Alterar o campo **origem de dados** para **ResourceGroupName** para **a entrada Runbook** e, em seguida, selecionar **ResourceGroupName**.<br> ![Parâmetros start-azvm](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.
1. Feche o Painel de teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare o VM que você começou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. Digite os `VMName` valores para e `ResourceGroupName` para a VM que você vai começar.
1. Quando o manual estiver concluído, certifique-se de que a VM foi iniciada.

## <a name="step-9---create-a-conditional-link"></a>Etapa 9: criar um link condicional

Agora você pode modificar o manual de execução para que ele só tente iniciar a VM se ela ainda não estiver iniciada. Faça isso adicionando um [cmdlet Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) que recupera o status de nível de ocorrência da VM. Em seguida, você pode adicionar um `Get Status` módulo de código powershell workflow chamado com um trecho do código PowerShell para determinar se o estado de VM está em execução ou parado. Um link condicional `Get Status` do módulo `Start-AzVM` só é executado se o estado de execução atual for interrompido. No final deste procedimento, seu manual `Write-Output` usa o cmdlet para enviar uma mensagem para informá-lo se a VM foi iniciada com sucesso.

1. Abra o **MyFirstRunbook-Graphical** no editor gráfico.
1. Remova o `Specify Subscription Id` link `Start-AzVM` entre e clicando nele e, em seguida, pressionando **Excluir**.
1. No controle da `Get-Az` Biblioteca, digite no campo de pesquisa.
1. Adicione `Get-AzVM` à tela.
1. Selecione `Get-AzVM` e clique **em Parâmetro Definido** para exibir os conjuntos do cmdlet. 
1. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet** . Os campos **ResourceGroupName** e **Name** têm pontos de exclamação ao lado, indicando que eles especificam os parâmetros necessários. Observe que ambos os campos esperam valores de seqüência.
1. Em **'Nome'** **para nome,** selecione **Executar a entrada**e o **VMName**. Clique em **OK**.
1. Em **Fonte de dados** para **ResourceGroupName,** selecione **executar a entrada runbook**e, em seguida, **ResourceGroupName**. Clique em **OK**.
1. Em **Data source** for **Status**, selecione **Valor constante**e **True**. Clique em **OK**.
1. Crie um `Specify Subscription Id` link `Get-AzVM`de .
1. No controle da Biblioteca, expanda o **Controle de Runbook** e adicione **Código** à tela.  
1. Crie um `Get-AzVM` link `Code`de .  
1. Clique `Code` e, no painel Configuração, altere a etiqueta para **Obter status**.
1. Selecione `Code` e a página Do Editor de código saqueie.  
1. Cole o seguinte trecho de código na página do editor.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Crie um `Get Status` link `Start-AzVM`de .

    ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecione o link e, no painel Configuração, **altere A condição de Aplicar** para **Sim**. Observe que o link se torna uma linha tracejada, indicando que a atividade de destino só é executada se a condição se resolver para a verdade.  
1. Para **expressão Condição,** digite `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM`agora só funciona se a VM for parada.
1. No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
1. Adicione `Write-Output` à tela duas vezes.
1. Para o `Write-Output` primeiro controle, clique **em Parâmetros** e altere o valor **do rótulo** para Notificar **o VM Started**.
1. Para **InputObject,** altere a **fonte de dados** para a expressão **PowerShell**e digite a expressão `$VMName successfully started.`.
1. No segundo `Write-Output` controle, clique **em Parâmetros** e altere o valor **do rótulo** para notificar falha de início **de VM**.
1. Para **InputObject,** altere a **fonte de dados** para a expressão **PowerShell**e digite a expressão `$VMName could not start.`.
1. Criar links `Start-AzVM` `Notify VM Started` de `Notify VM Start Failed`para e .
1. Selecione o `Notify VM Started` link e **altere Aplicar condição** para true.
1. Para a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` **Condição,** digite . Este `Write-Output` controle agora só é executado se a VM começar com sucesso.
1. Selecione o `Notify VM Start Failed` link e **altere Aplicar condição** para true.
1. Para o campo de `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`expressão **Condição,** digite . Este `Write-Output` controle agora só é executado se a VM não for iniciada com sucesso. Seu manual deve parecer a seguinte imagem.

    ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Salve o runbook e abra o Painel de teste.
1. Inicie o runbook com o VM parado, e a máquina deve começar.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a autoria gráfica, consulte [a autoria gráfica no Azure Automation](automation-graphical-authoring-intro.md).
* Para começar com os runbooks do PowerShell, consulte [Meu primeiro runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar com os runbooks do PowerShell Workflow, consulte [Meu primeiro runbook de fluxo de trabalho PowerShell](automation-first-runbook-textual.md).