---
title: 'WAN virtual: inquilino do Azure AD para diferentes grupos de usuários: autenticação Azure AD'
description: Você pode usar o P2S VPN para se conectar ao seu VNet usando a autenticação Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060709"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Crie um inquilino do Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao se conectar ao seu VNet, você pode usar autenticação baseada em certificados ou autenticação RADIUS. No entanto, quando você usa o protocolo Open VPN, você também pode usar a autenticação do Azure Active Directory. Se você quiser que diferentes usuários sejam capazes de se conectar a diferentes gateways, você pode registrar vários aplicativos em AD e vinculá-los a diferentes gateways.

Este artigo ajuda você a configurar um inquilino Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A autenticação Azure AD é&reg; suportada apenas para conexões de protocolo OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Crie uma nova configuração P2S

Uma configuração P2S define os parâmetros para conexão de clientes remotos.

1. Defina as variáveis a seguir, substituindo os valores conforme necessário para o seu ambiente.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Execute os seguintes comandos para criar a configuração:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Não use o ID de aplicativo do cliente Azure VPN nos comandos acima: Ele concederá a todos os usuários acesso ao gateway. Use o ID do aplicativo registrado.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Editar atribuição do hub

1. Navegue até a lâmina **Hubs** a WAN virtual.

2. Selecione o hub ao qual deseja associar a configuração do servidor de VPN e clique nas reticências (...).

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Clique em **Editar hub virtual**.

4. Marque a caixa de seleção **Incluir gateway ponto a site** e selecione a **Unidade de escala do gateway** desejada.

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Insira o **Pool de endereços** do qual os endereços IP serão atribuídos aos clientes VPN.

6. Clique em **Confirmar**.

7. Essa operação pode levar até 30 minutos para ser concluída.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Baixar o perfil VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN Virtual, clique em **Configurações de VPN do usuário**.

2. Na parte superior da página, clique em **Baixar configurações de VPN do usuário**.

3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.

4. Use o arquivo de perfil para configurar os clientes VPN.

5. Extrair o arquivo zip baixado.

6. Navegue até a pasta "AzureVPN" sem zíper.

7. Anote a localização do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração da conexão VPN e pode ser importado diretamente para o aplicativo Azure VPN Client. Você também pode distribuir este arquivo para todos os usuários que precisam se conectar por e-mail ou outros meios. O usuário precisará de credenciais Ad válidas do Azure para se conectar com sucesso.

## <a name="9-configure-user-vpn-clients"></a>9. Configurar clientes DE VPN do usuário

Para se conectar, você precisará baixar o Cliente VPN do Azure e importar o perfil do cliente VPN baixado nas etapas anteriores em cada computador que deseja se conectar à VNet.

> [!NOTE]
> A autenticação Azure AD é&reg; suportada apenas para conexões de protocolo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o Cliente VPN do Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![importar](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![importar](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![importar](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![importar](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![importar](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências (...) ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![excluir](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![excluir](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Para diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione as reticências (...) ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![diagnosticar](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Veja sua WAN virtual

1. Navegue até a WAN virtual.

2. Na página de visão geral, cada ponto do mapa representa um hub.

3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
