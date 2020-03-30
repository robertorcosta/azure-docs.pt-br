---
title: Solução Azure VMware by CloudSimple - Estique uma rede de camada 2 no local para nuvem privada
description: Descreve como configurar uma VPN de Camada 2 entre o NSX-T em uma Nuvem Privada CloudSimple e um cliente INDEPENDENTE NSX Edge no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2ddfa9611143d5c3f823539e018c8afc885c6a46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083217"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrar cargas de trabalho usando redes ampliadas da camada 2

Neste guia, você aprenderá a usar a Layer 2 VPN (L2VPN) para esticar uma rede de camada 2 do seu ambiente local para a nuvem privada CloudSimple. Essa solução permite a migração de cargas de trabalho em execução no ambiente VMware no local para a Nuvem Privada no Azure dentro do mesmo espaço de endereço de sub-rede sem ter que refazer o IP de suas cargas de trabalho.

O alongamento baseado em L2VPN de redes de camada 2 pode funcionar com ou sem redes baseadas em NSX em seu ambiente VMware local. Se você não tiver redes baseadas em NSX para cargas de trabalho no local, você pode usar um Gateway de Serviços NSX Edge autônomo.

> [!NOTE]
> Este guia abrange o cenário em que os data centers on-premises e os data centers privados da Nuvem estão conectados através da VPN site-to-site.

## <a name="deployment-scenario"></a>Cenário de implantação

Para esticar sua rede local usando l2VPN, você deve configurar um servidor L2VPN (roteador DE DESTINO NSX-T Tier0) e um cliente L2VPN (cliente autônomo de origem).  

Neste cenário de implantação, sua Nuvem Privada está conectada ao seu ambiente local através de um túnel VPN site-to-site que permite que o gerenciamento local e as sub-redes vMotion se comuniquem com o gerenciamento de Nuvem Privada e as sub-redes vMotion. Este arranjo é necessário para cross vCenter vMotion (xVC-vMotion). Um roteador NSX-T Tier0 é implantado como um servidor L2VPN na Nuvem Privada.

O STANDX Edge autônomo é implantado em seu ambiente local como um cliente L2VPN e, posteriormente, emparelhado com o servidor L2VPN. Um ponto final do túnel GRE é criado em cada lado e configurado para 'esticar' a rede de camada 2 no local para a sua Nuvem Privada. Esta configuração é representada na figura a seguir.

![Cenário de implantação](media/l2vpn-deployment-scenario.png)

