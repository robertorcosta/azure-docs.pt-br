---
title: Usar a WAN Virtual do Azure para criar uma conexão ponto a site com o Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN ponto a site com o Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 0319e3aec71d37b49a094861fdcbb3b96b6def67
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585427"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão de VPN do Usuário usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para obter mais informações sobre a WAN Virtual, consulte a [Visão Geral de WAN Virtual](virtual-wan-about.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um hub
> * Criar uma configuração P2S
> * Baixar um perfil de cliente VPN
> * Aplicar a configuração P2S a um hub
> * Conectar uma VNET a um hub
> * Fazer o download e aplicar a configuração do cliente VPN
> * Exibir a WAN virtual
> * Exibir a integridade dos recursos

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Criar uma WAN Virtual

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, clique em **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e clique em Enter.
2. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Criar WAN.
3. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Local do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo:** Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
4. Quando terminar de preencher os campos, selecione **Revisar + Criar**.
5. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.

## <a name="site"></a>Criar um hub virtual vazio

1. Em sua WAN Virtual, selecione Hubs e clique em **+Novo Hub**

   ![novo site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na página de criação do hub virtual, preencha os seguintes campos.

   **Região** – selecione a região na qual deseja implantar o hub virtual.

   **Nome** – digite o nome que deseja dar ao seu hub virtual.

   **Espaço de endereço privado do hub** – o intervalo de endereços do hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Clique em **Examinar + criar**
4. Na página **validação aprovada**, clique em **criar**

## <a name="site"></a>Criar uma configuração P2S

Uma configuração P2S define os parâmetros para conexão de clientes remotos.

1. Navegue até **Todos os recursos**.
2. Clique na WAN virtual criada.
3. Clique em **+Criar configuração de VPN do usuário** na parte superior da página para abrir a página **Criar nova configuração de VPN do usuário**.

   ![novo site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na página **Criar nova configuração de VPN do usuário**, preencha os campos a seguir:

   **Nome da configuração**: esse é o nome atribuído à sua configuração.

   **Tipo de túnel**: o protocolo a ser usado para o túnel.

   **Nome do certificado raiz**: um nome descritivo para o certificado.

   **Dados de certificado público** – dados do certificado X.509 codificado em Base 64.
  
   ![novo site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Clique em **Criar** para criar a configuração.

## <a name="hub"></a>Editar atribuição de hub

1. Navegue até a folha **Hubs** na WAN Virtual
2. Selecione o hub ao qual deseja associar a configuração do servidor de VPN e clique em **...**

   ![novo site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Clique em **Editar hub virtual**.
4. Marque a caixa de seleção **Incluir gateway ponto a site** e selecione a **Unidade de escala do gateway** desejada.

   ![novo site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Insira o **Pool de endereços** do qual os endereços IP serão atribuídos aos clientes VPN.
6. Clique em **Confirmar**
7. Essa operação pode demorar até 30 minutos para ser concluída.

## <a name="device"></a>Baixar o perfil de VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN Virtual, clique em **Configurações de VPN do usuário**.
2. Na parte superior da página, clique em **Baixar configurações de VPN do usuário**.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

### <a name="configure-user-vpn-clients"></a>Configurar clientes VPN do usuário
Use o perfil baixado para configurar os clientes de acesso remoto. O procedimento para cada sistema operacional é diferente. Siga as instruções corretas abaixo:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Faça o download e instale o cliente OpenVPN do site oficial.
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito pela guia Configurações de VPN do usuário no Portal do Azure ou em New-AzureRmVpnClientConfiguration no PowerShell.
3. Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Veja aqui como extrair a chave privada.
6. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7. Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
8. Clique com botão direito no ícone OpenVPN na bandeja do sistema e clique em conectar.

##### <a name="ikev2"></a>IKEv2

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'.
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em Mais informações e em Executar mesmo assim.
3. No computador cliente, navegue até Configurações de Rede e clique em VPN. A conexão VPN mostra o nome da rede virtual a que ele se conecta.
4. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para saber mais sobre como gerar certificados, consulte Gerar Certificados. Para saber mais sobre como instalar um certificado do cliente, consulte Instalar um certificado do cliente.

## <a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página de visão geral, cada ponto do mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub.
3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="viewhealth"></a>Exibir a integridade do recurso

1. Navegue até a WAN.
2. Na página de sua WAN, na seção **SUPORTE + Solução de problemas**, clique em **Integridade** e exiba seu recurso.


## <a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).