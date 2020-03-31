---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471664"
---
Enquanto na visualização, os discos gerenciados que tenham discos compartilhados ativados estão sujeitos às seguintes limitações:

- Atualmente, disponível apenas com SSDs premium.
- Atualmente, só é apoiado na região centro-oeste dos EUA.
- Todas as máquinas virtuais que compartilham um disco devem ser implantadas nos [mesmos grupos de colocação de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Só pode ser ativado em discos de dados, não em discos do sistema operacional.
- Apenas discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para obter detalhes ver [requisitos de hardware de clusterdo Failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O cache host ReadOnly não está disponível `maxShares>1`para SSDs premium com .
- Conjuntos de disponibilidade e conjuntos `FaultDomainCount` de escala de máquinas virtuais só podem ser usados com conjunto para 1.
- O suporte ao Azure Backup e ao Azure Site Recovery ainda não está disponível.

Se você estiver interessado em tentar discos compartilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).
