---
title: Configure pontos finais privados para tópicos ou domínios da Azure Event Grid
description: Este artigo descreve como configurar pontos finais privados para tópicos ou domínio do Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299901"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Configure pontos finais privados para tópicos ou domínios da Azure Event Grid (Preview)
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir a ingestão de eventos diretamente de sua rede virtual para seus tópicos e domínios com segurança através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. O ponto final privado usa um endereço IP do espaço de endereço VNet para o seu tópico ou domínio. Para obter mais informações conceituais, consulte [Segurança da rede](network-security.md).

Este artigo descreve como configurar pontos finais privados para tópicos ou domínios.

> [!IMPORTANT]
> O recurso de pontos finais privados está disponível apenas para tópicos e domínios em nível premium. Para atualizar do nível básico para o nível premium, consulte o artigo [do nível de preços de atualização.](update-tier.md) 

## <a name="use-azure-portal"></a>Usar o portal do Azure 
Esta seção mostra como usar o portal Azure para criar um ponto final privado para um tópico ou um domínio.

> [!NOTE]
> As etapas mostradas nesta seção são principalmente para tópicos. Você pode usar etapas semelhantes para criar pontos finais privados para **domínios**. 

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu tópico ou domínio.
2. Mude para a guia **Rede** da página tópico. Selecione **+ Ponto final privado** na barra de ferramentas.

    ![Adicionar ponto final privado](./media/configure-private-endpoints/add-button.png)
2. Uma página básica, siga **estes passos:** 
    1. Selecione **uma assinatura do Azure** na qual você deseja criar o ponto final privado. 
    2. Selecione **um grupo de recursos do Azure** para o ponto final privado. 
    3. Digite um **nome** para o ponto final. 
    4. Selecione a **região** para o ponto final. Seu ponto final privado deve estar na mesma região que sua rede virtual, mas pode em uma região diferente do recurso de link privado (neste exemplo, um tópico de grade de eventos). 
    5. Em seguida, **selecione Next: Botão de >de recursos** na parte inferior da página. 

      ![Ponto final privado - página básica](./media/configure-private-endpoints/basics-page.png)
