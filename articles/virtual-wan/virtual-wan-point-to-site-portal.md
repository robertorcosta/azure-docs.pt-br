---
title: Usar a WAN Virtual do Azure para criar uma conexão ponto a site com o Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN ponto a site com o Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560710"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão de VPN do Usuário usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para obter mais informações sobre a WAN Virtual, consulte a [Visão Geral de WAN Virtual](virtual-wan-about.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Especificar servidores de DNS
> * Baixar um perfil de cliente VPN
> * Exibir a WAN virtual

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [início rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, selecione **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e selecione **Enter**.
1. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, selecione **Criar** para abrir a página Criar WAN.
1. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Localização do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo:** Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
1. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
1. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração P2S define os parâmetros para conexão de clientes remotos.

1. Navegue até **Todos os recursos**.
1. Selecione a WAN Virtual que você criou.
1. Selecione **+Criar configuração de VPN do usuário** na parte superior da página para abrir a página **Criar configuração de VPN do usuário**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Configurações de VPN de Usuário":::

1. Na página **Criar nova configuração de VPN do usuário**, preencha os campos a seguir:

   * **Nome da configuração**: esse é o nome atribuído à sua configuração.
   * **Tipo de túnel**: o protocolo a ser usado para o túnel.
   * **Nome do certificado raiz**: um nome descritivo para o certificado.
   * **Dados de certificado público** – dados do certificado X.509 codificado em Base 64.
  
1. Selecione **Criar** para criar a configuração.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Criar um hub com o gateway ponto a site

1. Em sua WAN Virtual, selecione Hubs e **+Novo Hub**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="novo hub":::

1. Na página de criação do hub virtual, preencha os seguintes campos.

   * **Região** – selecione a região na qual deseja implantar o hub virtual.
   * **Nome** – digite o nome que deseja dar ao seu hub virtual.
   * **Espaço de endereço privado do hub** – o intervalo de endereços do hub na notação CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="criar hub virtual":::

1. Na guia Ponto a site, preencha os seguintes campos:

   * **Unidades de escala do gateway** – que representa a capacidade agregada do Gateway de VPN do Usuário.
   * **Configuração de ponto a site** – que você criou na etapa anterior.
   * **Pool de endereços do cliente** – para os usuários remotos.
   * **IP do Servidor DNS Personalizado**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="hub com ponto a site":::

1. Selecione **Examinar + criar**.
1. Na página **validação aprovada**, selecione **Criar**.

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar servidor DNS

Os gateways de VPN de Usuário da WAN Virtual permitem especificar até cinco servidores DNS. Você pode configurá-lo durante o processo de criação do hub ou modificá-lo posteriormente. Para fazer isso, localize o hub virtual. Em **VPN de Usuário (ponto a site)** , clique em configurar e insira os endereços IP do servidor DNS nas caixas de texto **Servidores DNS personalizados**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Baixar o perfil de VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN Virtual, selecione **Configurações de VPN do usuário**.
2. Na parte superior da página, selecione **Baixar configurações de VPN do usuário**. O download de uma configuração de nível de WAN fornece um perfil de VPN do Usuário baseado no Gerenciador de Tráfego interno. Para obter mais informações sobre perfis globais ou perfis baseados em hub, confira estes [Perfis de hub](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Cenários de failover são simplificados com o perfil global.

   Se, por algum motivo, um hub não estiver disponível, o gerenciamento de tráfego interno fornecido pelo serviço garantirá a conectividade por meio de um Hub diferente com os recursos do Azure para usuários de ponto a site. Você sempre pode baixar uma configuração de VPN específica do hub navegando até o hub específico. Em **VPN de Usuário (ponto a site)** , baixe o perfil de **VPN de Usuário** do hub virtual.

1. Depois que o arquivo for criado, selecione o link para baixá-lo.
1. Use o arquivo de perfil para configurar os clientes VPN.

### <a name="configure-user-vpn-clients"></a>Configurar clientes VPN do usuário

Use o perfil baixado para configurar os clientes de acesso remoto. O procedimento para cada sistema operacional é diferente. Siga as instruções corretas abaixo:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Faça o download e instale o cliente OpenVPN do site oficial.
1. Faça o download do perfil VPN para o gateway. Isso pode ser feito pela guia Configurações de VPN do usuário no Portal do Azure ou em New-AzureRmVpnClientConfiguration no PowerShell.
1. Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
1. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Para ver as etapas, confira [Como exportar um certificado para obter a chave pública codificada.](certificates-point-to-site.md)
1. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Para ver as etapas, confira [Como extrair a chave privada](howto-openvpn-clients.md#windows).
1. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
1. Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
1. Clique com o botão direito do mouse no ícone OpenVPN na bandeja do sistema e selecione **conectar**.

##### <a name="ikev2"></a>IKEv2

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'.
1. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, selecione **Mais informações** e **Executar mesmo assim**.
1. No computador cliente, navegue até **Configurações de Rede** e selecione **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.
1. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](certificates-point-to-site.md). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
1. Na página **Visão Geral**, cada ponto no mapa representa um hub.
1. Na seção **Hubs e conexões**, você pode ver o status do hub, o site, a região, o status de conexão de VPN e os bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
