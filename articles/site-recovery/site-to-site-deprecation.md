---
title: Depreciação da recuperação de desastres entre sites gerenciados pelo cliente (com VMM) usando o Azure Site Recovery | Microsoft Docs
description: Detalhes sobre a próxima depreciação do DR entre sites de propriedade do cliente usando Hyper-V e entre sites gerenciados pela SCVMM para o Azure e opções alternativas
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661663"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Depreciação da recuperação de desastres entre sites gerenciados pelo cliente (com VMM) usando o Azure Site Recovery

Este artigo descreve o próximo plano de depreciação, as implicações correspondentes e as opções alternativas disponíveis para os clientes para o seguinte cenário:

DR entre sites de propriedade do cliente gerenciados pelo System Center Virtual Machine Manager (SCVMM) usando a Recuperação de Sites

> [!IMPORTANT]
> Os clientes são aconselhados a tomar as medidas de remediação o mais cedo possível para evitar qualquer interrupção em seu ambiente. 

## <a name="what-changes-should-you-expect"></a>Que mudanças você deve esperar?

- A partir de março de 2020, você receberá notificações do portal Azure & comunicações por e-mail com a próxima depreciação da replicação local-a-local de Hiper-V VMs. A depreciação está prevista para março de 2023.

- Se você tiver uma configuração existente, não haverá impacto na configuração.

- Uma vez que os cenários são preteridos, a menos que o cliente siga as abordagens alternativas, as replicações existentes podem ser interrompidas. Os clientes não poderão visualizar, gerenciar ou realizar quaisquer operações relacionadas a DR através da experiência de recuperação do site Do Azure no portal Azure.
 
## <a name="alternatives"></a>Alternativas 

Abaixo estão as alternativas que o cliente pode escolher para garantir que sua estratégia de DR não seja impactada uma vez que o cenário é preterido. 

- Opção 1 (Recomendado): Escolha começar a [usar o Azure como alvo DR](hyper-v-vmm-azure-tutorial.md).


- Opção 2: Escolha continuar com a replicação local-a-site usando a [solução de réplica Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)subjacente, mas você não será capaz de gerenciar configurações de DR usando o Azure Site Recovery no portal Azure. 


## <a name="remediation-steps"></a>Etapas de remediação

Se você estiver escolhendo ir com a Opção 1, execute as seguintes etapas:

1. [Desativar a proteção de todas as máquinas virtuais associadas às VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Use a **opção Desativar a replicação e remover** ou executar os scripts mencionados para garantir que as configurações de replicação no local sejam limpas. 

2. [Cancelar o registro de todos os servidores VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) da configuração de replicação site-a-site.

3. [Prepare os recursos do Azure](tutorial-prepare-azure-for-hyperv.md) para habilitar a replicação de suas VMs.
4. [Preparar servidores Hyper-V locais](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurar a replicação das VMs na nuvem VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcional, mas recomendado: [Execute uma broca DR](tutorial-dr-drill-azure.md)

Se você estiver escolhendo ir com a Opção 2 de usar a réplica Hyper-V, execute as seguintes etapas:

1. Em **Itens Protegidos** > **Itens Replicados,** clique com o botão direito do mouse na máquina > **Desativar a replicação**.
2. Na **replicação de sacada,** **selecione Remover**.

    Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

## <a name="next-steps"></a>Próximas etapas
Planeje a depreciação e escolha uma opção alternativa mais adequada para sua infra-estrutura e negócios. Caso você tenha alguma dúvida sobre isso, entre em contato com o Suporte da Microsoft