3. Na página **Recursos,** siga estas etapas: 
    1. Para o método de conexão, se você selecionar **Conectar a um recurso Do Zure no meu diretório,** siga essas etapas. Este exemplo mostra como se conectar a um recurso do Azure em seu diretório. 
        1. Selecione a **assinatura do Azure** na qual seu **tópico/domínio** existe. 
        1. Para **o tipo de recurso,** selecione **Microsoft.EventGrid/tópicos** ou **Microsoft.EventGrid/domínios** para o **tipo de recurso**.
        2. Em **'Recurso',** selecione um tópico/domínio na lista de baixa. 
        3. Confirme se o **subrecurso Destino** está definido como **tópico** ou **domínio** (com base no tipo de recurso selecionado).    
        4. Selecione **A seguir: Configuração >** botão na parte inferior da página. 

            ![Ponto final privado - página de recursos](./media/configure-private-endpoints/resource-page.png)
    2. Se você selecionar **Conectar a um recurso usando um ID de recurso ou um alias,** siga estas etapas:
        1. Digite o ID do recurso. Por exemplo: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Para **Recurso,** digite **tópico** ou **domínio**. 
        3. (opcional) Adicione uma mensagem de solicitação. 
        4. Selecione **A seguir: Configuração >** botão na parte inferior da página. 

            ![Ponto final privado - página de recursos](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na página **Configuração,** você seleciona a sub-rede em uma rede virtual para onde deseja implantar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas as redes virtuais na assinatura e localização atualmente selecionadas estão listadas na lista de baixa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **A seguir: Tags >** botão na parte inferior da página. 

    ![Ponto final privado - página de configuração](./media/configure-private-endpoints/configuration-page.png)
5. Na página **Tags,** crie quaisquer tags (nomes e valores) que você deseja associar com o recurso de ponto final privado. Em seguida, **selecione 'Revisar + criar',** botão na parte inferior da página. 
6. Na **Revisão + criar,** revise todas as configurações e selecione **Criar** para criar o ponto final privado. 

    ![Ponto final privado - revisão & página de criação](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Gerenciar conexão de link privado

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso para o qual você está criando um ponto final privado estiver em seu diretório, você poderá aprovar a solicitação de conexão desde que tenha permissões suficientes. Se você estiver se conectando a um recurso do Azure em outro diretório, você deve esperar que o proprietário desse recurso aprove sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está pendente de aprovação do proprietário do recurso do Link privado. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso do link privado. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do recurso do link privado, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Como gerenciar uma conexão de ponto final privado
As seções a seguir mostram como aprovar ou rejeitar uma conexão de ponto final privado. 

1. Faça login no [portal Azure](https://portal.azure.com).
1. Na barra de pesquisa, digite **tópicos de Event Grid** ou **domínios da Event Grid**.
1. Selecione o **tópico** ou **domínio** que deseja gerenciar.
1. Selecione a guia **Rede.**
1. Se houver alguma conexão pendente, você verá uma conexão listada com **Pendente** no estado de provisionamento. 

### <a name="to-approve-a-private-endpoint"></a>Para aprovar um ponto final privado
Você pode aprovar um ponto final privado que está no estado pendente. Para aprovar, siga estas etapas: 

> [!NOTE]
> As etapas mostradas nesta seção são principalmente para tópicos. Você pode usar etapas semelhantes para aprovar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que deseja aprovar e **selecione Aprovar** na barra de ferramentas.

    ![Ponto final privado - estado pendente](./media/configure-private-endpoints/pending.png)
1. Na caixa de diálogo **Aprovar conexão,** digite um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - aprovar](./media/configure-private-endpoints/approve.png)
1. Confirme se você vê o status do ponto final como **Aprovado**. 

    ![Ponto final privado - estado aprovado](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Para rejeitar um ponto final privado
Você pode rejeitar um ponto final privado que está no estado pendente ou estado aprovado. Para rejeitar, siga estas etapas: 

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para rejeitar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que deseja rejeitar e **selecione Rejeitar** na barra de ferramentas.

    ![Ponto final privado - rejeitar](./media/configure-private-endpoints/reject-button.png)
1. Na caixa de diálogo **Rejeitar conexão,** digite um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - rejeitar](./media/configure-private-endpoints/reject.png)
1. Confirme se você vê o status do ponto final como **Rejeitado**. 

    ![Ponto final privado - estado rejeitado](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Você não pode aprovar um ponto final privado no portal Azure uma vez que ele é rejeitado. 


## <a name="use-azure-cli"></a>Usar a CLI do Azure
Para criar um ponto final privado, use o método [de criação de ponto final privado da rede az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Para obter descrições dos parâmetros usados no exemplo, consulte a documentação para [criar o ponto final privado da rede az](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Alguns pontos a serem observados neste exemplo são: 

- Para, `private-connection-resource-id`especificar o ID de recurso do **tópico** ou **domínio**. O exemplo anterior usa o tipo: tópico.
- para `group-ids`, `topic` `domain`especificar ou . No exemplo anterior, `topic` é usado. 

Para excluir um ponto final privado, use o método de exclusão de ponto final privado da [rede az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos finais privados para **domínios**. 

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado
Aqui está um script de exemplo que cria os seguintes recursos do Azure:

- Resource group
- Rede virtual
- Subnet na rede virtual
- Tópico azure Event Grid (nível premium)
- Ponto final privado para o tema

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos finais privados para domínios.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Aprove uma conexão de ponto final privado
O trecho CLI da amostra a seguir mostra como aprovar uma conexão de ponto final privado. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto final privado
O trecho CLI da amostra a seguir mostra como rejeitar uma conexão de ponto final privado. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Usar o PowerShell
Esta seção mostra como criar um ponto final privado para um tópico ou domínio usando o PowerShell. 

### <a name="prerequisite"></a>Pré-requisito
Siga as instruções de [Como: Use o portal para criar um aplicativo azure AD e um princípio de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo do Azure Active Directory e anotar os valores para **ID de diretório (inquilino),** **ID de aplicativo (Cliente)** e **aplicativo (cliente) secreto**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparar token e cabeçalhos para chamadas de API REST 
Execute os seguintes comandos pré-requisitos para obter um token de autenticação para usar com chamadas e autorização da API REST e outras informações de cabeçalho. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Criar uma sub-rede com políticas de rede de ponto final desativadas

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Crie um tópico de grade de eventos com um ponto final privado

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos finais privados para **domínios**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Ao verificar se o ponto final foi criado, você deve ver o resultado semelhante ao seguinte:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Aprove uma conexão de ponto final privado
O trecho da amostra a seguir do PowerShell mostra como aprovar um ponto final privado. 

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para aprovar pontos finais privados para **domínios**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto final privado
O exemplo a seguir mostra como rejeitar um ponto final privado usando o PowerShell. Você pode obter o GUID para o ponto final privado a partir do resultado do comando GET anterior. 

> [!NOTE]
> Os passos mostrados nesta seção são para tópicos. Você pode usar etapas semelhantes para rejeitar pontos finais privados para **domínios**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Você pode aprovar a conexão mesmo depois de rejeitada via API. Se você usar o portal Azure, você não pode aprovar um ponto final que foi rejeitado. 

## <a name="next-steps"></a>Próximas etapas
Para saber como configurar configurações de firewall IP, consulte [Configurar firewall IP para tópicos ou domínios do Azure Event Grid](configure-firewall.md).