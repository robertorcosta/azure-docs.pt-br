---
title: Integrar o barramento de serviço do Azure com o serviço de vínculo privado do Azure
description: Saiba como integrar o barramento de serviço do Azure com o serviço de vínculo privado do Azure
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: 33e6ce1d5feb50080b00fcbecdeb9e512980eab6
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141941"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integrar o barramento de serviço do Azure com o link privado do Azure (versão prévia)

O serviço de vínculo privado do Azure permite que você acesse os serviços do Azure (por exemplo, barramento de serviço do Azure, armazenamento do Azure e Azure Cosmos DB) e serviços hospedados de cliente/parceiro do Azure em um **ponto de extremidade privado** em sua rede virtual.

Um ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

>[!WARNING]
> A implementação de pontos de extremidade privados pode impedir que outros serviços do Azure interajam com o barramento de serviço.
>
> Os serviços confiáveis da Microsoft não têm suporte ao usar redes virtuais.
>
> Cenários comuns do Azure que não funcionam com Redes Virtuais (observe que a lista **NÃO** é exaustiva):
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
>
> Os serviços da Microsoft abaixo devem estar em uma rede virtual
> - Serviço de aplicativo do Azure
> - Funções do Azure

> [!IMPORTANT]
> Esse recurso é compatível com a camada **Premium** do barramento de serviço do Azure. Para obter mais informações sobre a camada Premium, consulte o artigo sobre as [camadas de sistema de mensagens Premium e Standard do barramento de serviço](service-bus-premium-messaging.md) .
>
> Este recurso está atualmente em **Visualização**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicionar um ponto de extremidade privado usando portal do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um namespace do barramento de serviço com o link privado do Azure, você precisará das seguintes entidades ou permissões:

- Um namespace do barramento de serviço.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou colaborador para o namespace do barramento de serviço e para a rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. O namespace do barramento de serviço pode estar em uma região diferente. E, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>etapas

Se você já tiver um namespace existente, poderá criar um ponto de extremidade privado seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **barramento de serviço**.
3. Selecione o **namespace** na lista à qual você deseja adicionar um ponto de extremidade privado.
4. Selecione a guia **rede** em **configurações**.
5. Selecione a guia **conexões de ponto de extremidade privado (versão prévia)** na parte superior da página
6. Selecione o botão **+ ponto de extremidade privado** na parte superior da página.

    ![Botão Adicionar ponto de extremidade privado](./media/private-link-service/private-link-service-3.png)
