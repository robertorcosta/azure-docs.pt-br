---
title: Atualize o nível de preços de um tópico ou domínio do Azure Event Grid
description: Este artigo descreve como atualizar o nível de preços de um tópico ou domínio do Azure Event Grid (básico para premium, premium ao básico) usando o portal Azure, Azure CLI e Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300135"
---
# <a name="update-pricing-tier"></a>Atualizar o nível de preços 
Este artigo mostra como atualizar o nível de preços de um tópico ou domínio do Azure Event Grid usando o portal Azure, o Azure CLI e o Azure PowerShell. 

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como alterar a camada de preços de um tópico ou um domínio no portal Azure. 

### <a name="overview-page"></a>Página de visão geral
Você pode alterar o nível de preços de um tópico ou um domínio na página **Visão Geral.** O exemplo a seguir mostra como atualizar um tópico do nível básico para o nível premium. As etapas para o downgrade do nível premium para o nível básico são semelhantes.

1. No [portal Azure,](https://portal.azure.com)navegue até a página tópico ou domínio. 
2. Na **página Visão geral,** selecione o nível de preços atual (no exemplo a seguir, é **básico**.)
    
    ![Selecione o nível de preços atual](./media/update-tier/select-tier.png)
3. Na página **de nível de preços,** altere o nível e selecione **OK**. 

    ![Atualize o nível de preços](./media/update-tier/change-tier.png)
4. Verifique o status da operação na página **Notificações.**

    ![Status de atualização](./media/update-tier/status.png)    
5. Confirme se você vê o nível atualizado na página **Visão geral.** 

    ![Status de atualização](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Página de rede
Você pode **atualizar** do nível básico para o nível premium na página **Rede.** Você não pode fazer o downgrade do nível premium para o nível básico nesta página. 

1. No [portal Azure,](https://portal.azure.com)navegue até a página tópico ou domínio. 
2. Na página **Rede,** mude para a guia Conexões de **ponto final Privado (visualização).** 
3. Se o nível de preços atual for **básico,** você verá a seguinte mensagem. Selecione-o. 

    ![Atualizar nível na página de conexões de ponto final privado](./media/update-tier/private-endpoint-connections-page.png)
4. Na **página Atualizar para o nível de preços premium,** selecione **Sim**. 
    
    ![Confirmar atualização](./media/update-tier/message-private-endpoint-connection.png)
5. Verifique o status da operação na página **Notificações.**

    ![Status de atualização](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Usar a CLI do Azure
Esta seção mostra como usar os comandos Azure CLI para alterar o nível de preços de um tópico ou domínio. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualize um tópico do básico para o premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Desamere um tópico de premium para básico

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Atualize um domínio do básico para o premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Rebaixe um domínio de premium para básico

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Esta seção mostra como usar os comandos PowerShell para alterar o nível de preços de um tópico ou domínio. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualize um tópico do básico para o premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Desamere um tópico de premium para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Atualize um domínio do básico para o premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Rebaixe um domínio de premium para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Próximas etapas
Para tópicos e domínios de nível premium, você pode configurar pontos finais privados para restringir o acesso somente de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [Configure pontos finais privados](configure-private-endpoints.md).
