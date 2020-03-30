---
title: Integre o Ônibus de Serviço Azure com o Serviço de Link Privado Do Azure
description: Saiba como integrar o Azure Service Bus com o Azure Private Link Service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478000"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integrar o Ônibus de Serviço Azure com o Azure Private Link (Visualização)

O Azure Private Link Service permite que você acesse os serviços do Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e o Azure hospedou serviços de clientes/parceiros em um **ponto final privado** em sua rede virtual.

Um ponto final privado é uma interface de rede que conecta você de forma privada e segura a um serviço alimentado pelo Azure Private Link. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, consulte [O que é o Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Este recurso é suportado com o nível **premium** do Azure Service Bus. Para obter mais informações sobre o nível premium, consulte o artigo [de camadas de mensagens Service Bus Premium e Standard.](service-bus-premium-messaging.md)
>
> Este recurso está atualmente em **pré-visualização**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um namespace de Barra de Serviço com o Azure Private Link, você precisará das seguintes entidades ou permissões:

- Um espaço de nome do Ônibus de Serviço.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou contribuinte para o espaço de nome do Ônibus de Serviço e para a rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. O espaço de nome do Seu Ônibus de Serviço pode estar em uma região diferente. E, seu ponto final privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>etapas

Se você já tiver um namespace existente, você pode criar um ponto final privado seguindo estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **em Service Bus**.
3. Selecione o **namespace** da lista à qual deseja adicionar um ponto final privado.
4. Selecione a guia **'Rede'** em **Configurações**.
5. Selecione a **guia Conexões de ponto final privado (visualização)** na parte superior da página
6. Selecione o botão **+ Ponto final privado** na parte superior da página.

    ![Adicionar botão de ponto final privado](./media/private-link-service/private-link-service-3.png)
7. Na página **Noções Básicas,** siga estas etapas: 
    1. Selecione a **assinatura do Azure** na qual deseja criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Digite um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. Seu ponto final privado deve estar na mesma região que sua rede virtual, mas pode estar em uma região diferente fro o recurso de link privado ao que você está se conectando. 
    6. Selecione **A seguir: Botão de >de recursos** na parte inferior da página.

        ![Criar ponto final privado - página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recursos,** siga estas etapas:
    1. Para o método de conexão, se você selecionar **Conectar a um recurso Do Zure no meu diretório,** siga estas etapas:   
        1. Selecione a **assinatura do Azure** na qual existe **o namespace do seu Service Bus.** 
        2. Para **o tipo de recurso,** selecione **Microsoft.ServiceBus/namespaces** para o **tipo Resource**.
        3. Em **'Recurso',** selecione um namespace de barramento de serviço na lista de paradas. 
        4. Confirme se o **subrecurso Destino** está definido como **namespace**.
        5. Selecione **A seguir: Configuração >** botão na parte inferior da página. 
        
            ![Criar ponto final privado - página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se você selecionar **Conectar a um recurso do Azure por ID de recurso ou alias,** siga estas etapas:
        1. Digite o **ID de recurso** ou **alias**. Pode ser o ID de recurso ou alias que alguns compartilharam com você.
        2. Para **sub-recurso Target,** digite **namespace**. É o tipo de sub-recurso que seu ponto final privado pode acessar. 
        3. (opcional) Digite uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto final privado. 
        4. Em seguida, **selecione Próximo: Configuração >** botão na parte inferior da página. 

            ![Criar ponto final privado - conectar usando id de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração,** você seleciona a sub-rede em uma rede virtual para onde deseja implantar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas as redes virtuais na assinatura e localização atualmente selecionadas estão listadas na lista de baixa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **A seguir: Tags >** botão na parte inferior da página. 

        ![Criar ponto final privado - página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que você deseja associar com o recurso de ponto final privado. Em seguida, **selecione 'Revisar + criar',** botão na parte inferior da página. 
11. Na **Revisão + criar,** revise todas as configurações e selecione **Criar** para criar o ponto final privado.
    
    ![Criar ponto final privado - revisar e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme se o ponto final privado foi criado. Se você é o proprietário do recurso e selecionou **Conectar a um recurso do Azure na opção do diretório** para o método **Conexão,** a conexão de ponto final deve ser **aprovada automaticamente**. Se estiver no estado **pendente,** consulte os pontos finais privados gerenciar usando a seção [portal do Azure.](#manage-private-endpoints-using-azure-portal)

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando o PowerShell
O exemplo a seguir mostra como usar o Azure PowerShell para criar uma conexão de ponto final privado a um namespace do Service Bus.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. O espaço de nome do Seu Ônibus de Serviço pode estar em uma região diferente. E, seu ponto final privado usa um endereço IP privado em sua rede virtual.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
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
1. Na barra de pesquisa, digite **em Service Bus**.
1. Selecione o **namespace** que deseja gerenciar.
1. Selecione a guia **Rede.**
5. Vá para a seção apropriada abaixo com base na operação que deseja: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprove uma conexão de ponto final privado

1. Se houver alguma conexão pendente, você verá uma conexão listada com **Pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **Aprovar.**

    ![Aprovar ponto final privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página **de conexão Aprovar,** digite um **comentário**opcional e selecione **Sim**. Se você selecionar **Não,** nada acontece. 

    ![Aprovar página de conexão](./media/private-link-service/approve-connection-page.png)
5. Você deve ver o status da conexão na lista alterado para **Aprovado**. 

    ![Status da conexão - aprovado](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto final privado

1. Se houver alguma conexão de ponto final privado que você deseja rejeitar, se é uma solicitação pendente ou conexão existente que foi aprovada anteriormente, selecione a conexão de ponto final e clique no botão **Rejeitar.**

    ![Botão de rejeição](./media/private-link-service/private-endpoint-reject.png)
2. Na página **de conexão Rejeitar,** digite um comentário opcional e selecione **Sim**. Se você selecionar **Não,** nada acontece. 

    ![Rejeitar página de conexão](./media/private-link-service/reject-connection-page.png)
3. Você deve ver o status da conexão na lista alterada **Rejeitada**. 

    ![Ponto final rejeitado](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Remova uma conexão de ponto final privado

1. Para remover uma conexão de ponto final privado, selecione-a na lista e selecione **Remover** na barra de ferramentas. 

    ![Botão Remover](./media/private-link-service/remove-endpoint.png)
2. Na página **Excluir conexão,** selecione **Sim** para confirmar a exclusão do ponto final privado. Se você selecionar **Não,** nada acontece. 

    ![Excluir página de conexão](./media/private-link-service/delete-connection-page.png)
3. Você deve ver o status alterado para **Desconectado**. Em seguida, você verá o ponto final desaparecer da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona

Você deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão se conectando ao seu namespace de Bus de Serviço em um endereço IP privado e que eles têm a integração de zona dns privada correta.

Primeiro, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **Rede:**

1. Especificar **rede virtual** e **sub-rede**. Você pode criar uma rede virtual ou selecionar uma existente. Se você estiver selecionando uma existente, verifique se a região é correspondente.
1. Especifique um recurso **IP público.**
1. Para **o grupo de segurança da rede NIC,** selecione **Nenhum**.
1. Para **equilibrar carga,** selecione **Não**.

Abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Se você executar o comando ns lookup para resolver o endereço IP de um namespace de Barra de Serviço em um ponto final público, você verá um resultado que se parece com isso:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Se você executar o comando ns lookup para resolver o endereço IP de um namespace de Barra de Serviço em um ponto final privado, você verá um resultado que se parece com isso:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preços**: Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: O ponto final privado para o Ônibus de Serviço Azure está em pré-visualização pública. O recurso está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por espaço de nome do Service Bus:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Ônibus de Serviço Azure](service-bus-messaging-overview.md)
