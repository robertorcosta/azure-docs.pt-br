---
title: Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure | Microsoft Docs
description: Configure a segurança de rede em camadas para sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 77ad8579f31ce900a67e2ba3ddc53a5b034b6d42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454662"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja e controle o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes corporativos exigem, com base no tipo e subconjunto de redes usadas. Quando as regras de rede são configuradas, apenas aplicativos que solicitam dados sobre o conjunto especificado de redes podem acessar uma conta de armazenamento. Você pode limitar o acesso à sua conta de armazenamento a solicitações originárias de endereços IP especificados, intervalos IP ou de uma lista de sub-redes em uma Rede Virtual Azure (VNet).

As contas de armazenamento têm um ponto final público acessível através da internet. Você também pode criar [pontos finais privados para sua conta de armazenamento,](storage-private-endpoints.md)que atribui um endereço IP privado do seu VNet à conta de armazenamento e protege todo o tráfego entre seu VNet e a conta de armazenamento por um link privado. O firewall de armazenamento do Azure fornece acesso de controle de acesso para o ponto final público da sua conta de armazenamento. Você também pode usar o firewall para bloquear todo o acesso através do ponto final público ao usar pontos finais privados. Sua configuração de firewall de armazenamento também permite que serviços de plataforma Azure confiáveis selecionem para acessar a conta de armazenamento com segurança.

Um aplicativo que acessa uma conta de armazenamento quando as regras da rede estão em vigor ainda requer a devida autorização para a solicitação. A autorização é suportada com credenciais do Azure Active Directory (Azure AD) para blobs e filas, com uma chave de acesso de conta válida ou com um token SAS.

