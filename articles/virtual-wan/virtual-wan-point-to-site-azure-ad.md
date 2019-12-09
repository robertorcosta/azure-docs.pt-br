---
title: Configurar a Autenticação do Azure AD para criar uma conexão ponto a site com o Azure | Microsoft Docs
description: Neste tutorial, saiba como configurar a Autenticação de Azure Active Directory para VPN de usuário.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: alzam
ms.openlocfilehash: 19aa029311584b5a9762691d24ed10c1666a032c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781723"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão de VPN do Usuário usando a WAN Virtual do Azure

Este tutorial mostra como configurar a Autenticação do Azure AD para a VPN de usuário na WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN ou OpenVPN. A Autenticação Azure Active Directory só está disponível para gateways usando o protocolo OpenVPN e clientes que executam o Windows.

Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

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

   ![WAN Virtual](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Local do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo:** Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
4. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
5. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.

## <a name="site"></a>Criar um hub virtual vazio

1. Em sua WAN Virtual, selecione Hubs e clique em **+Novo Hub**.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na página de criação do hub virtual, preencha os seguintes campos.

   **Região** – selecione a região na qual deseja implantar o hub virtual.

   **Nome** – digite o nome que deseja dar ao seu hub virtual.

   **Espaço de endereço privado do hub** – o intervalo de endereços do hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Clique em **Revisar + Criar**.
4. Na página **validação aprovada**, clique em **criar**.

## <a name="site"></a>Criar uma configuração P2S

Uma configuração P2S define os parâmetros para conexão de clientes remotos.

1. Defina as variáveis a seguir, substituindo os valores conforme necessário para o seu ambiente.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Execute os seguintes comandos para criar a configuração:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

## <a name="hub"></a>Editar atribuição de hub

1. Navegue até a folha **Hubs** na WAN Virtual.
2. Selecione o hub ao qual deseja associar a configuração do servidor de VPN e clique nas reticências (...).

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique em **Editar hub virtual**.
4. Marque a caixa de seleção **Incluir gateway ponto a site** e selecione a **Unidade de escala do gateway** desejada.

   ![novo site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Insira o **Pool de endereços** do qual os endereços IP serão atribuídos aos clientes VPN.
6. Clique em **Confirmar**.
7. Essa operação pode demorar até 30 minutos para ser concluída.

## <a name="device"></a>Baixar o perfil de VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN Virtual, clique em **Configurações de VPN do usuário**.
2. Na parte superior da página, clique em **Baixar configurações de VPN do usuário**.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

## <a name="configure-user-vpn-clients"></a>Configurar clientes VPN do usuário

Para se conectar, você precisa baixar o cliente VPN do Azure (versão prévia) e importar o perfil de cliente VPN que foi baixado nas etapas anteriores em cada computador que deseja se conectar à rede virtual.

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para baixar o Cliente VPN do Azure (versão prévia).

#### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![importar](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências (...) ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![excluir](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![excluir](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione as reticências (...) ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![diagnosticar](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

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
