---
title: Configurar regras de NAT de VPN para seu gateway
titleSuffix: Azure Virtual WAN
description: Saiba como configurar regras de NAT para o gateway de VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744259"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurar regras de NAT para o gateway de VPN de WAN virtual-visualização

> [!IMPORTANT]
> As regras de NAT estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode configurar seu gateway de VPN de WAN virtual com regras de NAT estática um-para-um. Uma regra NAT fornece um mecanismo para configurar a tradução de um para um de endereços IP. O NAT pode ser usado para interconectar duas redes IP que têm endereços IP incompatíveis ou sobrepostos. Um cenário típico é ramificações com IPs sobrepostos que desejam acessar recursos de VNet do Azure.

Essa configuração usa uma tabela de fluxo para rotear o tráfego de um endereço IP externo (host) para um endereço IP interno associado a um ponto de extremidade dentro de uma rede virtual (máquina virtual, computador, contêiner, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagrama mostrando a arquitetura.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Configurar e exibir regras

Você pode configurar e exibir as regras de NAT em suas configurações de gateway de VPN a qualquer momento.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Captura de tela mostrando regras de edição.":::

1. Navegue até o Hub virtual.
1. Selecione **VPN (site a site)**.
1. Selecione **regras de NAT (editar)**.
1. Na página **Editar regra NAT** , você pode **Adicionar/editar/Excluir** uma regra NAT usando os seguintes valores:

   * **Nome:** Um nome exclusivo para sua regra NAT.
   * **Tipo:** Auto-estática. O NAT estático um-para-um estabelece uma relação um-para-um entre um endereço interno e um endereço externo.
   * **Modo:** IngressSnat ou EgressSnat.  
      * O modo IngressSnat (também conhecido como NAT de origem de entrada) é aplicável ao tráfego que entra no gateway de VPN site a site do Hub do Azure.
      * O modo EgressSnat (também conhecido como NAT de origem de saída) é aplicável ao tráfego que sai do gateway de VPN site a site do Hub do Azure.
   * **InternalMapping:** Um intervalo de prefixo de endereço de IPs de origem na rede interna que será mapeado para um conjunto de IPs externos. Em outras palavras, seu intervalo de prefixo de endereço de NAT.
   * **ExternalMapping:** Um intervalo de prefixo de endereço de IPs de destino na rede externa à qual os IPs de origem serão mapeados. Em outras palavras, seu intervalo de prefixo de endereço de NAT.
   * **Conexão de link:** Recurso de conexão que conecta virtualmente um site de VPN ao gateway de VPN site a site do Hub do Azure.

### <a name="configuration-considerations"></a>Considerações sobre configuração

* O tamanho da sub-rede para o mapeamento interno e externo deve ser o mesmo para NAT estático um-para-um.
* Certifique-se de editar o site VPN no portal do Azure para adicionar prefixos **ExternalMapping** no campo ' espaço de endereço privado '. Atualmente, os sites que têm o BGP habilitado precisam garantir que o anunciante BGP local (configurações de BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externo.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre configurações de site a site, consulte [Configurar uma conexão site a site de WAN virtual](virtual-wan-site-to-site-portal.md).
