---
title: Depreciação do recurso de criptografia de dados do Azure Site Recovery | Microsoft Docs
description: Detalhes regarig Azure Site Recovery recurso de criptografia de dados
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134990"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Depreciação do recurso de criptografia de dados de recuperação de site

Este documento descreve os detalhes de depreciação e a ação de remediação que você precisa tomar se estiver usando o recurso de criptografia de dados do Site Recovery enquanto configura a recuperação de desastres de máquinas virtuais Hyper-V para o Azure. 

## <a name="deprecation-information"></a>Informações de depreciação


O recurso de criptografia de dados de recuperação do site estava disponível para os clientes que protegem vms Hyper-V para garantir que os dados replicados estavam protegidos contra ameaças à segurança. este recurso será preterido até **30 de dezembro de 2019**. Ele está sendo substituído pelo recurso Encryption [at Rest](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) mais avançado, que usa o Storage [Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Com o SSE, os dados são criptografados antes de persistirem no armazenamento e descriptografados na recuperação e, após o failover para o Azure, suas VMs serão executadas a partir das contas de armazenamento criptografadas, permitindo um RTO (Recovery Time Objective, objetivo de tempo de recuperação) melhorado.

Observe que se você é um cliente existente usando esse recurso, você teria recebido comunicações com os detalhes de depreciação e etapas de remediação. 


## <a name="what-are-the-implications"></a>Quais são as implicações?

Após **30 de dezembro de 2019**, quaisquer VMs que ainda usam o recurso de criptografia aposentado não serão autorizados a realizar failover. 

## <a name="required-action"></a>Ação necessária
Para continuar as operações de failover bem-sucedidas, e as replicação seguem as etapas mencionadas abaixo:

Siga estas etapas para cada VM: 
1.  [Desativar a replicação](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Crie uma nova política de replicação](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Habilite a replicação](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) e selecione uma conta de armazenamento com SSE ativado.

Depois de concluir a replicação inicial para contas de armazenamento com SSE ativado, suas VMs usarão criptografia no Rest com a Recuperação do Site do Azure.


## <a name="next-steps"></a>Próximas etapas
Planeje realizar as etapas de remediação e execute-as o mais cedo possível. Caso você tenha alguma dúvida sobre essa depreciação, entre em contato com o Suporte da Microsoft. Para ler mais sobre o cenário Hyper-V para Azure, consulte [aqui](hyper-v-vmm-architecture.md).

