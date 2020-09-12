---
title: 'Criar & instalar arquivos de configuração de cliente VPN P2S: autenticação de certificado'
titleSuffix: Azure VPN Gateway
description: Crie e instale arquivos de configuração do cliente VPN do Windows, Linux (strongSwan) e Mac OS X para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 17a9339fff27a0fbd7fa389933d21ef85e29248b
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420771"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar arquivos de configuração de cliente VPN para configurações P2S da autenticação de certificado nativa do Azure

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para que os clientes nativos do Windows, Mac IKEv2 VPN ou Linux se conectem a uma rede virtual em conexões ponto a site que usam a autenticação de certificado nativa do Azure.

Os arquivos de configuração do cliente são específicos para a configuração de VPN para a rede virtual. Se houver alterações na configuração de VPN Ponto a Site depois de gerar os arquivos de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, gere e instale novos arquivos de configuração de cliente VPN nos dispositivos do usuário. 

* Para saber mais sobre conexões Ponto a Site, confira [Sobre VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções sobre o OpenVPN, confira [Configurar o OpenVPN para P2S](vpn-gateway-howto-openvpn.md) e [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Gerar os arquivos de configuração de cliente VPN

Antes de começar, verifique se todos os usuários conectados têm um certificado válido instalado no dispositivo do usuário. Para saber mais sobre como instalar um certificado de cliente, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

É possível gerar arquivos de configuração do cliente usando o PowerShell ou usando o Portal do Azure. O método retorna o mesmo arquivo zip. Descompacte o arquivo para ver as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes dos instaladores do Windows de 32 bits e 64 bits, respectivamente. O pacote do instalador de **WindowsAmd64** serve para todos os clientes do Windows de 64 bits, não apenas ao Amd.
  * **Generic**, que contém informações gerais, usadas para criar sua própria configuração de cliente VPN. A pasta Genérico será fornecida se IKEv2 ou SSTP+IKEv2 tiver sido configurado no gateway. Se apenas o SSTP estiver configurado, a pasta Genérico não estará presente.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Gerenciar arquivos usando o Portal do Azure

1. No Portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
2. Na página de gateway de rede virtual, clique em **Configuração Ponto a Site**.

   ![baixar o portal do cliente](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. Na parte superior da página da configuração ponto a site, clique em **Baixar cliente VPN**. Levará alguns minutos para o pacote de configuração do cliente ser gerado.
4. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele terá o mesmo nome do seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Gerar arquivos usando o PowerShell


1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente de VPN usando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie a URL para o seu navegador para baixar o arquivo zip. Em seguida, descompacte o arquivo para exibir as pastas.

## <a name="windows"></a><a name="installwin"></a>Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais quer recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows a partir do qual deseja conectar-se.
>
>

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
2. Clique duas vezes no pacote para instalá-lo. Se você vir um pop-up do SmartScreen, clique em **mais informações**e em **executar mesmo assim**.
3. No computador cliente, navegue até **configurações de rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 
4. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Você precisa configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que se conecta ao Azure. O Azure não oferece arquivo mobileconfig para autenticação de certificado do Azure nativa. A **Generic** contém todas as informações que você precisa para configuração. Caso não veja a pasta Genérico em seu download, talvez o IKEv2 não tenha sido selecionado como um tipo de túnel. Observe que o SKU básico do gateway de VPN não oferece suporte a IKEv2. Depois que o IKEv2 for selecionado, gere o arquivo zip novamente para recuperar a pasta Genérico.<br>A pasta Genérico contém os seguintes arquivos:

* **VpnSettings.xml**, que contém configurações importantes, como o endereço do servidor e o tipo de túnel. 
* **VpnServerRoot. cer**, que contém o certificado raiz necessário para validar o gateway de VPN do Azure durante a configuração de conexão do P2S.

Use as seguintes etapas para configurar o cliente VPN nativo do Mac para autenticação de certificado. Você precisa concluir estas etapas em cada Mac que se conecta ao Azure:

1. Importe o certificado raiz **VpnServerRoot** para seu Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele. Clique em **Adicionar** para importar.

   ![adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Ao clicar duas vezes na caixa de diálogo **Adicionar**, o certificado poderá não ser exibido, mas será instalado no repositório correto. Você pode verificar o certificado no conjunto de chaves de logon, na categoria Certificados.
    >
  
2. Verifique se você instalou um certificado do cliente emitido pelo certificado raiz que você carregou no Azure quando definiu as configurações de P2S. Ele é diferente do VPNServerRoot que você instalou na etapa anterior. O certificado do cliente é usado para autenticação e é necessário. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Abra a caixa de diálogo **rede** em **preferências de rede** e clique em **' + '** para criar um novo perfil de conexão de cliente VPN para uma conexão P2S com a rede virtual do Azure.

   O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **Nome do serviço** e, em seguida, clique em **Criar** para criar o perfil de conexão de cliente VPN.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil.

   ![informações do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **Configurações de autenticação** e selecione **Certificado**.Para o **Catalina**, clique em **nenhum** e em **certificado**

   ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Para o Catalina, selecione **nenhum** e, em seguida, **certificado**. **Selecione** o certificado correto:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Clique em **selecionar...** para escolher o certificado que deseja usar para autenticação. Trata-se do certificado que você instalou na etapa 2.

   ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, clique em **Continuar**.

   ![identidade](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. No campo **ID local**, especifique o nome do certificado (da Etapa 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para salvar as alterações.

   ![aplicar](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na caixa de diálogo **Rede**, clique em **Aplicar** para salvar todas as alterações. Em seguida, clique em **conectar** para iniciar a conexão P2S com a rede virtual do Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (GUI strongSwan)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalar e configurar

As instruções a seguir foram criadas no Ubuntu 18.0.4. O Ubuntu 16.0.10 não dá suporte para GUI do StrongSwan. Se você quiser usar o Ubuntu 16.0.10, precisará usar a linha de [comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder às telas que você vê, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **Terminal** para instalar o **strongSwan** e seu Gerenciador de Rede executando o comando no exemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selecione **configurações**e, em seguida, selecione **rede**.

   ![editar conexões](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique no **+** botão para criar uma nova conexão.

   ![adicionar um conexão](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **IPSec/IKEv2 (strongSwan)** no menu e clique duas vezes em. Você pode nomear sua conexão nesta etapa.

   ![escolher um tipo de conexão](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o arquivo **VpnSettings.xml** da pasta **Genérico** contida nos arquivos de configuração do cliente baixados. Localize a marca chamada **VpnServer** e copie o nome, começando com ' azuregateway ' e terminando com '. cloudapp.net '.

   ![copiar nome](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Cole esse nome no campo **Endereço** da sua nova conexão VPN na seção **Gateway**. Em seguida, selecione o ícone da pasta no final do campo **Certificado**, navegue até a pasta **Genérico** e selecione o arquivo **VpnServerRoot**.
7. Na seção **Cliente** da conexão, da **Autenticação**, selecione **Certificado/chave privada**. Para **Certificado** e **Chave privada**, escolha o certificado e a chave privada que foram criados anteriormente. Em **Opções**, selecione **Solicitar um endereço IP interno**. Em seguida, clique em **Adicionar**.

   ![solicitar um endereço IP interno](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Ative a conexão.**

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (CLI do strongSwan)

### <a name="install-strongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar e configurar

1. Baixe o pacote de VPNClient do portal do Azure.
2. Extraia o arquivo.
3. Da pasta **Generic**, copie ou mova o VpnServerRoot.cer para /etc/ipsec.d/cacerts.
4. Copie ou mova o cp client.p12 para /etc/ipsec.d/private/. Este arquivo é um certificado de cliente para o Gateway de VPN do Azure.
5. Abra o arquivo VpnSettings.xml e copie o valor `<VpnServer>`. Você usará esse valor na próxima etapa.
6. Ajuste os valores no exemplo a seguir e adicione o exemplo à configuração /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Adicione o conteúdo a seguir a */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Execute os comandos a seguir:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Para solucionar problemas de conexões P2S, veja os seguintes artigos:

  * [Solução de problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Solucionar problemas de conexões VPN de clientes VPN do Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
