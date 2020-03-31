---
title: Otimizar o desempenho em máquinas virtuais da série Azure Lsv2 - Armazenamento
description: Aprenda a otimizar o desempenho para sua solução nas máquinas virtuais da série Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034763"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Otimizar o desempenho nas máquinas virtuais da série Lsv2

As máquinas virtuais da série Lsv2 suportam uma variedade de cargas de trabalho que precisam de alta I/O e throughput no armazenamento local em uma ampla gama de aplicativos e indústrias.  A série Lsv2 é ideal para big data, sql, bancos de dados NoSQL, data warehousing e grandes bancos de dados transacionais, incluindo Cassandra, MongoDB, Cloudera e Redis.

O design das Máquinas Virtuais (VMs) da série Lsv2 maximiza o processador AMD EPYC™ 7551 para fornecer o melhor desempenho entre o processador, memória, dispositivos NVMe e as VMs. Trabalhando com parceiros no Linux, várias compilações estão disponíveis no Azure Marketplace que são otimizadas para o desempenho da série Lsv2 e atualmente incluem:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

Este artigo fornece dicas e sugestões para garantir que suas cargas de trabalho e aplicativos atinjam o máximo desempenho projetado nas VMs. As informações nesta página serão continuamente atualizadas à medida que mais imagens otimizadas lsv2 forem adicionadas ao Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura de chipset AMD EYPC™

As VMs da série Lsv2 usam processadores AMD EYPC™ servidor baseados na microarquitetura Zen. A AMD desenvolveu o Infinity Fabric (IF) para EYPC™ como uma interconexão escalável para seu modelo NUMA que poderia ser usado para comunicações on-die, on-package e multi-package. Em comparação com o QPI (Quick-Path Interconnect) e o UPI (Ultra-Path Interconnect) usados em processadores monolíticos modernos da Intel, a arquitetura de pequenos-die da AMD pode trazer tanto benefícios de desempenho quanto desafios. O impacto real das restrições de largura de banda de memória e latência pode variar dependendo do tipo de cargas de trabalho em execução.

## <a name="tips-to-maximize-performance"></a>Dicas para maximizar o desempenho

* Se você estiver carregando um Linux GuestOS personalizado para sua carga de trabalho, observe que a Rede Acelerada será **OFF** por padrão. Se você pretende habilitar o Accelerated Networking, habilite-o no momento da criação da VM para obter o melhor desempenho.

* O hardware que alimenta as VMs da série Lsv2 utiliza dispositivos NVMe com oito Pares de Filas de I/O (QP)s. Cada fila de I/O do dispositivo NVMe é na verdade um par: uma fila de envio e uma fila de conclusão. O driver NVMe está configurado para otimizar a utilização desses oito QPs de I/O distribuindo I/O's em um cronograma de round robin. Para obter o desempenho máximo, execute oito trabalhos por dispositivo para combinar.

* Evite misturar comandos de admin NVMe (por exemplo, consulta de informações NVMe SMART, etc.) com comandos NVMe I/O durante cargas de trabalho ativas. Os dispositivos Lsv2 NVMe são apoiados pela tecnologia Hyper-V NVMe Direct, que muda para o "modo lento" sempre que quaisquer comandos de admin NVMe estão pendentes. Os usuários do Lsv2 podem ver uma queda dramática de desempenho no desempenho de I/O do NVMe se isso acontecer.

* Os usuários do Lsv2 não devem confiar nas informações do dispositivo NUMA (todas as 0) relatadas dentro da VM para unidades de dados para decidir a afinidade numa para seus aplicativos. A maneira recomendada para um melhor desempenho é espalhar cargas de trabalho em CPUs, se possível.

* A profundidade máxima de fila suportada por par de filas de I/O para o dispositivo Lsv2 VM NVMe é 1024 (vs. Limite de QD 32 da Amazon i3). Os usuários do Lsv2 devem limitar suas cargas de trabalho de benchmarking (sintéticas) à profundidade de fila 1024 ou inferior para evitar desencadear condições completas da fila, o que pode reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilizando o armazenamento NVMe local

O armazenamento local no disco NVMe de 1,92 TB em todas as VMs Lsv2 é efêmero. Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco NVMe local persistirão. Os dados não persistirão no NVMe se a VM for reimplantada, desalocada ou excluída. Os dados não persistirão se outro problema fizer com que a VM, ou o hardware em que está sendo executado, se torne insalubre. Quando isso acontece, todos os dados do host antigo são apagados com segurança.

Também haverá casos em que a VM precisa ser movida para uma máquina hospedeira diferente, por exemplo, durante uma operação de manutenção planejada. Operações planejadas de manutenção e algumas falhas de hardware podem ser antecipadas com [Eventos Programados](scheduled-events.md). Eventos programados devem ser usados para se manter atualizados sobre quaisquer operações de manutenção e recuperação previstas.

