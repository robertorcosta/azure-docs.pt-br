---
title: Solução Azure VMware por CloudSimple - Nuvens Privadas
description: Conheça cloudsimple private clouds e conceitos.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024936"
---
# <a name="cloudsimple-private-cloud-overview"></a>Visão geral da Nuvem Privada Cloud

CloudSimple transforma e estende cargas de trabalho do VMware para nuvens públicas em minutos. Usando o serviço CloudSimple, você pode implantar o VMware nativamente na infra-estrutura de metal nu do Azure. Sua implantação vive em locais do Azure e se integra totalmente com o resto da nuvem do Azure.

A solução CloudSimple fornece continuidade operacional completa do VMware. Esta solução oferece aos benefícios da nuvem pública:

* Elasticidade
* Inovação
* Eficiência

Com o CloudSimple, você se beneficia de um modelo de consumo em nuvem que reduz seu custo total de propriedade. Ele também oferece provisionamento demanda, pay-as-you-grow e otimização de capacidade.

O CloudSimple é totalmente compatível com:

* Ferramentas existentes
* Habilidades
* Processos

Essa compatibilidade permite que suas equipes gerenciem cargas de trabalho na nuvem do Azure, sem interromper esses tipos de políticas:

* Rede
* Segurança  
* Proteção de dados  
* Audit

A CloudSimple gerencia a infra-estrutura e todos os serviços de rede e gerenciamento necessários. O serviço CloudSimple permite que sua equipe se concentre em:

* Valor do negócio
* Provisionamento de aplicativo
* Continuidade de negócios
* Suporte
* Aplicação de políticas

## <a name="private-cloud-environment-overview"></a>Visão geral do ambiente de nuvem privada

Uma Nuvem Privada é uma pilha de VMware isolada que suporta:

* Hosts ESXi
* vCenter
* vSAN
* Nsx

As nuvens privadas são gerenciadas através do portal CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento.

A pilha é executada:

* Nós dedicados
* Nódulos de hardware de metal nu isolados

Os usuários consomem a pilha através de ferramentas VMware nativas, incluindo:

* vCenter
* Gerente NSX

Você pode implantar nódulos dedicados em locais do Azure. Então você pode gerenciá-los com Azure e CloudSimple. Uma Nuvem Privada consiste em um ou mais clusters vSphere, e cada cluster contém 3 a 16 nós.

Você pode criar uma Nuvem Privada usando nomes comprados, pay-as-you-go ou dedos dedicados reservados.

Você pode conectar a Nuvem Privada ao seu ambiente local e à rede Azure usando as seguintes conexões:

* Seguro
* VPN privada
* Azure ExpressRoute

O ambiente Private Cloud foi projetado para eliminar pontos únicos de falha:

* Os clusters ESXi são configurados com alta disponibilidade vSphere e são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* O vSAN fornece armazenamento primário redundante. VSan requer pelo menos três nodos para fornecer proteção contra uma única falha. Você pode configurar o vSAN para fornecer maior resiliência para clusters maiores.
* Você pode configurar vMs vCenter, PSC e NSX Manager com política de armazenamento RAID-10 para proteger contra falhas de armazenamento. O vSphere HA protege contra falhas de nó e rede.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários para implantação de uma Nuvem Privada

Aqui estão alguns exemplos de casos de uso para implantação do Private Cloud.

### <a name="data-center-retirement-or-migration"></a>Aposentadoria ou migração de data center

* Obtenha capacidade adicional quando atingir os limites do data center existente ou atualizar o hardware.
* Adicione a capacidade necessária na nuvem e elimine as dores de cabeça do gerenciamento de atualizações de hardware.
* Reduza o risco e o custo das migrações em nuvem em comparação com conversões ou rearquitetura demoradas.
* Use ferramentas e habilidades conhecidas do VMware para acelerar migrações em nuvem. Na nuvem, use os serviços do Azure para modernizar seus aplicativos no seu ritmo.

### <a name="expand-on-demand"></a>Expandir sob demanda

* Expanda-se para a nuvem para atender a necessidades inesperadas, como novos ambientes de desenvolvimento ou rajadas de capacidade sazonais.
* Crie uma nova capacidade sob demanda e mantenha-a apenas enquanto você precisar.
* Reduza seu investimento inicial, acelere a velocidade de provisionamento e reduza a complexidade com a mesma arquitetura e políticas tanto no local quanto na nuvem.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Recuperação de desastres e desktops virtuais na nuvem DoZure

* Estabeleça acesso remoto a dados, aplicativos e desktops na nuvem do Azure. Com conexões de alta largura de banda, você carrega / baixa dados rapidamente para se recuperar de incidentes. Redes de baixa latência dão tempos de resposta rápidos que os usuários esperam de um aplicativo de desktop.

* Replique todas as suas políticas e redes na nuvem usando o portal CloudSimple e ferramentas familiares do VMware. A replicação reduz o esforço e o risco de criar e gerenciar implementações de DR e VDI.

### <a name="high-performance-applications-and-databases"></a>Aplicativos e bancos de dados de alto desempenho

* Execute suas cargas de trabalho mais exigentes com a arquitetura hiperconvergente fornecida pelo CloudSimple.
* Execute o servidor Oracle, o servidor Microsoft SQL, os sistemas de middleware e os bancos de dados sem SQL de alto desempenho.
* Experimente a nuvem como seu próprio data center com conexões de rede de alta velocidade de 25 Gbps. As conexões de alta velocidade permitem executar aplicativos híbridos que abrangem no local, VMware no Azure e cargas de trabalho privadas do Azure, sem comprometer o desempenho.

### <a name="true-hybrid"></a>Verdadeiro híbrido

* Unifique devOps em serviços VMware e Azure.
* Otimize a administração do VMware para serviços e soluções do Azure que podem ser aplicadas em todas as suas cargas de trabalho.
* Acesse serviços de nuvem pública sem ter que expandir seu data center ou reprojetar seus aplicativos.
* Centralize identidades, políticas de controle de acesso, registro e monitoramento de aplicativos VMware no Azure.

## <a name="limits"></a>limites

A tabela a seguir lista os limites de nó nos recursos de uma Nuvem Privada.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nódulos em um cluster em uma nuvem privada | 16 |
| Número máximo de nódulos em uma nuvem privada | 64 |
| Número mínimo de nódulos em um novo cluster | 3 |

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [criar uma Nuvem Privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de Nuvem Privada](quickstart-create-private-cloud.md)
