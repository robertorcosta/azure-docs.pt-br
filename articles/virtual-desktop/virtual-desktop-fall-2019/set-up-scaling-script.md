---
title: Dimensionar hosts da sessão com a Automação do Azure – Azure
description: Como dimensionar automaticamente os hosts da sessão da Área de Trabalho Virtual do Windows com a Automação do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745707"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Dimensionar hosts da sessão usando a Automação do Azure

>[!IMPORTANT]
>Esse conteúdo se aplica à versão Outono 2019 que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

É possível reduzir o custo total de implantação da Área de Trabalho Virtual do Windows dimensionando suas VMs (máquinas virtuais). Isso significa desligar e desalocar VMs de host da sessão fora do horário de pico de uso, voltar a ligá-las e realocá-las durante os horários de pico.

Neste artigo, você conhecerá a ferramenta de dimensionamento criada com os Aplicativos Lógicos do Azure e a Automação do Azure que dimensionarão automaticamente as máquinas virtuais dos hosts da sessão no seu ambiente da Área de Trabalho Virtual do Windows. Para ver como usar a ferramenta de dimensionamento, pule para [Pré-requisitos](#prerequisites).

## <a name="report-issues"></a>Relatar problemas

No momento, os relatórios de problemas da ferramenta de dimensionamento estão sendo tratados no GitHub, e não no Suporte da Microsoft. Se encontrar algum problema com a ferramenta de dimensionamento, comunique-o abrindo um tíquete no GitHub rotulado "4a-WVD-scaling-logicapps" na [página da RDS do GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de dimensionamento

A ferramenta de dimensionamento fornece uma opção de automação de baixo custo para os clientes que desejam otimizar os custos de VM dos hosts da sessão.

A ferramenta de dimensionamento pode ser usada para:
 
- Agendar o início e o término das VMs com base nos horários comerciais de pico e fora de pico.
- Distribuir as VMs com base no número de sessões por núcleo de CPU.
- Dimensionar as VMs fora dos horários de pico, deixando o mínimo de VMs de host da sessão em execução.

Para funcionar, a ferramenta de dimensionamento usa uma combinação de runbooks do PowerShell de Automação do Azure, webhooks e Aplicativos Lógicos do Azure. Quando a ferramenta é executada, os Aplicativos Lógicos do Azure chamam um webhook para iniciar o runbook de Automação do Azure. O runbook então cria um trabalho.

Durante o período de uso de pico, o trabalho verifica o número atual de sessões e a capacidade da VM do host da sessão em execução atual para cada pool de hosts. Ele usa essas informações para calcular se as VMs do host da sessão em execução dão conta das sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o arquivo **createazurelogicapp.ps1**. Se as VMs de host da sessão não derem conta das sessões existentes, o trabalho iniciará VMs de host da sessão adicionais no pool de hosts.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões na VM. Esse parâmetro determina apenas quando novas VMs precisam ser iniciadas para equilibrar a carga das conexões. Para restringir o número de sessões, siga as instruções [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar adequadamente o parâmetro *MaxSessionLimit*.

Durante o período de uso fora de pico, o trabalho determina quais VMs de host da sessão devem ser desligadas com base no parâmetro *MinimumNumberOfRDSH*. O trabalho definirá as VMs de host da sessão para o modo esvaziar, a fim de evitar que novas sessões se conectem aos hosts. Se o parâmetro *LimitSecondsToForceLogOffUser* for definido com um valor positivo diferente de zero, o trabalho enviará uma notificação para que todos os usuários conectados no momento salvem o que estiverem fazendo, esperará o período configurado e, em seguida, forçará os usuários a se desconectare. Quando todas as sessões de usuário na VM de host da sessão tiverem sido desconectadas, o trabalho desligará a VM.

Se o parâmetro *LimitSecondsToForceLogOffUser* for definido como zero, o trabalho permitirá que a definição de configuração de sessão nas políticas de grupo especificadas manipule a desconexão das sessões de usuário. Para ver essas políticas de grupo, vá para **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Serviços do Terminal** > **Servidor do Terminal** > **Limites do Tempo de Sessão**. Se houver sessões ativas em uma VM de host da sessão, o trabalho deixará a VM de host da sessão em execução. Se não houver sessões ativas, o trabalho desligará a VM do host da sessão.

O trabalho é executado periodicamente com base em um intervalo de recorrência definido. Esse intervalo pode ser alterado com base no tamanho do ambiente da Área de Trabalho Virtual do Windows, mas lembre-se de que iniciar e desligar máquinas virtuais pode levar tempo; portanto, leve em conta esse atraso. É recomendável definir o intervalo de recorrência para ocorrer a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Essa solução se aplica somente a VMs de host da sessão em pool.
- Essa solução gerencia VMs em qualquer região, mas só pode ser usada na mesma assinatura que sua conta da Automação do Azure e dos Aplicativos Lógicos do Azure.

>[!NOTE]
>A ferramenta de dimensionamento controla o modo de balanceamento de carga do pool de hosts que está dimensionando. Ela o define para o balanceamento de carga em largura para os horários de pico e fora de pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de dimensionamento, verifique se estas opções estão prontas:

- Um [locatário e pool de host da Área de Trabalho Virtual do Windows](create-host-pools-arm-template.md)
- VMs de pool de hosts da sessão configuradas e registradas no serviço da Área de Trabalho Virtual do Windows
- Um usuário com [acesso de Colaborador](../../role-based-access-control/role-assignments-portal.md) na assinatura do Azure

O computador que você usar para implantar a ferramenta deverá ter: 

- O Windows PowerShell 5.1 ou posterior
- O módulo do PowerShell Microsoft AZ

Se estiver tudo pronto, vamos começar.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Primeiro, será preciso uma conta de Automação do Azure para executar o runbook do PowerShell. Veja como configurar sua conta:

1. Abra o Windows PowerShell como administrador.
2. Execute o seguinte cmdlet para entrar em sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Sua conta deve ter direitos de colaborador na assinatura do Azure em que você deseja implantar a ferramenta de dimensionamento.

3. Execute o seguinte cmdlet para baixar o script e criar a conta de Automação do Azure:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Execute o seguinte cmdlet para executar o script e criar a conta de Automação do Azure:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A saída do cmdlet incluirá um URI de webhook. Certifique-se de registrar o URI porque você o usará como parâmetro ao configurar o agendamento de execução para os Aplicativos Lógicos do Azure.

6. Depois de configurar sua conta de Automação do Azure, entre na sua assinatura do Azure e verifique se sua conta de Automação do Azure e o respectivo runbook apareceram no grupo de recursos especificado, conforme mostrado na seguinte imagem:

![Uma imagem da página de visão geral do Azure mostrando a conta de automação e o runbook recém-criados.](../media/automation-account.png)

  Para verificar se o webhook está no local certo, selecione o nome dele. Em seguida, vá para a seção Recursos do seu runbook e selecione **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma conta Executar como da Automação do Azure

Agora que você tem uma conta de Automação do Azure, também precisará criar uma conta Executar como da Automação do Azure para acessar os recursos do Azure.

Uma [conta Executar como da Automação do Azure](../../automation/manage-runas-account.md) fornece autenticação para o gerenciamento de recursos no Azure com os cmdlets do Azure. Quando você cria uma conta Executar como, ela cria um usuário de entidade de serviço no Azure Active Directory e atribui a função de Colaborador ao usuário da entidade de serviço no nível da assinatura. A conta Executar como do Azure é uma ótima maneira de fazer a autenticação com segurança usando certificados e um nome da entidade de serviço sem a necessidade de armazenar um nome de usuário e uma senha em um objeto de credencial. Saiba mais sobre a autenticação Executar como em [Permissões para limitar uma conta Executar como](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Todo usuário que é membro da função Administradores de Assinatura e coadministrador da assinatura pode criar uma conta Executar como seguindo as instruções da próxima seção.

Para criar uma conta Executar como na conta do Azure:

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, insira e selecione as **Contas de Automação**.

2. Na página de **Contas de Automação**, selecione o nome da Conta de Automação.

3. No painel do lado esquerdo da janela, selecione **Contas Executar como**, na seção Configurações de Conta.

4. Selecione **Conta Executar como do Azure**. Quando surgir o painel **Adicionar Conta Executar como do Azure**, examine as informações de visão geral e selecione **Criar** para iniciar o processo de criação da conta.

5. Aguarde alguns minutos para que o Azure crie a conta Executar como. O progresso da criação pode ser acompanhado no menu Notificações.

6. Ao término do processo, será criado um ativo chamado AzureRunAsConnection na conta de Automação especificada. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado. Lembre-se da ID do Aplicativo, pois você a usará mais tarde.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de função na Área de Trabalho Virtual do Windows

Em seguida, é preciso criar uma atribuição de função para que o AzureRunAsConnection possa interagir com a Área de Trabalho Virtual do Windows. Use o PowerShell para entrar com uma conta que tenha permissões para criar atribuições de função.

Primeiro, baixe e importe o [módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando encontrar o locatário com os pools de hosts que deseja dimensionar, siga as instruções em [Criar uma conta de Automação do Azure](#create-an-azure-automation-account) e use o nome do locatário obtido com o cmdlet anterior no cmdlet a seguir para criar a atribuição de função:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Criar o Aplicativo Lógico do Azure e o agendamento de execução

Por fim, será preciso criar o Aplicativo Lógico do Azure e configurar um agendamento de execução para sua nova ferramenta de dimensionamento.

1.  Abra o Windows PowerShell como Administrador

2.  Execute o seguinte cmdlet para entrar em sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

3. Execute o cmdlet a seguir para baixar o arquivo de script createazurelogicapp.ps1 no computador local.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Execute o cmdlet a seguir para entrar na Área de Trabalho Virtual do Windows com uma conta que tenha permissões de Proprietário da RDS ou de Colaborador da RDS.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Execute o seguinte script do PowerShell para criar o Aplicativo Lógico do Azure e o agendamento de execução.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Depois de executar o script, o aplicativo lógico deve aparecer em um grupo de recursos, conforme mostrado na imagem a seguir.

     ![Uma imagem da página de visão geral de um Aplicativo Lógico do Azure de exemplo.](../media/logic-app.png)

Para fazer alterações no agendamento de execução, como alterar o intervalo de recorrência ou o fuso horário, acesse o Agendador de dimensionamento automático e selecione **Editar** para ir para o Designer de Aplicativos Lógicos.

![Uma imagem do Designer de Aplicativos Lógicos. Os menus de Recorrência e Webhook que permitem ao usuário editar os horários de recorrência e o arquivo de webhook estão abertos.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerenciar a ferramenta de dimensionamento

Agora que você criou a ferramenta de dimensionamento, pode acessar a saída dela. Esta seção descreve alguns recursos que podem ser úteis.

### <a name="view-job-status"></a>Exibir status do trabalho

No portal do Azure, é possível exibir um status resumido de todos os trabalhos do runbook ou exibir um status mais detalhado de um trabalho específico do runbook.

À direita da conta de Automação selecionada, em "Estatísticas de Trabalho", é possível exibir uma lista de resumos de todos os trabalhos de runbook. Abrir a página **Trabalhos** no lado esquerdo da janela mostrará os status do trabalho atual, os horários de início e de conclusão.

![Uma captura de tela da página de status de trabalho.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Exibir logs e saída da ferramenta de dimensionamento

Exiba os logs das operações de expansão e redução abrindo o runbook e selecionando o nome do trabalho.

Navegue até o runbook (o nome padrão é WVDAutoScaleRunbook) no grupo de recursos que hospeda a conta de Automação do Azure e selecione **Visão geral**. Na página Visão geral, selecione um trabalho em Trabalhos recentes para exibir a saída da ferramenta de dimensionamento, conforme mostrado na imagem a seguir.

![Uma imagem da janela de saída da ferramenta de dimensionamento.](../media/tool-output.png)