7. Na página **noções básicas** , siga estas etapas: 
    1. Selecione a **assinatura do Azure** na qual você deseja criar o ponto de extremidade privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto de extremidade privado.
    3. Insira um **nome** para o ponto de extremidade privado. 
    5. Selecione uma **região** para o ponto de extremidade privado. Seu ponto de extremidade privado deve estar na mesma região que sua rede virtual, mas pode estar em uma região diferente do recurso de link privado ao qual você está se conectando. 
    6. Selecione **Avançar:** o botão >de recursos na parte inferior da página.

        ![Criar ponto de extremidade privado-página noções básicas](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página de **recursos** , siga estas etapas:
    1. Para o método de conexão, se você selecionar **conectar a um recurso do Azure em meu diretório**, siga estas etapas:   
        1. Selecione a **assinatura do Azure** na qual o **namespace do barramento de serviço** existe. 
        2. Para **tipo de recurso**, selecione **Microsoft. ServiceBus/namespaces** para o **tipo de recurso**.
        3. Para **recurso**, selecione um namespace do barramento de serviço na lista suspensa. 
        4. Confirme se o **subrecurso de destino** está definido como **namespace**.
        5. Selecione **Avançar: botão de configuração >** na parte inferior da página. 
        
            ![Criar ponto de extremidade privado – página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se você selecionar **conectar-se a um recurso do Azure por ID de recurso ou alias**, siga estas etapas:
        1. Insira a **ID do recurso** ou o **alias**. Pode ser a ID de recurso ou o alias que alguém compartilhou com você. A maneira mais fácil de obter a ID do recurso é navegar até o namespace do barramento de serviço no portal do Azure e copiar a parte do URI a `/subscriptions/`partir de. Consulte a imagem a seguir para obter um exemplo. 
        2. Para o **subrecurso de destino**, insira **namespace**. É o tipo do subrecurso que seu ponto de extremidade privado pode acessar. 
        3. adicional Insira uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto de extremidade particular. 
        4. Em seguida, selecione o botão **Avançar: configuração >** na parte inferior da página. 

            ![Criar ponto de extremidade privado-conectar usando a ID do recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **configuração** , selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. Somente as redes virtuais na assinatura e no local selecionados são listadas na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual que você selecionou. 
    3. Selecione **Avançar: marca >** botão na parte inferior da página. 

        ![Criar ponto de extremidade privado – página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **marcas** , crie quaisquer marcas (nomes e valores) que você deseja associar ao recurso de ponto de extremidade privado. Em seguida, selecione o botão **revisar + criar** na parte inferior da página. 
11. Na **revisão + criar**, examine todas as configurações e selecione **criar** para criar o ponto de extremidade privado.
    
    ![Criar ponto de extremidade privado – examinar e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme se o ponto de extremidade privado foi criado. Se você for o proprietário do recurso e tiver selecionado **conectar-se a um recurso do Azure na opção meu diretório** para o **método de conexão**, a conexão do ponto de extremidade deverá ser **aprovada automaticamente**. Se ele estiver no estado **pendente** , consulte a seção [gerenciar pontos de extremidade privados usando portal do Azure](#manage-private-endpoints-using-azure-portal) .

    ![Ponto de extremidade privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicionar um ponto de extremidade privado usando o PowerShell
O exemplo a seguir mostra como usar Azure PowerShell para criar uma conexão de ponto de extremidade privada para um namespace do barramento de serviço.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. O namespace do barramento de serviço pode estar em uma região diferente. E, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

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


## <a name="manage-private-endpoints-using-azure-portal"></a>Gerenciar pontos de extremidade privados usando o portal do Azure

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso para o qual você está criando um ponto de extremidade privado estiver em seu diretório, você poderá aprovar a solicitação de conexão desde que tenha permissões suficientes. Se você estiver se conectando a um recurso do Azure em outro diretório, deverá aguardar o proprietário desse recurso para aprovar sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está pendente de aprovação do proprietário do recurso do Link Privado. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso do link privado. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do recurso do link privado, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma conexão de ponto de extremidade privado

1. Entre no portal do Azure.
1. Na barra de pesquisa, digite **barramento de serviço**.
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

    ![Status da conexão-aprovado](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma conexão de ponto de extremidade particular

1. Se houver quaisquer conexões de ponto de extremidade privadas que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente que foi aprovada anteriormente, selecione a conexão de ponto de extremidade e clique no botão **rejeitar** .

    ![Botão rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **rejeitar conexão** , insira um comentário opcional e selecione **Sim**. Se você selecionar **não**, nada acontecerá. 

    ![Rejeitar a página de conexão](./media/private-link-service/reject-connection-page.png)
3. Você deve ver o status da conexão na lista foi **rejeitada**. 

    ![Ponto de extremidade rejeitado](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Remover uma conexão de ponto de extremidade privado

1. Para remover uma conexão de ponto de extremidade particular, selecione-a na lista e selecione **remover** na barra de ferramentas. 

    ![Botão Remover](./media/private-link-service/remove-endpoint.png)
2. Na página **excluir conexão** , selecione **Sim** para confirmar a exclusão do ponto de extremidade privado. Se você selecionar **não**, nada acontecerá. 

    ![Página excluir conexão](./media/private-link-service/delete-connection-page.png)
3. Você deve ver o status alterado para **desconectado**. Em seguida, você verá que o ponto de extremidade desaparecerá da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona

Você deve validar se os recursos dentro da mesma sub-rede do recurso de ponto de extremidade privado estão se conectando ao namespace do barramento de serviço por um endereço IP privado e se eles têm a integração de zona DNS privada correta.

Primeiro, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **rede** :

1. Especifique a **rede virtual** e a **sub-rede**. Você pode criar uma rede virtual ou selecionar uma existente. Se você estiver selecionando uma existente, verifique se a região é correspondente.
1. Especifique um recurso de **IP público** .
1. Para **grupo de segurança de rede NIC**, selecione **nenhum**.
1. Para **balanceamento de carga**, selecione **não**.

Abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Se você executar o comando de pesquisa NS para resolver o endereço IP de um namespace do barramento de serviço em um ponto de extremidade público, você verá um resultado parecido com este:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Se você executar o comando de pesquisa NS para resolver o endereço IP de um namespace do barramento de serviço em um ponto de extremidade privado, você verá um resultado parecido com este:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preço**: Para obter informações sobre preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: o ponto de extremidade privado para o barramento de serviço do Azure está em visualização pública. O recurso está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos de extremidade privados por namespace do barramento de serviço**: 120.

Para saber mais, confira [Serviço de Link Privado do Azure: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre o [barramento de serviço do Azure](service-bus-messaging-overview.md)
