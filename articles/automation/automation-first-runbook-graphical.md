---
title: Meu primeiro runbook gráfico na Automação do Azure
description: Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples.
keywords: runbook, modelo de runbook, automação de runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: b6b043f878ee93bc9713673fe66359e013b1dba6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191207"
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na automação do Azure. Comece com um runbook simples que testa e publica, enquanto aprende como acompanhar o status do trabalho de runbook. Em seguida, modifique o runbook para realmente gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Conclua o tutorial para tornar o runbook mais robusto adicionando parâmetros de runbook e links condicionais.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Como você interrompe e inicia esse computador, ele não deve ser uma VM de produção.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um runbook simples que produza o texto "Olá, Mundo".

1. No portal do Azure, abra sua conta da Automação. 

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos são os módulos incluídos automaticamente em uma nova conta de automação. Você também deve ter o ativo de credencial associado à sua assinatura.
2. Selecione **Runbooks** em **automação de processo** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **criar um runbook**.
4. Atribua o nome **MyFirstRunbook-Graphical**ao runbook.
5. Nesse caso, você vai criar um [runbook gráfico](automation-graphical-authoring-intro.md). Selecione **gráfico** para o **tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Etapa 2 – adicionar atividades

O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook. Você vai adicionar um cmdlet **Write-Output** para exibir nosso o texto a partir do runbook.

1. No controle biblioteca, clique no campo de pesquisa e digite **Write-Output**. Os resultados da pesquisa são mostrados na imagem a seguir. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Role até o final da lista. Clique com o botão direito do mouse em **Write-Output** e selecione **Adicionar à tela**. Como alternativa, você pode clicar nas reticências (...) ao lado do nome do cmdlet e, em seguida, selecionar **Adicionar à tela**.
1. Clique na atividade **Write-Output** na tela. Esta ação abrirá a página Controle da configuração, que permite configurar a atividade.
1. O campo de **rótulo** usa como padrão o nome do cmdlet, mas você pode alterá-lo para algo mais amigável. Altere-o para **Write Hello World to output**.
1. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar qual deles usar. Nesse caso, **Write-Output** tem apenas um conjunto de parâmetros.

1. Selecione o parâmetro *InputObject* . Esse é o parâmetro que você usa para especificar o texto a ser enviado ao fluxo de saída.
1. O menu suspenso **fonte de dados** fornece fontes que você pode usar para preencher um valor de parâmetro. Nesse menu, selecione **expressão do PowerShell**. 

   Você pode usar a saída de tais fontes como outra atividade, um ativo de automação ou uma expressão do PowerShell. Neste caso, a saída é apenas **Olá, Mundo**. Você pode usar uma expressão do PowerShell e especificar uma cadeia de caracteres.<br>

1. No campo **expressão** , digite **Olá, mundo** e clique em **OK** duas vezes para retornar à tela.
1. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para certificar-se de que ele funciona corretamente. O teste de um runbook executa sua versão de rascunho e permite que você exiba sua saída interativamente.

1. Selecione o **painel de teste** para abrir o painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
1. Observe que um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do trabalho é iniciado como **enfileirado**, indicando que o trabalho está aguardando que um runbook Worker na nuvem fique disponível. O status muda para **iniciando** quando um trabalho alega o trabalho. Por fim, o status se torna **em execução** quando o runbook realmente começa a ser executado.

