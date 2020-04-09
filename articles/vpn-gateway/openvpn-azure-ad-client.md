---
title: 'VPN Gateway: cliente VPN para conexões P2S do protocolo OpenVPN: autenticação Azure AD'
description: Você pode usar o P2S VPN para se conectar ao seu VNet usando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879438"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configure um cliente VPN para conexões de protocolo P2S OpenVPN: autenticação Azure AD

Este artigo ajuda você a configurar um cliente VPN para se conectar a uma rede virtual usando VPN point-to-site e autenticação do Azure Active Directory. Antes de se conectar e autenticar usando o Azure AD, você deve primeiro configurar o seu inquilino Azure AD. Para obter mais informações, consulte [Configure um inquilino Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Trabalhando com perfis de clientes

Para se conectar, você precisa baixar o Cliente Azure VPN e configurar um perfil de cliente VPN em cada computador que deseja se conectar ao VNet. Você pode criar um perfil de cliente em um computador, exportá-lo e importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o Cliente VPN do Azure. Certifique-se de que o Cliente Azure VPN tenha permissão para ser executado em segundo plano. Para verificar/ativar a permissão siga as etapas abaixo:

1. Vá para Iniciar, depois selecione Configurações > Privacidade > aplicativos em segundo plano.
2. Em Background Apps, certifique-se de que **os aplicativos executados em segundo plano** estão ligados.
3. Em Escolher quais aplicativos podem ser executados em segundo plano, gire as configurações para o Cliente VPN do Azure para **On**.

  ![permissão](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Para criar um perfil de cliente baseado em certificado

Ao trabalhar com um perfil baseado em certificado, certifique-se de que os certificados apropriados estão instalados no computador cliente. Para obter mais informações sobre certificados, consulte [Instalar certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O Server Secret pode ser exportado no perfil do cliente P2S VPN.  Instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Para exportar e distribuir um perfil de cliente

Uma vez que você tenha um perfil de trabalho e precise distribuí-lo para outros usuários, você pode exportá-lo usando as seguintes etapas:

1. Destaque o perfil de cliente VPN que deseja exportar, selecione o **...** e selecione **Exportar**.

    ![exportar](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione o local para o que deseja salvar este perfil, deixe o nome do arquivo como está e selecione **Salvar** para salvar o arquivo xml.

    ![exportar](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

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

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as elipses ao lado do perfil do cliente que deseja excluir. Em seguida, selecione **Remover**.

    ![excluir](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![excluir](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Crie uma conexão

1. Na página, **+** selecione , em seguida **, + Adicionar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de conexão. Se você não tiver certeza dos valores, entre em contato com o administrador. Depois de preencher os valores, **selecione Salvar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Conectar** para se conectar à VPN.

    ![conexão](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais adequadas e selecione **Continuar**.

    ![conexão](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez conectado com sucesso, o ícone ficará verde e dirá **Conectado**.

    ![conexão](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Para conectar automaticamente

Essas etapas ajudam você a configurar sua conexão para se conectar automaticamente com o Always-on.

1. Na página inicial do seu cliente VPN, selecione **Configurações de VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo de aplicativos de switch.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Certifique-se de que a conexão que deseja definir ainda não está conectada, em seguida, destaque o perfil e verifique a caixa de seleção **Conectar automaticamente.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **Conectar** para iniciar a conexão VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione o **...** ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![diagnosticar](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como adicionar sufixos DNS ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar os ** \< \<dnssufixos \<>\<dnssufix> /dnssufix>/dnssufixos>** tags

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Como adicionar servidores DNS personalizados ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar os ** \<dnsservers \<>dnsserver> \</dnsserver>\</dnsservers>** tags

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> O cliente AD do OpenVPN Azure utiliza entradas NRPT (DNT) de política de resolução `ipconfig /all`de nomes DNS, o que significa que os servidores DNS não serão listados sob a saída de . Para confirmar as configurações de DNS em uso, consulte [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) no PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como adicionar rotas personalizadas ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as ** \<rotas>rotas>\< \<destino>\<máscara> \</>\<de destino /máscara>\</>\<de rota /incluirotas>** tags

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Como bloquear (excluir) rotas do cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as ** \<rotas de exclusão \<>rota>\<destino>máscara> \< \</destination>\</mask>\</route>\</excluroutes>** tags

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Posso importar o perfil de um prompt de linha de comando?

Você pode importar o perfil de um prompt de linha de comando colocando o arquivo **azurevpn.xml** baixado no **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** folder e executando o seguinte comando:

```
azurevpn -i azurevpnconfig.xml 
```
para forçar a importação usar o interruptor **-f** também


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Criar um inquilino do Azure Active Directory para conexões P2S Open VPN que usam autenticação Azure AD](openvpn-azure-ad-tenant.md).
