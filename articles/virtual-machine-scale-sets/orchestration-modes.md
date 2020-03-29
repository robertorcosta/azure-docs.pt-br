---
title: Saiba mais sobre os modos de orquestração para conjuntos de escala de máquinas virtuais no Azure
description: Saiba mais sobre os modos de orquestração para conjuntos de escala de máquinas virtuais no Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279055"
---
# <a name="orchestration-mode-preview"></a>Modo de orquestração (pré-visualização)

Os conjuntos de escala de máquinas virtuais fornecem um agrupamento lógico de máquinas virtuais gerenciadas por plataformas. Com conjuntos de escala, você cria um modelo de configuração de máquina virtual, adiciona ou remove automaticamente instâncias adicionais com base na carga de CPU ou memória e atualiza automaticamente para a versão mais recente do SISTEMA OPERACIONAL. Tradicionalmente, os conjuntos de escala permitem criar máquinas virtuais usando um modelo de configuração VM fornecido no momento da criação do conjunto de escalas, e o conjunto de escala só pode gerenciar máquinas virtuais que são criadas implicitamente com base no modelo de configuração.

Com o modo de orquestração (visualização) definido em escala, agora você pode escolher se o conjunto de escalas deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de escala, ou instâncias de máquinas virtuais criadas implicitamente com base no modelo de configuração. O modo de orquestração de conjunto de escalas também ajuda você a projetar sua infra-estrutura de VM para alta disponibilidade, implantando essas VMs em domínios de falhas e Zonas de Disponibilidade.


Os conjuntos de escala de máquinas virtuais suportarão 2 modos de orquestração distintos:

- ScaleSetVM – As instâncias da máquina virtual adicionadas ao conjunto de escalas são baseadas no modelo de configuração do conjunto de escalas. O ciclo de vida da instância da máquina virtual - criação, atualização, exclusão - é gerenciado pelo conjunto de escalas.
- VM (máquinas virtuais) – Máquinas virtuais criadas fora do conjunto de escalas podem ser explicitamente adicionadas ao conjunto de escalas. 
 

> [!IMPORTANT]
> O modo de orquestração é definido quando você cria o conjunto de escalas e não pode ser alterado ou atualizado posteriormente. 
> 
> Este recurso de conjuntos de escala de máquinas virtuais está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modos de orquestração

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modelo de configuração vm      | Nenhum                                       | Obrigatório |
| Adicionando nova VM ao conjunto de escalas  | As VMs são explicitamente adicionadas ao conjunto de escalas quando a VM é criada. | As VMs são implicitamente criadas e adicionadas ao conjunto de escalas com base no modelo de configuração vM, contagem de instâncias e regras de AutoScaling | |
| Excluir VM                   | As VMs devem ser excluídas individualmente, o conjunto de escalas não será excluído se tiver alguma VM nele. | As VMs podem ser excluídas individualmente, excluindo o conjunto de escalas excluirá todas as instâncias de VM.  |
| Anexar/Desapegar VMs           | Sem suporte                              | Sem suporte |
| Ciclo de vida de instância (Criação através de exclusão) | VMs e seus artefatos (como discos e NICs) podem ser gerenciados de forma independente. | As instâncias e seus artefatos (como discos e NICs) estão implícitos nas instâncias de conjunto de escala que os criam. Eles não podem ser separados ou gerenciados separadamente fora do conjunto de escalas |
| Domínios de falha               | Pode definir domínios de falha. 2 ou 3 com base no suporte regional e 5 para zona de disponibilidade. | Pode definir domínios de falha indo de 1 a 5 |
| Atualizar domínios              | Os domínios de atualização são automaticamente mapeados para domínios de falha | Os domínios de atualização são automaticamente mapeados para domínios de falha |
| Zonas de Disponibilidades          | Suporta implantação regional ou VMs em uma Zona de Disponibilidade | Suporta implantação regional ou várias Zonas de Disponibilidade; Pode definir a estratégia de balanceamento de zona |
| Autoescala                   | Sem suporte                              | Com suporte |
| Atualização do SISTEMA OPERACIONAL                  | Sem suporte                              | Com suporte |
| Atualizações de modelos               | Sem suporte                              | Com suporte |
| Controle de instâncias            | Controle de VM completo. As VMs possuem URI totalmente qualificados que suportam toda a gama de recursos de gerenciamento de VM do Azure (como Azure Policy, Azure Backup e Azure Site Recovery) | VMs são recursos dependentes do conjunto de escalas. As instâncias só podem ser acessadas para o gerenciamento através do conjunto de escalas. |
| Modelo de instância              | Definição do modelo Microsoft.Compute/VirtualMachines. | Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines definição de modelo. |
| Capacity                    | Um conjunto de escalas vazia pode ser criado; até 200 VMs podem ser adicionados ao conjunto de escalas | Os conjuntos de escala podem ser definidos com uma contagem de ocorrências 0 - 1000 |
| Mover                        | Com suporte                                  | Com suporte |
| Grupo de colocação única == falso | Sem suporte                          | Com suporte |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte a [visão geral das opções de disponibilidade](availability.md).
