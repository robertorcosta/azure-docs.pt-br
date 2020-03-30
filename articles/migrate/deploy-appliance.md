---
title: Configure um Applianc Azure Migrate
description: Saiba como configurar um aparelho Azure Migrate para avaliar e migrar VMware VMware.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337656"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Configurou um dispositivo das Migrações para Azure

Este artigo resume as opções para configurar o aparelho Azure Migrate. 

O aparelho Azure Migrate é um aparelho leve de implantação no local, e é usado em vários cenários.

**Cenário** | **Detalhes**
--- | ---
Avalie as VMMs vMware com a azure migrar:avaliação do servidor | Descubra aplicativos e dependências de VM<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.
Avalie as VMs hiper-v com a migração do Azure:avaliação do servidor | Descubra Hiper-VVs<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.
Avaliar máquinas físicas | Descubra máquinas como servidores físicos<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.
Replique VMware VMs com migração sem agente. | Replique VMware VMs sem instalar nada nas VMs que deseja replicar.


## <a name="deployment-options"></a>Opções de implantação

Você pode implantar o aparelho de algumas maneiras.

**Cenário** | **Modelo** | **Script** 
--- | --- | --- | ---
**Avaliar VMs do VMware** | Implante com um modelo OVA baixado.<br/><br/> Aprenda a implantar o aparelho [usando um modelo,](how-to-set-up-appliance-vmware.md)ou inicie o [tutorial de avaliação](tutorial-prepare-vmware.md) e implante o aparelho com um modelo durante o tutorial.  | Implantar usando um script instalador PowerShell.<br/><br/>  [Revise](deploy-appliance-script.md) as instruções do script do aparelho.
**Avaliar VMs do Hyper-V** | Implantar com um modelo VHD baixado. <br/><br/> Aprenda a implantar o aparelho [usando um modelo,](how-to-set-up-appliance-vmware.md)ou inicie o [tutorial de avaliação](tutorial-prepare-vmware.md) e implante o aparelho com um modelo durante o tutorial. | Implantar usando um script instalador PowerShell.<br/><br/> [Revise](deploy-appliance-script.md) as instruções do script do aparelho. 
**Avaliar servidores físicos** | Sem modelo. | Implantar usando um script instalador PowerShell.<br/><br/> Revise [as instruções de script](how-to-set-up-appliance-physical.md)do aparelho ou inicie o tutorial de [avaliação](tutorial-prepare-physical.md)e implante o aparelho durante o tutorial.
**Replicar VMware VMs (sem agente)** | Implante com um modelo OVA baixado.<br/><br/> Se você já avaliou as VMs que você replica, então você já configurou o aparelho durante a avaliação.<br/><br/> Se você replicar VMware VMs sem avaliá-los, aprenda a implantar o aparelho usando um modelo ou inicie o [tutorial de migração sem agente](tutorial-migrate-vmware.md)e implante o aparelho com um modelo durante o tutorial. | Implantar usando um script instalador PowerShell. <br/><br/> [Revise](deploy-appliance-script.md) as instruções do script do aparelho. 




## <a name="next-steps"></a>Próximas etapas

[Revise](migrate-appliance.md) os requisitos do aparelho.