> [!IMPORTANT]
> A apelação de regras de firewall para sua conta de armazenamento bloqueia solicitações recebidas de dados por padrão, a menos que as solicitações tenham origem em um serviço operando dentro de uma Rede Virtual (VNet) do Azure ou de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.
>
> Você pode conceder acesso aos serviços do Azure que operam a partir de um VNet, permitindo que o tráfego da sub-rede hospede a instância de serviço. Você também pode habilitar um número limitado de cenários através do mecanismo [Exceções](#exceptions) descrito abaixo. Para acessar os dados da conta de armazenamento através do portal Azure, você precisaria estar em uma máquina dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger sua conta de armazenamento, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo tráfego de internet) no ponto final público, por padrão. Em seguida, você deve configurar regras que concedam acesso ao tráfego a partir de VNets específicos. Você também pode configurar regras para conceder acesso ao tráfego a partir de faixas públicas de endereços IP de internet selecionadas, permitindo conexões de clientes específicos da internet ou no local. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos.

Você pode combinar regras de firewall que permitem o acesso a partir de redes virtuais específicas e de faixas públicas de endereçoip na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas às contas de armazenamento existentes ou ao criar novas contas de armazenamento.

As regras de firewall de armazenamento se aplicam ao ponto final público de uma conta de armazenamento. Você não precisa de regras de acesso ao firewall para permitir o tráfego para pontos finais privados de uma conta de armazenamento. O processo de aprovação da criação de um ponto final privado concede acesso implícito ao tráfego a partir da sub-rede que hospeda o ponto final privado.

As regras de rede são aplicadas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB. Para acessar dados usando ferramentas como o portal Azure, o Storage Explorer e o AZCopy, as regras explícitas de rede devem ser configuradas.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações. Os tokens SAS que concedem acesso a um serviço de endereço IP específico limitam o acesso do proprietário do token, mas não concedem um novo acesso além das regras de rede configuradas.

O tráfego de disco da máquina virtual (incluindo as operações de montagem e desmontagem e E/S de disco) não é afetado pelas regras de rede. O acesso REST a blobs de página é protegido pelas regras de rede.

As contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

Você pode usar discos não gerenciados nas contas de armazenamento com as regras de rede aplicadas às VMs de backup e restauração com a criação de uma exceção. Esse processo está documentado na seção [Exceções](#exceptions) deste artigo. Exceções de firewall não são aplicáveis com discos gerenciados, pois eles já são gerenciados pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações em regras de rede pode afetar a capacidade de seus aplicativos se conectarem ao Armazenamento do Azure. Definir a regra de rede padrão para **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede que **concedam** acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerar as regras de acesso à rede padrão para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes selecionadas**. Para permitir o tráfego de todas as redes, opte por permitir o acesso a **todas as redes**.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar contas de armazenamento para permitir o acesso apenas a partir de sub-redes específicas. As sub-redes permitidas podem pertencer a um VNet na mesma assinatura, ou aquelas em uma assinatura diferente, incluindo assinaturas pertencentes a um inquilino diferente do Azure Active Directory.

Habilitar um [Ponto de extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) do Armazenamento do Microsoft Azure dentro da VNet. O ponto final do serviço direciona o tráfego do VNet através de um caminho ideal para o serviço de armazenamento Azure. As identidades da sub-rede e da rede virtual também são transmitidas a cada solicitação. Os administradores podem então configurar as regras de rede para a conta de armazenamento que permitem que as solicitações sejam recebidas de sub-redes específicas em um VNet. Os clientes com o acesso concedido por meio dessas regras de rede devem continuar a atender aos requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento pode dar suporte a até 100 regras da rede virtual que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos de extremidade de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure. Ao usar pontos de extremidade de serviço com o Armazenamento do Microsoft Azure, este escopo cresce para incluir a [região emparelhada](/azure/best-practices-availability-paired-regions). Pontos de extremidade de serviço permitem continuidade durante um failover regional e acesso a instâncias de armazenamento com redundância geográfica somente leitura (RA-GRS). As regras de rede que concedem acesso de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Ao planejar a recuperação de desastre durante uma interrupção regional, você deve criar as VNets na região emparelhada com antecedência. Habilite pontos de extremidade de serviço para o Armazenamento do Microsoft Azure, com as regras de rede concedendo acesso dessas redes virtuais alternativas. Em seguida, aplica essas regras às contas de armazenamento com redundância geográfica.

> [!NOTE]
> Os Pontos de Extremidade de Serviço não se aplicam ao tráfego de fora da região da rede virtual e ao par da região designada. Você pode aplicar as regras de rede concedendo acesso das redes virtuais para as conta de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designada.

### <a name="required-permissions"></a>Permissões necessárias

Para aplicar uma regra da rede virtual a uma conta de armazenamento, o usuário deve ter permissão para as sub-redes sendo adicionadas. A permissão necessária é *Ingressar o Serviço em uma Sub-rede* e está incluída na função interna *Colaborador da conta de armazenamento*. Também podem ser adicionado às definições de função personalizada.

A conta de armazenamento e as redes virtuais concedidas podem estar em diferentes assinaturas, incluindo assinaturas que fazem parte de um inquilino ad azure diferente.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um inquilino diferente do Azure Active Directory atualmente só são suportadas através de APIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser visualizadas no portal.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar as regras da rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **Redes virtuais**, clique em **Adicionar rede virtual existente**, selecione **Redes virtuais** e as opções **Sub-redes** e clique **Adicionar**. Para criar uma nova rede virtual e conceder acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Se um ponto de extremidade de Armazenamento do Microsoft Azure não foi configurado anteriormente para a rede virtual selecionada e as sub-redes, você pode configurá-lo como parte dessa operação.
    >
    > Atualmente, apenas redes virtuais pertencentes ao mesmo inquilino do Azure Active Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma sub-rede em uma rede virtual pertencente a outro inquilino, use Powershell, CLI ou APIs REST.

1. Para remover uma regra de rede virtual ou sub-rede, clique em **...** para abrir o menu de contexto da rede virtual ou da sub-rede e clique em **Remover**.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Liste as regras da rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em um VNet pertencente a outro inquilino Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/assinaturas/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/sub-net-name".

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Liste as regras da rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet pertencente a outro inquilino Azure AD,\<use um\>ID de\<sub-rede\>totalmente qualificado no formulário "/assinaturas/ ID de assinatura /resourceGroups/ resourceGroup/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/sub-net-name".\<\>
    >
    > Você pode usar o parâmetro **de assinatura** para recuperar o ID da sub-rede de um VNet pertencente a outro inquilino Azure AD.

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet

Você pode configurar as contas de armazenamento para permitir acesso dos intervalos de endereço IP da Internet pública. Essa configuração garante acesso a serviços públicos baseados na Internet e redes locais e bloqueia o tráfego de Internet geral.

Forneça intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário *16.17.18.0/24* ou como endereços IP individuas como *16.17.18.19*.

   > [!NOTE]
   > Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede de IP são permitidas apenas para endereços IP de **Internet pública**. Intervalos de endereços IP reservados para redes privadas (conforme definido em [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos nas regras de IP. As redes privadas incluem endereços que começam com _10.*_, _172.16.*_ - _172.31.*_ e _192.168.*_.

   > [!NOTE]
   > As regras de rede IP não terão efeito sobre solicitações originadas da mesma região do Azure que a conta de armazenamento. Use [as regras de rede virtual](#grant-access-from-a-virtual-network) para permitir solicitações da mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento usam endereços IP privados do Azure para comunicação. Assim, você não pode restringir o acesso a serviços específicos do Azure com base na faixa de endereço IP de entrada pública.

Apenas endereços IPV4 são suportados para configuração de regras de firewall de armazenamento.

Cada conta de armazenamento suporta até 100 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para Internet usados por sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando [ExpressRoute](/azure/expressroute/expressroute-introduction) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para peering da Microsoft, os endereços IP NAT usados são fornecidos pelo cliente ou fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP públicos do circuito do ExpressRoute de emparelhamento, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar as regras de rede IP para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a um intervalo de IP da Internet, digite o endereço IP ou a faixa de endereço (no formato CIDR) em **Firewall** > **Address Range**.

1. Para remover uma regra de rede IP, clique no ícone de lixeira próximo da regra de endereço.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Liste as regras de rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Liste as regras de rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="exceptions"></a>Exceções

As regras da rede ajudam a criar um ambiente seguro para conexões entre seus aplicativos e seus dados para a maioria dos cenários. No entanto, alguns aplicativos dependem de serviços do Azure que não podem ser isolados exclusivamente através de regras de rede virtual ou endereço IP. Mas esses serviços devem ser concedidos ao armazenamento para permitir a funcionalidade completa do aplicativo. Em tais situações, você pode usar a configuração ***Permitir serviços microsoft confiáveis...*** para permitir que esses serviços acessem seus dados, logs ou análises.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis

Alguns serviços da Microsoft operam a partir de redes que não podem ser incluídas nas regras de sua rede. Você pode conceder a um subconjunto de serviços Microsoft confiáveis acesso à conta de armazenamento, mantendo as regras de rede para outros aplicativos. Esses serviços confiáveis usarão autenticação forte para se conectar à sua conta de armazenamento com segurança. Habilitamos dois modos de acesso confiável para serviços da Microsoft.

- Recursos de alguns serviços, **quando registrados em sua assinatura,** podem acessar sua conta de armazenamento na mesma **assinatura** para operações selecionadas, como escrever logs ou backup.
- Os recursos de alguns serviços podem ter acesso explícito à sua conta de armazenamento **atribuindo uma função RBAC** à sua identidade gerenciada atribuída ao sistema.


Quando você habilita a configuração **Permitir serviços Microsoft confiáveis...** os recursos dos seguintes serviços registrados na mesma assinatura que sua conta de armazenamento têm acesso a um conjunto limitado de operações conforme descrito:

| Serviço                  | Nome do provedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Serviço de Backup do Azure             | Microsoft.RecoveryServices | Execute backups e restaurações de discos não gerenciados em máquinas virtuais IAAS. (não é necessário para discos gerenciados). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para o Azure usando a Data Box. [Saiba mais](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Criação de imagem personalizada e instalação de artefato. [Saiba mais](/azure/devtest-lab/devtest-lab-overview). |
| Grade de Eventos do Azure         | Microsoft.EventGrid        | Habilite a publicação de eventos do Armazenamento de Blobs e permita que a Grade de Eventos publique em filas de armazenamento. Saiba mais sobre [eventos de Armazenamento de Blobs](/azure/event-grid/event-sources) e [publicação em filas](/azure/event-grid/event-handlers). |
| Hubs de eventos do Azure         | Microsoft.EventHub         | Arquivar dados com a Captura de Hubs de Evento. [Saiba mais](/azure/event-hubs/event-hubs-capture-overview). |
| Sincronização de Arquivos do Azure          | Microsoft.StorageSync      | Permite transformar seu servidor de arquivos on-prem em um cache para compartilhamentos de arquivos Do Azure. Permitindo sincronização de vários locais, recuperação rápida de desastres e backup no lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Provisionar o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Exportação de importação do Azure      | Microsoft.ImportExport     | Permite a importação de dados para o Azure e a exportação de dados do Azure usando o serviço Importação/Exportação. [Saiba mais](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.insights         | Permite a gravação de dados de monitoramento para uma conta de armazenamento protegida, incluindo logs de diagnóstico de recursos, logs de login e auditoria do Azure Active Directory e logs de auditoria do Microsoft Intune. [Saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Rede do Azure         | Microsoft.Network          | Armazenar e analisar os logs de tráfego de rede. [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Habilite a replicação para a recuperação de desastres de máquinas virtuais Azure IaaS ao usar contas de armazenamento, origem ou armazenamento de destino habilitadas para firewall.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A **configuração Permitir serviços Microsoft confiáveis...** também permite que uma instância específica dos serviços abaixo acesse a conta de armazenamento, se você atribuir explicitamente [uma função RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) à identidade [gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recurso. Neste caso, o escopo de acesso para a instância corresponde à função RBAC atribuída à identidade gerenciada.

| Serviço                        | Nome do provedor de recursos                 | Finalidade            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Pesquisa Cognitiva do Azure         | Microsoft.Search/searchServices        | Permite que os serviços de Pesquisa Cognitiva acessem contas de armazenamento para indexação, processamento e consulta. |
| Tarefas do Registro de Contêiner do Azure | Microsoft.ContainerRegistry/registries | As tarefas do ACR podem acessar contas de armazenamento ao construir imagens de contêiner. |
| Fábrica de dados do Azure             | Microsoft.DataFactory/factories        | Permite o acesso às contas de armazenamento através do tempo de execução do ADF. |
| Azure Data Share               | Microsoft.DataShare/contas           | Permite acesso a contas de armazenamento através do Data Share. |
| Aplicativos Lógicos do Azure               | Microsoft.Logic/workflows              | Permite que aplicativos lógicos acessem contas de armazenamento. [Saiba mais](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Serviço do Azure Machine Learning | Microsoft.MachineLearningServices      | Espaços de trabalho autorizados do Azure Machine Learning escrevem saída de experimentos, modelos e logs para armazenamento Blob e leitura dos dados. [Saiba mais](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| SQL Data Warehouse do Azure       | Microsoft.Sql                          | Permite a importação e exportação de dados de instâncias específicas do Banco de Dados SQL usando o PolyBase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Stream Analytics do Azure         | Microsoft.StreamAnalytics             | Permite que dados de um trabalho de streaming sejam gravados no armazenamento Blob. Esse recurso está atualmente na visualização. [Saiba mais](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/espaços de trabalho          | Permite o acesso aos dados no Azure Storage do Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Acesso a dados de análise de armazenamento

Em alguns casos, o acesso para ler as métricas e logs de diagnóstico é necessário de fora do limite de rede. Ao configurar o acesso de serviços confiáveis à conta de armazenamento, você pode permitir o acesso à leitura dos arquivos de registro, tabelas métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gerenciando exceções

Você pode gerenciar as exceções de regra da rede através do portal do Azure, PowerShell ou CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Em **Exceções,** selecione as exceções que deseja conceder.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os pontos finais de serviço da Azure Network em [pontos finais do Serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde-se na segurança do Armazenamento Azure no [guia de segurança do Azure Storage](../blobs/security-recommendations.md).
