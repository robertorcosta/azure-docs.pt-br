---
title: Migração baseada em agente na migração do servidor Azure Migrate
description: Fornece uma visão geral da migração de VMM vms baseada em agentes no Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425843"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agente

Este artigo fornece uma visão geral da arquitetura e dos processos usados para replicação baseada em agentes de VMms VMware com a ferramenta [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Usando o Azure Migrate: Migração do servidor, você pode replicar VMware VMs com algumas opções:

- Migrar VMs usando replicação baseada em agente, conforme descrito neste artigo.
- Migre as VMMs vMware usando a replicação sem agente. Isso migra VMs sem precisar instalar nada sobre eles.

Saiba mais sobre [como selecionar e comparar](server-migrate-overview.md) métodos de migração para VMware VMs. 


## <a name="agent-based-migration"></a>Migração baseada em agentes

A migração baseada em agentes é usada para migrar vMs vmware e servidores físicos para o Azure. Ele também pode ser usado para migrar outros servidores virtualizados no local, bem como VMs de nuvem privada e pública, incluindo instâncias AWS e VMs GCP. A migração baseada em agentes no Azure Migrate usa algumas funcionalidades de back-end do serviço [Azure Site Recovery.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes de arquitetura

O diagrama ilustra os componentes envolvidos na migração baseada em agentes.

![Arquitetura](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes usados para migração baseada em agentes.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Aparelho de replicação** | O dispositivo de replicação (servidor de configuração/servidor de processo) é uma máquina local que atua como uma ponte entre o ambiente local e a migração do servidor. O aparelho descobre o inventário da máquina no local, para que a Migração do Servidor possa orquestrar a replicação e a migração. O aparelho tem dois componentes:<br/><br/> **Servidor de configuração**: Conecta-se à migração do servidor e coordena a replicação.<br/> **Servidor de processo**: lida com a replicação de dados. O servidor de processo recebe dados da máquina, compacta e criptografa e envia para o Azure. No Azure, a Migração do Servidor grava os dados em discos gerenciados. | Por padrão, o servidor de processo é instalado juntamente com o servidor de configuração no dispositivo de replicação.
**Serviço de mobilidade** | O serviço Mobility é um agente instalado em cada máquina que você deseja replicar e migrar. Ele envia dados de replicação da máquina para o servidor de processo. | Os arquivos de instalação para diferentes versões do serviço Mobility estão localizados no aparelho de replicação. Você baixa e instala o agente que precisa, de acordo com o sistema operacional e a versão da máquina que deseja replicar.

## <a name="mobility-service-installation"></a>Instalação do Serviço de mobilidade

Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

- **Instalação push**: O serviço de mobilidade é instalado pelo servidor de processo quando você habilita proteção para uma máquina. 
- **Instale manualmente**: Você pode instalar o serviço de mobilidade manualmente em cada máquina através de ui ou prompt de comando.

O serviço mobility se comunica com o aparelho de replicação e máquinas replicadas. Se você tiver um software antivírus em execução no aparelho de replicação, servidores de processo ou máquinas sendo replicadas, as seguintes pastas devem ser excluídas da digitalização:


- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (em máquinas Windows com o serviço de mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma máquina, a replicação inicial do Azure começa.
2. Durante a replicação inicial, o serviço Mobility lê dados dos discos da máquina e os envia para o servidor de processo.
3. Esses dados são usados para semear uma cópia do disco em sua assinatura do Azure. 
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. A replicação é de nível de bloco e quase contínua.
4. O serviço Mobility intercepta gravações na memória do disco, integrando-se ao subsistema de armazenamento do sistema operacional. Este método evita operações de I/O de disco na máquina de replicação, para replicação incremental. 
5. As alterações rastreadas para uma máquina são enviadas para o servidor de processo na porta de entrada HTTPS 9443. Essa porta pode ser modificada. O servidor de processo compacta e o criptografa e envia para o Azure. 

## <a name="ports"></a>Portas

**Dispositivo** | **Conexão**
--- | --- 
**Máquinas replicantes** | O serviço de mobilidade em execução em VMs comunica-se com o aparelho de replicação local na porta HTTPS 443 de entrada, para gerenciamento de replicação.<br/><br/> As máquinas enviam dados de replicação para o servidor de processo na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
**Aparelho de replicação** | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
**Servidor de processos** | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por padrão, você implanta um único dispositivo de replicação que executa tanto o servidor de configuração quanto o servidor de processo. Se você está apenas replicando algumas máquinas, esta implantação é suficiente. No entanto, se você estiver replicando e migrando centenas de máquinas, um único servidor de processo pode não ser capaz de lidar com todo o tráfego de replicação. Neste caso, você pode implantar servidores de processo adicionais de saída de escala.

### <a name="plan-vmware-deployment"></a>Planejar a implantação do VMware

Se você estiver replicando VMs vMware, você pode usar o [Site Recovery Deployment Planner for VMware](../site-recovery/site-recovery-deployment-planner.md), para ajudar a determinar os requisitos de desempenho, incluindo a taxa diária de alteração de dados e os servidores de processo que você precisa.

### <a name="replication-appliance-capacity"></a>Capacidade do aparelho de replicação

Use os valores nesta tabela para descobrir se você precisa de um servidor de processo adicional em sua implantação.

- Se sua taxa de variação diária (taxa de churn) for superior a 2 TB, implante um servidor de processo adicional.
- Se você estiver replicando mais de 200 máquinas, implante um aparelho de replicação adicional.

**Cpu** | **Memória** | **Cache de dados espaciais gratuitos** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores 
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100-150 máquinas.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB a 2 TB | 151-200 máquinas.

### <a name="sizing-scale-out-process-servers"></a>Dimensionamento de servidores de processo de escala

Se você precisar implantar um servidor de processo de saída de escala, use esta tabela para descobrir o dimensionamento do servidor.

**Servidor de processos** | **Espaço livre para cache de dados** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Até 85 máquinas 
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB    | 86-150 máquinas.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), memória de 24 GB | 1 TB | 1-2 TB | 151-225 máquinas.

## <a name="throttle-upload-bandwidth"></a>Aceleração carregar largura de banda.

o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar o throughput de upload estrangulando a largura de banda nas máquinas que estão sendo executados como servidores de processo. Você pode influenciar a largura de banda usando esta chave de registro:

- O valor de registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação. O valor padrão é quatro. O valor máximo é 32. Monitore o tráfego para otimizar o valor.
- Além disso, você pode acelerar a largura de banda na máquina do servidor de processo da seguinte forma:

    1. Na máquina do servidor de processo, abra o snap-in do Azure Backup MMC. Há um atalho na área de trabalho ou na pasta C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. No snap-in, selecione **Alterar as Propriedades**.
    3. Em **Estrangular,** **selecione Ativar o estrangulamento do uso da largura de banda da Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.


## <a name="next-steps"></a>Próximas etapas

Experimente [a migração baseada em agentes](tutorial-migrate-vmware-agent.md) para [VMware](tutorial-migrate-vmware-agent.md) ou [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
