---
title: Soluções VMware do Azure (AVS)-nuvens privadas da AVS
description: Saiba mais sobre as nuvens e os conceitos do AVS privado.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024936"
---
# <a name="avs-private-cloud-overview"></a>Visão geral da sincronização de nuvem privada

A AVS transforma e estende cargas de trabalho do VMware para nuvens públicas em minutos. Usando o serviço AVS, você pode implantar o VMware nativamente na infraestrutura bare-metal do Azure. Sua implantação reside em locais do Azure e integra-se totalmente com o restante da nuvem do Azure.

A solução AVS fornece continuidade operacional VMware completa. Essa solução oferece os benefícios da nuvem pública do:

* Elasticidade
* Inovação
* Eficiência

Com a AVS, você se beneficia de um modelo de consumo de nuvem que reduz o custo total de propriedade. Ele também oferece provisionamento sob demanda, pagamento conforme o crescimento e otimização da capacidade.

A AVS é totalmente compatível com:

* Ferramentas existentes
* Habilidades
* Processos

Essa compatibilidade permite que suas equipes gerenciem cargas de trabalho na nuvem do Azure, sem interromper esses tipos de políticas:

* Rede
* Segurança  
* Proteção de dados  
* Audit

A AVS gerencia a infraestrutura e todos os serviços de rede e gerenciamento necessários. O serviço AVS permite que sua equipe se concentre:

* Valor comercial
* Provisionamento de aplicativos
* Continuidade dos negócios
* Suporte
* Aplicação de políticas

## <a name="avs-private-cloud-environment-overview"></a>Visão geral do ambiente de nuvem privada da AVS

Uma nuvem privada de AVS é uma pilha do VMware isolada que dá suporte a:

* Hosts ESXi
* vCenter
* vSAN
* NSX

As nuvens privadas da AVS são gerenciadas por meio do portal da AVS. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento.

A pilha é executada em:

* Nós dedicados
* Nós de hardware bare metal isolados

Os usuários consomem a pilha por meio de ferramentas nativas do VMware, incluindo:

* vCenter
* NSX Manager

Você pode implantar nós dedicados em locais do Azure. Em seguida, você pode gerenciá-los com o Azure e a AVS. Uma nuvem privada de AVS consiste em um ou mais clusters vSphere, e cada cluster contém de 3 a 16 nós.

Você pode criar uma nuvem privada de AVS usando nós adquiridos, pagos conforme o uso ou nós reservados e dedicados.

Você pode conectar a nuvem privada da AVS ao seu ambiente local e à rede do Azure usando as seguintes conexões:

* Segura
* VPN privada
* Azure ExpressRoute

O ambiente de nuvem privada AVS foi projetado para eliminar pontos únicos de falha:

* Os clusters ESXi são configurados com alta disponibilidade do vSphere e são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* a vSAN fornece armazenamento primário redundante. a vSan requer pelo menos três nós para fornecer proteção contra uma única falha. Você pode configurar a vSAN para fornecer maior resiliência para clusters maiores.
* Você pode configurar VMs do vCenter, PSC e NSX Manager com a política de armazenamento RAID-10 para proteger contra falhas de armazenamento. o vSphere HA protege contra falhas de nó e rede.

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>Cenários para implantar uma nuvem privada de AVS

Aqui estão alguns exemplos de casos de uso para a implantação de nuvem privada da AVS.

### <a name="data-center-retirement-or-migration"></a>Desativação ou migração do Data Center

* Obtenha capacidade adicional quando atingir os limites de seu datacenter existente ou atualizar o hardware.
* Adicione a capacidade necessária na nuvem e elimine as dores de cabeça de gerenciamento de atualizações de hardware.
* Reduza o risco e o custo das migrações na nuvem em comparação com conversões demoradas ou rearquitetura.
* Use ferramentas e habilidades conhecidas do VMware para acelerar as migrações na nuvem. Na nuvem, use os serviços do Azure para modernizar seus aplicativos no seu ritmo.

### <a name="expand-on-demand"></a>Expandir sob demanda

* Expanda para a nuvem para atender a necessidades imprevistas, como novos ambientes de desenvolvimento ou picos de capacidade sazonal.
* Crie uma nova capacidade sob demanda e mantenha-a somente contanto que você precise dela.
* Reduza seu investimento antecipado, acelere a velocidade do provisionamento e reduza a complexidade com a mesma arquitetura e políticas em ambos locais e na nuvem.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Recuperação de desastres e áreas de trabalho virtuais na nuvem do Azure

* Estabeleça acesso remoto a dados, aplicativos e áreas de trabalho na nuvem do Azure. Com conexões de largura de banda alta, você carrega/baixa dados rapidamente para se recuperar de incidentes. Redes de baixa latência oferecem tempos de resposta rápidos que os usuários esperam de um aplicativo de desktop.

* Replique todas as suas políticas e redes na nuvem usando o portal da AVS e as ferramentas familiares do VMware. A replicação reduz o esforço e o risco de criar e gerenciar implementações de DR e de VDI.

### <a name="high-performance-applications-and-databases"></a>Aplicativos e bancos de dados de alto desempenho

* Execute suas cargas de trabalho mais exigentes com a arquitetura hiperconvergente fornecida pela AVS.
* Execute Oracle, Microsoft SQL Server, sistemas de middleware e bancos de dados não SQL de alto desempenho.
* Experimente a nuvem como sua própria data center com conexões de rede de 25 Gbps de alta velocidade. Conexões de alta velocidade permitem que você execute aplicativos híbridos que abrangem locais, VMware no Azure e cargas de trabalho privadas do Azure, sem comprometer o desempenho.

### <a name="true-hybrid"></a>Verdadeiro híbrido

* Unificar o DevOps entre os serviços VMware e Azure.
* Otimize a administração do VMware para serviços e soluções do Azure que podem ser aplicadas em todas as suas cargas de trabalho.
* Acesse serviços de nuvem pública sem precisar expandir seu data center ou rearquitetar seus aplicativos.
* Centralize identidades, políticas de controle de acesso, registro em log e monitoramento para aplicativos VMware no Azure.

## <a name="limits"></a>Limites

A tabela a seguir lista os limites de nó nos recursos de uma nuvem privada de AVS.

| Grupos | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada de AVS | 3 |
| Número máximo de nós em um cluster em uma nuvem privada de AVS | 16 |
| Número máximo de nós em uma nuvem privada de AVS | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar uma nuvem privada de AVS](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada AVS](quickstart-create-private-cloud.md)