1. Quando o trabalho de runbook for concluído, a página de teste exibirá sua saída. Neste caso, você vê **Olá, Mundo**.<br> ![Olá mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está no modo de rascunho. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Selecione **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Role para a esquerda para exibir o runbook na página Runbooks e observe que o valor de **status de criação** é definido como **publicado**.
1. Role de volta para a direita para visualizar a página para **MyFirstRunbook-Graphical**.

   As opções na parte superior permitem que você inicie o runbook agora, agende uma hora de início futura ou crie um [webhook](automation-webhooks.md) para que o runbook possa ser iniciado por meio de uma chamada http.

1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.
1. Um painel de trabalho é aberto para o trabalho de runbook que foi criado. Verifique se o campo **status do trabalho** mostra **concluído**.
1. Clique em **saída** para abrir a página saída, onde você pode ver **Olá, mundo** exibidos.
1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver **Olá, mundo** no fluxo de saída. 

    Observe que o painel fluxos pode mostrar outros fluxos para um trabalho de runbook, como os fluxos de erro e detalhados, se o runbook gravar neles.
1. Feche o painel fluxos e o painel trabalho para retornar à página **MyFirstRunbook-gráfico** .
1. Para exibir todos os trabalhos do runbook, selecione **trabalhos** em **recursos**. A página trabalhos lista todos os trabalhos criados pelo seu runbook. Você deve ver apenas um trabalho listado, pois você só executou o trabalho uma vez.
1. Clique no nome do trabalho para abrir o mesmo painel de trabalho exibido quando você iniciou o runbook. Use este painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---create-variable-assets"></a>Etapa 5: criar ativos de variável

Você testou e publicou seu runbook, mas até o momento ele não faz nada útil para gerenciar os recursos do Azure. Antes de configurar o runbook para autenticar, você deve criar uma variável para conter a ID da assinatura, configurar uma atividade para autenticar e, em seguida, fazer referência à variável. Incluir uma referência ao contexto de assinatura permite que você trabalhe facilmente com várias assinaturas.

1. Copie sua ID da assinatura da opção **assinaturas** no painel de navegação.
1. Na página contas de automação, selecione **variáveis** em **recursos compartilhados**.
1. Selecione **Adicionar uma variável**.
1. Na página nova variável, faça as seguintes configurações nos campos fornecidos.

    * **Nome** --insira **AzureSubscriptionId**.
    * **Valor** -Insira sua ID de assinatura. 
    * **Tipo** --manter cadeia de caracteres selecionada.
    * **Criptografia** --use o valor padrão.
1. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Etapa 6 – adicionar autenticação

Agora que você tem uma variável para manter a ID da assinatura, você pode configurar o runbook para autenticar com as credenciais executar como para sua assinatura. Faça isso adicionando a conexão executar como do Azure como um ativo. Você também deve adicionar o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e o cmdlet [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) à tela.

>[!NOTE]
>Para runbooks do PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são aliases para **Connect-AzAccount**. Observe que esses aliases não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só pode usar o **Connect-AzAccount** em si.

1. Navegue até o runbook e selecione **Editar** na página **MyFirstRunbook-gráfico** .
1. Você não precisa do **Olá, mundo de gravação para gerar** mais entrada. Basta clicar nas reticências e selecionar **excluir**.
1. No controle biblioteca, expanda **ativos**e, em seguida, **conexões**. Adicione **AzureRunAsConnection** à tela selecionando **Adicionar à tela**.
1. No controle biblioteca, digite **Connect-AzAccount** no campo de pesquisa.
1. Adicione **Connect-AzAccount** à tela.
1. Passe o mouse sobre **Obter Conexão Executar Como** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Connect-AzAccount** para formar um link. O runbook começa com **Get execute as Connection** e, em seguida, executa **Connect-AzAccount**.<br> ![Criar link entre as atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, selecione **Connect-AzAccount**. No painel de controle configuração, digite **logon no Azure** no campo **rótulo** .
1. Clique em **parâmetros**e a página configuração do parâmetro de atividade será exibida.
1. O cmdlet **Connect-AzAccount** tem vários conjuntos de parâmetros e você precisa selecionar um antes de fornecer valores de parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
1. Os parâmetros para esse conjunto de parâmetros são exibidos na página configuração do parâmetro de atividade. Clique em **APPLICATIONID**.<br> ![adicionar parâmetros de conta do Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página valor do parâmetro, faça as seguintes configurações e clique em **OK**.

   * **Fonte de dados** --selecione a **saída da atividade**.
   * Lista de fontes de dados--selecione **obter conexão executar como**.
   * **Caminho do campo** --tipo **ApplicationId**. Você está especificando o nome da propriedade para o caminho do campo porque a atividade produz um objeto com várias propriedades.
1. Clique em **CERTIFICATETHUMBPRINT**e, na página valor do parâmetro, faça as seguintes configurações e clique em **OK**.

    * **Fonte de dados** --selecione a **saída da atividade**.
    * Lista de fontes de dados--selecione **obter conexão executar como**.
    * **Caminho do campo** --tipo **CertificateThumbprint**.
1. Clique em **servicePrincipalName**e, na página valor do parâmetro, selecione **constante** para o campo **fonte de dados** ; Clique na opção **true**; e clique em **OK**.
1. Clique em **tenantid**e faça as seguintes configurações na página valor do parâmetro. Quando terminar, clique em **OK** duas vezes.

    * **Fonte de dados** --selecione a **saída da atividade**. 
    * Lista de fontes de dados--selecione **obter conexão executar como**.
    * **Caminho do campo** --digite **tenantid**. 
1. No controle biblioteca, digite **set-AzContext** no campo de pesquisa.
1. Adicione **set-AzContext** à tela.
1. Selecione **set-AzContext** na tela. No painel de controle configuração, insira **especificar ID da assinatura** no campo **rótulo** .
1. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.
1. O cmdlet **set-AzContext** tem vários conjuntos de parâmetros e você precisa selecionar um antes de fornecer valores de parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **SubscriptionId**.
1. Os parâmetros para esse conjunto de parâmetros são exibidos na página configuração do parâmetro de atividade. Clique em **SubscriptionId**.
1. Na página valor do parâmetro, selecione **ativo variável** para o campo **fonte de dados** e selecione **AzureSubscriptionId** na lista origem. Quando terminar, clique em **OK** duas vezes.
1. Passe o mouse sobre **Login no Azure** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Assinatura**. 

Seu runbook deve ser semelhante ao seguinte: <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Etapa 7: adicionar a atividade para iniciar uma máquina virtual

Agora você deve adicionar uma atividade **Start-AzVM** para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure e, por enquanto, está codificando seu nome no cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. No controle biblioteca, digite **Start-AZ** no campo de pesquisa.
2. Adicione **Start-AzVM** à tela e, em seguida, clique e arraste-o para baixo de **especificar ID da assinatura**.
1. Passe o mouse sobre **Especificar Id de Assinatura** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzVM**.
1. Selecione **Start-AzVM**. Clique em **parâmetros** e em **conjunto de parâmetros** para exibir os conjuntos da atividade.
1. Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName** . Os campos **ResourceGroupName** e **Name** têm pontos de exclamação ao lado para indicar que são parâmetros obrigatórios. Observe que os dois campos esperam valores de cadeia de caracteres.
1. Selecione **Name**. Escolha **expressão do PowerShell** para o campo **fonte de dados** . Para a VM que você usa para iniciar este runbook, digite o nome do computador entre aspas duplas. Clique em **OK**.
1. Selecione **ResourceGroupName**. Use o valor **expressão do PowerShell** para o campo **fonte de dados** e digite o nome do grupo de recursos entre aspas duplas. Clique em **OK**.
1. Clique em **Painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Após a conclusão, verifique se a VM foi iniciada. 

Seu runbook deve ser semelhante ao seguinte: <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Etapa 8 – adicionar parâmetros de entrada adicionais

O runbook inicia atualmente a VM no grupo de recursos que você especificou para o cmdlet **Start-AzVM** . O runbook será mais útil se você especificar o nome e o grupo de recursos quando o runbook for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook-Graphical**.
1. Selecione **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.
1. Faça as seguintes configurações nos campos fornecidos e clique em **OK**.
   * **Nome** – especifique **VMName**.
   * **Tipo** – mantenha a configuração da cadeia de caracteres.
   * **Obrigatório** --altere o valor para **Sim**.
1. Crie um segundo parâmetro de entrada obrigatório chamado *ResourceGroupName* e clique em **OK** para fechar o painel de entrada e saída.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecione a atividade **Start-AzVM** e clique em **parâmetros**.
1. Altere o campo de **fonte de dados** para **nome** para **entrada de runbook**. Em seguida, selecione **VMName**.
1. Altere o campo **fonte de dados** de **ResourceGroupName** para **entrada de runbook** e, em seguida, selecione **ResourceGroupName**.<br> ![parâmetros Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.
1. Feche o Painel de teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a VM que você iniciou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. Digite os valores para **VMName** e **RESOURCEGROUPNAME** para a VM que você vai iniciar.
1. Quando o runbook for concluído, verifique se a VM foi iniciada.

## <a name="step-9---create-a-conditional-link"></a>Etapa 9: criar um link condicional

Agora você pode modificar o runbook para que ele só tente iniciar a VM se ela ainda não tiver sido iniciada. Faça isso adicionando um cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) que recupera o status em nível de instância da VM. Em seguida, você pode adicionar um módulo de código do fluxo de trabalho do PowerShell chamado **Get status** com um trecho de código do PowerShell para determinar se o estado da VM está em execução ou parado. Um link condicional do módulo **obter status** só executa **Start-AzVM** se o estado de execução atual for parado. No final deste procedimento, seu runbook usa o cmdlet **Write-Output** para gerar uma mensagem para informá-lo se a VM foi iniciada com êxito.

1. Abra o **MyFirstRunbook-Graphical** no editor gráfico.
1. Remova o link entre **especificar ID da assinatura** e **Start-AzVM** clicando nele e, em seguida, pressionando **delete**.
1. No controle biblioteca, digite **Get-AZ** no campo de pesquisa.
1. Adicione **Get-AzVM** à tela.
1. Selecione **Get-AzVM** e, em seguida, **conjunto de parâmetros** para exibir os conjuntos para o cmdlet. 
1. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet** . Os campos **ResourceGroupName** e **Name** têm pontos de exclamação ao lado deles, indicando que eles especificam os parâmetros necessários. Observe que os dois campos esperam valores de cadeia de caracteres.
1. Em **fonte de dados** para **nome**, selecione **entrada de runbook**e, em seguida, **VMName**. Clique em **OK**.
1. Em **fonte de dados** para **ResourceGroupName**, selecione **entrada de runbook**e, em seguida, **ResourceGroupName**. Clique em **OK**.
1. Em **fonte de dados** para **status**, selecione **valor constante**e, em seguida, **verdadeiro**. Clique em **OK**.
1. Crie um link de **especificar ID de assinatura** para **Get-AzVM**.
1. No controle de biblioteca, expanda **controle de runbook** e adicione **código** à tela.  
1. Crie um link de **Get-AzVM** para **código**.  
1. Clique em **código** e, no painel configuração, altere o rótulo para **obter o status**.
1. Selecione **código** e a página Editor de código será exibida.  
1. Cole o trecho de código a seguir na página do editor.

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

1. Crie um link de **obter status** para **Start-AzVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecione o link e, no painel configuração, altere **aplicar condição** para **Sim**. Observe que o link se torna uma linha tracejada, indicando que a atividade de destino só será executada se a condição for resolvida como true.  
1. Para **expressão de condição**, digite `$ActivityOutput['Get Status'] -eq "Stopped"`. **Start-AzVM** agora só será executado se a VM for interrompida.
1. No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
1. Adicione **Write-Output** à tela duas vezes.
1. Para o primeiro controle **Write-Output** , clique em **parâmetros** e altere o valor do **rótulo** para **notificar a VM iniciada**.
1. Para **InputObject**, altere a **fonte de dados** para expressão do **PowerShell**e digite a expressão **$VMName iniciada com êxito.** .
1. No segundo controle **Write-Output** , clique em **parâmetros** e altere o valor do **rótulo** para **notificar a inicialização da VM**.
1. Para **InputObject**, altere a **fonte de dados** para expressão do **PowerShell**e digite a expressão **$VMName não pôde ser iniciada.** .
1. Crie links de **Start-AzVM** para **notificar a VM iniciada** e **notificar a inicialização da VM com falha**.
1. Selecione o link para **notificar a VM iniciada** e altere **aplicar condição** para verdadeiro.
1. Para a **expressão de condição**, digite `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Esse controle **Write-Output** agora só será executado se a VM for iniciada com êxito.
1. Selecione o link para **notificar a inicialização da VM falhou** e altere **aplicar condição** para verdadeiro.
1. Para o campo **expressão de condição** , digite `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Esse controle **Write-Output** agora só será executado se a VM não for iniciada com êxito. Seu runbook deve ser semelhante à seguinte imagem: <br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Salve o runbook e abra o Painel de teste.
1. Inicie o runbook com a VM interrompida e o computador deve iniciar.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação gráfica, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).