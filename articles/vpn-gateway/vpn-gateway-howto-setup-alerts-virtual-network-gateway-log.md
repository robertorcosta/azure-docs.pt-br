---
title: 'Azure VPN Gateway: Configure alertas sobre eventos de registro de diagnóstico'
description: Etapas para configurar alertas em eventos de registro de diagnóstico do VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: b587fa31f1aa42e8dbd5a9d2430c0117891f2091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239781"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configure alertas sobre eventos de log de diagnóstico do VPN Gateway

Este artigo ajuda você a configurar alertas com base em eventos de registro de diagnóstico do Azure VPN Gateway usando o Azure Log Analytics. 

Os seguintes registros estão disponíveis no Azure:

|***Nome*** | ***Descrição*** |
|---        | ---               |
|GatewayDiagnosticLog | Contém registros de diagnóstico para eventos de configuração de gateway, alterações primárias e eventos de manutenção |
|TunnelDiagnosticLog | Contém eventos de mudança de estado do túnel. Os eventos de conexão/desconexão do túnel têm uma razão resumida para a alteração do estado, se aplicável |
|RouteDiagnosticLog | Logs alterações em rotas estáticas e eventos BGP que ocorrem no gateway |
|IKEDiagnosticLog | Logs ike controlam mensagens e eventos no gateway |
|P2SDiagnosticLog | Logs mensagens de controle ponto a local e eventos no gateway |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Configurar alertas no Portal do Azure

As etapas de exemplo a seguir criarão um alerta para um evento de desconexão que envolve um túnel VPN site-a-site:


1. No portal Azure, procure por **Log Analytics** em Todos os **serviços** e selecione **espaços de trabalho do Log Analytics**.

   ![Seleções para ir a espaços de trabalho do Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Criar")

2. Selecione **Criar** na página **Do Log Analytics.**

   ![Página do Log Analytics com botão Criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecionar")

3. Selecione **Criar novo** e preencha os detalhes.

   ![Detalhes para criar um espaço de trabalho do Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecionar")

4. Encontre o gateway VPN na lâmina de**configurações do** **Monitor** > Diagnósticos.

   ![Seleções para encontrar o gateway VPN nas configurações de diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecionar")

5. Para ativar o diagnóstico, clique duas vezes no gateway e, em seguida, **selecione Ativar diagnósticos**.

   ![Seleções para ligar diagnósticos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecionar")

6. Preencha os detalhes e **certifique-se de** que o Send to Log Analytics e **o TunnelDiagnosticLog** sejam selecionados. Escolha o espaço de trabalho do Log Analytics que você criou na etapa 3.

   ![Caixas de seleção selecionadas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecionar")

   > [!NOTE]
   > Pode levar algumas horas para os dados aparecerem inicialmente.

7. Vá para a visão geral do recurso de gateway de rede virtual e selecione **Alertas** na guia **Monitoramento.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecionar")

   ![ponto a ponto](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecionar")
8. Selecione o espaço de trabalho do Log Analytics e o recurso.

   ![Seleções para espaço de trabalho e recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecionar")

9. Selecione **pesquisa de log personalizado** como a lógica do sinal em Condição de **Adicionar**.

   ![Seleções para uma pesquisa de log personalizado](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecionar")

10. Insira a consulta a seguir na caixa de texto **Consulta de pesquisa**. Substitua os valores em <> e TimeGenerated conforme apropriado.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Defina o valor limite como 0 e selecione **Feito**.

    ![Inserindo uma consulta e selecionando um limiar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecionar")

11. Na página **Criar regra,** selecione **Criar novo** na seção GRUPOS **DE AÇÃO.** Preencha os detalhes e selecione **OK**.

    ![Detalhes para um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecionar")

12. Na página **Criar regra,** preencha os detalhes para Personalizar ações e **certifique-se** de que o nome correto seja exibido na seção **NOME DO GRUPO DE AÇÃO.** Selecione **Criar regra de alerta** para criar a regra.

    ![Seleções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecionar")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configure alertas usando o PowerShell

As etapas do exemplo a seguir criam um alerta para um evento de desconexão que envolve um túnel VPN site-site.

1. Criar um espaço de trabalho do Log Analytics:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Ativar diagnósticos para o gateway VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Crie um grupo de ação.

   Esse código cria um grupo de ação que envia uma notificação por e-mail quando um alerta é acionado:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Crie uma regra de alerta com base em uma pesquisa de log personalizada:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em métricas de túneis, consulte [Configurar alertas nas métricas do VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
