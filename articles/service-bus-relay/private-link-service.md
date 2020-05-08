---
title: Integrar a retransmissão do Azure com o serviço de vínculo privado do Azure
description: Saiba como integrar a retransmissão do Azure com o serviço de vínculo privado do Azure
services: service-bus-relay
author: spelluru
ms.author: spelluru
ms.date: 05/07/2020
ms.service: service-bus-relay
ms.topic: article
ms.openlocfilehash: ad57ea4ddeab2fb2af0da68d199445d9737a67cd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984454"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Integrar a retransmissão do Azure com o link privado do Azure (versão prévia)
O **serviço de vínculo privado** do Azure permite que você acesse os serviços do Azure (por exemplo, retransmissão do Azure, barramento de serviço do Azure, hubs de eventos do Azure, armazenamento do azure e Azure Cosmos DB) e serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. Para obter mais informações, confira [O que é o Link Privado do Azure (versão prévia)?](../private-link/private-link-overview.md)

Um **ponto de extremidade privado** é uma interface de rede que permite que suas cargas de trabalho em execução em uma rede virtual se conectem de forma privada e segura a um serviço que tem um **recurso de link privado** (por exemplo, um namespace de retransmissão). O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado, portanto, não são necessários gateways, dispositivos NAT, ExpressRoute, conexões VPN ou endereços IP públicos. O tráfego entre sua rede virtual e o serviço atravessa a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode fornecer um nível de granularidade no controle de acesso, permitindo conexões a namespaces específicos de retransmissão do Azure. 


> [!IMPORTANT]
> Este recurso está atualmente em **Visualização**. 
>
> Atualmente, damos suporte a conexões de link privado em clientes do remetente. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicionar um ponto de extremidade privado usando portal do Azure

### <a name="prerequisites"></a>Pré-requisitos
Para integrar um namespace de retransmissão do Azure com o link privado do Azure (versão prévia), você precisará das seguintes entidades ou permissões:

- Um namespace de retransmissão do Azure.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou colaborador na rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. Seu namespace pode estar em uma região diferente.

Seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>Etapas
Para obter as instruções passo a passo sobre como criar um novo namespace de retransmissão do Azure e entidades nele, consulte [criar um namespace de retransmissão do Azure usando o portal do Azure](relay-create-namespace-portal.md).

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **retransmissões**.
3. Selecione o **namespace** na lista à qual você deseja adicionar um ponto de extremidade privado.
4. Selecione a guia **rede** em **configurações**.
5. Selecione a guia **conexões de ponto de extremidade privado (versão prévia)** na parte superior da página
6. Selecione o botão **+ ponto de extremidade privado** na parte superior da página.

    ![Botão Adicionar ponto de extremidade privado](./media/private-link-service/add-private-endpoint-button.png)
