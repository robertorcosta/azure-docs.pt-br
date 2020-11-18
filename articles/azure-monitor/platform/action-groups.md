---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a5d685e49d941d7b6febbc220cdbfbcb631c4496
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746356"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário. Você pode configurar até 2 mil grupos de ação em uma assinatura.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Tipo**: a notificação ou ação executada. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo.
* **Name**: um identificador exclusivo dentro do grupo de ações.
* **Detalhes**: os detalhes correspondentes que variam por *tipo*.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **Monitor**. O painel **Monitor** consolida todas as configurações e dados de monitoramento em uma exibição.

1. Selecione **alertas** e, em seguida, selecione **Gerenciar ações**.

    ![Botão Gerenciar Ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ação** e preencha os campos relevantes na experiência do assistente.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Definir configurações básicas do grupo de ações

Em **detalhes do projeto**:

Selecione a **assinatura** e o **grupo de recursos** em que o grupo de ações é salvo.

Em **Detalhes da instância**:

1. Insira um **nome de grupo de ação**.

1. Insira um **nome de exibição**. O nome de exibição é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Configurar notificações

1. Clique no botão **Avançar: notificações >** para ir para a guia **notificações** ou selecione a guia **notificações** na parte superior da tela.

1. Defina uma lista de notificações a serem enviadas quando um alerta for disparado. Forneça o seguinte para cada notificação:

    a. **Tipo de notificação**: selecione o tipo de notificação que você deseja enviar. As opções disponíveis são:
      * Função de Azure Resource Manager de email – envie um email para os usuários atribuídos a determinadas funções de ARM de nível de assinatura.
      * Email/SMS/Push/voz-Envie esses tipos de notificação para destinatários específicos.
    
    b. **Nome**: Insira um nome exclusivo para a notificação.

    c. **Detalhes**: com base no tipo de notificação selecionado, insira um endereço de email, número de telefone, etc.
    
    d. **Esquema comum de alertas**: Você pode optar por habilitar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter um conteúdo de alerta extensível e unificado em todos os serviços de alerta no Azure Monitor.

    ![A guia notificações](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Configurar ações

1. Clique no botão **Avançar: ações >** para ir para a guia **ações** ou selecione a guia **ações** na parte superior da tela.

1. Defina uma lista de ações a serem disparadas quando um alerta for disparado. Forneça o seguinte para cada ação:

    a. **Tipo de ação**: selecione runbook de automação, Azure Function, ITSM, aplicativo lógico, webhook seguro, webhook.
    
    b. **Nome**: Insira um nome exclusivo para a ação.

    c. **Detalhes**: com base no tipo de ação, insira um URI de webhook, um aplicativo do Azure, uma conexão de ITSM ou um runbook de automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.
    
    d. **Esquema comum de alertas**: Você pode optar por habilitar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter um conteúdo de alerta extensível e unificado em todos os serviços de alerta no Azure Monitor.
    
    ![A guia ações](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Criar o grupo de ações

1. Você pode explorar as configurações de **Marcas** se desejar. Isso permite associar pares de chave/valor ao grupo de ações para sua categorização e é um recurso disponível para qualquer recurso do Azure.

    ![A guia marcas](./media/action-groups/action-group-4-tags.png)
    
1. Clique em **Revisar + criar** para revisar as configurações. Isso fará uma validação rápida de suas entradas para garantir que todos os campos obrigatórios sejam selecionados. Se houver problemas, eles serão relatados aqui. Depois de examinar as configurações, clique em **criar** para provisionar o grupo de ações.
    
    ![A guia revisar + criar](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Quando você configura uma ação para notificar uma pessoa por email ou SMS, elas recebem uma confirmação indicando que foram adicionadas ao grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação

Depois de criar um grupo de ações, você poderá ver os **Grupos de ações** selecionando **Gerenciar ações** na página de aterrissagem **Alertas** no painel **Monitor**. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> Confira [Limites do serviço de assinatura para monitoramento](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) para limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Runbook de automação
Veja os [Limites do serviço de assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites nos conteúdos do Runbook.

É possível ter um número limitado de ações de runbook em um grupo de ações. 

### <a name="azure-app-push-notifications"></a>Notificações por push do aplicativo Azure
É possível ter um número limitado de ações ao aplicativo Azure em um grupo de ações.

### <a name="email"></a>Email
Os emails serão enviados dos endereços de email a seguir. Certifique-se de que a filtragem de email esteja configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

É possível ter um número limitado de ações de email em um grupo de ações. Confira o artigo [informações de limitação da taxa](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Email para a Função do Azure Resource Manager
Envie o email aos membros da função de assinatura. O email será enviado somente aos membros do **usuário do Azure AD** da função. O email não será enviado aos grupos ou às entidades de serviço do Azure AD.

Um email de notificação é enviado somente para o endereço de *email principal* .

É possível ter um número limitado de ações de email em um grupo de ações. Confira o artigo [informações de limitação da taxa](./alerts-rate-limiting.md).

### <a name="function"></a>Função
Chama um ponto de extremidade de gatilho HTTP existente no [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

É possível ter um número limitado de ações de função em um grupo de ações.

### <a name="itsm"></a>ITSM
Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](./itsmc-overview.md).

É possível ter um número limitado de ações de ITSM em um grupo de ações. 

### <a name="logic-app"></a>Aplicativo Lógico
É possível ter um número limitado de ações de aplicativo lógico em um grupo de ações.

### <a name="secure-webhook"></a>Webhook Seguro
A ação de webhook dos grupos de ações permite que você aproveite o Azure Active Directory para proteger a conexão entre o grupo de ações e a API Web protegida (ponto de extremidade do webhook). O fluxo de trabalho geral para aproveitar essa funcionalidade é descrito abaixo. Para obter uma visão geral dos aplicativos do Azure AD e das entidades de serviço, confira a [Visão geral da plataforma de identidade da Microsoft (v2.0)](../../active-directory/develop/v2-overview.md).

1. Crie um aplicativo do Azure AD para sua API Web protegida. Consulte [API Web protegida: registro de aplicativo](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configure sua API protegida para ser [chamada por um aplicativo daemon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Habilite grupos de ações para usar seu aplicativo Azure AD.

    > [!NOTE]
    > Você deve ser membro da [função Administrador do aplicativo Azure AD](../../active-directory/roles/permissions-reference.md#available-roles) para executar esse script.
    
    - Modifique a chamada Connect-AzureAD do script do PowerShell para usar sua ID de locatário do Azure AD.
    - Modifique a variável do script do PowerShell $myAzureADApplicationObjectId para usar a ID de objeto do seu aplicativo do Azure AD.
    - Execute o script modificado.
    
3. Configure a ação de webhook segura do grupo de ações.
    - Copie o valor $myApp.ObjectId do script e insira-o no campo ID de objeto do aplicativo na definição de ação do webhook.
    
    ![Proteger a ação de webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Proteger o script do PowerShell de webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Confira as [informações de limitação de taxa](./alerts-rate-limiting.md) e o [comportamento de alerta do SMS](./alerts-sms-behavior.md) para obter informações adicionais importantes. 

É possível ter um número limitado de ações de SMS em um grupo de ações.

> [!NOTE]
> Se a interface do usuário do grupo de ações do portal do Azure não permitir que você selecione o código do país/região, o SMS não terá suporte para seu país/região.  Se o código de seu país/região não estiver disponível, você poderá votar no [UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) para que seu país/região seja adicionado. Enquanto isso, uma solução alternativa é fazer com que seu grupo de ações chame um webhook para um provedor de SMS de terceiros com suporte em seu país/região.  

Os preços de países/regiões com suporte são listados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Voz
Confira o artigo sobre [informações de limitação de taxa](./alerts-rate-limiting.md) para comportamento adicional importante.

É possível ter um número limitado de ações de voz em um grupo de ações.

> [!NOTE]
> Se a interface do usuário do grupo de ações do portal do Azure não permitir que você selecione o código do país/região, as chamadas de voz não terão suporte para seu país/região. Se o código de seu país/região não estiver disponível, você poderá votar no [UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) para que seu país/região seja adicionado.  Enquanto isso, uma solução alternativa é fazer com que seu grupo de ações chame um webhook para um provedor de chamada de voz de terceiros com suporte em seu país/região.  

Os preços de países/regiões com suporte são listados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook
WebHooks são processados usando as seguintes regras
- Uma chamada de webhook é tentada no máximo três vezes.
- A chamada será repetida se uma resposta não for recebida dentro do período de tempo limite ou se um dos seguintes códigos de status HTTP for retornado: 408, 429, 503 ou 504.
- A primeira chamada aguardará 10 segundos por uma resposta.
- A segunda e terceira tentativas aguardarão 30 segundos por uma resposta.
- Depois que as 3 tentativas de chamar o webhook falharam, nenhum grupo de ação chamará o ponto de extremidade por 15 minutos.

Intervalos de endereços IP de origem:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020: A04:402:: 178/125
 - 2603:1020: B04:402:: 178/125
 - 2603:1020: C04:402:: 178/125
 - 2603:1020: D04:402:: 178/125
 - 2603:1020: E04:402:: 178/125
 - 2603:1020: F04:402:: 178/125
 - 2603:1020:1004:800:: F8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030: f:400:: 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400:: F0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40B: 400:: 978/125
 - 2603:1030:40C: 402:: 178/125
 - 2603:1030:504:802:: F8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030: A07:402:: 8f8/125
 - 2603:1030: B04:402:: 178/125
 - 2603:1030: C06:400:: 978/125
 - 2603:1030: F05:402:: 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040: A06:402:: 178/125
 - 2603:1040: B04:402:: 178/125
 - 2603:1040: C06:402:: 178/125
 - 2603:1040: D04:800:: F8/125
 - 2603:1040: F05:402:: 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400:: 1f8/125

Para receber atualizações sobre as alterações para esses endereços IP, é recomendável que você configure um alerta de Integridade do Serviço, que monitora notificações informativas sobre o serviço de grupos de ação.

É possível ter um número limitado de ações de webhook em um grupo de ações.

Atualizações frequentes em endereços IP de origem podem ser muito demoradas no webhook. O uso da **marca de serviço** para o The *Action* ajuda a minimizar a complexidade de atualizações frequentes para endereços IP manualmente. Os prefixos de intervalo de endereços IP de origem compartilhados acima são gerenciados automaticamente pela Microsoft, abrangedos por **marca de serviço**.

#### <a name="service-tag"></a>Marca de serviço
Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados, minimizando a complexidade de atualizações frequentes para regras de segurança de rede para um The Action.

1. Em portal do Azure em pesquisa de serviços do Azure para *grupo de segurança de rede*.
2. Clique em **Adicionar** e crie um grupo de segurança de rede.

   1. Adicione o nome do grupo de recursos e insira os *detalhes da instância*.
   1. Clique em **revisão + criar** e, em seguida, clique em *criar*.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Exemplo de como criar um grupo de segurança de rede."border="true":::

3. Vá para o grupo de recursos e clique em *grupo de segurança de rede* que você criou.

    1. Selecione *regras de segurança de entrada*.
    1. Clique em **Adicionar**.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Exemplo de como adicionar uma marca de serviço."border="true":::

4. Uma nova janela será aberta no painel direito.
    1.  Selecionar origem: **marca de serviço**
    1.  Marca de serviço de **origem: resourcegroup**
    1.  Clique em **Adicionar**.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Exemplo de como adicionar a marca de serviço."border="true":::

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [comportamento de alertas por SMS](./alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](./activity-log-alerts-webhook.md).  
* Saiba mais sobre [conector ITSM](./itsmc-overview.md).
* Saiba mais sobre a [limitação de taxa](./alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](./alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../service-health/alerts-activity-log-service-notifications-portal.md).
