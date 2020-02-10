---
title: 'Gateway de VPN: cliente VPN para conexões P2S do protocolo OpenVPN: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: alzam
ms.openlocfilehash: 045d1cad130adad34d74009b34b193ce0d3d4dc9
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110565"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configurar um cliente VPN para conexões de protocolo P2S OpenVPN: autenticação do Azure AD

Este artigo ajuda você a configurar um cliente VPN para se conectar a uma rede virtual usando VPN ponto a site e autenticação Azure Active Directory. Antes de poder se conectar e autenticar usando o Azure AD, você deve primeiro configurar seu locatário do Azure AD. Para obter mais informações, consulte [configurar um locatário do Azure ad](openvpn-azure-ad-tenant.md).

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

## <a name="profile"></a>Trabalhando com perfis de cliente

Para se conectar, você precisa baixar o cliente VPN do Azure e configurar um perfil de cliente VPN em todos os computadores que queiram se conectar à VNet. Você pode criar um perfil de cliente em um computador, exportá-lo e, em seguida, importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o cliente VPN do Azure.

### <a name="cert"></a>Para criar um perfil de cliente baseado em certificado

Ao trabalhar com um perfil baseado em certificado, verifique se os certificados apropriados estão instalados no computador cliente. Para obter mais informações sobre certificados, consulte [instalar certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O segredo do servidor pode ser exportado no perfil do cliente VPN P2S.  As instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="export"></a>Para exportar e distribuir um perfil de cliente

Quando tiver um perfil de trabalho e precisar distribuí-lo para outros usuários, você poderá exportá-lo usando as seguintes etapas:

1. Realce o perfil do cliente VPN que você deseja exportar, selecione **...** e, em seguida, selecione **Exportar**.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione o local no qual você deseja salvar esse perfil, deixe o nome do arquivo como está e, em seguida, selecione **salvar** para salvar o arquivo XML.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![importar](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![importar](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![importar](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![importar](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![importar](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Criar uma conexão

1. Na página, selecione **+** e **+ Adicionar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de conexão. Se você não tiver certeza dos valores, contate o administrador. Depois de preencher os valores, selecione **salvar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Conectar** para se conectar à VPN.

    ![conexão](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais apropriadas e, em seguida, selecione **continuar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez conectado com êxito, o ícone ficará verde e informará **conectado**.

    ![conexão](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Para se conectar automaticamente

Essas etapas ajudam a configurar sua conexão para se conectar automaticamente com o Always on.

1. Na home page do cliente VPN, selecione configurações de **VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo Alternar aplicativos.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verifique se a conexão que você deseja definir ainda não está conectada e, em seguida, realce o perfil e marque a caixa de seleção **conectar automaticamente** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **conectar** para iniciar a conexão VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione o **...** ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como fazer adicionar sufixos DNS ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcas de **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >**

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para obter mais informações, consulte [criar um locatário de Azure Active Directory para conexões VPN abertas do P2S que usam a autenticação do Azure ad](openvpn-azure-ad-tenant.md).