7. Na página **noções básicas** , siga estas etapas: 
    1. Selecione a **assinatura do Azure** na qual você deseja criar o ponto de extremidade privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto de extremidade privado.
    3. Insira um **nome** para o ponto de extremidade privado. 
    5. Selecione uma **região** para o ponto de extremidade privado. Seu ponto de extremidade privado deve estar na mesma região que sua rede virtual, mas pode estar em uma região diferente do namespace de retransmissão do Azure ao qual você está se conectando. 
    6. Selecione **Avançar:** o botão >de recursos na parte inferior da página.

        ![Criar ponto de extremidade privado-página noções básicas](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página de **recursos** , siga estas etapas:
    1. Para o método de conexão, se você selecionar **conectar a um recurso do Azure em meu diretório**, você tem acesso de proprietário ou colaborador ao namespace e esse namespace está no mesmo diretório que o ponto de extremidade privado, siga estas etapas: 
        1. Selecione a **assinatura do Azure** na qual o **namespace de retransmissão do Azure** existe. 
        2. Para **tipo de recurso**, selecione **Microsoft. Relay/namespaces** para o **tipo de recurso**.
        3. Para **recurso**, selecione um namespace de retransmissão na lista suspensa. 
        4. Confirme se o **subrecurso de destino** está definido como **namespace**.
        5. Selecione **Avançar: botão de configuração >** na parte inferior da página. 
        
            ![Criar ponto de extremidade privado – página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se você selecionar **conectar-se a um recurso do Azure por ID de recurso ou alias** porque o namespace não está no mesmo diretório que o ponto de extremidade privado, siga estas etapas:
        1. Insira a **ID do recurso** ou o **alias**. Pode ser a ID de recurso ou o alias que alguém compartilhou com você. A maneira mais fácil de obter a ID de recurso é navegar até o namespace de retransmissão do Azure na portal do Azure e copiar a parte do URI `/subscriptions/`a partir de. Aqui está um exemplo:`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Para o **subrecurso de destino**, insira **namespace**. É o tipo do subrecurso que seu ponto de extremidade privado pode acessar.
        3. adicional Insira uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto de extremidade particular.
        4. Em seguida, selecione o botão **Avançar: configuração >** na parte inferior da página.

            ![Criar ponto de extremidade privado-conectar usando a ID do recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **configuração** , selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. Somente as redes virtuais na assinatura e no local selecionados são listadas na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual que você selecionou. 
    3. Habilite a **integração com a zona DNS privada** se desejar integrar seu ponto de extremidade privado a uma zona DNS privada. 
    
        Para conectar-se de forma privada com seu ponto de extremidade particular, você precisa de um registro DNS. Recomendamos que você integre seu ponto de extremidade privado a uma **zona DNS privada**. Você também pode utilizar seus próprios servidores DNS ou criar registros DNS usando os arquivos de host em suas máquinas virtuais. Para obter mais informações, consulte [configuração de DNS do ponto de extremidade privado do Azure](../private-link/private-endpoint-dns.md). Neste exemplo, a opção **integrar com a zona DNS privada** é selecionada e uma zona DNS privada será criada para você. 
    3. Selecione **Avançar: marca >** botão na parte inferior da página. 

        ![Criar ponto de extremidade privado – página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **marcas** , crie quaisquer marcas (nomes e valores) que você deseja associar ao ponto de extremidade privado e à zona DNS privada (se você tiver habilitado a opção). Em seguida, selecione o botão **revisar + criar** na parte inferior da página. 
11. Na **revisão + criar**, examine todas as configurações e selecione **criar** para criar o ponto de extremidade privado.
    
    ![Criar ponto de extremidade privado – examinar e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na página **ponto de extremidade privado** , você pode ver o status da conexão de ponto de extremidade particular. Se você for o proprietário do namespace de retransmissão ou tiver a opção gerenciar o acesso e tiver selecionado **conectar-se a um recurso do Azure no meu diretório** para o **método de conexão**, a conexão do ponto de extremidade deverá ser **aprovada automaticamente**. Se ele estiver no estado **pendente** , consulte a seção [gerenciar pontos de extremidade privados usando portal do Azure](#manage-private-endpoints-using-azure-portal) .

    ![Página de ponto de extremidade particular](./media/private-link-service/private-endpoint-page.png)
13. Navegue de volta para a página de **rede** do **namespace**e alterne para a guia conexões de **ponto de extremidade privado (versão prévia)** . Você deve ver o ponto de extremidade privado que você criou. 

    ![Ponto de extremidade privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicionar um ponto de extremidade privado usando o PowerShell
O exemplo a seguir mostra como usar Azure PowerShell para criar uma conexão de ponto de extremidade privada para um namespace de retransmissão do Azure.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. O namespace de retransmissão do Azure pode estar em uma região diferente. E, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

## <a name="manage-private-endpoints-using-azure-portal"></a>Gerenciar pontos de extremidade privados usando o portal do Azure

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso (namespace de retransmissão) para o qual você está criando um ponto de extremidade privado estiver em seu diretório, você poderá aprovar a solicitação de conexão, desde que você tenha privilégios de gerenciamento sobre o namespace de retransmissão. Se você estiver se conectando a um namespace de retransmissão para o qual você não tem acesso de gerenciamento, você deve aguardar o proprietário desse recurso para aprovar sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está aguardando aprovação do proprietário do namespace de retransmissão do Azure. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do namespace de retransmissão do Azure. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do namespace de retransmissão do Azure, o ponto de extremidade privado torna-se informativo e deve ser excluído para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma conexão de ponto de extremidade privado

1. Entre no portal do Azure.
1. Na barra de pesquisa, digite **retransmissão**.
1. Selecione o **namespace** que você deseja gerenciar.
1. Selecione a guia **rede** .
5. Vá para a seção apropriada abaixo com base na operação que você deseja: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma conexão de ponto de extremidade particular

1. Se houver conexões pendentes, você verá uma conexão listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto de extremidade privado** que você deseja aprovar
3. Selecione o botão **aprovar** .

    ![Aprovar ponto de extremidade privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página **aprovar conexão** , insira um **Comentário**opcional e selecione **Sim**. Se você selecionar **não**, nada acontecerá. 

    ![Aprovar página de conexão](./media/private-link-service/approve-connection-page.png)
5. Você deve ver o status da conexão na lista alterada para **aprovada**.

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto de extremidade particular

1. Se houver quaisquer conexões de ponto de extremidade privadas que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente que foi aprovada anteriormente, selecione a conexão de ponto de extremidade e clique no botão **rejeitar** .

    ![Botão rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **rejeitar conexão** , insira um comentário opcional e selecione **Sim**. Se você selecionar **não**, nada acontecerá. 

    ![Rejeitar a página de conexão](./media/private-link-service/reject-connection-page.png)
3. Você deve ver o status da conexão na lista foi **rejeitada**.


### <a name="remove-a-private-endpoint-connection"></a>Remover uma conexão de ponto de extremidade privado

1. Para remover uma conexão de ponto de extremidade particular, selecione-a na lista e selecione **remover** na barra de ferramentas. 

    ![Botão Remover](./media/private-link-service/remove-endpoint.png)
2. Na página **excluir conexão** , selecione **Sim** para confirmar a exclusão do ponto de extremidade privado. Se você selecionar **não**, nada acontecerá. 

    ![Página excluir conexão](./media/private-link-service/delete-connection-page.png)
3. Você deve ver o status alterado para **desconectado**. Em seguida, você verá que o ponto de extremidade desaparecerá da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona
Você deve validar que os recursos dentro da mesma sub-rede do ponto de extremidade privado estão se conectando ao namespace de retransmissão do Azure por meio de seu endereço IP privado.

Para este teste, crie uma máquina virtual seguindo as etapas em [criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **rede** : 

1. Especifique a **rede virtual** e a **sub-rede**. Você deve selecionar a rede virtual na qual você implantou o ponto de extremidade privado.
2. Especifique um recurso de **IP público** .
3. Para **grupo de segurança de rede NIC**, selecione **nenhum**.
4. Para **balanceamento de carga**, selecione **não**.

Conecte-se à VM e abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Você deverá ver um resultado semelhante ao seguinte. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

### <a name="design-considerations"></a>Considerações sobre o design
- O ponto de extremidade privado para a retransmissão do Azure está em **Visualização pública**. 
- para obter informações sobre preço, confira [Preço do Link Privado do Azure (versão prévia)](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Limitações 
- Número máximo de pontos de extremidade privados por namespace de retransmissão do Azure: 64.
- Número máximo de namespaces de retransmissão do Azure com pontos de extremidade privados por assinatura: 64.
- As regras do NSG (grupo de segurança de rede) e as rotas definidas pelo usuário não se aplicam ao ponto de extremidade privado. Para obter mais informações, consulte [serviço de vínculo privado do Azure: limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure (versão prévia)](../private-link/private-link-service-overview.md)
- Saiba mais sobre a [retransmissão do Azure](relay-what-is-it.md)
