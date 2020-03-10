---
title: Sessão de escala hospeda a automação do Azure – Azure
description: Como dimensionar automaticamente hosts de sessão de área de trabalho virtual do Windows com a automação do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: helohr
ms.openlocfilehash: f88d8681bbb1cfc9482e84c467bbd514aed41764
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945246"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Dimensionar hosts de sessão usando a automação do Azure

Você pode reduzir o custo total de implantação de área de trabalho virtual do Windows dimensionando suas VMs (máquinas virtuais). Isso significa desligar e desalocar VMs de host de sessão fora do horário de pico de uso, ligá-las novamente e realocá-las durante horários de pico.

Neste artigo, você aprenderá sobre a ferramenta de dimensionamento criada com a automação do Azure e com o aplicativo lógico do Azure que dimensionará automaticamente as máquinas virtuais do host de sessão no seu ambiente de área de trabalho virtual do Windows. Para saber como usar a ferramenta de dimensionamento, pule para os [pré-requisitos](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de dimensionamento

A ferramenta de dimensionamento fornece uma opção de automação de baixo custo para clientes que desejam otimizar seus custos de VM host de sessão.

Você pode usar a ferramenta de dimensionamento para:
 
- Agende as VMs para iniciar e parar com base no horário comercial de pico e fora do pico.
- Expanda as VMs com base no número de sessões por núcleo de CPU.
- Dimensione em VMs fora do horário de pico, deixando o número mínimo de VMs de host de sessão em execução.

A ferramenta de dimensionamento usa uma combinação de runbooks do PowerShell de automação do Azure, WebHooks e aplicativos lógicos do Azure para funcionar. Quando a ferramenta é executada, o aplicativo lógico do Azure chama um webhook para iniciar o runbook de automação do Azure. Em seguida, o runbook cria um trabalho.

Durante o tempo de uso de pico, o trabalho verifica o número atual de sessões e a capacidade da VM do host de sessão em execução atual para cada pool de hosts. Ele usa essas informações para calcular se as VMs do host da sessão em execução podem dar suporte a sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o arquivo **createazurelogicapp. ps1** . Se as VMs de host de sessão não conseguirem dar suporte a sessões existentes, o trabalho iniciará VMs de host de sessão adicionais no pool de hosts.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões na VM. Esse parâmetro determina apenas quando novas VMs precisam ser iniciadas para balancear a carga das conexões. Para restringir o número de sessões, você precisa seguir as instruções [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar o parâmetro *MaxSessionLimit* de acordo.

Durante o tempo de uso fora de pico, o trabalho determina quais VMs de host de sessão devem ser desligadas com base no parâmetro *MinimumNumberOfRDSH* . O trabalho definirá as VMs do host da sessão para o modo de descarga para evitar novas sessões se conectando aos hosts. Se você definir o parâmetro *LimitSecondsToForceLogOffUser* para um valor positivo diferente de zero, o trabalho notificará qualquer usuário conectado no momento para salvar seu trabalho, aguardará a quantidade de tempo configurada e, em seguida, forçará os usuários a se desconectarem. Depois que todas as sessões de usuário na VM host de sessão forem desconectadas, o trabalho desligará a VM.

Se você definir o parâmetro *LimitSecondsToForceLogOffUser* como zero, o trabalho permitirá que a definição de configuração de sessão em políticas de grupo especificadas manipule a assinatura de sessões de usuário. Para ver essas políticas de grupo, vá para **configuração do computador** > **políticas** > **Modelos Administrativos** > **componentes do Windows** > serviços de **terminal** ** > Terminal Server > ** limites de **tempo de sessão**. Se houver sessões ativas em uma VM host de sessão, o trabalho deixará a VM host de sessão em execução. Se não houver nenhuma sessão ativa, o trabalho desligará a VM do host da sessão.

O trabalho é executado periodicamente com base em um intervalo de recorrência definido. Você pode alterar esse intervalo com base no tamanho do seu ambiente de área de trabalho virtual do Windows, mas lembre-se de que iniciar e desligar máquinas virtuais pode levar algum tempo, portanto, lembre-se de considerar o atraso. É recomendável definir o intervalo de recorrência para a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Essa solução se aplica somente a VMs de host de sessão em pool.
- Essa solução gerencia VMs em qualquer região, mas só pode ser usada na mesma assinatura que a sua conta de automação do Azure e aplicativos lógicos do Azure.

>[!NOTE]
>A ferramenta de dimensionamento controla o modo de balanceamento de carga do pool de hosts que está dimensionando. Ele o define para o balanceamento de carga de primeira amplitude para os horários de pico e fora do horário de pico.

## <a name="prerequisites"></a>Prerequisites

Antes de começar a configurar a ferramenta de dimensionamento, verifique se você tem as seguintes opções prontas:

- Um [locatário de área de trabalho virtual do Windows e um pool de hosts](create-host-pools-arm-template.md)
- VMs do pool de hosts de sessão configuradas e registradas no serviço de área de trabalho virtual
- Um usuário com [acesso de colaborador](../role-based-access-control/role-assignments-portal.md) na assinatura do Azure

O computador que você usa para implantar a ferramenta deve ter: 

- Windows PowerShell 5,1 ou posterior
- O módulo Microsoft AZ PowerShell

Se você tiver tudo pronto, vamos começar.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Primeiro, você precisará de uma conta de automação do Azure para executar o runbook do PowerShell. Veja como configurar sua conta:

1. Abra o Windows PowerShell como administrador.
2. Execute o cmdlet a seguir para entrar em sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Sua conta deve ter direitos de colaborador na assinatura do Azure na qual você deseja implantar a ferramenta de dimensionamento.

3. Execute o seguinte cmdlet para baixar o script para criar a conta de automação do Azure:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Execute o seguinte cmdlet para executar o script e criar a conta de automação do Azure:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A saída do cmdlet incluirá um URI de webhook. Certifique-se de manter um registro do URI porque você o usará como um parâmetro ao configurar o agendamento de execução para os aplicativos lógicos do Azure.

Depois de configurar sua conta de automação do Azure, entre na sua assinatura do Azure e verifique se sua conta de automação do Azure e o runbook relevante foram exibidos no grupo de recursos especificado, conforme mostrado na imagem a seguir:

![Uma imagem da página de visão geral do Azure mostrando a conta de automação e o runbook recém-criados.](media/automation-account.png)

Para verificar se o seu webhook é onde deveria estar, vá para a lista de recursos no lado esquerdo da tela e selecione **webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma conta Executar como da automação do Azure

Agora que você tem uma conta de automação do Azure, também precisará criar uma conta Executar como da automação do Azure para acessar os recursos do Azure.

Uma [conta Executar como da automação do Azure](../automation/manage-runas-account.md) fornece autenticação para gerenciar recursos no Azure com os cmdlets do Azure. Quando você cria uma conta Executar como, ela cria um novo usuário de entidade de serviço no Azure Active Directory e atribui a função colaborador ao usuário da entidade de serviço no nível da assinatura, a conta Executar como do Azure é uma ótima maneira de autenticar com segurança com certificados e um nome da entidade de serviço sem a necessidade de armazenar um nome de usuário e senha em um objeto de credencial. Para saber mais sobre a autenticação executar como, consulte [limitando as permissões da conta Executar como](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Qualquer usuário que seja membro da função Administradores de assinatura e coadministrador da assinatura pode criar uma conta Executar como seguindo as instruções da próxima seção.

Para criar uma conta Executar como em sua conta do Azure:

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, insira e selecione **contas de automação**.

2. Na página **contas de automação** , selecione o nome da sua conta de automação.

3. No painel no lado esquerdo da janela, selecione **contas Executar como** na seção Configurações de conta.

4. Selecione **conta Executar como do Azure**. Quando o painel **adicionar conta Executar como do Azure** for exibido, examine as informações de visão geral e, em seguida, selecione **criar** para iniciar o processo de criação de conta.

5. Aguarde alguns minutos para que o Azure crie a conta Executar como. Você pode acompanhar o progresso da criação no menu em notificações.

6. Quando o processo for concluído, ele criará um ativo chamado AzureRunAsConnection na conta de automação especificada. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado. Lembre-se da ID do aplicativo, pois você a usará mais tarde.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de função na Área de Trabalho Virtual do Windows

Em seguida, você precisa criar uma atribuição de função para que o AzureRunAsConnection possa interagir com a área de trabalho virtual do Windows. Certifique-se de usar o PowerShell para entrar com uma conta que tenha permissões para criar atribuições de função.

Primeiro, baixe e importe o [módulo do PowerShell de área de trabalho virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando você encontrar o locatário com os pools de hosts que deseja dimensionar, siga as instruções em [criar uma conta de automação do Azure](#create-an-azure-automation-account) e use o nome do locatário obtido do cmdlet anterior no seguinte cmdlet para criar a atribuição de função:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Criar o aplicativo lógico do Azure e o agendamento de execução

Por fim, você precisará criar o aplicativo lógico do Azure e configurar um agendamento de execução para sua nova ferramenta de dimensionamento.

1.  Abrir o Windows PowerShell como administrador

2.  Execute o cmdlet a seguir para entrar em sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

3. Execute o cmdlet a seguir para baixar o arquivo de script createazurelogicapp. ps1 no computador local.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Execute o cmdlet a seguir para entrar na área de trabalho virtual do Windows com uma conta que tenha permissões de proprietário RDS ou de colaborador do RDS.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Execute o seguinte script do PowerShell para criar o aplicativo lógico do Azure e o agendamento de execução.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you’d like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you’d like the job to run in minutes, e.g. ‘15’"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don’t want to be managed by this scaling tool"

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

     ![Uma imagem da página de visão geral de um aplicativo lógico do Azure de exemplo.](media/logic-app.png)

Para fazer alterações no agendamento de execução, como alterar o intervalo de recorrência ou o fuso horário, vá para o Agendador de dimensionamento automático e selecione **Editar** para ir para o designer de aplicativos lógicos.

![Uma imagem do designer de aplicativos lógicos. Os menus de recorrência e webhook que permitem ao usuário editar os horários de recorrência e o arquivo de webhook estão abertos.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerenciar sua ferramenta de dimensionamento

Agora que você criou sua ferramenta de dimensionamento, pode acessar sua saída. Esta seção descreve alguns recursos que podem ser úteis.

### <a name="view-job-status"></a>Exibir status do trabalho

Você pode exibir um status resumido de todos os trabalhos de runbook ou exibir um status mais detalhado de um trabalho de runbook específico no portal do Azure.

À direita da sua conta de automação selecionada, em "estatísticas do trabalho", você pode exibir uma lista de resumos de todos os trabalhos de runbook. Abrir a página **trabalhos** no lado esquerdo da janela mostra os status do trabalho atual, os horários de início e os horários de conclusão.

![Uma captura de tela da página status do trabalho.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Exibir saída da ferramenta de dimensionamento e logs

Você pode exibir os logs das operações de expansão e redução horizontal abrindo seu runbook e selecionando o nome do seu trabalho.

Navegue até o runbook (o nome padrão é WVDAutoScaleRunbook) em seu grupo de recursos que hospeda a conta de automação do Azure e selecione **visão geral**. Na página Visão geral, selecione um trabalho em trabalhos recentes para exibir sua saída da ferramenta de dimensionamento, conforme mostrado na imagem a seguir.

![Uma imagem da janela de saída da ferramenta de dimensionamento.](media/tool-output.png)
