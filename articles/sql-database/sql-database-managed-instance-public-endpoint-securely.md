---
title: Pontos finais públicos de instância gerenciada segura
description: Use com segurança pontos finais públicos no Azure com uma instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821732"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Use uma instância gerenciada do Azure SQL Database com segurança com pontos finais públicos

As instâncias gerenciadas do Azure SQL Database podem fornecer conectividade ao usuário sobre [pontos finais públicos](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como tornar essa configuração mais segura.

## <a name="scenarios"></a>Cenários

Uma instância gerenciada do Banco de Dados SQL fornece um ponto final privado para permitir a conectividade de dentro de sua rede virtual. A opção padrão é fornecer o máximo de isolamento. No entanto, existem cenários em que você precisa fornecer uma conexão de ponto final público:

- A instância gerenciada deve integrar-se com ofertas de plataforma como serviço (PaaS) somente para vários inquilinos.
- Você precisa de um throughput maior de troca de dados do que é possível quando você está usando uma VPN.
- As políticas da empresa proíbem o PaaS dentro de redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implantar uma instância gerenciada para acesso ao ponto final público

Embora não seja obrigatório, o modelo de implantação comum para uma instância gerenciada com acesso a ponto final público é criar a instância em uma rede virtual isolada dedicada. Nesta configuração, a rede virtual é usada apenas para isolamento de cluster virtual. Não importa se o espaço de endereço IP da instância gerenciada se sobrepõe ao espaço de endereço IP de uma rede corporativa.

## <a name="secure-data-in-motion"></a>Dados seguros em movimento

O tráfego de dados de instância gerenciada é sempre criptografado se o driver cliente suportar criptografia. Os dados enviados entre a instância gerenciada e outras máquinas virtuais do Azure ou serviços do Azure nunca deixam a espinha dorsal do Azure. Se houver uma conexão entre a instância gerenciada e uma rede local, recomendamos que você use o Azure ExpressRoute com o peering da Microsoft. O ExpressRoute ajuda você a evitar o movimento de dados pela internet pública. Para conectividade privada de instância gerenciada, somente peering privado pode ser usado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloquear conectividade de entrada e saída

O diagrama a seguir mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear conectividade de entrada e saída](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Uma instância gerenciada tem um [endereço de ponto final público dedicado](sql-database-managed-instance-find-management-endpoint-ip-address.md). No firewall de saída do lado do cliente e nas regras do grupo de segurança da rede, defina esse endereço IP de ponto final público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerenciada esteja vindo de fontes confiáveis, recomendamos a conexão de fontes com endereços IP bem conhecidos. Use um grupo de segurança de rede para limitar o acesso ao ponto final público de instância gerenciada na porta 3342.

Quando os clientes precisarem iniciar uma conexão a partir de uma rede local, certifique-se de que o endereço de origem seja traduzido para um conjunto bem conhecido de endereços IP. Se você não pode [fazê-lo](sql-database-managed-instance-configure-p2s.md)(por exemplo, uma força de trabalho móvel sendo um cenário típico), recomendamos que você use conexões VPN ponto a ponto e um ponto final privado .

Se as conexões forem iniciadas a partir do Azure, recomendamos que o tráfego venha de um [endereço IP virtual](../virtual-network/virtual-networks-reserved-public-ip.md) bem conhecido (por exemplo, uma máquina virtual). Para facilitar o gerenciamento de endereços VIP (VIRTUAL) virtuais, você pode querer usar [prefixos públicos de endereço IP](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar o ponto final público para gerenciar instâncias: [Configure o ponto final público](sql-database-managed-instance-public-endpoint-configure.md)
