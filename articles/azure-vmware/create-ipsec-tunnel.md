---
title: Criar um túnel IPSec na solução VMware do Azure
description: Saiba como criar um hub WAN virtual para estabelecer um túnel IPSec em soluções VMware do Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491821"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Criar um túnel IPSec na solução VMware do Azure

Neste artigo, vamos percorrer as etapas para estabelecer um encerramento de túnel site a site de VPN (IKEv1 e IKEv2), no Microsoft Azure Hub de WAN virtual. Vamos criar um hub de WAN virtual do Azure e um gateway de VPN com um endereço IP público anexado a ele. Em seguida, criaremos um gateway de ExpressRoute do Azure e estabeleceremos um ponto de extremidade de solução VMware do Azure. Também veremos os detalhes da habilitação de uma configuração local de VPN baseada em políticas. 

## <a name="topology"></a>Topologia

![Diagrama mostrando a arquitetura de túnel VPN site a site.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

O Hub virtual do Azure contém o gateway de ExpressRoute da solução VMware do Azure e o gateway de VPN site a site. Ele conecta um dispositivo VPN local a um ponto de extremidade de solução VMware do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para criar o túnel VPN site a site, você precisará criar um endereço IP voltado para o público terminando em um dispositivo VPN local.

## <a name="create-a-virtual-wan-hub"></a>Criar um hub de WAN Virtual

1. Na portal do Azure, pesquise em **WANs virtuais**. Selecione **+Adicionar**. A página Criar WAN é aberta.  

2. Insira os campos obrigatórios na página **criar Wan** e, em seguida, selecione **revisar + criar**.
   
   | Campo | Valor |
   | --- | --- |
   | **Assinatura** | O valor é preenchido previamente com a assinatura pertencente ao grupo de recursos. |
   | **Grupo de recursos** | A WAN virtual é um recurso global e não é confinada em uma região específica.  |
   | **Localização do grupo de recursos** | Para criar o Hub WAN virtual, você precisa definir um local para o grupo de recursos.  |
   | **Nome** |   |
   | **Tipo** | Selecione **padrão**, que permitirá mais do que apenas o tráfego de gateway de VPN.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Captura de tela mostrando a página Criar WAN no portal do Azure.":::

3. No portal do Azure, selecione a WAN virtual que você criou na etapa anterior, selecione **criar Hub virtual**, insira os campos obrigatórios e, em seguida, selecione **Avançar: site a site**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A seleção de uma região é necessária de uma perspectiva de gerenciamento.  |
   | **Nome** |    |
   | **Espaço de endereço privado do Hub** | Insira a sub-rede usando um `/24` (mínimo).  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Captura de tela mostrando a página Criar Hub virtual.":::

4. Na guia **site a site** , defina o Gateway site a site definindo a taxa de transferência agregada na lista suspensa unidades de **escala do gateway** . 

   >[!TIP]
   >Uma unidade de escala = 500 Mbps. As unidades de escala estão em pares para redundância, cada uma com suporte a 500 Mbps.
  
5. Na guia **ExpressRoute** , crie um gateway de expressroute. 

   >[!TIP]
   >Um valor de unidade de escala é 2 Gbps. 

    Leva aproximadamente 30 minutos para criar cada Hub. 

## <a name="create-a-vpn-site"></a>Criar um site VPN 

1. Em **recursos recentes** no portal do Azure, selecione a WAN virtual que você criou na seção anterior.

2. Na **visão geral** do Hub virtual, selecione **conectividade**  >  **VPN (site a site)** e, em seguida, selecione **criar novo site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Captura de tela da página de visão geral do Hub virtual, com VPN (site a site) e criar novo site de VPN selecionado.":::  
 
3. Na guia **noções básicas** , insira os campos obrigatórios e, em seguida, selecione **Avançar: links**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A mesma região especificada na seção anterior.  |
   | **Nome** |  |
   | **Fornecedor do dispositivo** |  |
   | **Border Gateway Protocol** | Defina para **habilitar** o para garantir que a solução do Azure VMware e os servidores locais anunciem suas rotas no túnel. Se desabilitado, as sub-redes que precisam ser anunciadas devem ser mantidas manualmente. Se as sub-redes forem perdidas, o HCX não conseguirá formar a malha de serviço. Para obter mais informações, consulte  [sobre o BGP com o gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Espaço de endereço privado**  | Insira o bloco CIDR local.  Ele é usado para rotear todo o tráfego associado para o local pelo túnel.  O bloco CIDR só será necessário se você não habilitar o BGP. |
   | **Conectar-se a** |   |

4. Na guia **links** , preencha os campos obrigatórios e selecione **revisar + criar**. A especificação de nomes de link e provedor permite que você Diferencie entre qualquer número de gateways que possam eventualmente ser criados como parte do Hub. O BGP e o número do sistema autônomo (ASN) devem ser exclusivos dentro de sua organização.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Adicional Definindo um site VPN para túneis VPN site a site baseados em políticas

Esta seção se aplica somente a VPNs baseadas em políticas. As configurações de VPN baseadas em política (ou estática, baseada em rota) são controladas por recursos de dispositivo VPN local na maioria dos casos. Eles exigem a especificação de redes de solução local e do Azure VMware. Para a solução do Azure VMware com um hub de WAN virtual do Azure, você não pode selecionar *nenhuma* rede. Em vez disso, você precisa especificar todos os intervalos relevantes do hub de WAN da solução do Azure VMware e de soluções locais. Esses intervalos de Hub são usados para especificar o domínio de criptografia do ponto de extremidade local de túnel VPN de base de política. O lado da solução do Azure VMware requer que o indicador de seletor de tráfego baseado em políticas esteja habilitado. 

1. No portal do Azure, acesse o site do Hub WAN virtual. Em **conectividade**, selecione **VPN (site a site)**.

2. Selecione o nome do site de VPN, as reticências (...) na extrema direita e, em seguida, **edite a conexão VPN para esse Hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Captura de tela da página no Azure para o site do Hub WAN virtual mostrando uma elipse selecionada para acessar editar conexão VPN com esse Hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edite a conexão entre o site VPN e o Hub e, em seguida, selecione **salvar**.
   - IPSec (Internet Protocol Security), selecione **personalizado**.
   - Use o seletor de tráfego baseado em políticas, selecione **habilitar**
   - Especifique os detalhes da **fase 1 do Ike** e do **Ike fase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Captura de tela da página Editar conexão VPN."::: 
 
   Seus seletores de tráfego ou sub-redes que fazem parte do domínio de criptografia baseada em políticas devem ser:
    
   - O Hub WAN virtual/24
   - A nuvem privada/22 da solução Azure VMware
   - A rede virtual conectada do Azure (se presente)

## <a name="connect-your-vpn-site-to-the-hub"></a>Conectar seu site VPN ao Hub

1. Selecione o nome do site VPN e, em seguida, selecione **conectar sites VPN**. 
1. No campo **chave pré-compartilhada** , insira a chave definida anteriormente para o ponto de extremidade local. 

   >[!TIP]
   >Se você não tiver uma chave definida anteriormente, poderá deixar esse campo em branco. Uma chave é gerada automaticamente para você. 
 
   >[!IMPORTANT]
   >Habilite somente a **rota padrão de propagação** se você estiver implantando um firewall no Hub e for o próximo salto para conexões por meio desse túnel.

1. Selecione **Conectar**. Uma tela de status da conexão mostra o status da criação do túnel.

2. Vá para a visão geral da WAN virtual e abra a página do site VPN para baixar o arquivo de configuração de VPN para o ponto de extremidade local.  

3. Corrija a solução do Azure VMware ExpressRoute no Hub de WAN virtual. Esta etapa requer a criação da sua nuvem privada pela primeira vez.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Vincule a solução VMware do Azure e o gateway de VPN juntos no Hub WAN virtual. 
   1. No portal do Azure, abra a WAN virtual que você criou anteriormente. 
   1. Selecione o Hub de WAN virtual criado e, em seguida, selecione **ExpressRoute** no painel esquerdo. 
   1. Selecione **+ resgatar chave de autorização**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Captura de tela da página do ExpressRoute para a nuvem privada, com a chave de autorização resgatar selecionada.":::

   1. Cole a chave de autorização no campo chave de autorização.
   1. Após a ID do ExpressRoute no campo **URI do circuito par** . 
   1. Selecione **associar automaticamente este circuito do ExpressRoute ao Hub.** 
   1. Selecione **Adicionar** para estabelecer o link. 

5. Teste sua conexão [criando um segmento do NSX-T](./tutorial-nsx-t-network-segment.md) e provisionando uma VM na rede. Execute ping nos pontos de extremidade da solução local e do Azure VMware.
