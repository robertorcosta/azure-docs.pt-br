---
title: Recuperação regional de redundância e failover com cache Azure HPC
description: Técnicas para fornecer recursos de failover para recuperação de desastres com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982169"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Use vários caches para recuperação regional de failover

Cada instância de cache do Azure HPC é executada dentro de uma assinatura específica e em uma região. Isso significa que seu fluxo de trabalho de cache pode ser interrompido se a região tiver uma paralisação total.

Este artigo descreve uma estratégia para reduzir o risco de interrupção do trabalho usando uma segunda região para failover de cache.

A chave é o uso de armazenamento back-end acessível em várias regiões. Esse armazenamento pode ser um sistema NAS no local com suporte dns apropriado ou armazenamento Azure Blob que reside em uma região diferente do cache.

À medida que seu fluxo de trabalho prossegue em sua região primária, os dados são salvos no armazenamento de longo prazo fora da região. Se a região do cache ficar indisponível, você poderá criar uma instância de cache Azure HPC duplicada em uma região secundária, conectar-se ao mesmo armazenamento e retomar o trabalho a partir do novo cache.

## <a name="planning-for-regional-failover"></a>Planejamento para failover regional

Para configurar um cache preparado para possível failover, siga estas etapas:

1. Certifique-se de que seu armazenamento back-end esteja acessível em uma segunda região.
1. Ao planejar criar a instância de cache principal, você também deve se preparar para replicar esse processo de configuração na segunda região. Inclua estes itens:

   1. Estrutura de rede virtual e sub-rede
   1. Capacidade de cache
   1. Detalhes do destino de armazenamento, nomes e caminhos de namespace
   1. Detalhes sobre máquinas clientes, se eles estão localizados na mesma região que o cache
   1. Comando de montagem para uso por clientes de cache

   > [!NOTE]
   > O Cache Azure HPC pode ser criado de forma programática, seja através de um [modelo do Azure Resource Manager](../azure-resource-manager/templates/overview.md) ou acessando diretamente sua API. Entre em contato com a equipe de cache do Azure HPC para obter detalhes.

## <a name="failover-example"></a>Exemplo de failover

Como exemplo, imagine que você deseja localizar seu Cache Azure HPC na região leste dos EUA do Azure. Ele acessará dados armazenados em seu data center local.

Você pode usar um cache na região oeste dos EUA 2 como um backup failover.

Ao criar o cache no Leste dos EUA, prepare um segundo cache para implantação no West US 2. Você pode usar scripting ou modelos para automatizar essa preparação.

No caso de uma falha em toda a região no leste dos EUA, crie o cache que você preparou na região oeste dos EUA 2.

Depois que o cache for criado, adicione metas de armazenamento que apontam para os mesmos armazenamentos de dados no local e use os mesmos caminhos de namespace agregados que os alvos de armazenamento do cache antigo.

Se os clientes originais forem afetados, crie novos clientes na região oeste dos EUA 2 para uso com o novo cache.

Todos os clientes precisarão montar o novo cache, mesmo que os clientes não tenham sido afetados pela paralisação da região. O novo cache tem diferentes endereços de montagem do antigo.

## <a name="learn-more"></a>Saiba mais

O guia de arquitetura de aplicativos do Azure inclui mais informações sobre como [se recuperar de uma interrupção de serviço em toda a região.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
