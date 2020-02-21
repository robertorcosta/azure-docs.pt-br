---
title: Diretrizes de desempenho para o SQL Server no Azure | Microsoft Docs
description: Fornece diretrizes para otimização do desempenho do SQL Server em VMs do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 409e73f05366065f1c4159e9f1cd7e5bf8bb5ceb
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486236"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure

## <a name="overview"></a>Visão geral

Este artigo fornece diretrizes para otimizar o desempenho do SQL Server na Máquina Virtual do Microsoft Azure. Durante a execução do SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho aplicáveis ao SQL Server no ambiente de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores como, por exemplo, o tamanho de uma máquina virtual e a configuração dos discos de dados.

As [imagens do SQL Server provisionadas no portal do Azure](quickstart-sql-vm-create-portal.md) seguem as melhores práticas de configuração de armazenamento gerais (para mais informações sobre como o armazenamento é configurado, consulte [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie suas escolhas na carga de trabalho e verifique por meio de testes.

> [!TIP]
> Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Este artigo concentra-se na obtenção do *melhor* desempenho para o SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todos os aprimoramentos relacionados abaixo. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="quick-check-list"></a>Lista de verificação rápida

Veja a seguir uma lista de verificação rápida para obter o melhor desempenho do SQL Server em máquinas virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) | -Use tamanhos de VM com 4 ou mais vCPU, como [E4S_v3](../../ev3-esv3-series.md) ou superior, ou [DS12_v2](../../dv2-dsv2-series-memory.md) ou superior.<br/><br/> as [séries - es, EAS, DS e das](../../sizes-general.md) oferecem a máxima memória à taxa de vCPU necessária para o desempenho da carga de trabalho OLTP. <br/><br/> a [série M](../../m-series.md) - oferece a maior memória à taxa de vCPU necessária para o desempenho crítico e é ideal para cargas de trabalho de data warehouse. <br/><br/> -Colete os requisitos de [IOPS](../premium-storage-performance.md#iops), [taxa de transferência](../premium-storage-performance.md#throughput) e [latência](../premium-storage-performance.md#latency) da carga de trabalho de destino em horários de pico seguindo a lista de verificação de requisitos de [desempenho do aplicativo](../premium-storage-performance.md#application-performance-requirements-checklist) e selecione o tamanho da [VM](../sizes-general.md) que pode ser dimensionado para os requisitos de desempenho da carga de trabalho.|
| [Storage](#storage-guidance) | -Para obter testes detalhados de desempenho de SQL Server em VMs do Azure com o TPC-E e TPC_C benchmarks, consulte o blog [otimizar o desempenho do OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> -Use o [SSDs Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) para obter as melhores vantagens de preço/desempenho. Configure o [cache ReadOnly](../premium-storage-performance.md#disk-caching) para arquivos de dados e nenhum cache para o arquivo de log. <br/><br/> -Use [ultra discos](../disks-types.md#ultra-disk) se menos de 1 MS latências de armazenamento forem exigidas pela carga de trabalho. <br/><br/> -Coletar os requisitos de latência de armazenamento para dados de SQL Server, log e arquivos de BD temporários [monitorando o aplicativo](../premium-storage-performance.md#application-performance-requirements-checklist) antes de escolher o tipo de disco. Se < latências de armazenamento 1 MS forem necessárias, use ultra discos, caso contrário, use o SSD Premium. Se as latências baixas forem necessárias apenas para o arquivo de log e não para arquivos de dados, [provisione o ultra Disk](../disks-enable-ultra-ssd.md) nos níveis de taxa de transferência e IOPS necessários somente para o arquivo de log. <br/><br/> -  [compartilhamentos de arquivos Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) são recomendados como armazenamento compartilhado para uma instância de cluster de failover SQL Server. Os compartilhamentos de arquivos Premium não dão suporte ao cache e oferecem desempenho limitado em comparação com os discos SSD Premium. Escolha discos gerenciados por SSD Premium em compartilhamentos de arquivos premium para instâncias do SQL Autônomas; Mas aproveite os compartilhamentos de arquivos premium para o armazenamento compartilhado da instância de cluster de failover para facilitar a manutenção e a escalabilidade flexível. <br/><br/> -O armazenamento padrão é recomendado apenas para fins de desenvolvimento e teste ou para arquivos de backup e não deve ser usado para cargas de trabalho de produção. <br/><br/> - Mantenha a [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e a VM do SQL Server na mesma região.<br/><br/> -Desabilitar o [armazenamento com redundância geográfica](../../../storage/common/storage-redundancy.md) do Azure (replicação geográfica) na conta de armazenamento.  |
| [Discos](#disks-guidance) | -Use um mínimo de 2 [discos SSD Premium](../disks-types.md#premium-ssd) (1 para arquivo de log e 1 para arquivos de dados). <br/><br/> -Para cargas de trabalho que exigem < 1 ms e latências de e/s, habilite o acelerador de gravação para a série M e considere usar discos SSD Ultra para a série es e DS. <br/><br/> – Habilite o [cache somente leitura](../premium-storage-performance.md#disk-caching) nos discos que hospedam os arquivos de dados.<br/><br/> -Adicionar mais capacidade de taxa de transferência/IOPS Premium de 20% do que sua carga de trabalho requer ao [Configurar o armazenamento para SQL Server dados, log e arquivos tempdb](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br/><br/> - Não habilite o cache em discos que hospedam o arquivo de log.  **Importante**: Pare o serviço de SQL Server ao alterar as configurações de cache para um disco de VM do Azure.<br/><br/> – Distribua vários discos de dados do Azure para obter maior taxa de transferência de armazenamento.<br/><br/> - Formate com os tamanhos de alocação documentados. <br/><br/> -Coloque o TempDB na unidade de `D:\` de SSD local para cargas de trabalho de SQL Server de missão crítica (depois de escolher o tamanho correto da VM). Se você criar a VM de modelos de início rápido do portal do Azure ou do Azure e inserir o banco de informações [temporário no disco local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) , você não precisará de nenhuma ação adicional; para todos os outros casos, siga as etapas no blog para [usar o SSDs para armazenar o tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após a reinicialização. Se a capacidade da unidade local não for suficiente para o tamanho do BD temporário, coloque o DB temporário em um pool de armazenamento [removido](../premium-storage-performance.md) em discos SSD Premium com [cache somente leitura](../premium-storage-performance.md#disk-caching). |
| [E/S](#io-guidance) |- Habilite a compactação de página do banco de dados.<br/><br/> - Habilite a inicialização instantânea de arquivos para arquivos de dados.<br/><br/> - Limite o aumento automático do banco de dados.<br/><br/> - Desabilite a redução automática no banco de dados.<br/><br/> - Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.<br/><br/> - Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados.<br/><br/> -Configurar locais de arquivo de banco de dados e backup padrão.<br/><br/> - [habilitar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Aplique as correções de desempenho do SQL Server. |
| [Recursos específicos](#feature-specific-guidance) | - Faça backup diretamente no armazenamento de blobs.<br/><br/>-Use [backups de instantâneo de arquivo](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) para bancos de dados maiores que 12 TB. <br/><br/>-Use vários arquivos temp DB, 1 arquivo por núcleo, até 8 arquivos.<br/><br/>-Defina a memória máxima do servidor em 90% ou até 50 GB restantes para o sistema operacional. <br/><br/>-Habilitar soft NUMA. |

Para obter mais informações sobre *como* e *por que* fazer essas otimizações, consulte os detalhes e as diretrizes fornecidas nas seções a seguir.

## <a name="vm-size-guidance"></a>Diretrizes de tamanho de VM

Comece coletando os requisitos de CPU, memória e taxa de transferência de armazenamento da carga de trabalho em horários de pico. Contadores de desempenho \LogicalDisk\Disk leituras de páginas/s e \LogicalDisk\Disk/s podem ser usados para coletar requisitos de IOPS de leitura e gravação e o contador de \LogicalDisk\Disk bytes/s pode ser usado para coletar [requisitos de taxa de transferência de armazenamento](../premium-storage-performance.md#disk-caching) para arquivos de dados, log e DB temporário. Depois que os requisitos de IOPS e taxa de transferência forem definidos no pico, avalie os tamanhos de VM oferece essa capacidade. Por exemplo, se sua carga de trabalho exigir 20 K de IOPS de leitura e IOPS de gravação de 10K no pico, você poderá escolher E16s_v3 (com até 20 K de 32 K armazenados em cache e 25600 IOPS não armazenado em cache) ou M16_s (com 10 mil KB em cache e 10.000 IOPS não armazenados em cache) com 2 discos p30. Certifique-se de entender os requisitos de taxa de transferência e IOPS da carga de trabalho, pois as VMs têm limites de escala diferentes para IOPS e taxa de transferência.<br/><br/>A [DSv_3](../../dv3-dsv3-series.md) e [a série Es_v3](../../ev3-esv3-series.md) são hospedadas em hardware de uso geral com processadores Intel Haswell ou Broadwell. A [série M](../../m-series.md) oferece a mais alta contagem de vCPU e memória para as maiores cargas de trabalho de SQL Server e hospedada em hardware com otimização de memória com a família de processadores Skylake. Essas séries de VMs dão suporte ao armazenamento Premium, que é recomendado para o melhor desempenho com o cache de leitura no nível do host. As séries Es_v3 e M também estão disponíveis em [tamanhos de núcleo restritos](../../windows/constrained-vcpu.md), o que poupa dinheiro para cargas de trabalho com demandas de alta capacidade de computação e de armazenamento mais baixas. 

## <a name="storage-guidance"></a>Orientação de armazenamento

Para obter testes detalhados de desempenho de SQL Server em VMs do Azure com o TPC-E e TPC_C parâmetros de comparação, consulte o blog [otimizar o desempenho do OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

O cache de BLOBs do Azure com SSDs Premium é recomendado para todas as cargas de trabalho de produção. 

> [!WARNING]
> Os SSDs e HDDs Standard têm largura de banda e latências variáveis e só são recomendado para cargas de trabalho de desenvolvimento e teste. As cargas de trabalho de produção devem usar os SSDs Premium.

Além disso, recomendamos a criação de sua conta de armazenamento do Azure no mesmo data center que suas máquinas virtuais do SQL Server a fim de reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois não há garantia para uma ordem de gravação consistente em vários discos. Em vez disso, considere a configuração de uma tecnologia de recuperação de desastres do SQL Server entre dois data centers do Azure. Para obter mais informações, confira [Alta disponibilidade e recuperação de desastres para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos principais de discos em uma VM do Azure:

* **Disco do SO**: quando você cria uma Máquina Virtual do Azure, a plataforma anexa pelo menos um disco (identificado como unidade **C**) à VM para o disco do seu sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.
* **Disco temporário**: máquinas virtuais do Azure contêm outro disco denominado disco temporário (rotulado como a unidade **D**:). É um disco localizado no nó que pode ser usado como espaço de rascunho.
* **Discos de dados**: você também pode anexar outros discos à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como blobs de páginas.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e é rotulado como a unidade **C** .

A política padrão de caching no disco do sistema operacional é de **Leitura/Gravação**. Para aplicativos sensíveis ao desempenho, recomendamos o uso de discos de dados em vez do disco do sistema operacional. Consulte a seção sobre Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a unidade **D** , não é persistida no armazenamento de BLOBs do Azure. Não armazene seus arquivos de banco de dados do usuário ou arquivos de log de transações do usuário na unidade **D**:.

Coloque o TempDB na unidade de `D:\` de SSD local para cargas de trabalho de SQL Server de missão crítica (depois de escolher o tamanho correto da VM). Se você criar a VM de modelos de início rápido do portal do Azure ou do Azure e inserir o banco de informações [temporário no disco local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), você não precisará de nenhuma ação adicional; para todos os outros casos, siga as etapas no blog para [usar o SSDs para armazenar o tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após a reinicialização. Se a capacidade da unidade local não for suficiente para o tamanho do BD temporário, coloque o DB temporário em um pool de armazenamento [removido](../premium-storage-performance.md) em discos SSD Premium com [cache somente leitura](../premium-storage-performance.md#disk-caching).

Para VMs que dão suporte a SSDs Premium, você também pode armazenar TempDB em um disco que dá suporte a SSDs Premium com cache de leitura habilitado.


### <a name="data-disks"></a>Discos de dados

* **Usar discos SSD Premium para arquivos de dados e de log**: se você não estiver usando a distribuição de disco, use dois discos SSD Premium em que um disco contém o arquivo de log e o outro contém os dados. Cada SSD Premium fornece um número de IOPS e largura de banda (MB/s) dependendo de seu tamanho, conforme descrito no artigo, [Selecione um tipo de disco](../disks-types.md). Se você estiver usando uma técnica de distribuição de disco, como Espaços de Armazenamento, obterá o desempenho ideal tendo dois pools, um para os arquivos de log e outro para os arquivos de dados. No entanto, se você planeja usar SQL Server FCI (instâncias de cluster de failover), deverá configurar um pool ou utilizar [compartilhamentos de arquivos Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) em vez disso.

   > [!TIP]
   > - Para resultados de teste em várias configurações de disco e carga de trabalho, consulte a seguinte postagem no blog: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Diretrizes de configuração de armazenamento para SQL Server na VM do Azure).
   > - Para desempenho de missão crítica de SQL Servers que precisem de aproximadamente 50.000 IOPS, considere substituir 10 discos -P30 por um SSD Ultra. Para obter mais informações, consulte a seguinte postagem de blog: [desempenho crítico com SSD ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Quando você provisiona uma VM do SQL Server no portal, tem a opção de editar sua configuração de armazenamento. Dependendo de sua configuração, o Azure configura um ou mais discos. Vários discos são combinados em um único pool de armazenamento com distribuição. Tanto os dados quanto os arquivos de log residem juntos nessa configuração. Para saber mais, confira [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Distribuição de Discos**: para produtividade mais alta, você pode adicionar outros discos de dados e usar a Distribuição de Discos. Para determinar o número de discos de dados, você precisa analisar o número de IOPS e a largura de banda necessários para os arquivos de log e para os arquivos de dados e do TempDB. Observe que diferentes tamanhos de VM têm diferentes limites no número de IOPS e na largura de banda com suporte; consulte as tabelas sobre IOPS por [tamanho de VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Use as seguintes diretrizes:

  * Para Windows 8/Windows Server 2012 ou posterior, use os [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho da faixa) como 64 KB (65.536 bytes) para cargas de trabalho OLTP e 256 KB (262.144 bytes) para cargas de trabalho de data warehouse para evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.
      2. Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não interface do usuário do Gerenciador do Servidor). 

    Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação de 64 KB e o número de colunas de 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Essa opção foi preterida a partir do Windows 8/Windows Server 2012. Para obter informações, veja a declaração de suporte em [O Serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se você estiver usando [Espaços de Armazenamento Diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [Instâncias de Cluster de Failover do SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), será necessário configurar um único pool. Embora volumes diferentes possam ser criados nesse único pool, todos eles compartilharão as mesmas características, como a mesma política de cache.

  * Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para obter mais informações, confira [Tamanhos das Máquinas Virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se você não estiver usando os SSDs Premium (cenários de desenvolvimento/teste), a recomendação será adicionar o número máximo de discos de dados com suporte pelo [Tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usar a Distribuição de Discos.

* **Política de Cache**: observe as recomendações a seguir para a política de cache, dependendo da configuração de armazenamento.

  * Se você estiver usando discos separados para dados e arquivos de log, habilite o cache de leitura nos discos de dados que hospedam os arquivos de dados e arquivos de dados do TempDB. Isso pode resultar em um benefício de desempenho significativo. Não habilite o cache no disco que contém o arquivo de log, pois isso causa uma pequena diminuição no desempenho.

  * Se você estiver usando a distribuição de disco em um único pool de armazenamento, a maioria das cargas de trabalho se beneficiará do cache de leitura. Se você tiver pools de armazenamento separados para os arquivos de log e de dados, habilite o cache de leitura apenas no pool de armazenamento para os arquivos de dados. Em determinadas cargas de trabalho de gravação pesada, um melhor desempenho pode ser obtido sem o cache. Isso somente pode ser determinado por meio de testes.

  * As recomendações anteriores aplicam-se aos SSDs Premium. Se você não estiver usando os SSDs Premium, não habilite o caching em discos de dados.

  * Para obter instruções sobre como configurar o cache de disco, consulte os artigos a seguir. Para o modelo de implantação clássico (ASM), consulte: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implantação Azure Resource Manager, consulte: [set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Interrompa o serviço SQL Server ao alterar a configuração do cache dos discos da VM do Azure a fim de evitar a possibilidade de corrupção do banco de dados.

* **Tamanho de unidade de alocação de NTFS**: ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB.

* **Práticas recomendadas de gerenciamento de disco**: ao remover um disco de dados ou alterar seu tipo de cache, interrompa o serviço SQL Server durante a alteração. Quando as configurações do cache são alteradas no disco do SO, o Azure interrompe a VM, altera o tipo de cache e reinicia a VM. Quando as configurações do cache de um disco de dados são alteradas, a VM não é interrompida, mas o disco de dados é desanexado da VM durante a mudança e depois reanexado.

  > [!WARNING]
  > A não interrupção do serviço SQL Server durante essas operações pode causar corrupção no banco de dados.


## <a name="io-guidance"></a>Diretrizes de E/S

* Os melhores resultados com os SSDs Premium são obtidos ao paralelizar seu aplicativo e as solicitações. Os SSDs Premium foram projetados para cenários nos quais a profundidade da fila de E/S é maior do que um. Portanto, você verá pouco ou nenhum ganho de desempenho para solicitações em série de segmento único (mesmo se usarem muito armazenamento). Por exemplo, isso pode afetar os resultados do teste de segmento único de ferramentas de análise de desempenho, por exemplo, o SQLIO.

* Considere o uso da [compactação de página do banco de dados](https://msdn.microsoft.com/library/cc280449.aspx) , pois isso pode ajudar a melhorar o desempenho de cargas de trabalho com uso intenso de E/S. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

* Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, conceda a SE_MANAGE_VOLUME_NAME à conta de serviço do SQL Server (MSSQLSERVER) e adicione-a à política de segurança **Executar Tarefas de Manutenção de Volume**. Se você estiver usando uma imagem da plataforma do SQL Server para o Azure, a conta de serviço padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança **Executar Tarefas de Manutenção de volume**. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de arquivos de bancos de dados](https://msdn.microsoft.com/library/ms175935.aspx).

* **aumento automático** é considerado meramente uma contingência para o aumento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando a opção Tamanho.

* Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.

* Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema. Para obter mais informações, veja [Mover bancos de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados Isso pode ser feito no SQL Server Configuration Manager clicando duas vezes na Instância do SQL Server e selecionando Propriedades. As configurações log de erros e arquivo de rastreamento podem ser alteradas na guia **parâmetros de inicialização** . O diretório de despejo é especificado na guia **avançado** . A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erros.

    ![Captura de Tela de Log de Erros do SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configure os locais do arquivo de banco de dados e backup padrão. Use as recomendações neste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de tela a seguir demonstra onde fazer essas alterações.

    ![Log de Dados de SQL e Arquivos de Backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se você estiver executando o SQL Server 2012, instale a Atualização Cumulativa 10 do Service Pack 1. Essa atualização contém a correção para o desempenho ruim de E/S ao executar a instrução seleção na tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](https://support.microsoft.com/kb/2958012).

* Considere a compactação de qualquer arquivo de dados durante a transferência de entrada/saída do Azure.

## <a name="feature-specific-guidance"></a>Diretriz específica do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a obter um desempenho maior:

### <a name="back-up-to-azure-storage"></a>Fazer backup no armazenamento do Azure
Ao realizar backups do SQL Server em execução em máquinas virtuais do Azure, você poderá usar [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados. Quando fizer backup ou restaurar de/para o armazenamento do Azure, siga as recomendações fornecidas em [Práticas Recomendadas de Backup do SQL Server para URL e Solução de Problemas e Restauração de Backups Armazenados no Armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

Antes do SQL Server 2012, use a [Ferramenta de backup do SQL Server para o Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência do backup utilizando vários alvos de distribuição de backup.

### <a name="sql-server-data-files-in-azure"></a>SQL Server arquivos de dados no Azure

Este recurso novo, [Arquivos de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível desde o SQL Server 2014. A execução do SQL Server com os arquivos de dados no Azure demonstra características de desempenho comparáveis as dos discos de dados do Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instância de cluster de failover e espaços de armazenamento

Se você estiver usando espaços de armazenamento, ao adicionar nós ao cluster na página de **confirmação** , desmarque a caixa de seleção **Adicionar todo o armazenamento elegível ao cluster**. 

![Desmarque armazenamento qualificado](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Se você estiver usando Espaços de Armazenamento e não desmarcar **Adicionar todo o armazenamento qualificado ao cluster**, o Windows desconectará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecem no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Espaços de Armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [Espaços de Armazenamento](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre armazenamento e desempenho, consulte [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Diretrizes de configuração de armazenamento para SQL Server na VM do Azure)

Para conferir as práticas recomendadas de segurança, consulte [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-security.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
