---
title: Link privado-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como o link privado funciona para o banco de dados do Azure para PostgreSQL-servidor único.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371674"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Link privado para o banco de dados do Azure para PostgreSQL-servidor único

O link privado permite criar pontos de extremidade privados para o banco de dados do Azure para PostgreSQL-servidor único e, por isso, traz os serviços do Azure dentro de sua VNet (rede virtual privada). O ponto de extremidade privado expõe um IP privado que você pode usar para se conectar ao servidor de banco de dados, assim como qualquer outro recurso na VNet.

Para obter uma lista dos serviços de PaaS que dão suporte à funcionalidade de link privado, examine a [documentação](https://docs.microsoft.com/azure/private-link/index)do link privado. Um ponto de extremidade privado é um endereço IP privado em uma [VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e sub-rede específicas.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para PostgreSQL oferece suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="data-exfiltration-prevention"></a>Prevenção contra exportação de dados

Data ex-filtragem no banco de dados do Azure para PostgreSQL servidor único é quando um usuário autorizado, como um administrador de banco de dados, é capaz de extrair dados de um sistema e movê-los para outro local ou sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário que executa o PGAdmin dentro de uma VM (máquina virtual) do Azure que está se conectando a um banco de dados do Azure para PostgreSQL de servidor único provisionado no oeste dos EUA. O exemplo a seguir mostra como limitar o acesso com pontos de extremidade públicos no banco de dados do Azure para PostgreSQL servidor único usando controles de acesso à rede.

* Desabilite todo o tráfego de serviço do Azure para o banco de dados do Azure para PostgreSQL servidor único por meio do ponto de extremidade público, definindo permitir que os *Serviços do Azure* sejam Certifique-se de que nenhum endereço IP ou intervalo tenha permissão para acessar o servidor por meio de [regras de firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) ou pontos de extremidade de [serviço de rede virtual](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Permitir somente o tráfego para o banco de dados do Azure para PostgreSQL servidor único usando o endereço IP privado da VM. Para obter mais informações, consulte os artigos sobre as regras de [ponto de extremidade de serviço](concepts-data-access-and-security-vnet.md) e [Firewall VNet.](howto-manage-vnet-using-portal.md)

* Na VM do Azure, restrinja o escopo da conexão de saída usando NSGs (grupos de segurança de rede) e Marcas de Serviço, conforme mostrado a seguir

    * Especifique uma regra de NSG para permitir o tráfego para a *marca de serviço = SQL. Westus* -permitindo somente a conexão com o banco de dados do Azure para PostgreSQL servidor único no oeste dos EUA
    * Especifique uma regra de NSG (com prioridade mais alta) para negar o tráfego para a *marca de serviço = SQL* negando conexões com o banco de dados PostgreSQL em todas as regiões</br></br>

No final desta configuração, a VM do Azure pode se conectar somente ao banco de dados do Azure para PostgreSQL servidor único na região oeste dos EUA. No entanto, a conectividade não é restrita a um único servidor único do banco de dados do Azure para PostgreSQL. A VM ainda pode se conectar a qualquer banco de dados do Azure para PostgreSQL com um único servidor na região oeste dos EUA, incluindo os bancos que não fazem parte da assinatura. Embora tenhamos reduzido o escopo da exportação de dados no cenário acima a uma região específica, não o eliminamos por completo.</br>

Com o link privado, agora você pode configurar controles de acesso à rede como NSGs para restringir o acesso ao ponto de extremidade privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um insider mal-intencionado só pode acessar o recurso de PaaS mapeado (por exemplo, um servidor único do banco de dados do Azure para PostgreSQL) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local no emparelhamento privado

Quando você se conecta ao ponto de extremidade público de computadores locais, seu endereço IP precisa ser adicionado ao firewall baseado em IP usando uma regra de firewall de nível de servidor. Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciá-lo em um ambiente de produção.

Com o link privado, você pode habilitar o acesso entre instalações ao ponto de extremidade privado usando o er ( [rota expressa](https://azure.microsoft.com/services/expressroute/) ), o emparelhamento privado ou o [túnel VPN](https://docs.microsoft.com/azure/vpn-gateway/). Em seguida, eles podem desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Configurar link privado para o banco de dados do Azure para PostgreSQL servidor único

### <a name="creation-process"></a>Processo de criação

Pontos de extremidade privados são necessários para habilitar o link privado. Isso pode ser feito usando os seguintes guias de instruções.

* [Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo de aprovação
Depois que o administrador de rede cria o ponto de extremidade privado (PE), o administrador do PostgreSQL pode gerenciar a conexão de ponto de extremidade privado (PEC) para o banco de dados do Azure para PostgreSQL. Essa separação de tarefas entre o administrador de rede e o DBA é útil para o gerenciamento da conectividade do banco de dados do Azure para PostgreSQL. 

* Navegue até o recurso de servidor banco de dados do Azure para PostgreSQL no portal do Azure. 
    * Selecione as conexões de ponto de extremidade privado no painel esquerdo
    * Mostra uma lista de todas as conexões de ponto de extremidade privado (PECs)
    * Ponto de extremidade particular correspondente (PE) criado

![Selecione o portal de ponto de extremidade privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione uma PEC individual na lista selecionando-a.

![Selecione a aprovação de ponto de extremidade particular pendente](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O administrador do servidor PostgreSQL pode optar por aprovar ou rejeitar um PEC e, opcionalmente, adicionar uma resposta de texto curto.

![Selecione a mensagem de ponto de extremidade particular](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após a aprovação ou rejeição, a lista refletirá o estado apropriado junto com o texto de resposta

![Selecione o estado final do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Casos de uso de link privado para o banco de dados do Azure para PostgreSQL

Os clientes podem se conectar ao ponto de extremidade privado da mesma VNet, rede virtual emparelhada na mesma região ou via conexão VNet a VNet entre regiões. Além disso, os clientes podem se conectar localmente usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

![Selecione a visão geral do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Como se conectar por meio de uma VM do Azure na VNET (Rede Virtual) emparelhada
Configure o [emparelhamento VNet](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) para estabelecer a conectividade com o banco de dados do Azure para PostgreSQL-servidor único de uma VM do Azure em uma VNet emparelhada.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Como se conectar por meio de uma VM do Azure no ambiente VNET a VNET
Configure a [conexão de gateway VPN de vnet para vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) para estabelecer a conectividade com um banco de dados do Azure para PostgreSQL-servidor único de uma VM do Azure em uma região ou assinatura diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Como se conectar por meio de um ambiente local pela VPN
Para estabelecer a conectividade de um ambiente local para o banco de dados do Azure para PostgreSQL-servidor único, escolha e implemente uma das opções:

* [Conexão ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Conexão VPN site a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Link privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o link privado em combinação com regras de firewall:

* Se você não configurar nenhuma regra de firewall, por padrão, nenhum tráfego será capaz de acessar o banco de dados do Azure para PostgreSQL servidor único.

* Se você configurar o tráfego público ou um ponto de extremidade de serviço e criar pontos de extremidades privados, diferentes tipos de tráfego de entrada serão autorizados pelo tipo de regra de firewall correspondente.

* Se você não configurar nenhum tráfego público ou ponto de extremidade de serviço e criar pontos de extremidades privados, o servidor de banco de dados do Azure para PostgreSQL só poderá ser acessado por meio de pontos de extremidade privados. Se você não configurar o tráfego público ou um ponto de extremidade de serviço, depois que todos os pontos de extremidades particulares aprovados forem rejeitados ou excluídos, nenhum tráfego poderá acessar o banco de dados do Azure para PostgreSQL servidor único.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Negar acesso público para banco de dados do Azure para PostgreSQL servidor único

Se você quiser confiar apenas nos pontos de extremidade privados para acessar o banco de dados do Azure para PostgreSQL servidor único, você pode desabilitar a configuração de todos os pontos de extremidade públicos ([regras de firewall](concepts-firewall-rules.md) e pontos de extremidade de [serviço de VNet](concepts-data-access-and-security-vnet.md)) definindo a configuração de **acesso de rede pública Deny** no servidor de banco de dados. 

Quando essa configuração é definida como *Sim* , somente as conexões por meio de pontos de extremidade privados são permitidas para o banco de dados do Azure para PostgreSQL. Quando essa configuração é definida como *nenhum* cliente pode se conectar ao banco de dados do Azure para PostgreSQL com base em sua configuração de ponto de extremidade de serviço de rede virtual ou firewall. Além disso, quando o valor do acesso à rede privada é definido como os clientes não podem adicionar e/ou atualizar a regra ' regras de firewall ' e ' ponto de extremidade de serviço VNet ' existente

> [!Note]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para PostgreSQL-o servidor único dá suporte a tipos de preço Uso Geral e com otimização de memória.
>
> Essa configuração não tem nenhum impacto sobre as configurações de SSL e TLS para o banco de dados do Azure para PostgreSQL servidor único.

Para saber como definir o **acesso de rede pública Deny** para seu banco de dados do Azure para PostgreSQL servidor único do portal do Azure, consulte [como configurar o acesso de rede pública Deny](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança de servidor único do banco de dados do Azure para PostgreSQL, confira os seguintes artigos:

* Para configurar um firewall para um servidor único do banco de dados do Azure para PostgreSQL, consulte [suporte a firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Para saber como configurar um ponto de extremidade de serviço de rede virtual para o banco de dados do Azure para PostgreSQL servidor único, consulte [Configurar o acesso de redes virtuais](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Para obter uma visão geral do banco de dados do Azure para PostgreSQL conectividade de servidor único, consulte [arquitetura de conectividade do banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)