---
title: Private Link - Banco de dados Azure para MySQL
description: Saiba como o link privado funciona para o Banco de Dados Azure para MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371283"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Link privado para banco de dados Azure para MySQL

O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Virtual privada (VNet). Os recursos PaaS podem ser acessados usando o endereço IP privado, assim como qualquer outro recurso no VNet.

Para obter uma lista de serviços PaaS que suportem a funcionalidade do Private Link, revise a [documentação](https://docs.microsoft.com/azure/private-link/index)do Private Link . Um ponto de extremidade privado é um endereço IP privado em uma [VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e sub-rede específicas.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure, onde o Azure Database for MySQL suporta níveis de preços otimizados de uso geral e memória.

## <a name="data-exfiltration-prevention"></a>Prevenção contra exportação de dados

Exfiltração de dados no Banco de Dados Do Azure para MySQL é quando um usuário autorizado, como um administrador de banco de dados, é capaz de extrair dados de um sistema e movê-los para outro local ou sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário executando mysql workbench dentro de uma Máquina Virtual (VM) do Azure que está se conectando a um banco de dados Azure para servidor MySQL provisionado no Oeste dos EUA. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos no Banco de Dados Azure para MySQL usando controles de acesso à rede.

* Desative todo o tráfego de serviço do Azure para o MySQL via ponto final público, definindo *Permitir que os serviços do Azure* desacabem. Certifique-se de que nenhum endereço IP ou intervalos são permitidos para acessar o servidor através de regras de [firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) ou [pontos finais de serviço de rede virtual](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Apenas permita o tráfego para o Banco de Dados Azure para MySQL usando o endereço IP privado da VM. Para obter mais informações, confira os artigos sobre o [ponto de extremidade de serviço](concepts-data-access-and-security-vnet.md) e as [regras de firewall da VNET](howto-manage-vnet-using-portal.md).

* Na VM do Azure, restrinja o escopo da conexão de saída usando NSGs (grupos de segurança de rede) e Marcas de Serviço, conforme mostrado a seguir

    * Especifique uma regra DO NSG para permitir o tráfego para *tag de serviço = SQL. WestUs* - permitindo apenas a conexão ao Banco de Dados Azure para MySQL no Oeste dos EUA
    * Especifique uma regra NSG (com uma prioridade maior) para negar tráfego para *Tag de Serviço = SQL* - negando conexões ao Update to Azure Database for MySQL em todas as regiões</br></br>

No final desta configuração, o Azure VM pode se conectar apenas ao Azure Database for MySQL na região oeste dos EUA. No entanto, a conectividade não está restrita a um único banco de dados Azure para MySQL. O VM ainda pode se conectar a qualquer banco de dados do Azure para MySQL na região oeste dos EUA, incluindo os bancos de dados que não fazem parte da assinatura. Embora tenhamos reduzido o escopo da exportação de dados no cenário acima a uma região específica, não o eliminamos por completo.</br>

Com o Private Link, agora você pode configurar controles de acesso de rede como NSGs para restringir o acesso ao ponto final privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um insider mal-intencionado só pode acessar o recurso PaaS mapeado (por exemplo, um banco de dados Azure para MySQL) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local no emparelhamento privado

Quando você se conecta ao ponto final público a partir de máquinas locais, seu endereço IP precisa ser adicionado ao firewall baseado em IP usando uma regra de firewall em nível de servidor. Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciá-lo em um ambiente de produção.

Com o Private Link, você pode habilitar o acesso entre instalações ao ponto final privado usando [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), peering privado ou [túnel VPN](https://docs.microsoft.com/azure/vpn-gateway/). Posteriormente, eles podem desativar todo o acesso via ponto final público e não usar o firewall baseado em IP.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Configure o Link Privado para Banco de Dados Azure para MySQL

### <a name="creation-process"></a>Processo de criação

Os pontos finais privados são necessários para ativar o Private Link. Isso pode ser feito usando os seguintes guias de como fazer.

* [Portal Azure](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo de aprovação
Uma vez que o administrador de rede crie o ponto final privado (PE), o administrador Do MySQL pode gerenciar o Programa de Conexão de ponto final privado (PEC) para o Banco de Dados Azure para MySQL. Essa separação de funções entre o administrador de rede e o DBA é útil para o gerenciamento do Banco de Dados Azure para conectividade MySQL. 

* Navegue até o banco de dados do Azure para o recurso do servidor MySQL no portal Azure. 
    * Selecione as conexões de ponto final privado no painel esquerdo
    * Mostra uma lista de todas as Conexões de ponto final privado (PECs)
    * Ponto final privado correspondente (PE) criado

![selecionar o portal de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione uma PEC individual na lista selecionando-a.

![selecionar o ponto final privado pendente aprovação](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O admin do servidor MySQL pode optar por aprovar ou rejeitar uma PEC e, opcionalmente, adicionar uma resposta de texto curta.

![selecionar a mensagem de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após aprovação ou rejeição, a lista refletirá o estado apropriado junto com o texto de resposta

![selecionar o estado final do ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Use casos de Private Link para Banco de Dados Azure para MySQL

Os clientes podem se conectar ao ponto final privado a partir do mesmo VNet, VNet peered na mesma região ou através da conexão VNet-to-VNet entre regiões. Além disso, os clientes podem se conectar localmente usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

![selecionar a visão geral do ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Como se conectar por meio de uma VM do Azure na VNET (Rede Virtual) emparelhada
Configure o [vnet peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) para estabelecer a conectividade com o Banco de Dados Azure para MySQL a partir de uma VM Azure em um VNet peered.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Como se conectar por meio de uma VM do Azure no ambiente VNET a VNET
Configure [a conexão de gateway VNet-to-VNet VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) para estabelecer conectividade a um banco de dados Azure para MySQL a partir de um VM Azure em uma região ou assinatura diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Como se conectar por meio de um ambiente local pela VPN
Para estabelecer a conectividade a partir de um ambiente local ao Banco de Dados Azure para MySQL, escolha e implemente uma das opções:

* [Conexão ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Conexão VPN site a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o Private Link em combinação com as regras de firewall:

* Se você não configurar nenhuma regra de firewall, então, por padrão, nenhum tráfego poderá acessar o Banco de Dados Do Azure para MySQL.

* Se você configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada serão autorizados pelo tipo correspondente de regra de firewall.

* Se você não configurar nenhum tráfego público ou ponto final de serviço e criar pontos finais privados, então o Banco de Dados Azure para MySQL será acessível apenas através dos pontos finais privados. Se você não configurar o tráfego público ou um ponto final de serviço, depois que todos os pontos finais privados aprovados forem rejeitados ou excluídos, nenhum tráfego poderá acessar o Banco de Dados Do Azure para MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Negar acesso público ao Banco de Dados Azure para MySQL

Se você quiser confiar apenas em pontos finais privados para acessar seu Banco de Dados Azure para MySQL, você pode desativar a configuração de todos os pontos finais públicos (ou [seja, regras](concepts-firewall-rules.md) de firewall e [pontos finais de serviço VNet)](concepts-data-access-and-security-vnet.md)definindo a configuração Deny Public Network **Access** no servidor de banco de dados. 

Quando esta configuração é definida como *YES*, somente conexões via endpoints privados são permitidas ao seu Banco de Dados Azure para MySQL. Quando esta configuração estiver definida como *NO,* os clientes podem se conectar ao banco de dados Do Azure para MySQL com base nas configurações de ponto final do firewall ou do vnet. Além disso, uma vez definido o valor do acesso à rede privada, você não pode adicionar e/ou atualizar as regras de ponto final de serviço de firewall e vnet existentes.

> [!Note]
> Esse recurso está disponível em todas as regiões do Azure onde o Banco de Dados Azure para PostgreSQL - Servidor único suporta níveis de preços otimizados para propósito geral e memória otimizada.
>
> Essa configuração não tem nenhum impacto nas configurações SSL e TLS para o seu Banco de Dados Azure para MySQL.

Para saber como definir o Acesso à **Rede Pública Negar** para o seu Banco de Dados Azure para MySQL a partir do portal Azure, consulte Como configurar o Deny Public Network [Access](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do Azure para mysql, consulte os seguintes artigos:

* Para configurar um firewall para o Azure Database for MySQL, consulte [suporte ao Firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Para saber como configurar um ponto final de serviço de rede virtual para o seu Banco de Dados Azure para MySQL, consulte [Configurar acesso a partir de redes virtuais](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Para obter uma visão geral do banco de dados Azure para conectividade MySQL, consulte [O banco de dados Azure para arquitetura de conectividade MySQL](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
