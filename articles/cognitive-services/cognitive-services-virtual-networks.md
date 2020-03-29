---
title: Redes Virtuais
titleSuffix: Azure Cognitive Services
description: Configure a segurança da rede em camadas para os recursos dos serviços cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371215"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configure redes virtuais do Azure Cognitive Services

O Azure Cognitive Services fornece um modelo de segurança em camadas. Este modelo permite que você proteja suas contas de Serviços Cognitivos para um subconjunto específico de redes. Quando as regras da rede são configuradas, apenas aplicativos que solicitam dados sobre o conjunto especificado de redes podem acessar a conta. Você pode limitar o acesso aos seus recursos com a filtragem de solicitação. Permitindo apenas solicitações originárias de endereços IP especificados, faixas IP ou de uma lista de sub-redes em [Redes Virtuais Do Azure](../virtual-network/virtual-networks-overview.md). Se você estiver interessado nesta oferta, você precisará [solicitar acesso de pré-visualização](https://aka.ms/cog-svc-vnet-signup).

Um aplicativo que acessa um recurso de Serviços Cognitivos quando as regras da rede estão em vigor requer autorização. A autorização é suportada com credenciais [do Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ou com uma chave API válida.

> [!IMPORTANT]
> Ligar as regras de firewall para sua conta de Serviços Cognitivos bloqueia as solicitações recebidas de dados por padrão. Para permitir a realização de solicitações, uma das seguintes condições precisa ser atendida:
> * A solicitação deve ter origem em um serviço que opera dentro de uma Rede Virtual Azure (VNet) na lista de sub-rede permitida da conta de Serviços Cognitivos alvo. O ponto final nas solicitações originadas do VNet precisa ser definido como o [subdomínio personalizado](cognitive-services-custom-subdomains.md) da sua conta de Serviços Cognitivos.
> * Ou a solicitação deve se originar de uma lista permitida de endereços IP.
>
> Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger seu recurso de Serviços Cognitivos, você deve primeiro configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo tráfego de internet) por padrão. Em seguida, você deve configurar regras que concedam acesso ao tráfego a partir de VNets específicos. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos. Você também pode configurar regras para conceder acesso ao tráfego a partir de faixas públicas de endereços IP de internet selecionadas, permitindo conexões de clientes específicos da internet ou no local.

As regras de rede são aplicadas em todos os protocolos de rede para os Serviços Cognitivos Do Azure, incluindo REST e WebSocket. Para acessar dados usando ferramentas como os consoles de teste Do Azure, regras explícitas de rede devem ser configuradas. Você pode aplicar regras de rede aos recursos existentes dos Serviços Cognitivos ou quando criar novos recursos de Serviços Cognitivos. Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações.

## <a name="supported-regions-and-service-offerings"></a>Regiões e ofertas de serviços suportadas

O suporte à rede virtual para serviços cognitivos listados abaixo está limitado às regiões *Central US EUAP,* *Centro-Sul dos EUA,* *Leste dos EUA,* *OESTE DOS EUA 2,* *Norte da Europa,* Norte da *África do Sul,* *Europa Ocidental,* *Índia Central,* *Austrália Oriental,* *Oeste dos EUA*e NORTE *Gov Virginia* Azure. Se a oferta de serviços não estiver listada aqui, ela não suporta redes virtuais.

> [!div class="checklist"]
> * [Detector de Anomalias](./anomaly-detector/index.yml)
> * [Pesquisa Visual Computacional](./computer-vision/index.yml)
> * [Moderador de conteúdo](./content-moderator/index.yml)
> * [Visão Personalizada](./custom-vision-service/index.yml)
> * [Face](./face/index.yml)
> * [Reconhecimento de Formulários](./form-recognizer/index.yml)
> * [Luis](./luis/index.yml)
> * [Personalizador](./personalizer/index.yml)
> * [Análise de texto](./text-analytics/index.yml)
> * [Fabricante de QnA](./qnamaker/index.yml)

O suporte à rede virtual para serviços cognitivos listados abaixo é limitado às regiões *Central US EUAP,* *South Central US,* *East US*, West *US 2,* *Global*e US *Gov Virginia* Azure.
> [!div class="checklist"]
> * [Texto do Tradutor](./translator/index.yml)

## <a name="service-tags"></a>Marcas de serviço
Além de suportar pontos finais de serviço de rede virtual para os serviços acima, os Serviços Cognitivos também suportam uma tag de serviço para configuração de regras de rede de saída. Os seguintes serviços estão incluídos na tag de serviço CognitiveServicesManagement.
> [!div class="checklist"]
> * [Detector de Anomalias](./anomaly-detector/index.yml)
> * [Pesquisa Visual Computacional](./computer-vision/index.yml)
> * [Moderador de conteúdo](./content-moderator/index.yml)
> * [Visão Personalizada](./custom-vision-service/index.yml)
> * [Face](./face/index.yml)
> * [Reconhecimento de Formulários](./form-recognizer/index.yml)
> * [Luis](./luis/index.yml)
> * [Personalizador](./personalizer/index.yml)
> * [Análise de texto](./text-analytics/index.yml)
> * [Fabricante de QnA](./qnamaker/index.yml)
> * [Texto do Tradutor](./translator/index.yml)
> * [Serviço de Fala](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, os recursos dos Serviços Cognitivos aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações nas regras de rede pode afetar a capacidade de seus aplicativos de se conectarem aos Serviços Cognitivos do Azure. Definir a regra de rede padrão para **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede que **concedam** acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso. Se você permitir a listagem de endereços IP para sua rede local, certifique-se de adicionar todos os endereços IP públicos de saída possíveis da sua rede local.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerenciar as regras padrão de acesso à rede para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou a CLI do Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Vá para o recurso de Serviços Cognitivos que você deseja proteger.

1. Selecione o menu **DE GERENCIAMENTO DE RECURSOS** chamado Rede **Virtual**.

   ![Opção de rede virtual](media/vnet/virtual-network-blade.png)

1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes selecionadas**. Apenas com a configuração **de redes selecionadas,** desacompanhada por **redes Virtuais** configuradas ou **intervalos de endereços** - todo o acesso é efetivamente negado. Quando todo o acesso é negado, solicitações que tentam consumir o recurso dos Serviços Cognitivos não são permitidas. O portal Azure, Azure PowerShell ou Azure CLI ainda pode ser usado para configurar o recurso de Serviços Cognitivos.
1. Para permitir o tráfego de todas as redes, opte por permitir o acesso a **todas as redes**.

   ![Redes virtuais negam](media/vnet/virtual-network-deny.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [faça login](/powershell/azure/authenticate-azureps)ou **selecione Experimente.**

1. Exibir o status da regra padrão para o recurso Serviços Cognitivos.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça login](/cli/azure/authenticate-azure-cli)ou **selecione Experimente.**

1. Exibir o status da regra padrão para o recurso Serviços Cognitivos.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar recursos de Serviços Cognitivos para permitir o acesso apenas a partir de sub-redes específicas. As sub-redes permitidas podem pertencer a um VNet na mesma assinatura, ou em uma assinatura diferente, incluindo assinaturas pertencentes a um inquilino diferente do Azure Active Directory.

Habilite um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para os Serviços Cognitivos Do Azure dentro do VNet. O ponto final do serviço direciona o tráfego do VNet através de um caminho ideal para o serviço de Serviços Cognitivos Azure. As identidades da sub-rede e da rede virtual também são transmitidas a cada solicitação. Os administradores podem então configurar as regras de rede para o recurso Serviços Cognitivos que permitem que as solicitações sejam recebidas de sub-redes específicas em um VNet. Os clientes que têm acesso através dessas regras de rede devem continuar a cumprir os requisitos de autorização do recurso de Serviços Cognitivos para acessar os dados.

Cada recurso do Cognitive Services suporta até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Permissões necessárias

Para aplicar uma regra de rede virtual a um recurso de Serviços Cognitivos, o usuário deve ter as permissões apropriadas para que as sub-redes sejam adicionadas. A permissão necessária é a função *contribuinte* padrão ou a função *contribuinte de serviços cognitivos.* As permissões necessárias também podem ser adicionadas às definições de função personalizadas.

Os recursos dos Serviços Cognitivos e as redes virtuais concedidas podem estar em diferentes assinaturas, incluindo assinaturas que fazem parte de um inquilino diferente do Azure AD.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um inquilino diferente do Azure Active Directory atualmente só são suportadas através de APIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser visualizadas no portal.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar regras de rede virtuais para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou a CLI do Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Vá para o recurso de Serviços Cognitivos que você deseja proteger.

1. Selecione o menu **DE GERENCIAMENTO DE RECURSOS** chamado Rede **Virtual**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma regra de rede existente, em **redes virtuais,** selecione **Adicionar rede virtual existente**.

   ![Adicionar vNet existente](media/vnet/virtual-network-add-existing.png)

1. Selecione as opções **redes virtuais** e **subredes** e selecione **Ativar**.

   ![Adicionar detalhes de vNet existentes](media/vnet/virtual-network-add-existing-details.png)

1. Para criar uma nova rede virtual e conceder-lhe acesso, selecione **Adicionar nova rede virtual**.

   ![Adicionar novo vNet](media/vnet/virtual-network-add-new.png)

1. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **Criar**.

   ![Criar vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se um ponto final de serviço para o Azure Cognitive Services não foi configurado anteriormente para a rede virtual e sub-redes selecionadas, você pode configurá-lo como parte desta operação.
    >
    > Atualmente, apenas redes virtuais pertencentes ao mesmo inquilino do Azure Active Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma sub-rede em uma rede virtual pertencente a outro inquilino, use Powershell, CLI ou APIs REST.

1. Para remover uma regra de rede virtual ou sub-rede, selecione **...** para abrir o menu de contexto para a rede virtual ou sub-rede, e selecione **Remover**.

   ![Remover vNet](media/vnet/virtual-network-remove.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [faça login](/powershell/azure/authenticate-azureps)ou **selecione Experimente.**

1. Liste as regras da rede virtual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Habilite o ponto final de serviço para os Serviços Cognitivos Do Azure em uma rede e sub-rede virtuais existentes.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em um VNet pertencente a outro inquilino Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/assinaturas/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/sub-net-name".

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça login](/cli/azure/authenticate-azure-cli)ou **selecione Experimente.**

1. Liste as regras da rede virtual.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Habilite o ponto final de serviço para os Serviços Cognitivos Do Azure em uma rede e sub-rede virtuais existentes.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em um VNet pertencente a outro inquilino Azure AD, use um ID de sub-rede totalmente qualificado no formulário "/assinaturas/subscription-ID/resourceGroups/resourceGroup/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/sub-net-name".
    > 
    > Você pode usar o parâmetro **de assinatura** para recuperar o ID da sub-rede de um VNet pertencente a outro inquilino Azure AD.

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet

Você pode configurar recursos de Serviços Cognitivos para permitir o acesso a partir de faixas específicas de endereços IP públicos da Internet. Essa configuração concede acesso a serviços específicos e redes locais, bloqueando efetivamente o tráfego geral de internet.

Fornecer faixas de endereços de internet `16.17.18.0/24` permitidas usando [notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário ou como endereços IP individuais como `16.17.18.19`.

   > [!Tip]
   > Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede de IP são permitidas apenas para endereços IP de **Internet pública**. Intervalos de endereços IP reservados para redes privadas (conforme definido em [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos nas regras de IP. As redes privadas `10.*`incluem `172.16.*`  -  `172.31.*`endereços que começam com , e `192.168.*`.

   > [!NOTE]
   > As regras da rede IP não têm efeito sobre solicitações originárias da mesma região do Azure que o recurso serviços cognitivos. Use [as regras de rede virtual](#grant-access-from-a-virtual-network) para permitir solicitações da mesma região.

Só há suporte para endereços IPV4 no momento. Cada recurso do Cognitive Services suporta até 100 regras de rede IP, que podem ser combinadas com [regras de rede virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais ao seu recurso de Serviços Cognitivos com uma regra de rede IP, você deve identificar os endereços IP voltados para a internet usados pela sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando [o ExpressRoute](../expressroute/expressroute-introduction.md) no local para peering público ou peering da Microsoft, você precisará identificar os endereços IP NAT. Para peering público, cada circuito ExpressRoute por padrão usa dois endereços IP NAT. Cada um deles é aplicado ao tráfego de serviço sinuoso do Azure quando o tráfego entra no backbone da rede Microsoft Azure. Para peering da Microsoft, os endereços IP NAT que são usados são fornecidos pelo cliente ou fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP públicos do circuito do ExpressRoute de emparelhamento, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar as regras de rede IP para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou a CLI do Azure.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

1. Vá para o recurso de Serviços Cognitivos que você deseja proteger.

1. Selecione o menu **DE GERENCIAMENTO DE RECURSOS** chamado Rede **Virtual**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a um intervalo de IP da Internet, digite o endereço IP ou a faixa de endereço (em [formato CIDR)](https://tools.ietf.org/html/rfc4632)em **Firewall** > **Address Range**. Somente são aceitos endereços ip públicos válidos (não reservados).

   ![Adicionar intervalo IP](media/vnet/virtual-network-add-ip-range.png)

1. Para remover uma regra de rede <span class="docon docon-delete x-hidden-focus"></span> IP, selecione o ícone da lata de lixo ao lado da faixa de endereços.

   ![Excluir faixa IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [faça login](/powershell/azure/authenticate-azureps)ou **selecione Experimente.**

1. Liste as regras de rede IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça login](/cli/azure/authenticate-azure-cli)ou **selecione Experimente.**

1. Liste as regras de rede IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="next-steps"></a>Próximas etapas

* Explore os vários [Serviços Cognitivos do Azure](welcome.md)
* Saiba mais sobre os pontos [finais do serviço de rede virtual do Azure](../virtual-network/virtual-network-service-endpoints-overview.md)