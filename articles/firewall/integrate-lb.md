---
title: Integrar o Firewall do Azure com o Azure Standard Load Balancer
description: Você pode integrar um Firewall do Azure em uma rede virtual com um Azure Standard Load Balancer (público ou interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196681"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Firewall do Azure com o Azure Standard Load Balancer

Você pode integrar um Firewall do Azure em uma rede virtual com um Azure Standard Load Balancer (público ou interno). 

O design preferido é integrar um balanceador de carga interna com o firewall do Azure, já que este é um design muito mais simples. Você pode usar um balanceador de carga pública se você já tiver um implantado e quiser mantê-lo no lugar. No entanto, você precisa estar ciente de um problema de roteamento assimétrico que pode interromper a funcionalidade com o cenário de balanceador de carga público.

Para obter mais informações sobre o Azure Load Balancer, confira [O que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um balanceador de carga público, o balanceador de carga é implantado com um endereço IP de front-end público.

### <a name="asymmetric-routing"></a>Roteamento assimétrico

O roteamento assimétrico ocorre quando um pacote usa um caminho para o destino e outro caminho para retornar para a origem. Isso ocorre quando uma sub-rede tem uma rota padrão que vai para o endereço IP privado do firewall e você está usando um balanceador de carga público. Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Uma vez que o firewall é imponente, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão tão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema de roteamento

Quando você implanta um Firewall do Azure em uma sub-rede, uma etapa é criar uma rota padrão para a sub-rede, direcionando os pacotes por meio do endereço IP privado do firewall localizado no AzureFirewallSubnet. Para obter mais informações, consulte [Tutorial: Implante e configure o Firewall Do Azure usando o portal Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduz o firewall em seu cenário de balanceador de carga, você deseja que o tráfego de Internet entre por meio do endereço IP público do firewall. Daí, o firewall aplica suas regras de firewall e NATs aos pacotes para o endereço IP público do balanceador de carga. E o problema ocorre aí. Os pacotes chegam no endereço IP público do firewall, mas retornam para o firewall por meio do endereço IP privado (usando a rota padrão).
Para evitar esse problema, crie uma rota de host adicional para o endereço IP público do firewall. Pacotes que vão para o endereço IP público do firewall são roteadas pela Internet. Isso evita o uso da rota padrão para o endereço IP privado do firewall.

![Roteamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exemplo de tabela de rota

Por exemplo, as seguintes rotas são para um firewall no endereço IP público 20.185.97.136 e endereço IP privado 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela de rotas](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exemplo de regra NAT

No exemplo a seguir, uma regra NAT traduz o tráfego RDP para o firewall em 20.185.97.136 para o balanceador de carga em 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Regra NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Investigações de integridade

Lembre-se, você precisa ter um serviço web em execução nos hosts no pool de balanceadores de carga se você usar testes de saúde TCP para porta80 ou testes HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um balanceador de carga interno, o balanceador de carga é implantado com um endereço IP de front-end privado.

Não há nenhum problema de roteamento assimétrico com esse cenário. Os pacotes de entrada chegam ao endereço IP público do firewall, são convertidos para o endereço IP privado do balanceador de carga e, em seguida, retornam para o endereço IP privado do firewall usando o mesmo caminho de retorno.

Portanto, você pode implantar este cenário de forma semelhante ao cenário do balanceador de carga público, mas sem a necessidade da rota de host com endereço IP público do firewall.

## <a name="additional-security"></a>Segurança adicional

Para melhorar ainda mais a segurança do seu cenário de balanceamento de carga, você pode usar NSGs (grupos de segurança de rede).

Por exemplo, você pode criar um NSG na sub-rede de back-end em que se encontram as máquinas virtuais de balanceamento de carga. Permita o tráfego de entrada provenientes do endereço IP/porta do firewall.

![Grupo de segurança de rede](media/integrate-lb/nsg-01.png)

Para obter mais informações sobre os NSGs, consulte [grupos de segurança](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).