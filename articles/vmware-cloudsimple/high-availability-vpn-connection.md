---
title: Solução Azure VMware by CloudSimple - Configure alta disponibilidade de locais para gateway CLOUDSimple VPN
description: Descreve como configurar uma conexão de alta disponibilidade do seu ambiente local para um gateway CLOUDSimple VPN habilitado para alta disponibilidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025258"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configure uma conexão de alta disponibilidade do local para o gateway CloudSimple VPN

Os administradores de rede podem configurar uma conexão VPN IPsec de alta disponibilidade de site para site a partir de seu ambiente local para um gateway CLOUDSimple VPN.

Este guia apresenta etapas para configurar um firewall local para uma conexão de alta disponibilidade IPsec Site-to-Site VPN. As etapas detalhadas são específicas para o tipo de firewall local. Como exemplos, este guia apresenta etapas para dois tipos de firewalls: Cisco ASA e Palo Alto Networks.

## <a name="before-you-begin"></a>Antes de começar

Complete as seguintes tarefas antes de configurar o firewall local.

1. Verifique se sua organização [forneceu](create-nodes.md) os nós necessários e criou pelo menos uma CloudSimple Private Cloud.
2. [Configure um gateway VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre sua rede local e sua Nuvem Privada CloudSimple.

Consulte [a visão geral dos gateways VPN](cloudsimple-vpn-gateways.md) para as propostas de fase 1 e fase 2 suportadas.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configure o firewall Cisco ASA no local

As instruções nesta seção aplicam-se à versão 8.4 da Cisco ASA e posteriores. No exemplo de configuração, o Software 9.10 do Cisco Adaptive Security Appliance é implantado e configurado no modo IKEv1.

Para que a VPN site-to-site funcione, você deve permitir que o UDP 500/4500 e o ESP (protocolo IP 50) a partir do IP público primário e secundário cloudSimple (PEER IP) na interface externa do gateway Cisco ASA VPN no local.

### <a name="1-configure-phase-1-ikev1"></a>1. Configure a fase 1 (IKEv1)

Para habilitar a fase 1 (IKEv1) na interface externa, digite o seguinte comando CLI no firewall CISCO ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Crie uma política IKEv1

Crie uma política IKEv1 que define os algoritmos e métodos a serem usados para hashing, autenticação, grupo Diffie-Hellman, vida útil e criptografia.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Crie um grupo de túneis

Crie um grupo de túneis os atributos IPsec. Configure o endereço IP do peer e a chave pré-compartilhada do túnel, que você define ao [configurar o gateway VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Configure a fase 2 (IPsec)

Para configurar a fase 2 (IPsec), crie uma lista de controle de acesso (ACL) que define o tráfego a ser criptografado e encapsulado. No exemplo a seguir, o tráfego de interesse é do túnel que é originado da sub-rede local no local (10.16.1.0/24) para a sub-rede remota Private Cloud (192.168.0.0/24). A ACL pode conter várias entradas se houver várias sub-redes entre os sites.

Nas versões 8.4 e posteriores do Cisco ASA, podem ser criados objetos ou grupos de objetos que servem como contêineres para as redes, sub-redes, endereços IP host ou vários objetos. Crie um objeto para as sub-redes locais e um objeto para as sub-redes remotas e use-os para as instruções cripto ACL e NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Defina uma sub-rede local no local como um objeto

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Defina a sub-rede remota CloudSimple como um objeto

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configure uma lista de acesso para o tráfego de interesse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Configure o conjunto de transformação

Configure o conjunto de transformação (TS), que deve envolver a palavra-chave ```ikev1```. Os atributos de criptografia e hash especificados no TS devem coincidir com os parâmetros listados na [configuração Padrão para gateways CLOUDSimple VPN](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configure o mapa cripto

Configure o mapa cripto, que contém esses componentes:

* Endereço IP do Peer
* ACL definida que contém o tráfego de interesse
* Conjunto de transformação

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Aplique o mapa cripto

Aplique o mapa cripto na interface externa:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Confirme as regras de NAT aplicáveis

A seguir está a regra NAT que é usada. Certifique-se de que o tráfego de VPN não está sujeito a nenhuma outra regra NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Amostra iPsec Site-to-Site VPN estabelecida saída a partir da Cisco ASA

Saída da fase 1:

![Saída fase 1 para firewall CISCO ASA](media/ha-vpn-connection-cisco-phase1.png)

Saída da fase 2:

![Saída fase 2 para firewall CISCO ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configurar o firewall palo alto networks no local

As instruções nesta seção aplicam-se à palo alto networks versão 7.1 e posterior. Neste exemplo de configuração, palo alto networks vm-series software versão 8.1.0 é implantado e configurado no modo IKEv1.

Para que a VPN site-to-site funcione, você deve permitir que o UDP 500/4500 e o ESP (protocolo IP 50) do IP público primário e secundário CloudSimple (PEER IP) na interface externa do gateway Palo Alto Networks no local.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Crie interfaces de túnel primárias e secundárias

Faça login no firewall palo alto, selecione **'Rede** > **Interfaces** > Tunnel **'Add',****Tunnel** > configure os seguintes campos e clique em **OK**.

* Nome da interface. O primeiro campo é autopovoado com a palavra-chave 'túnel'. No campo adjacente, digite qualquer número entre 1 e 9999. Esta interface será usada como uma interface de túnel principal para transportar tráfego local-a-local entre o datacenter local e a Nuvem Privada.
* Comentário. Insira comentários para facilitar a identificação do propósito do túnel
* Perfil netflow. Mantenha o padrão.
* Config. Atribuir interface para: Roteador Virtual: Selecione **padrão**. 
        Zona de segurança: Selecione a região para tráfego de LAN confiável. Neste exemplo, o nome da região para tráfego de LAN é 'Trust'.
* IPv4. Clique **em Adicionar** e adicionar qualquer endereço ip não sobreposto não sobreposto /32 em seu ambiente, que será atribuído à interface do túnel principal e será usado para monitorar os túneis (explicado posteriormente).

Como essa configuração é para uma VPN de alta disponibilidade, duas interfaces de túnel são necessárias: uma primária e uma secundária. Repita as etapas anteriores para criar a interface do túnel secundário. Selecione um ID de túnel diferente e um endereço ip diferente /32 não utilizado.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Configure rotas estáticas para subredes privadas de nuvem a serem alcançadas através da VPN site-to-site

As rotas são necessárias para que as sub-redes no local cheguem às sub-redes privadas cloud do CloudSimple.

Selecione > **Roteadores virtuais** > de rede padrão **Network**Rotas > *estáticas* > **Static Routes****Adicionar,** configurar os seguintes campos e clicar em **OK**.

* Nome. Digite qualquer nome para facilitar a identificação do propósito da rota.
* Destino. Especifique as sub-redes de nuvem privadas CloudSimple a serem alcançadas sobre interfaces de túnel S2S a partir de locais
* Interface. Selecione a interface de túnel principal criada na etapa 1 (Seção-2) a partir da estada. Neste exemplo, é túnel.20.
* Próximo salto. Selecione **Nenhum**.
* Distância de admin. Mantenha o padrão.
* Métrica. Digite qualquer valor de 1 a 65535. A chave é inserir métrica mais baixa para a rota correspondente à interface do túnel primário em comparação com a interface de túnel secundário correspondente à rota que faz com que a rota anterior seja preferida. Se o túnel.20 tem valor métrico de 20 em oposição a um valor métrico de 30 para túnel.30, o túnel.20 é preferido.
* Tabela de rotas. Mantenha o padrão.
* Perfil BFD. Mantenha o padrão.
* Monitoramento de caminhos. Deixe essa opção desmarcada.

Repita as etapas anteriores para criar outra rota para as sub-redes privadas da Nuvem para usar como uma rota secundária/backup via interface de túnel secundário. Desta vez, selecione diferentes ID de túnel e uma métrica mais alta do que para a rota principal.

### <a name="3-define-the-cryptographic-profile"></a>3. Defina o perfil criptográfico

Defina um perfil criptográfico que especifique os protocolos e algoritmos para identificação, autenticação e criptografia a serem usados para configurar túneis VPN na Fase 1 do IKEv1.

Selecione **Rede** > Expandir perfis de rede**IKE Crypto** > **Add,** configure os**seguintes** > campos e clique em **OK**.

* Nome. Digite qualquer nome do perfil cripto IKE.
* Grupo DH. Clique **em Adicionar** e selecione o grupo DH apropriado.
* Criptografia. Clique **em Adicionar** e selecione o método de criptografia apropriado.
* Autenticação. Clique **em Adicionar** e selecione o método de autenticação apropriado.
* Vida útil chave. Mantenha o padrão.
* Autenticação IKEv2 Múltipla. Mantenha o padrão.

### <a name="4-define-ike-gateways"></a>4. Defina gateways IKE

Defina gateways IKE para estabelecer a comunicação entre os pares em cada extremidade do túnel VPN.

Selecione **Rede** > **Expandir perfis de rede IKE** > Gateways**Adicionar,** configurar os**seguintes** > campos e clicar em **OK**.

Guia geral:

* Nome. Digite o nome do gateway IKE a ser olhado com o principal peer cloudsimple VPN.
* Versão. Selecione **o modo somente IKEv1**.
* Tipo de endereço. Selecione **IPv4**.
* Interface. Selecione a interface pública virada ou externa.
* Endereço IP local. Mantenha o padrão.
* Tipo de endereço IP do peer. Selecione **IP**.
* Endereço dos pares. Digite o endereço IP principal do peer CloudSimple VPN.
* Autenticação. Selecione **a chave pré-compartilhada**.
* Chave pré-compartilhada / Confirmar chave pré-compartilhada. Digite a chave pré-compartilhada para corresponder à chave de gateway CloudSimple VPN.
* Identificação local. Digite o endereço IP público do firewall Palo Alto no local.
* Identificação por pares. Digite o endereço IP principal do peer CloudSimple VPN.

Guia Opções avançadas:

* Habilitar modo passivo. Deixe essa opção desmarcada.
* Habilite a Travessia NAT. Deixe descontrolado se o firewall Palo Alto no local não está atrás de nenhum dispositivo NAT. Caso contrário, selecione a caixa de seleção.

IKEv1:

* Modo de troca. Selecione **principal**.
* Perfil cripto IKE. Selecione o perfil IKE Crypto que você criou anteriormente. Deixe a caixa Ativar fragmentação desmarcada.
* Detecção de pares mortos. Deixe a caixa desmarcada.

Repita as etapas anteriores para criar o gateway IKE secundário.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Defina perfis cripto IPSEC

Selecione **Rede** > Expandir perfis de rede**IPSEC Crypto** > **Add,** configure os**seguintes** > campos e clique em **OK**.

* Nome. Digite um nome para o perfil cripto IPsec.
* Protocolo IPsec. Selecione **ESP**.
* Criptografia. Clique **em Adicionar** e selecione o método de criptografia apropriado.
* Autenticação. Clique **em Adicionar** e selecione o método de autenticação apropriado.
* Grupo DH. Selecione **no-pfs**.
* Vida. Programado como 30 minutos.
* Habilitar. Deixe a caixa desmarcada.

Repita as etapas anteriores para criar outro perfil cripto IPsec, que será usado como o peer VPN cloudsimple secundário. O mesmo perfil IPSEC Crypto também pode ser usado para os túneis IPsec primário e secundário (veja o procedimento a seguir).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Defina perfis de monitorpara monitoramento de túneis

Selecione **'Expandir a** > **rede' Monitore** > o**monitor,** > **Add**configure os seguintes campos e clique em **OK**.

* Nome. Digite qualquer nome do perfil do Monitor a ser usado para monitoramento de túneis para reação proativa à falha.
* Ação. Selecione **'Falha'.**
* Intervalo. Digite o valor **3**.
* Limite. Digite o valor **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Configure túneis IPsec primários e secundários.

Selecione **Rede** > **Túneis IPsec** > **Adicionar,** configurar os seguintes campos e clicar em **OK**.

Guia geral:

* Nome. Digite qualquer nome para que o túnel IPSEC principal seja espiado com o principal peer cloudsimple VPN.
* Interface do túnel. Selecione a interface do túnel principal.
* Digite. Mantenha o padrão.
* Tipo de endereço. Selecione **IPv4**.
* IKE Gateway. Selecione o gateway IKE principal.
* Perfil cripto IPsec. Selecione o perfil iPsec principal. Selecione **Mostrar opções avançadas**.
* Habilite a proteção de replay. Mantenha o padrão.
* Copiar cabeçalho TOS. Deixe a caixa desmarcada.
* Monitor de Túnel. Verifique a caixa.
* IP de destino. Digite qualquer endereço IP pertencente à sub-rede Cloud Private Cloud do Cloud que seja permitida durante a conexão Site-to-Site. Certifique-se de que as interfaces do túnel (como o túnel.20 - 10.64.5.2/32 e o túnel.30 - 10.64.6.2/32) em Palo Alto podem alcançar o endereço IP da Cloud Private Cloud do Cloud através da VPN site-to-site. Veja a configuração a seguir para IDs proxy.
* Perfil. Selecione o perfil do monitor.

Guia IDs proxy: Clique em **IPv4** > **Adicionar** e configurar o seguinte:

* ID proxy. Digite qualquer nome para o tráfego interessante. Pode haver vários IDs proxy levados dentro de um túnel IPsec.
* Local. Especifique as sub-redes locais no local que podem se comunicar com sub-redes privadas da Nuvem através da VPN site-to-site.
* Remoto. Especifique as sub-redes remotas private cloud que podem se comunicar com as sub-redes locais.
* Protocolo. Selecione **qualquer**.

Repita as etapas anteriores para criar outro túnel IPsec para usar para o peer VPN CloudSimple secundário.

## <a name="references"></a>Referências

Configuração do NAT no Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guia de configuração da série Cisco ASA 5500</a>

Atributos IKEv1 e IKEv2 suportados no Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guia de configuração cisco ASA Series CLI</a>

Configuração iPsec Vpn site-to-site no Cisco ASA com a versão 8.4 e posterior:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configure túneis IKEv1 IPsec local-a-local com o ASDM ou CLI no ASA</a>

Configuração do Cisco Adaptive Security Appliance virtual (ASAv) no Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Guia de início rápido do Cisco Adaptive Security Virtual Appliance (ASAv)</a>

Configuração de VPN site-to-site com IDs proxy em Palo Alto:

[Configurar vpn site-to-site](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Configuração do monitor de túneis:

[Configurar o monitoramento do túnel](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operações de túnel IKE ou IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Ativar/desativar, atualizar ou reiniciar um gateway IKE ou túnel IPsec</a>