Para saber mais sobre migração usando L2 VPN, consulte [Redes Privadas Virtuais](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Pré-requisitos para implantação da solução

Verifique se o seguinte está no lugar antes de implantar e configurar a solução:

* A versão vSphere no local é 6.7U1+ ou 6.5P03+.
* A licença vSphere no local está no nível Enterprise Plus (para o switch distribuído vSphere).
* Identifique a rede de carga de trabalho camada 2 a ser estendida à sua Nuvem Privada.
* Identifique uma rede de camada 2 em seu ambiente local para implantar seu dispositivo cliente L2VPN.
* [Uma Nuvem Privada já foi criada.](create-private-cloud.md)
* A versão do aparelho autônomo NSX-T Edge é compatível com a versão NSX-T Manager (NSX-T 2.3.0) usada no ambiente Private Cloud.
* Um grupo de porta-tronco foi criado no vCenter local com transmissões forjadas habilitadas.
* Um endereço IP público foi reservado para usar para o endereço IP do cliente autônomo NSX-T, e o NAT 1:1 está no lugar para tradução entre os dois endereços.
* O encaminhamento de DNS é definido nos servidores DNS locais para o domínio az.cloudsimple.io apontar para os servidores DNS privados da nuvem.
* A latência RTT é menor ou igual a 150 ms, conforme necessário para que o vMotion funcione nos dois sites.

## <a name="limitations-and-considerations"></a>Limitações e considerações

As listas de tabelas a seguir suportadas versões vSphere e tipos de adaptadores de rede.  

| versão vSphere | Tipo vSwitch de origem | Driver de NIC virtual | Tipo vSwitch de destino | Compatível? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Todos | Dvs | Todos | Dvs | Sim |
| vSphere 6.7UI ou superior, 6.5P03 ou superior | Dvs | VMXNET3 | N-VDS | Sim |
| vSphere 6.7UI ou superior, 6.5P03 ou superior | Dvs | E1000 | N-VDS | [Não suportado por VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI ou 6.5P03, NSX-V ou versões abaixo de NSX-T2.2, 6.5P03 ou superior | Todos | Todos | N-VDS | [Não suportado por VWware](https://kb.vmware.com/s/article/56991) |

A partir da versão VMware NSX-T 2.3:

* O interruptor lógico no lado privado da Nuvem que é estendido para o local sobre L2VPN não pode ser roteado ao mesmo tempo. O interruptor lógico esticado não pode ser conectado a um roteador lógico.
* As VPNs L2VPN e IPSEC baseadas em rota só podem ser configuradas usando chamadas de API.

Para obter mais informações, consulte [Redes Privadas Virtuais](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Endereçamento de implantação de VPN de amostra L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rede local onde o ESG autônomo (cliente L2 VPN) é implantado

| **Item** | **Valor** |
|------------|-----------------|
| Nome da rede | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Endereço IP do aparelho de borda autônoma | 10.250.0.111 |
| Endereço IP do aparelho NaT do aparelho De borda autônoma | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rede no local a ser esticada

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Esquema de IP privado na nuvem para roteador NSX-T Tier0 (serviço L2 VPN)

| **Item** | **Valor** |
|------------|-----------------|
| Interface loopback | 192.168.254.254/32 |
| Interface do túnel | 5.5.5.1/29 |
| Interruptor lógico (esticado) | Stretch_LS |
| Interface de loopback (endereço IP NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rede Privada Cloud a ser mapeada para a rede esticada

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Busque o ID do roteador lógico necessário para L2VPN

As etapas a seguir mostram como buscar o ID de roteador lógico da instância do roteador lógico Tier0 DR para os serviços IPsec e L2VPN. O ID de roteador lógico é necessário mais tarde ao implementar o L2VPN.

1. Faça login no NSX-T Manager https://*nsx-t-manager-ip-address* e selecione Visão geral do Provedor de**Roteadores** > **de Rede-LR** > **Overview** **Networking** > . Para **o modo de alta disponibilidade,** selecione **Active-Standby**. Esta ação abre uma janela pop-up que mostra a VM edge na qual o roteador Tier0 está ativo no momento.

    ![Selecione ativo-standby](media/l2vpn-fetch01.png)

2.  >  **Selecione****Bordas de nó de** > **malha**. Anote o endereço IP de gerenciamento da VM Edge ativa (Edge VM1) identificada na etapa anterior.

    ![IP de gerenciamento de notas](media/l2vpn-fetch02.png)

3. Abra uma sessão de SSH para o endereço IP de gerenciamento da VM edge. Execute ```get logical-router``` o comando com o **admin** de nome de usuário e senha **CloudSimple 123!**.

    ![obter saída de roteador lógico](media/l2vpn-fetch03.png)

4. Se você não ver uma entrada 'DR-Provider-LR', complete as seguintes etapas.

5. Crie dois switches lógicos apoiados por sobreposição. Um switch lógico é estendido para os locais onde residem as cargas de trabalho migradas. Outro interruptor lógico é um interruptor. Para obter instruções, consulte [Criar um interruptor lógico](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) na documentação vMware.

    ![Criar interruptor lógico](media/l2vpn-fetch04.png)

6. Anexe o switch manequim ao roteador Tier1 com um endereço IP local de link ou qualquer sub-rede não sobreposta do local ou da sua Nuvem Privada. Consulte [Adicionar uma porta downlink em um roteador lógico de nível 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) na documentação do VMware.

    ![Anexar interruptor de boneco](media/l2vpn-fetch05.png)

7. Execute `get logical-router` o comando novamente na sessão SSH da VM Edge. O UUID do roteador lógico 'DR-Provider-LR' é exibido. Anote o UUID, que é necessário ao configurar o L2VPN.

    ![obter saída de roteador lógico](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Busque o ID de switch lógico necessário para L2VPN

1. Faça login no [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Selecione**Switching De** >  **comutação** > **de rede<\Visão**  >  geral** do switch\>** > **lógico**.
3. Anote o UUID do switch lógico de estiramento, que é necessário ao configurar o L2VPN.

    ![obter saída de roteador lógico](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerações de roteamento e segurança para L2VPN

Para estabelecer uma VPN baseada em rota IPsec entre o roteador NSX-T Tier0 e o cliente autônomo NSX Edge, a interface de loopback do roteador NSX-T Tier0 deve ser capaz de se comunicar com o endereço IP público do cliente autônomo NSX no local através do UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Permitir UDP 500/4500 para IPsec

1. [Crie um endereço IP público](public-ips.md) para a interface de loopback NSX-T Tier0 no portal CloudSimple.

2. [Crie uma tabela de firewall](firewall.md) com regras estaduais que permitam tráfego de entrada UDP 500/4500 e conecte a tabela de firewall à sub-rede HOSTTransport DO NSX-T.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anuncie o IP da interface de loopback para a rede de underlay

1. Crie uma rota nula para a rede de interface de loopback. Faça login no GERENCIADOR NSX-T e**Routing** > selecione Rotas**estáticas** > **Routers** > de **roteamento de rede** > **do provedor-LR** > .**Static Routes** Clique em **Adicionar**. Para **Rede,** insira o endereço IP da interface de loopback. Para **Próximos Saltos,** clique **em Adicionar,** especifique 'Nulo' para o próximo salto e mantenha o padrão de 1 para Distância de Admin.

    ![Adicionar rota estática](media/l2vpn-routing-security01.png)

2. Crie uma lista de prefixos IP. Faça login no NSX-T Manager e selecione Listas**Routing** > de**prefixos IP****Routers** > de **roteamento de** > **roteamento** > de rede**do provedor-LR** > . Clique em **Adicionar**. Digite um nome para identificar a lista. Em **For Prefixos,** clique **em Adicionar** duas vezes. Na primeira linha, digite '0.0.0.0/0' para **Rede** e 'Negar' para **Ação**. Na segunda linha, selecione **Qualquer** para **Rede** e **Permissão** para **Ação**.
3. Anexar a lista de prefixos IP a ambos os vizinhos BGP (TOR). Anexar a lista de prefixos IP ao vizinho BGP impede que a rota padrão seja anunciada no BGP aos switches TOR. No entanto, qualquer outra rota que inclua a rota nula anunciará o endereço IP da interface de loopback para os switches TOR.

    ![Criar lista de prefixos IP](media/l2vpn-routing-security02.png)

4. Faça login no NSX-T Manager e selecione **Networking** > **Roting Roting** > **Roting Rotrs** > **Provider-LR** > **Routing** > **BGP** > **Neighbors**. Selecione o primeiro vizinho. Clique **em Editar** > **famílias de endereços**. Para a família IPv4, edite a coluna **Filtro de saída** e selecione a lista de prefixos IP que você criou. Clique em **Salvar**. Repita este passo para o segundo vizinho.

    ![Anexar lista de](media/l2vpn-routing-security03.png) ![prefixo IP 1 Anexar lista de prefixo IP 2](media/l2vpn-routing-security04.png)

5. Redistribua a rota estática nula em BGP. Para anunciar a rota da interface de loopback para a underlay, você deve redistribuir a rota estática nula em BGP. Faça login no GERENTE NSX-T e selecione Os**Routing** >  >  > **vizinhos** > de**redistribuição** > **Neighbors**de rota de**roteamento**de **roteamento**de rede**do provedor-LR** > . Selecione **Provider-LR-Route_Redistribution** e clique **em Editar**. Selecione a caixa de seleção **Estática** e clique **em Salvar**.

    ![Redistribuir rota estática nula em BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configure uma VPN baseada em rota no roteador NSX-T Tier0

Use o modelo a seguir para preencher todos os detalhes para configurar uma VPN baseada em rota no roteador NSX-T Tier0. Os UUIDs em cada chamada POST são necessários em chamadas POST subseqüentes. Os endereços IP para as interfaces de loopback e túnel para L2VPN devem ser únicos e não se sobrepor às redes on-premises ou Private Cloud.

Os endereços IP escolhidos para loopback e interface de túnel usados para L2VPN devem ser únicos e não se sobrepor às redes on-premises ou Private Cloud. A rede de interface de loopback deve ser sempre /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Para todas as seguintes chamadas de API, substitua o endereço IP pelo endereço IP do Gerenciador NSX-T. Você pode executar todas essas chamadas de API `curl` do cliente POSTMAN ou usando comandos.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Habilite o serviço DE VPN IPSec no roteador lógico

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Criar perfis: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Criar perfis: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Criar perfis: Túnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Crie um ponto final local

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Criar um ponto final de pares

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Crie uma sessão vpn baseada em rota

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configure l2VPN no roteador NSX-T Tier0

Preencha as seguintes informações após cada chamada DO POST. Os IDs são necessários em chamadas POST subseqüentes.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Crie o serviço L2VPN

A saída do seguinte comando GET estará em branco, porque a configuração ainda não está completa.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Para o seguinte comando POST, o ID do roteador lógico é o UUID do roteador lógico Tier0 DR obtido anteriormente.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Crie a sessão L2VPN

Para o seguinte comando POST, o ID do serviço L2VPN é o ID que você acabou de obter e o ID de sessão iPsec VPN é o ID obtido na seção anterior.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Essas chamadas criam um ponto final do túnel GRE. Para verificar o status, execute o seguinte comando.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Criar porta lógica com o ID do túnel especificado

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Obtenha o código de pares para L2VPN no lado NSX-T

Obtenha o código de pares do ponto final NSX-T. O código de pares é necessário ao configurar o ponto final remoto. O l2VPN <> de id de sessão pode ser obtido na seção anterior. Para obter mais informações, consulte o [Guia de API NSX-T 2.3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Implantar o cliente autônomo NSX-T (no local)

Antes de implantar, verifique se as regras de firewall no local permitem tráfego UDP 500/4500 de entrada e saída de/para o endereço IP público CloudSimple que foi reservado anteriormente para a interface de loopback do roteador NSX-T T0. 

1. [Baixe o cliente autônomo NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF e Extrair os arquivos do pacote baixado em uma pasta.

    ![Baixe o cliente autônomo NSX Edge](media/l2vpn-deploy-client01.png)

2. Vá para a pasta com todos os arquivos extraídos. Selecione todos os vmdks (NSX-l2t-client-large.mf e NSX-l2t-client-large.ovf para tamanho de aparelho grande ou NSX-l2t-client-Xlarge.mf e NSX-l2t-client-Xlarge.ovf para tamanho extra grande do aparelho). Clique em **Avançar**.

    ![Selecionar](media/l2vpn-deploy-client02.png) ![modelo Selecionar modelo](media/l2vpn-deploy-client03.png)

3. Digite um nome para o cliente autônomo NSX-T e clique **em Next**.

    ![Digite o nome do modelo](media/l2vpn-deploy-client04.png)

4. Clique **em Next** conforme necessário para alcançar as configurações do armazenamento de dados. Selecione o datastore apropriado para cliente autônomo NSX-T e clique **em Next**.

    ![Selecione o datastore](media/l2vpn-deploy-client06.png)

5. Selecione os grupos de porta corretos para Trunk (Trunk PG), Public (Uplink PG) e HA interface (Uplink PG) para o cliente autônomo NSX-T. Clique em **Avançar**.

    ![Selecionar grupos de portas](media/l2vpn-deploy-client07.png)

6. Preencha os seguintes detalhes na tela **personalizar o modelo** e clique em **Seguir**:

    Expandir L2T:

    * **Endereço de pares**. Digite o endereço IP reservado no portal Azure CloudSimple para interface de loopback NSX-T Tier0.
    * **Código por Pares**. Cole o código de pares obtido na última etapa da implantação do Servidor L2VPN.
    * **Sub Interfaces VLAN (ID do túnel)**. Digite o ID VLAN a ser esticado. Entre parênteses (), digite o ID do túnel que foi configurado anteriormente.

    Expandir interface uplink:

    * **Endereço IP DNS .** Digite o endereço IP DNS no local.
    * **Gateway padrão**.  Digite o gateway padrão da VLAN que funcionará como um gateway padrão para este cliente.
    * **Endereço IP**. Digite o endereço IP uplink do cliente autônomo.
    * **Comprimento do prefixo**. Digite o comprimento do prefixo da VLAN/sub-rede uplink.
    * **Admin CLI/enable/root User Password**. Defina a senha para admin /enable /root account.

      ![Personalizar](media/l2vpn-deploy-client08.png)
      ![modelo Personalizar modelo - mais](media/l2vpn-deploy-client09.png)

7. Revise as configurações e clique **em Terminar**.

    ![Concluir a configuração](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configure uma porta de pia no local

Se um dos sites vpn não tiver o NSX implantado, você pode configurar uma VPN L2 implantando um NSX Edge autônomo nesse site. Um NSX Edge autônomo é implantado usando um arquivo OVF em um host que não é gerenciado pelo NSX. Isso implanta um aparelho NSX Edge Services Gateway para funcionar como um cliente L2 VPN.

Se um vNIC de porta-malas de borda autônoma estiver conectado a um switch distribuído vSphere, seja no modo promíscuo ou em uma porta de pia é necessário para a função L2 VPN. O uso do modo promíscuo pode causar pings duplicados e respostas duplicadas. Por essa razão, use o modo de porta de adoção da pia na configuração autônoma l2 VPN NSX Edge. Consulte [a Configure uma porta de pia](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) na documentação vMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verificação iPsec VPN e L2VPN

Use os seguintes comandos para verificar as sessões De Psec e L2VPN a partir do STANDAlone NSX-T Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Use os seguintes comandos para verificar as sessões De Psec e L2VPN do roteador NSX-T Tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Use os seguintes comandos para verificar a porta da pia no host ESXi onde a VM autônoma DO NSX-T reside no ambiente local.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