No caso de um evento de manutenção planejado exigir que a VM seja recriada em um novo host com discos locais vazios, os dados precisarão ser ressincronizados (novamente, com qualquer dado no antigo host sendo apagado com segurança). Isso ocorre porque as VMs da série Lsv2 não suportam atualmente a migração ao vivo no disco NVMe local.

Existem dois modos para manutenção planejada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção padrão controlada pelo cliente VM

- O VM é movido para um host atualizado durante uma janela de 30 dias.
- Os dados de armazenamento local do Lsv2 podem ser perdidos, por isso recomenda-se o backup de dados antes do evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar a manutenção controlada pelo cliente ou no caso de procedimentos de emergência, como um evento de dia zero de segurança.
- Destina-se a preservar os dados do cliente, mas há um pequeno risco de um vm congelar ou reiniciar.
- Os dados de armazenamento local do Lsv2 podem ser perdidos, por isso recomenda-se o backup de dados antes do evento.

Para quaisquer eventos de serviço futuros, use o processo de manutenção controlada para selecionar um horário mais conveniente para a atualização. Antes do evento, você pode fazer backup de seus dados no armazenamento premium. Depois que o evento de manutenção é concluído, você pode retornar seus dados para o armazenamento NVMe local Lsv2 VMs atualizado.

Os cenários que mantêm dados em discos NVMe locais incluem:

- A VM está funcionando e saudável.
- A VM é reiniciada no lugar (por você ou pelo Azure).
- O VM é pausado (parado sem desalocação).
- A maioria das operações planejadas de manutenção.

Cenários que apagam dados com segurança para proteger o cliente incluem:

- O VM é reimplantado, parado (desalocado) ou excluído (por você).
- A VM torna-se insalubre e tem que servir a cura para outro nó devido a um problema de hardware.
- Um pequeno número das operações planejadas de manutenção que exigem que a VM seja realocada para outro host para manutenção.

Para saber mais sobre opções de backup de dados no armazenamento local, consulte [Backup e recuperação de desastres para discos Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **Como começar a implantar VMs da série Lsv2?**  
   Assim como qualquer outra VM, use o [Portal,](quick-create-portal.md) [Azure CLI](quick-create-cli.md)ou [PowerShell](quick-create-powershell.md) para criar uma VM.

* **Uma única falha de disco NVMe fará com que todas as VMs do host falhem?**  
   Se for detectada uma falha de disco no nó de hardware, o hardware será falhado. Quando isso ocorre, todas as VMs no nó são automaticamente desalocadas e movidas para um nó saudável. Para VMs da série Lsv2, isso significa que os dados do cliente sobre o nó falhado também são apagados com segurança e precisarão ser recriados pelo cliente no novo nó. Como observado, antes que a migração ao vivo se torne disponível no Lsv2, os dados sobre o nó falhado serão movidos proativamente com as VMs à medida que forem transferidos para outro nó.

* **Preciso fazer algum ajuste no rq_affinity para o desempenho?**  
   A configuração rq_affinity é um pequeno ajuste ao usar as operações máximas de entrada/saída por segundo (IOPS). Uma vez que tudo está funcionando bem, então tente definir rq_affinity a 0 para ver se faz diferença.

* **Preciso mudar as configurações de blk_mq?**  
   O RHEL/CentOS 7.x usa automaticamente blk-mq para os dispositivos NVMe. Não são necessárias alterações de configuração ou configurações. A configuração scsi_mod.use_blk_mq é apenas para SCSI e foi usada durante o Lsv2 Preview porque os dispositivos NVMe eram visíveis nas VMs convidadas como dispositivos SCSI. Atualmente, os dispositivos NVMe são visíveis como dispositivos NVMe, de modo que a configuração SCSI blk-mq é irrelevante.

* **Preciso mudar "fio"?**  
   Para obter o Máximo IOPS com uma ferramenta de medição de desempenho como 'fio' nos tamanhos VM L64v2 e L80v2, defina "rq_affinity" como 0 em cada dispositivo NVMe.  Por exemplo, esta linha de comando definirá "rq_affinity" a zero para todos os dispositivos 10 NVMe em um VM L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Observe também que o melhor desempenho é obtido quando a I/O é feita diretamente para cada um dos dispositivos NVMe brutos sem particionamento, sem sistemas de arquivos, sem configuração RAID 0, etc. Antes de iniciar uma sessão de teste, certifique-se de `blkdiscard` que a configuração esteja em um estado fresco/limpo conhecido, executando em cada um dos dispositivos NVMe.
   
## <a name="next-steps"></a>Próximas etapas

* Veja as especificações de todas as [VMs otimizadas para o desempenho de armazenamento](sizes-storage.md) no Azure
