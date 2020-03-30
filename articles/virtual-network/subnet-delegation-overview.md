---
title: O que é delegação de sub-rede na rede virtual Azure?
description: Conheça a delegação da sub-rede na rede virtual Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281331"
---
# <a name="what-is-subnet-delegation"></a>O que é a delegação da sub-rede?

A delegação de sub-rede permite que você designe uma sub-rede específica para um serviço Azure PaaS de sua escolha que precisa ser injetado em sua rede virtual. A delegação da Subnet fornece controle total ao cliente sobre o gerenciamento da integração dos serviços do Azure em suas redes virtuais.

Quando você delega uma sub-rede a um serviço Azure, você permite que esse serviço estabeleça algumas regras básicas de configuração de rede para essa sub-rede, que ajudam o serviço do Azure a operar suas instâncias de forma estável. Como resultado, o serviço Azure pode estabelecer algumas condições de pré ou pós-implantação, tais como:
- implantar o serviço em uma sub-rede compartilhada versus dedicada.
- adicionar ao serviço um conjunto de políticas de intenção de rede pós implantação que é necessário para que o serviço funcione corretamente.

##  <a name="advantages-of-subnet-delegation"></a>Vantagens da delegação de sub-rede

Delegar uma sub-rede a serviços específicos oferece as seguintes vantagens:

- ajuda a designar uma sub-rede para um ou mais serviços do Azure e gerenciar as instâncias na sub-rede conforme os requisitos. Por exemplo, o proprietário da rede virtual pode definir o seguinte para uma sub-rede delegada para gerenciar melhor os recursos e acessar da seguinte forma:
    - políticas de filtragem de rede com grupos de segurança de rede.
    - políticas de roteamento com rotas definidas pelo usuário.
    - integração de serviços com configurações de pontos finais de serviço.
- ajuda os serviços injetados a se integrarem melhor à rede virtual, definindo suas pré-condições de implantações a forma de Políticas de Intenção de Rede. Isso garante que todas as ações que possam afetar o funcionamento do serviço injetado possam ser bloqueadas no PUT.


## <a name="who-can-delegate"></a>Quem pode delegar?
A delegação de sub-rede é um exercício que os proprietários de rede virtuais precisam realizar para designar uma das sub-redes para um Serviço Azure específico. O Azure Service, por sua vez, implanta as instâncias nesta sub-rede para consumo pelas cargas de trabalho do cliente.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impacto da delegação de sub-rede em sua sub-rede
Cada serviço do Azure define seu próprio modelo de implantação, onde eles podem definir quais propriedades eles fazem ou não suportam em uma sub-rede delegada para fins de injeção, como segue:
- subnet compartilhada com outros Serviços Azure ou VM /escala de máquina virtual definida na mesma sub-rede, ou só suporta uma sub-rede dedicada com apenas instâncias deste serviço nele.
- apoia a associação DO NSG com a sub-rede delegada.
- suporta NSG associado à sub-rede delegada também pode ser associado a qualquer outra sub-rede.
- permite a associação da tabela de rotas com a sub-rede delegada.
- permite que a tabela de rota associada à sub-rede delegada seja associada a qualquer outra sub-rede.
- dita o número mínimo de endereços IP na sub-rede delegada.
- dita o espaço de endereço IP na sub-rede delegada a ser do espaço de endereço IP privado (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- dita que a configuração personalizada do DNS tem uma entrada DNS do Azure.

Os serviços injetados também podem adicionar suas próprias políticas da seguinte forma:
- **Políticas de segurança**: Cobrança de regras de segurança necessárias para um determinado serviço funcionar.
- **Políticas de rotas**: Coleta de rotas necessárias para um determinado serviço funcionar.

## <a name="what-subnet-delegation-does-not-do"></a>O que a delegação da sub-rede não faz

Os serviços do Azure que estão sendo injetados em uma sub-rede delegada ainda têm o conjunto básico de propriedades disponíveis para sub-redes não delegadas, tais como:
-  Os serviços do Azure podem injetar instâncias nas sub-redes dos clientes, mas não podem afetar as cargas de trabalho existentes.
-  As políticas ou rotas que esses serviços aplicam são flexíveis e podem ser substituídas pelo cliente.

## <a name="next-steps"></a>Próximas etapas

- [Delegue uma sub-rede](manage-subnet-delegation.md)
