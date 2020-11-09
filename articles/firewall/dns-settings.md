---
title: Configurações de DNS do firewall do Azure
description: Você pode configurar o Firewall do Azure com o servidor DNS e as configurações de proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380939"
---
# <a name="azure-firewall-dns-settings"></a>Configurações de DNS do firewall do Azure

Você pode configurar um servidor DNS personalizado e habilitar o proxy DNS para o Firewall do Azure. Você pode definir essas configurações ao implantar o firewall ou posterior na página de **configurações de DNS** .

## <a name="dns-servers"></a>Servidores DNS

Um servidor DNS mantém e resolve nomes de domínio para endereços IP. Por padrão, o Firewall do Azure usa o DNS do Azure para resolução de nomes. A configuração do **servidor DNS** permite que você configure seus próprios servidores DNS para a resolução de nomes do firewall do Azure. Você pode configurar um único ou vários servidores.

> [!NOTE]
> Para firewalls do Azure gerenciados usando o Gerenciador de firewall do Azure, as configurações de DNS são definidas na política de firewall do Azure associada.

### <a name="configure-custom-dns-servers---azure-portal"></a>Configurar servidores DNS personalizados-portal do Azure

1. Em **configurações** do firewall do Azure, selecione **configurações de DNS**.
2. Em **servidores DNS** , você pode digitar ou adicionar servidores DNS existentes que foram especificados anteriormente em sua rede virtual.
3. Clique em **Salvar**.
4. O firewall agora direciona o tráfego DNS para os servidores DNS especificados para a resolução de nomes.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Servidores DNS":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Configurar servidores DNS personalizados-CLI do Azure

O exemplo a seguir atualiza o Firewall do Azure com servidores DNS personalizados usando CLI do Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> O comando `az network firewall` requer que a extensão de CLI do Azure `azure-firewall` seja instalada. Ele pode ser instalado usando o comando `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Configurar servidores DNS personalizados-Azure PowerShell

O exemplo a seguir atualiza o Firewall do Azure com servidores DNS personalizados usando Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy DNS

Você pode configurar o Firewall do Azure para atuar como um proxy DNS. Um proxy DNS atua como um intermediário para solicitações de DNS de máquinas virtuais de cliente para um servidor DNS. Se você configurar um servidor DNS personalizado, deverá habilitar o proxy DNS para evitar a incompatibilidade de resolução de DNS e habilitar a filtragem de FQDN em regras de rede.

Se você não habilitar o proxy DNS, as solicitações de DNS do cliente poderão viajar para um servidor DNS em um momento diferente ou retornar uma resposta diferente em comparação com a do firewall. O proxy DNS coloca o Firewall do Azure no caminho das solicitações do cliente para evitar inconsistências.

Há dois tipos de função de cache que ocorrem quando o Firewall do Azure é um proxy DNS:

- Cache positivo – a resolução de DNS foi bem-sucedida. O firewall usa o TTL (vida útil) do pacote ou objeto. 

- Cache negativo – a resolução DNS resulta em nenhuma resposta ou nenhuma resolução. O firewall armazena em cache essas informações por uma hora.

O proxy DNS armazena todos os endereços IP resolvidos dos FQDNs nas regras de rede. Como prática recomendada, use FQDNs que resolvem para um endereço IP.  

### <a name="dns-proxy-configuration"></a>Configuração de proxy DNS

A configuração de proxy DNS requer três etapas:
1. Habilite o proxy DNS nas configurações de DNS do firewall do Azure.
2. Opcionalmente, configure seu servidor DNS personalizado ou use o padrão fornecido.
3. Por fim, você deve configurar o endereço IP privado do firewall do Azure como um endereço DNS personalizado em suas configurações de servidor DNS de rede virtual. Isso garante que o tráfego DNS seja direcionado para o Firewall do Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Configurar o proxy DNS-portal do Azure

Para configurar o proxy DNS, você deve definir a configuração de servidores DNS da rede virtual para usar o endereço IP privado do firewall. Em seguida, habilite o proxy DNS nas **configurações de DNS** do firewall do Azure.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS da rede virtual 

1. Selecione a rede virtual na qual o tráfego DNS será roteado por meio do firewall do Azure.
2. Em **Configurações** , selecione **Servidores DNS**.
3. Selecione **personalizado** em **servidores DNS**.
4. Insira o endereço IP privado do firewall.
5. Clique em **Salvar**.
6. Reinicie as VMs que estão conectadas à rede virtual, para que as novas configurações do servidor DNS sejam atribuídas a elas. As VMs continuam a usar suas configurações de DNS atuais até que elas sejam reiniciadas.

##### <a name="enable-dns-proxy"></a>Habilitar proxy DNS

1. Selecione o Firewall do Azure.
2. Em **configurações** , selecione **configurações de DNS**.
3. Por padrão, o **proxy DNS** está desabilitado. Quando habilitado, o firewall escuta na porta 53 e encaminha as solicitações de DNS para os servidores DNS configurados.
4. Examine a configuração dos **servidores DNS** para certificar-se de que as configurações são apropriadas para o seu ambiente.
5. Clique em **Salvar**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Proxy DNS":::

#### <a name="configure-dns-proxy---azure-cli"></a>Configurar o proxy DNS-CLI do Azure

Você pode usar CLI do Azure para definir as configurações de proxy DNS no firewall do Azure e atualizar as redes virtuais para usar o Firewall do Azure como o servidor DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS da rede virtual

Este exemplo configura a VNet para usar o Firewall do Azure como servidor DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Habilitar proxy DNS

Este exemplo habilita o recurso de proxy DNS no firewall do Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Configurar o proxy DNS-Azure PowerShell

Você pode usar Azure PowerShell para definir as configurações de proxy DNS no firewall do Azure e atualizar as redes virtuais para usar o Firewall do Azure como o servidor DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS da rede virtual

 Este exemplo configura a VNet para usar o Firewall do Azure como servidor DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Habilitar proxy DNS

Este exemplo habilita o recurso de proxy DNS no firewall do Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Próximas etapas

[Filtragem de FQDN em regras de rede](fqdn-filtering-network-rules.md)
