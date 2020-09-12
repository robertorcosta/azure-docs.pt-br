---
title: 'Conectar-se a uma VNet de uma VPN de computador-P2S e autenticação de certificado nativa do Azure: PowerShell'
description: Conecte clientes Windows e Mac OS X com segurança a uma rede virtual do Azure usando P2S e certificados autoassinados ou emitidos por autoridade de certificação. Este artigo usa o PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2b7522e4c1074c3c52e62453e815cce859a86148
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435755"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configurar uma conexão VPN ponto a site para uma VNet usando a autenticação de certificado nativa do Azure: PowerShell

Este artigo ajuda você a conectar clientes individuais que executem Windows, Linux ou Mac OS X a uma VNet do Azure com segurança. As conexões VPN Ponto a Site são úteis quando você deseja se conectar à rede virtual de um local remoto, como ao trabalhar de casa ou em uma conferência. Também é possível usar P2S em vez de uma VPN Site a Site, quando você tiver apenas alguns clientes que precisam se conectar a uma VNet. As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público. A P2S cria a conexão VPN no SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Para obter mais informações sobre conexões VPN Ponto a Site, consulte [Sobre VPN Ponto a Site](point-to-site-about.md).

![Conectar um computador a um diagrama de conexão de ponto para Site da rede virtual do Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Arquitetura

Conexões nativas de autenticação de certificado do Azure Ponto a Site usam os itens a seguir, os quais são configurados neste exercício:

* Gateway de VPN RouteBased.
* A chave pública (arquivo .cer) para um certificado raiz, que é carregado no Azure. Depois que o certificado é carregado, ele é considerado um certificado confiável e é usado para autenticação.
* Um certificado do cliente que é gerado a partir do certificado raiz. O certificado do cliente instalado em cada computador cliente que se conectará à VNet. Esse certificado é usado para autenticação do cliente.
* Uma configuração de cliente VPN. Os arquivos de configuração de cliente VPN contém as informações necessárias para o cliente se conectar à VNet. Os arquivos configuram o cliente VPN existente que é nativo do sistema operacional. Cada cliente que se conecta deve ser configurado usando as configurações nos arquivos de configuração.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

>[!NOTE]
> A maioria das etapas neste artigo pode usar Azure Cloud Shell. No entanto, para carregar a chave pública do certificado raiz, você deve usar o PowerShell localmente ou o portal do Azure.
>

### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Você pode usar os valores do exemplo para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo. As variáveis são definidas na seção [1](#declare) do artigo. Você pode usar as etapas como um passo a passo e usar os valores sem alterá-los, ou alterá-los para refletir seu ambiente.

* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16** e **10.254.0.0/16**<br>Este exemplo usa mais de um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para que o gateway de VPN funcione.
  * **Intervalo de endereços da GatewaySubnet: 192.168.200.0/24** 
* **Pool de endereços do cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente VPN.
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de recursos: TestRG**
* **Local: leste dos EUA**
* **Servidor DNS: Endereço IP** do servidor DNS que você deseja usar para a resolução de nome. (opcional)
* **Nome de GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="1-sign-in-and-set-variables"></a><a name="declare"></a>1. entre e defina variáveis

Nesta seção, entre e declare os valores usados para esta configuração. Os valores declarados são usados nos scripts de exemplo. Altere os valores para refletir seu próprio ambiente. Ou, você pode usar os valores declarados e percorrer as etapas como um exercício.

### <a name="sign-in"></a>Entrar

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Declarar variáveis

Declare as variáveis que você quer usar. Use o exemplo a seguir, substituindo os valores existentes pelos seus quando necessário. Se você fechar sua sessão do PowerShell/Cloud Shell a qualquer momento durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-configure-a-vnet"></a><a name="ConfigureVNet"></a>2. configurar uma VNet

1. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```
2. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada por você.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $besub = New-AzVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```
3. Crie a rede virtual.

   Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando a partir da sua rede virtual. Este exemplo um endereço IP privado, mas é provável que ele não seja o endereço IP do seu servidor DNS. Use seus próprios valores. O valor especificado é usado pelos recursos que são implantados para a rede virtual, não com a conexão de P2S no cliente de VPN.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Especifique as variáveis da rede virtual que você criou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```
5. Um gateway de VPN deve ter um endereço IP público. Você primeiro solicita o recurso de endereço IP e, em seguida, faz referência a ele ao criar seu gateway de rede virtual. O endereço IP é atribuído dinamicamente ao recurso quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. Você não pode solicitar uma atribuição de endereço IP Público Estático. No entanto, isso não significa que o endereço IP mudará depois de ter sido atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

   Solicite um endereço IP público atribuído dinamicamente.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-create-the-vpn-gateway"></a><a name="creategateway"></a>3. criar o gateway de VPN

Configurar e criar o gateway de rede virtual para sua rede virtual.

* O -GatewayType deve ser **Vpn** e o -VpnType deve ser **RouteBased**.
* O -VpnClientProtocols é usado para especificar os tipos de túneis que você deseja habilitar. As opções de túnel são **OpenVPN, SSTP** e **IKEv2**. Você pode optar por habilitar uma delas ou qualquer combinação com suporte. Se você quiser habilitar vários tipos, especifique os nomes separados por uma vírgula. OpenVPN e SSTP não podem ser habilitados juntos. O cliente strongSwan no Android e no Linux, e o cliente VPN IKEv2 nativo no iOS e no OSX usarão somente o túnel IKEv2 para se conectar. Os clientes Windows tentam o IKEv2 primeiro e, se isso gerar a conexão, retornarão ao SSTP. Você pode usar o cliente OpenVPN para se conectar ao tipo de túnel OpenVPN.
* O SKU ' básico ' do gateway de rede virtual não oferece suporte à autenticação IKEv2, OpenVPN ou RADIUS. Se você estiver planejando clientes Mac, se conecte à sua rede virtual, não use a SKU básica.
* Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do [SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md) selecionado. Este exemplo usa IKEv2.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="4-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>4. Adicionar o pool de endereços do cliente VPN

Depois que o gateway de VPN é criado, você pode adicionar o pool de endereços do cliente VPN. O pool de endereços de cliente VPN é o intervalo do qual os clientes VPN recebem um endereço IP ao se conectar. Use um intervalo de endereço IP privado que não coincida com o local de onde você se conecta ou com a rede virtual à qual você deseja se conectar. Neste exemplo, o pool de endereços de cliente VPN é declarado como uma [variável](#declare) na etapa 1.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="5-generate-certificates"></a><a name="Certificates"></a>5. gerar certificados

Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Você pode carregar as informações da chave públicas do certificado raiz no Azure. A chave pública é considerada “trusted” (confiável). Os certificados de cliente devem ser gerados a partir do certificado raiz confiável e, em seguida, em cada computador cliente no repositório de certificados de usuário/pessoal de certificados atual. O certificado é usado para autenticar o cliente quando ele inicia uma conexão de rede virtual. 

Se você usa certificados autoassinados, eles devem ser criados usando parâmetros específicos. Você pode criar um certificado autoassinado usando as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou, se não tiver o Windows 10, com o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que você siga as etapas nas instruções ao gerar os certificados raiz autoassinados e os certificados de cliente. Caso contrário, os certificados gerados não serão compatíveis com conexões P2S e você receberá um erro de conexão.

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. Obtenha o arquivo. cer para o certificado raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="2-generate-a-client-certificate"></a><a name="generate"></a>2. gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="6-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>6. carregar as informações de chave pública do certificado raiz

Verifique se a criação do gateway de VPN foi concluída. Depois de concluído, você pode carregar o arquivo. cer (que contém as informações de chave pública) para um certificado raiz confiável do Azure. Uma vez carregado o arquivo .cer, o Azure pode usá-lo para autenticar clientes com um certificado de cliente instalado gerado a partir de um certificado raiz confiável. Você pode carregar arquivos de certificado raiz confiável adicionais - até um total de 20 - posteriormente, se necessário.

>[!NOTE]
> Não é possível carregar o arquivo. cer usando Azure Cloud Shell. Você pode usar o PowerShell localmente em seu computador ou pode usar as etapas de [portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Declare a variável para o nome do certificado, substituindo o valor pelo seu.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```
2. Substitua o caminho do arquivo pelo seu e execute os cmdlets.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
   ```
3. Carregue as informações de chave pública para o Azure. Depois que as informações do certificado são carregadas, o Azure o considera um certificado raiz confiável. Ao carregar, verifique se você está executando o PowerShell localmente no seu computador ou, em vez disso, você pode usar as [etapas de portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Não é possível carregar usando Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
   ```

## <a name="7-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>7. instalar um certificado de cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

Verifique se o certificado do cliente foi exportado como um .pfx juntamente com a cadeia de certificado inteira (que é o padrão). Caso contrário, as informações do certificado raiz não estão presentes no computador cliente e este não será capaz de fazer a autenticação corretamente. 

Para as etapas de instalação, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="8-configure-the-native-vpn-client"></a><a name="clientconfig"></a>8. configurar o cliente VPN nativo

Os arquivos de configuração de cliente de VPN contêm configurações para definir os dispositivos a fim de se conectar a uma VNet através de uma conexão de P2S. Para obter instruções sobre como gerar e instalar arquivos de configuração de cliente de VPN, confira [Criar e instalar arquivos de configuração de cliente de VPN para configurações de PS2 de autenticação de certificado nativa do Azure ](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="9-connect-to-azure"></a><a name="connect"></a>9. conectar-se ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para conectar-se a partir de um cliente de VPN do Windows

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows do qual você está se conectando.
>
>

1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Clique em **Continuar** para usar privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se você vir uma tela **Selecionar Certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.

   ![Cliente VPN se conecta ao Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A conexão é estabelecida.

   ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Solucionar problemas de conexões P2S de cliente do Windows

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Para conectar-se a partir de um cliente de VPN do Mac

Na caixa de diálogo de Rede, localize o perfil de cliente que você deseja usar e, em seguida, clique em **Conectar**.
Verifique [ Instalar - Mac (OSX)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) para obter instruções detalhadas. Se você estiver tendo problemas para se conectar, verifique se que o gateway de rede virtual não está usando uma SKU básica. Não há suporte para a SKU básica para clientes Mac.

  ![Conexão Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Para verificar sua conexão

Essas instruções se aplicam a clientes do Windows.

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados são semelhantes a este exemplo:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Para conectar-se a uma máquina virtual

Essas instruções se aplicam a clientes do Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Para adicionar ou remover um certificado raiz

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado raiz, os clientes que possuem um certificado gerado pelo certificado raiz não podem fazer a autenticação e, portanto, não conseguem se conectar. Se você deseja que um clientes faça autenticação e se conecte, você precisa instalar um novo certificado de cliente gerado a partir de um certificado confiável (carregado) no Azure.

### <a name="to-add-a-trusted-root-certificate"></a><a name="addtrustedroot"></a>Para adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. As seguintes etapas o ajudarão a adicionar um certificado raiz:

#### <a name="method-1"></a><a name="certmethod1"></a>Método 1


Esse método é a maneira mais eficiente de carregar um certificado raiz. Ele requer cmdlets do Azure PowerShell instalados localmente em seu computador (e não o Azure Cloud Shell).

1. Prepare o arquivo .cer para upload:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
   ```
2. Carregue o arquivo. Você só pode carregar um arquivo por vez.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
   ```

3. Para verificar se o arquivo de certificado foi carregado:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

#### <a name="method-2---azure-portal"></a><a name="certmethod2"></a>Método 2 - portal do Azure

Esse método tem mais etapas que o Método 1, mas com o mesmo resultado. Ele foi incluído caso você precise exibir os dados do certificado. Ele requer cmdlets do Azure PowerShell instalados localmente em seu computador (e não o Azure Cloud Shell).

1. Crie e prepare o novo certificado raiz para adicionar ao Azure. Exporte a chave pública como X.509 codificado em Base 64 (.CER) e abra-a com um editor de texto. Copie os valores, conforme mostra o exemplo a seguir:

   ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

   > [!NOTE]
   > Ao copiar os dados do certificado, certifique-se de copiar o texto como uma linha contínua sem retornos de carro ou alimentações de linha. Talvez seja necessário modificar a exibição no editor de texto para 'Mostrar símbolo/Mostrar todos os caracteres' para ver os retornos de carro e alimentações de linha.
   >
   >

2. Especifique o nome do certificado e as informações de chave como uma variável. Substitua as informações por suas próprias, conforme mostrado no seguinte exemplo:

   ```azurepowershell
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
3. Adicionar o novo certificado raiz. Você pode adicionar apenas um certificado por vez.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
4. Você pode verificar se o novo certificado foi adicionado corretamente usando o exemplo a seguir:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

### <a name="to-remove-a-root-certificate"></a><a name="removerootcert"></a>Adicionar ou remover um certificado raiz

1. Declare as variáveis.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
2. Remova o certificado.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
3. Use o exemplo a seguir para verificar se o certificado foi removido com êxito.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-a-client-certificate"></a><a name="revoke"></a>Para revogar um certificado de cliente

É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticação.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="revoke-a-client-certificate"></a><a name="revokeclientcert"></a>Revogar um certificado de cliente

1. Recupere a impressão digital do certificado de cliente. Para obter mais informações, consulte [como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua. A cadeia de caracteres é declarada como uma variável na próxima etapa.
3. Declare as variáveis. Declare a impressão digital recuperada na etapa anterior.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
4. Adicione a impressão digital à lista de certificados revogados. Você verá "Êxito" quando a impressão digital tiver sido adicionada.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```
5. Verifique se a impressão digital foi adicionada à lista de certificados revogados.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```
6. Após a adição da impressão digital, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado recebem uma mensagem informando que o certificado não é mais válido.

### <a name="to-reinstate-a-client-certificate"></a><a name="reinstateclientcert"></a>Para restabelecer um certificado do cliente

Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1. Declare as variáveis. Declare a impressão digital correta para o certificado que você deseja restabelecer.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
2. Remova a impressão digital do certificado da lista de revogação de certificado.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```
3. Verifique se a impressão digital foi removida da lista revogada.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
