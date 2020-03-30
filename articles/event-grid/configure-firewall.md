---
title: Configure firewall IP para tópicos ou domínios do Azure Event Grid (Preview)
description: Este artigo descreve como configurar configurações de firewall para tópicos ou domínios do Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299862"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Configure firewall IP para tópicos ou domínios do Azure Event Grid (Preview)
Por padrão, tópico e domínio são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida). Para obter mais informações sobre os recursos de segurança da rede suportados pelo Event Grid, consulte [Segurança de rede para a Grade de Eventos](network-security.md).

Este artigo descreve como configurar configurações de firewall IP para tópicos ou domínios do Azure Event Grid.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal Azure para criar regras de firewall IP de entrada. Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras ip de entrada para **domínios**. 

1. No [portal Azure,](https://portal.azure.com)navegue até o tópico ou domínio da grade de eventos e mude para a guia **Rede.**
2. Selecione **redes públicas** para permitir que todas as redes, incluindo a internet, acessem o recurso. 

    Você pode restringir o tráfego usando regras de firewall baseadas em IP. Especifique um único endereço IPv4 ou uma série de endereços IP na notação DE ROTEAmento interdomínio (CIDR) sem classe. 

    ![Página de redes públicas](./media/configure-firewall/public-networks-page.png)
3. Selecione **pontos finais privados apenas** para permitir que apenas conexões de ponto final privado acessem esse recurso. Use a **guia Conexões de ponto final privado** nesta página para gerenciar conexões. 

    ![Página de redes públicas](./media/configure-firewall/private-endpoints-page.png)
4. Selecione **Salvar** na barra de ferramentas. 



## <a name="use-azure-cli"></a>Usar a CLI do Azure
Esta seção mostra como usar os comandos Azure CLI para criar tópicos com regras ip de entrada. Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras ip de entrada para **domínios**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Permitir o acesso à rede pública para um tópico existente
Por padrão, o acesso à rede pública é habilitado para tópicos e domínios. Você pode restringir o tráfego configurando regras de firewall IP de entrada. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso à rede pública para um tópico existente
Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego pela internet pública não é permitido. Somente conexões de ponto final privado poderão acessar esses recursos. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Criar tópico com regras ip de entrada
O comando CLI da amostra a seguir cria um tópico de grade de eventos com regras IP de entrada em uma etapa. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Crie o tópico primeiro e, em seguida, adicione regras ip de entrada
Este exemplo cria primeiro um tópico de grade de eventos e, em seguida, adiciona regras ip de entrada para o tópico em um comando separado. Ele também atualiza as regras de IP de entrada que foram definidas no segundo comando. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Usar o PowerShell
Esta seção mostra como usar os comandos Do Azure PowerShell para criar tópicos do Azure Event Grid com regras de firewall IP de entrada. Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras ip de entrada para **domínios**. 

### <a name="prerequisite"></a>Pré-requisito
Siga as instruções de [Como: Use o portal para criar um aplicativo azure AD e um princípio de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo do Azure Active Directory e anotar os seguintes valores:

- ID do diretório (locatário)
- ID de aplicação (cliente)
- Segredo de aplicativo (cliente)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparar token e cabeçalhos para chamadas de API REST 
Execute os seguintes comandos pré-requisitos para obter um token de autenticação para usar com chamadas de API REST e autorização e outras informações de cabeçalho. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Permitir o acesso à rede pública para um tópico existente
Por padrão, o acesso à rede pública é habilitado para tópicos e domínios. Você pode restringir o tráfego configurando regras de firewall IP de entrada. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso à rede pública para um tópico existente
Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego pela internet pública não é permitido. Somente conexões de ponto final privado poderão acessar esses recursos. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Crie um tópico de grade de eventos com regras de entrada em uma etapa

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Criar o tópico da grade de eventos primeiro e, em seguida, adicionar regras ip de entrada

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
