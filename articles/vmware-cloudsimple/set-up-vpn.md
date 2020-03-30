---
title: Solução Azure VMware by CloudSimple - Configure VPN entre o local e a Nuvem Privada
description: Descreve como configurar uma conexão VPN local-para-site ou ponto a ponto entre sua rede local e sua nuvem privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087132"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configure uma conexão VPN com sua Nuvem Privada Cloud Simple

Os gateways VPN permitem que você se conecte à rede CloudSimple a partir de sua rede local e de um computador cliente remotamente.  Neste artigo, você pode encontrar informações sobre a configuração de gateways VPN a partir do portal CloudSimple.  Uma conexão VPN entre sua rede local e sua rede CloudSimple fornece acesso ao vCenter e cargas de trabalho em sua Nuvem Privada. O CloudSimple suporta os gateways DE VPN ponto a ponto e local.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* **A** conexão VPN ponto a ponto é a maneira mais simples de se conectar à nuvem privada a partir do seu computador. Use conectividade VPN ponto a ponto para se conectar remotamente à Nuvem Privada.
* A conexão **VPN site-to-site** permite que você configure suas cargas de trabalho da Nuvem Privada para acessar serviços locais. Você também pode usar o Active Directory no local como uma fonte de identidade para autenticar o seu vCenter privado da Nuvem.  Atualmente, o tipo **de VPN baseado em políticas** é suportado.

Em uma região, você pode criar um gateway VPN site-to-site e um gateway VPN ponto a ponto.

## <a name="point-to-site-vpn"></a>VPN ponto a site

Para criar um gateway VPN ponto a ponto, consulte [Criar gateway VPN ponto a ponto](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Conecte-se ao CloudSimple usando vpn ponto a ponto

O cliente VPN é necessário para se conectar ao CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Inicie o portal CloudSimple e selecione **Network**.
2. Selecione **vpn Gateway**.
3. Na lista de gateways VPN, clique no gateway VPN point-to-site.
4. Selecionar **usuários**.
5. Clique em **Baixar minha configuração VPN**

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração em seu cliente VPN

    * Instruções para [importar configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importação de configuração no macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conecte-se ao gateway CloudSimple VPN.

Exemplo abaixo mostra conexão de importação usando **viscosidade client**.

#### <a name="import-connection-on-viscosity-client"></a>Conexão de importação no cliente Viscosity

1. Extrair o conteúdo da configuração VPN do arquivo .zip baixado.

2. Abra viscosidade no seu computador.

3. Clique **+** no ícone e selecione **Importar conexão A** > **partir do arquivo**.

    ![Importar configuração VPN do arquivo](media/import-p2s-vpn-config.png)

4. Selecione o arquivo de configuração OpenVPN (.ovpn) para o protocolo que deseja usar e clique em **Abrir**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

A conexão agora aparece no menu Viscosity.

#### <a name="connect-to-the-vpn"></a>Conectar-se ao VPN

Para se conectar à VPN usando o cliente Viscosity OpenVPN, selecione a conexão no menu. O ícone do menu atualiza para indicar que a conexão está estabelecida.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Conectando-se a múltiplas nuvens privadas

Uma conexão VPN ponto a ponto resolve os nomes DNS da primeira Nuvem Privada que você cria. Quando você quiser acessar outras nuvens privadas, você deve atualizar o servidor DNS em seu cliente VPN.

1. Inicie [o portal CloudSimple](access-cloudsimple-portal.md).

2. Navegue para **Recursos** > **Nuvens Privadas** e selecione a Nuvem Privada à sua vontade de se conectar.

3. Na página **Resumo** da Nuvem Privada, copie o endereço IP do servidor Private Cloud DNS em **Informações Básicas**.

    ![Servidores DNS privados em nuvem](media/private-cloud-dns-server.png)

4. Clique com o botão direito do mouse no ícone Viscosity na bandeja do sistema do computador e selecione **Preferências**.

    ![VPN](media/vis00.png)

5. Selecione a conexão CLOUDSimple VPN.

    ![Conexão VPN](media/viscosity-client.png)

6. Clique **em Editar** para alterar as propriedades de conexão.

    ![Editar conexão VPN](media/viscosity-edit-connection.png)

7. Clique na guia **Rede** e digite os endereços IP do servidor Private Cloud ```cloudsimple.io```DNS separados por uma comuma ou espaço e o domínio como .  Selecione **Ignorar as configurações de DNS enviadas pelo servidor VPN**.

    ![Rede VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Para se conectar à sua primeira Nuvem Privada, remova essas configurações e conecte-se ao servidor VPN.

## <a name="site-to-site-vpn"></a>VPN de site a site

Para criar um gateway VPN site-to-site, consulte [Criar gateway VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  A conexão VPN site-to-site da sua rede local para a nuvem privada fornece esses benefícios.  

* Acessibilidade do seu VCenter de Nuvem Privada a partir de qualquer estação de trabalho em sua rede local
* Use seu Active Directory no local como fonte de identidade do vCenter
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos no local para o seu vCenter privado em nuvem
* Acessibilidade de cargas de trabalho em execução em sua Nuvem Privada a partir de sua rede local

Para configurar o gateway VPN no local no modo de alta disponibilidade, consulte [Configure uma conexão VPN](high-availability-vpn-connection.md)de alta disponibilidade .

> [!IMPORTANT]
>    1. Defina o Grampo TCP MSS em 1200 no seu dispositivo VPN. Ou se seus dispositivos VPN não suportam a fixação mSS, você pode, alternativamente, definir o MTU na interface do túnel para 1240 bytes.
> 2. Depois que a VPN site-to-site for configurada, encaminhe as solicitações de DNS para *.cloudsimple.io para os servidores DNS privados da nuvem.  Siga as instruções na [configuração DNS no local](on-premises-dns-setup.md).
