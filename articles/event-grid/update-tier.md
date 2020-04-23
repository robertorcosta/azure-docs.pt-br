---
title: Atualizar o tipo de preço de um tópico ou domínio da grade de eventos do Azure
description: Este artigo descreve como atualizar o tipo de preço de um tópico ou domínio da grade de eventos do Azure (básico para Premium, Premium para básico) usando portal do Azure, CLI do Azure e Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101630"
---
# <a name="update-pricing-tier"></a>Atualizar tipo de preço 
Este artigo mostra como atualizar o tipo de preço de um domínio ou tópico da grade de eventos do Azure usando portal do Azure, CLI do Azure e Azure PowerShell. 

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como alterar o tipo de preço de um tópico ou um domínio no portal do Azure. 

### <a name="overview-page"></a>Página de visão geral
Você pode alterar o tipo de preço de um tópico ou um domínio na página **visão geral** . O exemplo a seguir mostra como atualizar um tópico da camada básica para a camada Premium. As etapas para fazer o downgrade da camada Premium para a camada básica são semelhantes.

1. Na [portal do Azure](https://portal.azure.com), navegue até a página tópico ou domínio. 
2. Na página **visão geral** , selecione o tipo de preço atual (no exemplo a seguir, é **básico**).
    
    ![Selecione o tipo de preço atual](./media/update-tier/select-tier.png)
3. Na página **tipo de preço** , altere a camada e selecione **OK**. 

    ![Atualizar o tipo de preço](./media/update-tier/change-tier.png)
4. Verifique o status da operação na página **notificações** .

    ![Status da atualização](./media/update-tier/status.png)    
5. Confirme que você vê a camada atualizada na página **visão geral** . 

    ![Status da atualização](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Página de rede
Você pode **Atualizar** da camada básica para a camada Premium na página **rede** . No entanto, não é possível fazer downgrade da camada Premium para a camada básica nesta página. 

1. Na [portal do Azure](https://portal.azure.com), navegue até a página tópico ou domínio. 
2. Na página **rede** , alterne para a guia **conexões de ponto de extremidade privado (versão prévia)** . 
3. Se o tipo de preço atual for **básico**, você verá a mensagem a seguir. Selecione-o. 

    ![Atualizar camada na página conexões de ponto de extremidade privado](./media/update-tier/private-endpoint-connections-page.png)
4. Na página **atualizar para o tipo de preço premium** , selecione **Sim**. 
    
    ![Confirmar atualização](./media/update-tier/message-private-endpoint-connection.png)
5. Verifique o status da operação na página **notificações** .

    ![Status da atualização](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Usar a CLI do Azure
Esta seção mostra como usar CLI do Azure comandos para alterar o tipo de preço de um tópico. Para atualizar o tipo de preço de um domínio, `az eventgrid domain update` use o comando de maneira semelhante.

### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da grade de eventos do Azure para a CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, execute o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualizar um tópico do básico para o Premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Fazer downgrade de um tópico de Premium para básico

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Esta seção mostra como usar comandos do PowerShell para alterar o tipo de preço de um tópico ou domínio. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparar token e cabeçalhos para chamadas à API REST 
Execute os seguintes comandos de pré-requisito para obter um token de autenticação para usar com chamadas à API REST e autorização e outras informações de cabeçalho. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualizar um tópico do básico para o Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Fazer downgrade de um tópico de Premium para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Atualizar um domínio de básico para Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Fazer downgrade de um domínio de Premium para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Próximas etapas
Para tópicos e domínios da camada Premium, você pode configurar pontos de extremidade privados para restringir o acesso somente de redes virtuais selecionadas. Para obter as instruções passo a passo, consulte [Configurar pontos de extremidade privados](configure-private-endpoints.md).
