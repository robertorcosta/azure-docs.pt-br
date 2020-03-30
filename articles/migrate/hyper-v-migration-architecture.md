---
title: Como funciona a migração do Hyper-V no Azure Migrate?
description: Saiba mais sobre a migração do Hyper-V com o Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185875"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação do Hyper-V?

Este artigo fornece uma visão geral da arquitetura e dos processos usados quando você migra as VMs Hyper-V com a ferramenta Azure Migrate Server Migration.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

## <a name="agentless-migration"></a>Migração sem agente

A ferramenta Migração de Servidor Azure Migrate fornece replicação sem agente para VMs Hyper-V no local, usando um fluxo de trabalho de migração otimizado para Hyper-V. Você instala um agente de software apenas em hosts Hyper-V ou nós de cluster. Nada precisa ser instalado em Hiper-VVs.

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e recuperação do site do Azure

O Azure Migrate Server Migration é uma ferramenta para migrar cargas de trabalho no local e VMs baseadas em nuvem para o Azure. O Site Recovery é uma ferramenta de recuperação de desastres. As ferramentas compartilham alguns componentes de tecnologia comuns usados para replicação de dados, mas servem a diferentes propósitos. 


## <a name="architectural-components"></a>Componentes de arquitetura

![Arquitetura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implantação** | 
--- | --- 
**Provedor de replicação** | O provedor de recuperação do site do Microsoft Azure está instalado em hosts Hyper-V e registrado no Azure Migration Server Migration.<br/> O provedor orquestra a replicação de VMs Hyper-V.
**Agente dos Serviços de Recuperação** | O agente microsoft azure recovery service lida com a replicação de dados. Ele trabalha com o provedor para replicar dados de VMs Hyper-V para Azure.<br/> Os dados replicados são carregados em uma conta de armazenamento em sua assinatura do Azure. A ferramenta Migração do servidor processa os dados replicados e aplica-os a discos de réplica na assinatura. Os discos de réplica são usados para criar as VMs do Azure quando você migra.

- Os componentes são instalados por um único arquivo de configuração, baixado do Azure Migrate Server Migration no portal.
- O provedor e o aparelho usam conexões HTTPS de saída 443 para se comunicar com a migração do servidor Azure Migrate.
- As comunicações do provedor e do agente são seguras e criptografadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para um Hiper-V VM, a replicação inicial começa.
2. Uma foto do Hyper-V VM é tirada.
3. VHDs na VM são replicados um por um, até que todos sejam copiados para o Azure. O tempo inicial de replicação depende do tamanho da VM e da largura de banda da rede.
4. As alterações de disco que ocorrem durante a replicação inicial são rastreadas usando a Réplica Hyper-V e armazenadas em arquivos de log (arquivos hrl).
    - Os arquivos de log estão na mesma pasta que os discos.
    - Cada disco tem um arquivo hrl associado que é enviado para armazenamento secundário.
    - O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Após o término da replicação inicial, o instantâneo do VM é excluído e a replicação delta começa.
5. Alterações incrementais do disco são rastreadas em arquivos hrl. Os registros de replicação são carregados periodicamente em uma conta de armazenamento do Azure pelo agente Serviços de Recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

O desempenho de replicação do Hyper-V é influenciado por fatores que incluem o tamanho da VM, a taxa de alteração de dados (churn) das VMs, o espaço disponível no host Hyper-V para armazenamento de arquivos de log, a largura de banda de upload para dados de replicação e o armazenamento de destino no Azure.

- Se você estiver replicando várias máquinas ao mesmo tempo, use o [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) for Hyper-V, para ajudar a otimizar a replicação.
- Planeje sua replicação Hyper-V e distribua a replicação sobre contas de armazenamento do Azure, de acordo com a capacidade.

### <a name="control-upload-throughput"></a>Throughput de upload de controle

Você pode limitar a quantidade de largura de banda usada para carregar dados para o Azure em cada host Hyper-V. Tenha cuidado. Se você definir os valores muito baixos, isso afetará negativamente a replicação e atrasará a migração.


1. Faça login no host Hyper-V ou node de cluster.
2. Executar **C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, para abrir o snap-in do Windows Azure Backup MMC.
3. No snap-in, selecione **Alterar as Propriedades**.
4. Em **Estrangular,** **selecione Ativar o estrangulamento do uso da largura de banda da Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenciar a eficiência do upload

Se você tiver largura de banda sobressalente para replicação e quiser aumentar os uploads, poderá aumentar o número de threads alocados para a tarefa de upload, da seguinte forma:

1. Abra o registro com Regedit.
2. Navegue até a tecla HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replicação\UploadThreadsPerVM
3. Aumente o valor do número de threads usados para upload de dados para cada VM replicante. O valor padrão é 4 e o valor máximo é 32. 




## <a name="next-steps"></a>Próximas etapas

Experimente a [migração do Hyper-V](tutorial-migrate-hyper-v.md) usando a migração do servidor Azure Migrate.
