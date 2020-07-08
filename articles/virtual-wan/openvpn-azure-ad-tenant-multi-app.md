---
title: 'WAN virtual: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: a1471226bcc22373904e65f95d0c07c2d5041f55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749828"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário do Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways.

Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para &reg; conexões de protocolo OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. criar uma nova configuração do P2S

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
   > Não use a ID do aplicativo do cliente de VPN do Azure nos comandos acima: ele concederá a todos os usuários acesso ao gateway. Use a ID dos aplicativos que você registrou.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Editar atribuição de Hub

1. Navegue até a folha **hubs** na WAN virtual.

2. Selecione o hub ao qual deseja associar a configuração do servidor de VPN e clique nas reticências (...).

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Clique em **Editar hub virtual**.

4. Marque a caixa de seleção **Incluir gateway ponto a site** e selecione a **Unidade de escala do gateway** desejada.

    ![novo site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Insira o **Pool de endereços** do qual os endereços IP serão atribuídos aos clientes VPN.

6. Clique em **Confirmar**.

7. Essa operação pode levar até 30 minutos para ser concluída.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. baixar perfil VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN Virtual, clique em **Configurações de VPN do usuário**.

2. Na parte superior da página, clique em **Baixar configurações de VPN do usuário**.

3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.

4. Use o arquivo de perfil para configurar os clientes VPN.

5. Extraia o arquivo zip baixado.

6. Navegue até a pasta "AzureVPN" descompactada.

7. Anote o local do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="9-configure-user-vpn-clients"></a>9. configurar clientes VPN de usuário

Para se conectar, você precisará baixar o Cliente VPN do Azure e importar o perfil do cliente VPN baixado nas etapas anteriores em cada computador que deseja se conectar à VNet.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para &reg; conexões de protocolo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o Cliente VPN do Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências (...) ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![excluir](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![excluir](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Para diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione as reticências (...) ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![diagnose](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![diagnose](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![diagnose](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![diagnose](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. exibir sua WAN virtual

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
