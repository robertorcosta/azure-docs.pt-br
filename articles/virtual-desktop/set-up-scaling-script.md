---
title: Sessão de escala sela Azure Automation - Azure
description: Como dimensionar automaticamente os hosts de sessão do Windows Virtual Desktop com o Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349876"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Hosts de sessão de escala usando o Azure Automation

Você pode reduzir o custo total de implantação do Windows Virtual Desktop dimensionando suas máquinas virtuais (VMs). Isso significa desligar e negociar VMs host de sessão durante horários de uso fora do pico, em seguida, atiça-los novamente e realocá-los durante os horários de pico.

Neste artigo, você aprenderá sobre a ferramenta de dimensionamento construída com o Azure Automation e o Azure Logic Apps que irá escalar automaticamente máquinas virtuais host de sessão em seu ambiente Windows Virtual Desktop. Para saber como usar a ferramenta de dimensionamento, pule para [pré-requisitos](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de dimensionamento

A ferramenta de dimensionamento oferece uma opção de automação de baixo custo para clientes que desejam otimizar os custos de VM do host de sessão.

Você pode usar a ferramenta de dimensionamento para:
 
- Agende as VMs para iniciar e parar com base nos horários comerciais peak e off-peak.
- Dimensione as VMs com base no número de sessões por núcleo da CPU.
- Dimensione em VMs durante as horas de off-peak, deixando o número mínimo de VMs host de sessão em execução.

A ferramenta de dimensionamento usa uma combinação de runbooks PowerShell de automação azure, webhooks e aplicativos de lógica azure para funcionar. Quando a ferramenta é executada, o Azure Logic Apps chama um webhook para iniciar o runbook de automação do Azure. O manual então cria um emprego.

Durante o tempo de pico de uso, o trabalho verifica o número atual de sessões e a capacidade de VM do host de sessão em execução atual para cada pool de host. Ele usa essas informações para calcular se as VMs do host de sessão em execução podem suportar sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o arquivo **createazurelogicapp.ps1.** Se as VMs do host de sessão não puderem suportar sessões existentes, o trabalho iniciará VMs adicionais de host de sessão no pool de host.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões na VM. Esse parâmetro só determina quando novas VMs precisam ser iniciadas para equilibrar as conexões. Para restringir o número de sessões, você precisa seguir as instruções [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar o parâmetro *MaxSessionLimit* de acordo.

Durante o tempo de uso fora do pico, o trabalho determina quais VMs de host de sessão devem ser fechadas com base no parâmetro *MinimumNumberOfRDSH.* O trabalho definirá as VMs do host de sessão para drenar o modo para evitar que novas sessões se conectem aos hosts. Se você definir o parâmetro *LimitSecondsToForceLogOffUser* como um valor positivo não zero, o trabalho notificará qualquer usuário atualmente conectado para salvar seu trabalho, esperar o tempo configurado e, em seguida, forçar os usuários a sair. Uma vez que todas as sessões de usuário na VM host de sessão tenham sido assinadas, o trabalho será encerrado na VM.

Se você definir o parâmetro *LimitSecondsToForceLogOffUser* como zero, o trabalho permitirá que a configuração da sessão em políticas de grupo especificadas seja capaz de lidar com a assinatura das sessões de usuário. Para ver essas políticas de grupo, vá para Políticas **de configuração** > de computador**Modelos** > **administrativos** > Limites de tempo de**sessão**do terminal**de serviços** >  > do**Windows.** > **Terminal Services** Se houver sessões ativas em uma VM de host de sessão, o trabalho deixará a VM do host de sessão em execução. Se não houver sessões ativas, o trabalho encerrará a VM do host de sessão.

O trabalho é executado periodicamente com base em um intervalo de recorrência definido. Você pode alterar esse intervalo com base no tamanho do seu ambiente de Desktop Virtual do Windows, mas lembre-se que iniciar e desligar máquinas virtuais pode levar algum tempo, então lembre-se de explicar o atraso. Recomendamos definir o intervalo de recorrência para cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Esta solução se aplica apenas a VMs de host de sessão agrupadas.
- Esta solução gerencia VMs em qualquer região, mas só pode ser usada na mesma assinatura que sua conta azure Automation e Azure Logic Apps.

>[!NOTE]
>A ferramenta de dimensionamento controla o modo de balanceamento de carga do pool de host que está dimensionando. Ele define-o para o equilíbrio de carga de largura-primeiro para o pico e fora do horário de pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de dimensionamento, certifique-se de ter as seguintes coisas prontas:

- Um [inquilino e um pool de host](create-host-pools-arm-template.md) do Windows Virtual Desktop
- VMs do pool de host de sessão configurados e registrados com o serviço de desktop virtual do Windows
- Um usuário com [acesso ao Contribuinte](../role-based-access-control/role-assignments-portal.md) na assinatura do Azure

A máquina que você usa para implantar a ferramenta deve ter: 

- Windows PowerShell 5.1 ou posterior
- O módulo Microsoft Az PowerShell

Se você tem tudo pronto, então vamos começar.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Primeiro, você precisará de uma conta do Azure Automation para executar o runbook do PowerShell. Veja como configurar sua conta:

1. Abra o Windows PowerShell como administrador.
2. Execute o cmdlet a seguir para entrar na sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Sua conta deve ter direitos de contribuinte na assinatura do Azure em que você deseja implantar a ferramenta de dimensionamento.

3. Execute o cmdlet a seguir para baixar o script para criar a conta azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Execute o cmdlet a seguir para executar o script e crie a conta Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A saída do cmdlet incluirá um URI webhook. Certifique-se de manter um registro do URI porque você o usará como parâmetro ao configurar o cronograma de execução dos aplicativos Azure Logic.

6. Depois de configurar sua conta do Azure Automation, faça login na sua assinatura do Azure e verifique se sua conta do Azure Automation e o manual relevante apareceram em seu grupo de recursos especificado, conforme mostrado na imagem a seguir:

   ![Uma imagem da página de visão geral do Azure mostrando a conta de automação recém-criada e o runbook.](media/automation-account.png)

  Para verificar se o webhook está onde deveria estar, selecione o nome do seu runbook. Em seguida, vá para a seção Recursos do seu runbook e selecione **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Crie uma conta de automação do Azure como conta

Agora que você tem uma conta do Azure Automation, você também precisará criar uma conta do Azure Automation Run As para acessar seus recursos do Azure.

Uma [conta Azure Automation Run As](../automation/manage-runas-account.md) fornece autenticação para gerenciar recursos no Azure com os cmdlets do Azure. Quando você cria uma conta Run As, ela cria um novo usuário principal de serviço no Azure Active Directory e atribui a função Contribuinte ao usuário principal do serviço no nível de assinatura, a Conta Azure Run As é uma ótima maneira de autenticar com segurança com certificados e um nome principal do serviço sem a necessidade de armazenar um nome de usuário e senha em um objeto de credencial. Para saber mais sobre a autenticação Run As, consulte [Limitando as permissões de conta](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Qualquer usuário que seja membro da função Administradores de Assinatura e coadministrador da assinatura pode criar uma conta Run As seguindo as instruções da próxima seção.

Para criar uma conta Run As em sua conta do Azure:

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, insira e selecione **Contas de Automação**.

2. Na página Contas de **Automação,** selecione o nome da sua conta de Automação.

3. No painel do lado esquerdo da janela, **selecione Executar como contas** na seção Configurações da conta.

4. Selecione **Azure Run as Account**. Quando o **painel Adicionar a zure executar como conta** for exibido, revise as informações de visão geral e selecione **Criar** para iniciar o processo de criação da conta.

5. Aguarde alguns minutos para que o Azure crie a conta Run As. Você pode acompanhar o progresso da criação no menu em Notificações.

6. Quando o processo for concluído, ele criará um ativo chamado AzureRunAsConnection na conta de Automação especificada. O ativo de conexão contém o ID do aplicativo, o ID do inquilino, o ID de assinatura e a impressão digital do certificado. Lembre-se do ID do aplicativo, porque você vai usá-lo mais tarde.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de função na Área de Trabalho Virtual do Windows

Em seguida, você precisa criar uma atribuição de função para que o AzureRunAsConnection possa interagir com o Windows Virtual Desktop. Certifique-se de usar o PowerShell para fazer login com uma conta que tenha permissões para criar atribuições de função.

Primeiro, baixe e importe o [módulo PowerShell da área de trabalho virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usar na sessão PowerShell, se você ainda não tiver. Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando você encontrar o inquilino com os pools de host que deseja dimensionar, siga as instruções em [Criar uma conta de Automação Do Azure](#create-an-azure-automation-account) e use o nome do inquilino que você obteve do cmdlet anterior no cmdlet a seguir para criar a atribuição de função:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Crie o aplicativo azure logic e o cronograma de execução

Finalmente, você precisará criar o Aplicativo Azure Logic e configurar um cronograma de execução para sua nova ferramenta de dimensionamento.

1.  Abra o Windows PowerShell como administrador

2.  Execute o cmdlet a seguir para entrar na sua conta do Azure.

     ```powershell
     Login-AzAccount
     ```

3. Execute o cmdlet a seguir para baixar o arquivo de script createazurelogicapp.ps1 em sua máquina local.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Execute o cmdlet a seguir para entrar no Windows Virtual Desktop com uma conta que tenha permissões rds proprietário ou RDS Contribuinte.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Execute o seguinte script powerShell para criar o aplicativo e o cronograma de execução do aplicativo Azure Logic.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

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

     Depois de executar o script, o App Lógico deve aparecer em um grupo de recursos, conforme mostrado na imagem a seguir.

     ![Uma imagem da página de visão geral de um aplicativo azure logic.](media/logic-app.png)

Para fazer alterações no cronograma de execução, como alterar o intervalo de recorrência ou o fuso horário, vá para o agendador de escala automática e selecione **Editar** para ir ao Logic Apps Designer.

![Uma imagem do Logic Apps Designer. Os menus Recorrência e Webhook que permitem ao usuário editar tempos de recorrência e o arquivo webhook estão abertos.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerencie sua ferramenta de dimensionamento

Agora que você criou sua ferramenta de dimensionamento, você pode acessar sua saída. Esta seção descreve alguns recursos que você pode achar úteis.

### <a name="view-job-status"></a>Exibir status do trabalho

Você pode visualizar um status resumido de todos os trabalhos de runbook ou visualizar um status mais aprofundado de um trabalho de runbook específico no portal Azure.

À direita da sua conta de Automação selecionada, em "Estatísticas de Emprego", você pode visualizar uma lista de resumos de todos os trabalhos de runbook. Abrir a página **Jobs** no lado esquerdo da janela mostra os status atuais do trabalho, os tempos de início e os tempos de conclusão.

![Uma captura de tela da página de status do trabalho.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Exibir logs e a saída da ferramenta de dimensionamento

Você pode visualizar os registros de operações de escala e scale-in abrindo seu livro de execução e selecionando o nome do seu trabalho.

Navegue até o runbook (o nome padrão é WVDAutoScaleRunbook) em seu grupo de recursos hospedando a conta Azure Automation e selecione **Visão geral**. Na página visão geral, selecione um trabalho em Trabalhos Recentes para exibir sua saída de ferramenta de dimensionamento, conforme mostrado na imagem a seguir.

![Uma imagem da janela de saída para a ferramenta de dimensionamento.](media/tool-output.png)

## <a name="report-issues"></a>Relatar problemas

Se você encontrar quaisquer problemas com a ferramenta de dimensionamento, você pode denunciá-los na [página RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
