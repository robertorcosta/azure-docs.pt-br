---
title: Integre hubs de eventos do Azure com o serviço de link privado do Azure
description: Saiba como integrar hubs de eventos do Azure com o Serviço de Link Privado do Azure
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: cff1b3b79b34d3f0bed27a2ea50799185958a8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477844"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integre hubs de eventos do Azure com o Azure Private Link (Preview)
O Azure Private Link Service permite que você acesse os Serviços Do Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure hospedou serviços de clientes/parceiros em um **ponto final privado** em sua rede virtual.

Um ponto final privado é uma interface de rede que conecta você de forma privada e segura a um serviço alimentado pelo Azure Private Link. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, consulte [O que é o Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Esse recurso é suportado apenas com o nível **dedicado.** Para obter mais informações sobre o nível dedicado, consulte [Visão geral do Event Hubs Dedicated](event-hubs-dedicated-overview.md). 
>
> Este recurso está atualmente em **pré-visualização**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um namespace do Event Hubs com o Azure Private Link, você precisará das seguintes entidades ou permissões:

- Um namespace dos Hubs de Eventos.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou contribuinte para o namespace e a rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. Seu espaço de nome pode ser em uma região diferente.

Seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>Etapas
Se você já tiver um namespace do Event Hubs, você pode criar uma conexão de link privada seguindo estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **hubs de eventos**.
3. Selecione o **namespace** da lista à qual deseja adicionar um ponto final privado.
4. Selecione a guia **'Rede'** em **Configurações**.
5. Selecione a **guia Conexões de ponto final privado (visualização)** na parte superior da página. Se você não estiver usando um nível dedicado de Hubs de Eventos, você verá uma mensagem: **Conexões de ponto final privado em Hubs de eventos são suportadas apenas por namespaces criados em um cluster dedicado**.
6. Selecione o botão **+ Ponto final privado** na parte superior da página.

    ![Imagem](./media/private-link-service/private-link-service-3.png)
7. Na página **Noções Básicas,** siga estas etapas: 
    1. Selecione a **assinatura do Azure** na qual deseja criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Digite um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. Seu ponto final privado deve estar na mesma região que sua rede virtual, mas pode estar em uma região diferente fro o recurso de link privado ao que você está se conectando. 
    6. Selecione **A seguir: Botão de >de recursos** na parte inferior da página.

        ![Criar ponto final privado - página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recursos,** siga estas etapas:
    1. Para o método de conexão, se você selecionar **Conectar a um recurso Do Zure no meu diretório,** siga estas etapas: 
        1. Selecione a **assinatura do Azure** na qual existe **o namespace do Event Hubs.** 
        2. Para **o tipo de recurso,** selecione **Microsoft.EventHub/namespaces** para o **tipo Resource**.
        3. Em **'Recurso',** selecione um namespace do Event Hubs na lista de baixa. 
        4. Confirme se o **subrecurso Destino** está definido como **namespace**.
        5. Selecione **A seguir: Configuração >** botão na parte inferior da página. 
        
            ![Criar ponto final privado - página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se você selecionar **Conectar a um recurso do Azure por ID de recurso ou alias,** siga estas etapas:
        1. Digite o **ID de recurso** ou **alias**. Pode ser o ID de recurso ou alias que alguns compartilharam com você.
        2. Para **sub-recurso Target,** digite **namespace**. É o tipo de sub-recurso que seu ponto final privado pode acessar.
        3. (opcional) Digite uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto final privado.
        4. Em seguida, **selecione Próximo: Configuração >** botão na parte inferior da página.

            ![Criar ponto final privado - conecte usando id de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração,** você seleciona a sub-rede em uma rede virtual para onde deseja implantar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas as redes virtuais na assinatura e localização atualmente selecionadas estão listadas na lista de baixa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **A seguir: Tags >** botão na parte inferior da página. 

        ![Criar ponto final privado - página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que você deseja associar com o recurso de ponto final privado. Em seguida, **selecione 'Revisar + criar',** botão na parte inferior da página. 
11. Na **Revisão + criar,** revise todas as configurações e selecione **Criar** para criar o ponto final privado.
    
    ![Criar ponto final privado - revisar e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme se você vê a conexão de ponto final privado que você criou aparece na lista de pontos finais. Neste exemplo, o ponto final privado é aprovado automaticamente porque você está conectado a um recurso do Azure em seu diretório e tem permissões suficientes. 

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando o PowerShell
O exemplo a seguir mostra como usar o Azure PowerShell para criar uma conexão de ponto final privado. Ele não cria um cluster dedicado para você. Siga as etapas [deste artigo](event-hubs-dedicated-cluster-create-portal.md) para criar um cluster dedicado do Event Hubs. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Gerenciar pontos finais privados usando o portal Azure

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso para o qual você está criando um ponto final privado estiver em seu diretório, você poderá aprovar a solicitação de conexão desde que tenha permissões suficientes. Se você estiver se conectando a um recurso do Azure em outro diretório, você deve esperar que o proprietário desse recurso aprove sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está pendente de aprovação do proprietário do recurso do Link Privado. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso do link privado. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do recurso do link privado, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma conexão de ponto final privado

1. Entre no portal do Azure.
2. Na barra de pesquisa, digite **hubs de eventos**.
3. Selecione o **namespace** que deseja gerenciar.
4. Selecione a guia **Rede.**
5. Vá para a seção apropriada abaixo com base na operação que deseja: aprovar, rejeitar ou remover.

### <a name="approve-a-private-endpoint-connection"></a>Aprove uma conexão de ponto final privado
1. Se houver alguma conexão pendente, você verá uma conexão listada com **Pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **Aprovar.**

    ![Imagem](./media/private-link-service/approve-private-endpoint.png)
4. Na página **de conexão Aprovar,** adicione um comentário (opcional) e selecione **Sim**. Se você selecionar **Não,** nada acontece. 
5. Você deve ver o status da conexão de ponto final privado na lista alterada para **Approved**. 

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto final privado

1. Se houver alguma conexão de ponto final privado que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente, selecione a conexão e clique no botão **Rejeitar.**

    ![Imagem](./media/private-link-service/private-endpoint-reject-button.png)
2. Na página **de conexão Rejeitar,** digite um comentário (opcional) e selecione **Sim**. Se você selecionar **Não,** nada acontece. 
3. Você deve ver o status da conexão de ponto final privado na lista alterada para **Rejeitada**. 

### <a name="remove-a-private-endpoint-connection"></a>Remova uma conexão de ponto final privado

1. Para remover uma conexão de ponto final privado, selecione-a na lista e selecione **Remover** na barra de ferramentas.
2. Na página **Excluir conexão,** selecione **Sim** para confirmar a exclusão do ponto final privado. Se você selecionar **Não,** nada acontece.
3. Você deve ver o status alterado para **Desconectado**. Em seguida, você verá o ponto final desaparecer da lista.

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona

Você deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão se conectando ao namespace do Event Hubs em um endereço IP privado e que eles têm a integração de zona de DNS privada correta.

Primeiro, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **Rede:**

1. Especificar **rede virtual** e **sub-rede**. Você pode criar uma rede virtual ou selecionar uma existente. Se você estiver selecionando uma existente, verifique se a região é correspondente.
1. Especifique um recurso **IP público.**
1. No grupo de segurança da **rede NIC,** selecione **Nenhum**.
1. No **balanceamento Carga, selecione** **Não**.

Abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se você executar o comando ns lookup para resolver o endereço IP de um namespace do Event Hubs em um ponto final público, você verá um resultado que se parece com isso:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se você executar o comando ns lookup para resolver o endereço IP de um namespace do Event Hubs em um ponto final privado, você verá um resultado que se parece com isso:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preços**: Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: O Private Endpoint for Azure Event Hubs está em pré-visualização pública. O recurso está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por namespace do Event Hubs:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Azure Event Hubs](event-hubs-about.md)
