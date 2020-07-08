---
title: Barramento de Serviço do Azure – Atualizar automaticamente as unidades do sistema de mensagens
description: Este artigo mostra como você pode usar um runbook de Automação do Azure para atualizar automaticamente as unidades do sistema de mensagens de um namespace do Barramento de Serviço.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341486"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Atualizar automaticamente as unidades do sistema de mensagens de um namespace do Barramento de Serviço do Azure 
Este artigo mostra como atualizar automaticamente [unidades do sistema de mensagens](service-bus-premium-messaging.md) de um namespace do Barramento de Serviço do Azure com base no uso de recursos (CPU ou memória). 

O exemplo neste artigo mostra como aumentar as unidades do sistema de mensagens para um namespace do Barramento de Serviço do Azure quando o uso da CPU do namespace fica acima de 75%. As etapas de alto nível são:

1. Crie um runbook de Automação do Azure com o script do PowerShell que escala verticalmente (aumenta) as unidades do sistema de mensagens para um namespace do Barramento de Serviço do Azure. 
2. Crie um alerta de uso de CPU no namespace do Barramento de Serviço do Azure, que invoca o script do PowerShell quando o uso da CPU do namespace está acima de 75%. 

> [!IMPORTANT]
> Este artigo aplica-se apenas à camada **premium** do Barramento de Serviço do Azure. 


## <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço
Crie um namespace de Barramento de Serviço de camada premium. Siga as etapas do artigo [Criar um namespace no portal do Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) para criar o namespace. 

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure
Crie uma conta de Automação do Azure. Para isso, siga as instruções do artigo [Criar uma conta de Automação do Azure](../automation/automation-quickstart-create-account.md). 

## <a name="import-azservice-module-from-gallery"></a>Importar o módulo Az.Service da galeria
Importe os módulos `Az.Accounts` e `Az.ServiceBus` da galeria para a conta de automação. O módulo `Az.ServiceBus` depende do módulo `Az.Accounts`. Portanto, ele deve ser instalado primeiro. 

Para obter instruções detalhadas, consulte [Importar um módulo da galeria de módulos](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Criar e publicar um runbook do PowerShell

1. Crie um runbook do PowerShell seguindo as instruções no artigo [Criar um runbook do PowerShell](../automation/automation-quickstart-create-runbook.md). 

    Aqui está um exemplo de script do PowerShell que você pode usar para aumentar as unidades do sistema de mensagens para um namespace do Barramento de Serviço. Esse script do PowerShell em um runbook de automação aumenta os MUs de 1 para 2, 2 para 4 ou 4 para 8. Os valores permitidos para essa propriedade são: 1, 2, 4 e 8. Você pode criar outro runbook para diminuir as unidades do sistema de mensagens.

    Os parâmetros **namespaceName** e **resourceGroupName** são usados para testar o script separadamente do cenário de alerta. 
    
    O parâmetro **WebHookData** serve para que o alerta passe informações como nome do grupo de recursos, nome do recurso etc. no tempo de execução. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
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
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Teste a pasta de trabalho](../automation/manage-runbooks.md#test-a-runbook) especificando valores para os parâmetros **namespaceName** e **resourceGroupName**. Confirme que as unidades do sistema de mensagens no namespace estão atualizadas. 
3. Após testar com êxito, [publicar a pasta de trabalho](..//automation/manage-runbooks.md#publish-a-runbook) para que ela fique disponível para ser adicionada como uma ação para um alerta no namespace mais tarde. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Criar um alerta no namespace para disparar o runbook
Consulte o artigo [Usar um alerta para disparar um runbook de Automação do Azure](../automation/automation-create-alert-triggered-runbook.md) para configurar um alerta em seu namespace do Barramento de Serviço e disparar o runbook de automação criado. Por exemplo, você pode criar um alerta na métrica **Uso de CPU por namespace** ou **Uso de tamanho de memória por namespace** e adicionar uma ação para disparar o runbook de automação que você criou. Para obter detalhes sobre essas métricas, consulte [Métricas de uso de recursos](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

O procedimento a seguir mostra como criar um alerta que dispara o runbook de automação quando o namespace **uso da CPU** passa de **75%** .

1. Na página **Namespace do Barramento de Serviço** do seu namespace, selecione **Alertas** no menu à esquerda. Em seguida, selecione **+ Nova regra de alerta** na barra de ferramentas. 
    
    ![Página alertas – Botão de nova regra de alerta](./media/automate-update-messaging-units/alerts-page.png)
2. Na página **Criar regra de alerta**, clique em **Selecionar condição**. 

    ![Página Criar regra de alerta: selecione condição](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Na página **Configurar lógica de sinal**, selecione **CPU** para o sinal. 

    ![Configurar a lógica de sinal – Selecionar a CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Insira um **valor limite** (neste exemplo, é **75%** ) e selecione **Concluído**. 

    ![Configurar sinal de CPU](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Agora, na página **Criar alerta**, clique em **Selecionar grupo de ações**.
    
    ![Selecionar o grupo de ações](./media/automate-update-messaging-units/select-action-group-button.png)
6. Selecione o botão **Criar grupo de ações** na barra de ferramentas. 

    ![Botão Criar grupo de ações](./media/automate-update-messaging-units/create-action-group-button.png)
7. Na página **Adicionar grupo de ações**, execute as seguintes etapas:
    1. Insira um **nome** para o grupo de ações. 
    2. Insira um **nome curto** para o grupo de ações.
    3. Selecione a **assinatura** na qual você deseja criar o grupo de ações.
    4. Selecione o **grupo de recursos**. 
    5. Na seção **Ações**, insira um **nome para a ação** e selecione **Runbook de automação** para **Tipo de ação**. 

        ![Adicionar página do grupo de ações](./media/automate-update-messaging-units/add-action-group-page.png)
8. Na página **Configurar runbook**, execute as seguintes etapas:
    1. Para **Origem do runbook**, selecione **Usuário**. 
    2. Para **assinatura**, selecione a **assinatura** do Azure que contém a conta de automação. 
    3. Para **Conta de automação**, selecione a sua **conta de automação**.
    4. Para **Runbook**, selecione seu runbook. 
    5. Selecione **OK** na página **Configurar runbook**. 
        ![Configurar runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Selecione **OK** na página **Adicionar grupo de ações**. 
5. Agora, na página **Criar regra de alerta**, insira um **nome para a regra** e, em seguida, selecione **Criar regra de alerta**. 
    ![Criar regra de alerta](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Agora, quando o uso de CPU do namespace ficar acima de 75, o alerta disparará o runbook de automação, que aumenta as unidades do sistema de mensagens do namespace do Barramento de Serviço. Da mesma forma, você pode criar um alerta para outro runbook de automação, o que diminui as unidades do sistema de mensagens se o uso da CPU de namespace ficar abaixo de 25. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as unidades do sistema de mensagens, consulte o [Mensagens premium](service-bus-premium-messaging.md)
