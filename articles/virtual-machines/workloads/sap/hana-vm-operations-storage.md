---
title: Configurações de armazenamento de máquinavirtual SAP HANA Azure | Microsoft Docs
description: Recomendações de armazenamento para VM que tenham O SAP HANA implantado neles.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4e8b544ea3daeb23b22f3864beb21ba9d3f342f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255610"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento de máquina virtual do SAP HANA no Azure

O Azure fornece diferentes tipos de armazenamento que são adequados para VMs Azure que estão executando O SAP HANA. Os **tipos de armazenamento Azure certificados pelo SAP HANA** que podem ser considerados para a lista de implantações do SAP HANA como: 

- Azure Premium SSD  
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber mais sobre esses tipos de disco, consulte o artigo [Selecione um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/). 

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independente do tipo de armazenamento Azure escolhido, o sistema de arquivos que é usado nesse armazenamento precisa ser suportado pela SAP para o sistema operacional específico e o DBMS. [Nota de suporte sap #405827](https://launchpad.support.sap.com/#/notes/405827) lista os sistemas de arquivos suportados para diferentes sistemas operacionais e bancos de dados, incluindo SAP HANA. Isso se aplica a todos os volumes que o SAP HANA pode acessar para leitura e escrita para qualquer tarefa. Especificamente usando NFS no Azure para SAP HANA, restrições adicionais de versões NFS se aplicam como indicado mais tarde neste artigo 


As condições mínimas certificadas sap HANA para os diferentes tipos de armazenamento são: 

- SSD Premium Azure - /hana/log é necessário para ser armazenado em cache com o Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). O volume /hana/data pode ser colocado no SSD Premium sem o Azure Write Accelerator ou no disco Ultra
- Disco Azure Ultra pelo menos para o volume /hana/log. O volume /hana/data pode ser colocado em ssd premium sem o Azure Write Accelerator ou para obter tempos de reinicialização mais rápidos Ultra disk
- **VOLUMES NFS v4.1** em cima de Arquivos Azure NetApp para /hana/log **e** /hana/dados. O volume de /hana/compartilhado pode usar o protocolo NFS v3 ou NFS v4.1. O protocolo NFS v4.1 é obrigatório para os volumes /hana/data e/hana/log.

Alguns dos tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar /hana/data no Armazenamento Premium e /hana/log pode ser colocado no armazenamento em disco Ultra, a fim de obter a baixa latência necessária. No entanto, se você usar um volume NFS v4.1 baseado em ANF para /hana/data, você será obrigado a usar outro volume NFS v4.1 baseado em ANF para /hana/log. O uso de NFS em cima do ANF para um dos volumes (como /hana/data) e O Armazenamento Premium do Azure ou ultra disco para o outro volume (como /hana/log) não é **suportado**.

No mundo local, você raramente tinha que se preocupar com os subsistemas de I/O e suas capacidades. O motivo era que o fornecedor do dispositivo precisava certificar-se de que os requisitos mínimos de armazenamento fossem atendidos para o SAP HANA. À medida que você constrói a infra-estrutura do Azure você mesmo, você deve estar ciente de alguns desses requisitos emitidos pela SAP. Algumas das características mínimas de throughput exigidas do SAP estão resultando na necessidade de:

- Habilitar leitura/gravação em **/hana/log** de 250 MB/seg com tamanhos de I/O de 1 MB
- Habilite a atividade de leitura de pelo menos 400MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB
- Habilite a atividade gravação de pelo menos 250 MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB

Uma vez que a baixa latência de armazenamento é crítica aos sistemas DBMS, mesmo que o DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é **obrigatório** aproveitar os Discos Premium do Azure para **volumes /hana/data** e **/hana/log.** Para alcançar a taxa de transferência mínima **hana/log** e **hana/data** conforme desejado pelo SAP, você precisa para criar um RAID 0 utilizando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E usar os volumes RAID como **hana/data** e **hana/log** volumes. 

**Recomendação: Como tamanhos de listras para o RAID 0, a recomendação é usar:**

- 256 KB para **/hana/data**
- 32KB para **hana/log**

> [!IMPORTANT]
> O tamanho da listra para /hana/dados foi alterado de recomendações anteriores que pediam 64 KB ou 128 KB para 256 KB com base nas experiências do cliente com versões linux mais recentes. O tamanho de 256 KB está proporcionando um desempenho ligeiramente melhor

> [!NOTE]
> Não é necessário configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento Premium e Standard do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem rendimento de E/S suficiente.

A acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em discos de Armazenamento Premium do Azure de 3 x P30, ele deverá fornecer três vezes a IOPS e três vezes a taxa de transferência de armazenamento de um único disco P30 do Armazenamento Premium do Azure.

Considere a taxa de transferência de E/S geral ao dimensionar ou escolher uma VM. A taxa de transferência de armazenamento de VM geral está documentada no artigo [Tamanhos da máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo Agendador de E/S do Linux
O Linux possui vários modos de agendamento de E/S diferentes. A recomendação comum através de fornecedores Linux e SAP é reconfigurar o modo agendador de I/O para volumes de disco do modo **mq-deadline** ou **kyber** para o modo **noop** (não multi-filaue) ou **nenhum** para o modo (multiqueue). Os detalhes são referenciados no [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com armazenamento premium e acelerador de gravação Azure para máquinas virtuais Azure M-Series
Azure Write Accelerator é uma funcionalidade que está disponível exclusivamente para VMs da série M do Azure. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. Para o SAP HANA, acelerador de gravação deve ser usado em relação a **hana/log** somente no volume. Portanto, o **/hana/data** e **/hana/log** são volumes separados com o Azure Write Accelerator suportando apenas o volume **/hana/log.** 

> [!IMPORTANT]
> Ao usar o Azure Premium Storage, o uso do Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) ou do volume /hana/log é obrigatório. O Write Accelerator está disponível apenas para VMs premium de armazenamento e série Mv2 e Série MV2.

As recomendações de cache abaixo pressupõem que as características de E/S para o SAP HANA sejam listadas como:

- Praticamente, não há nenhuma carga de trabalho de leitura nos arquivos de dados do HANA. As exceções são E / Ss de tamanho grande após o reinício da instância do HANA ou quando os dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, em grande parte, o cache de leitura não faz sentido pois, na maioria dos casos, todos os volumes de arquivos de dados precisam ser lidos na íntegra.
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Os pontos de salvamento de gravação são assíncronos e não impedem nenhuma transação de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. No entanto, a recuperação de pane devem ser situações excepcionais
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são E/Ss grandes ao executar backups de log de transações, recuperação de pane ou na fase de reinicialização de uma instância do HANA.  
- A carga principal no arquivo de log de refazer do SAP HANA são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações precisam ser persistidas em disco de forma confiável

**Recomendação: Como resultado desses padrões de I/O observados pelo SAP HANA, o cache para os diferentes volumes usando o Azure Premium Storage deve ser definido como:**

- **hana/data** -sem cache
- **/hana/log** - sem cache - exceção para Série M e Mv2 onde o Write Accelerator deve ser ativado sem leitura de cache. 
- **/hana/compartilhado** - leia cache

### <a name="production-recommended-storage-solution"></a>Solução de armazenamento recomendada para produção

> [!IMPORTANT]
> Certificação do SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de gravação do Azure para o **hana/log** volume. Como resultado, as implantações de SAP HANA de cenário de produção em máquinas virtuais da série M do Azure devem ser configurados com o Acelerador de gravação do Azure para o **hana/log** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As recomendações são frequentemente superiores aos requisitos mínimos sap, conforme estabelecido anteriormente neste artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o throughput máximo de armazenamento que os diferentes tipos de VM fornecem.

**Recomendação: As configurações recomendadas para cenários de produção parecem:**

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Verifique se o throughput de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que os listados aumenta o throughput iOPS e I/O dentro dos limites do tipo de máquina virtual Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o **hana/log** volume precisam ser implantados como discos gerenciados.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para um VM M128xx e M416xx
- 8 VHDs para um VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Acelerador de Gravação do Azure podem ser encontradas no artigo [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Gravação do Azure podem ser localizados na mesma documentação.

**Recomendação: Você precisa usar o Write Accelerator para discos que formam o volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuração com consciência de custos do Armazenamento do Microsoft Azure
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também usam para hospedar O SAP HANA em VMs Azure. Pode haver alguns tipos de VM que podem não atender a todos os critérios mínimos de armazenamento para SAP HANA ou não são suportados oficialmente com SAP HANA pela SAP. Mas até o momento essas VMs parecem funcionar adequadamente para cenários de não produção. Os **/hana/data** **s/hana/log** são combinados a um volume. Como resultado, o uso do Azure Write Accelerator pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários suportados por SAP, verifique se um determinado tipo de VM é suportado para SAP HANA por SAP na [documentação SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma mudança em relação às recomendações anteriores para uma solução de custo consciente é passar dos [discos HDD padrão do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para [discos SSD padrão azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) de melhor desempenho e melhor desempenho

As recomendações são frequentemente superiores aos requisitos mínimos sap, conforme estabelecido anteriormente neste artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o throughput máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1.000 GiB | 1.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1.750 GiB | 1.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2.000 GiB | 2.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se você precisar de alterações no volume **/hana/backup,** que foi dimensionado para manter backups que representam o dobro do volume de memória, sinta-se livre para ajustar.   
Verifique se o throughput de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que os listados aumenta o throughput iOPS e I/O dentro dos limites do tipo de máquina virtual Azure. 

> [!NOTE]
> As configurações acima NÃO seriam benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), pois ele utiliza uma combinação do Armazenamento Premium do Azure e Armazenamento Standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. É necessário escolher o Armazenamento Premium para todos os discos acima listados como o Armazenamento Standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA da VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos que o SAP expressa para seus provedores de infraestrutura. Em cenários de carga de trabalho e implantações de clientes reais, houve situações em que essas recomendações ainda não forneciam recursos suficientes. Essas poderiam ser situações em que um cliente precisava de um recarregamento mais rápido dos dados após uma reinicialização do HANA ou em que as configurações de backup demandavam mais alta largura de banda para o armazenamento. Outros casos incluíram **/hana/log**, onde 5000 IOPS não foram suficientes para a carga de trabalho específica. Portanto, considere essas recomendações como um ponto de partida e adapte-se com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento em disco Azure Ultra para SAP HANA
A Microsoft está em processo de implantação de um novo tipo de armazenamento [Azure chamado azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). A diferença significativa entre o armazenamento Azure oferecido até agora e o disco Ultra é que os recursos do disco não estão mais vinculados ao tamanho do disco. Como cliente, você pode definir esses recursos para o disco Ultra:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a 160K IOPS (o máximo depende também dos tipos de VM)
- Throughput de armazenamento de 300 MB/seg a 2.000 MB/seg

O disco ultra oferece a possibilidade de definir um único disco que satisfaça o seu tamanho, IOPS e alcance de throughput de disco. Em vez de usar gerentes de volume lógicos como LVM ou MDADM em cima do Azure Premium Storage para construir volumes que atendem aos requisitos de throughput de IOPS e armazenamento. Você pode executar uma mistura de configuração entre o disco Ultra e o Armazenamento Premium. Como resultado, você pode limitar o uso do disco Ultra ao desempenho crítico /hana/data e /hana/log volumes e cobrir os outros volumes com o Azure Premium Storage

Outras vantagens do disco Ultra podem ser a latência de melhor leitura em comparação com o Armazenamento Premium. A latência de leitura mais rápida pode ter vantagens quando você deseja reduzir os tempos de inicialização do HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento em disco Ultra também podem ser sentidas quando o HANA está escrevendo pontos de salvamento. Uma vez que os discos de armazenamento Premium para /hana/data geralmente não são armazenados em cache no Write Accelerator, a latência de gravação para /hana/dados no Armazenamento Premium em comparação com o disco Ultra é maior. Pode-se esperar que a gravação de savepoint com disco Ultra esteja funcionando melhor no disco Ultra.

> [!IMPORTANT]
> O disco Ultra ainda não está presente em todas as regiões do Azure e também ainda não está suportando todos os tipos de VM listados abaixo. Para obter informações detalhadas sobre onde o disco Ultra está disponível e quais famílias de VM são suportadas, verifique o artigo [Quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada de produção com configuração de disco Ultra puro
Nesta configuração, você mantém os volumes /hana/data e /hana/log separadamente. Os valores sugeridos são derivados dos KPIs que a SAP tem para certificar os tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [Whitepaper de armazenamento SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações são frequentemente superiores aos requisitos mínimos sap, conforme estabelecido anteriormente neste artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o throughput máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/volume de dados | /hana/data I/O throughput | /hana/data IOPS | /hana/volume de log | /hana/log I/O throughput | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 600 GB | 700 MBps | 7.500 | 512 GB | 500 MBps  | 2.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M64ls | 512 GiB | 1.000 MB/s | 600 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 1.200 MBps |9.000 | 512 GB | 800 MBps  | 3.000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 1200 MBps |9.000 | 512 GB | 800 MBps  | 3.000 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 1.000 MBps | 9.000 | 512 GB | 400 MBps  | 2.500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 1.000 MBps | 9.000 | 512 GB | 400 MBps  | 2.500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.500 MBps | 9.000 | 512 GB | 800 MBps  | 3.000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 9.000 | 512 GB | 800 MBps  | 3.000 |   

Os valores listados destinam-se a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o disco Azure Ultra é que os valores para IOPS e throughput podem ser adaptados sem a necessidade de desligar a VM ou interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com armazenamento em disco Ultra não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os Serviços de Backup do Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento consciente de custo com configuração de disco Ultra puro
Nesta configuração, você os volumes /hana/data e /hana/log no mesmo disco. Os valores sugeridos são derivados dos KPIs que a SAP tem para certificar os tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [Whitepaper de armazenamento SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

As recomendações são frequentemente superiores aos requisitos mínimos sap, conforme estabelecido anteriormente neste artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o throughput máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume para /hana/data e /log | /hana/data s/log I/O throughput | /hana/data s/log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 1.200 GB | 1.200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1.000 MB/s | 1.100 GB | 900 MBps | 10.000 | 
| M64s | 1.000 GiB | 1.000 MB/s |  1.700 GB | 900 MBps | 10.000 | 
| M64ms | 1.750 GiB | 1.000 MB/s | 2.600 GB | 900 MBps | 10.000 | 
| M128s | 2.000 GiB | 2.000 MB/s |2.900 GB | 1.800 MBps |12.000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 5.300 GB | 1.800 MBps |12.000 |  
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4.000 GB | 900 MBps | 10.000 |  
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.700 GB | 900 MBps | 10.000 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.700 GB | 1.800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 15.000 GB | 1.800 MBps | 12.000 |    

Os valores listados destinam-se a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o disco Azure Ultra é que os valores para IOPS e throughput podem ser adaptados sem a necessidade de desligar a VM ou interromper a carga de trabalho aplicada ao sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>VOLUMES NFS v4.1 em arquivos Azure NetApp
O Azure NetApp Files fornece compartilhamentos NFS nativos que podem ser usados para /hana/compartilhado, /hana/data e /hana/log volumes. O uso de ações NFS baseadas em ANF para os volumes /hana/data e /hana/log requer o uso do protocolo v4.1 NFS. O protocolo NFS v3 não é suportado para o uso de volumes /hana/data e /hana/log ao basear as ações na ANF. 

> [!IMPORTANT]
> O protocolo NFS v3 implementado no Azure NetApp Files **não** é suportado para ser usado para /hana/data e /hana/log. O uso do NFS 4.1 é obrigatório para os volumes /hana/data e /hana/log do ponto de vista funcional. Considerando que, para o volume /hana/compartilhado, o protocolo NFS v3 ou o NFS v4.1 podem ser usados do ponto de vista funcional.

### <a name="important-considerations"></a>Considerações importantes
Ao considerar os Arquivos Azure NetApp para o SAP Netweaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB.  
- O tamanho mínimo do volume é de 100 GiB
- Os Arquivos Do Azure NetApp e todas as máquinas virtuais, onde os volumes de Arquivos do Azure NetApp serão montados, devem estar na mesma Rede Virtual Azure ou em [redes virtuais peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede, delegada aos Arquivos do Azure NetApp.
- O throughput de um volume do Azure NetApp é uma função da cota de volume e nível de serviço, conforme documentado no [nível de serviço para arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes do HANA Azure NetApp, certifique-se de que o throughput resultante atenda aos requisitos do sistema HANA.  
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (Read&Write, Read Only, etc.). 
- O recurso Azure NetApp Files ainda não está ciente da zona. Atualmente, o recurso Azure NetApp Files não está implantado em todas as regiões de disponibilidade em uma região do Azure. Esteja ciente das possíveis implicações de latência em algumas regiões do Azure.  
- É importante ter as máquinas virtuais implantadas nas proximidades do armazenamento Azure NetApp para baixa latência. 
- O ID do usuário para <b>o adm sid</b>e o ID de grupo nas `sapsys` máquinas virtuais devem corresponder à configuração em Arquivos Azure NetApp. 

> [!IMPORTANT]
> Para as cargas de trabalho SAP HANA, a baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de arquivos do Azure NetApp sejam implantados de perto.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre o ID do Usuário para <b>o Sid</b>Adm e o Group ID para `sapsys` entre a máquina virtual e a configuração do Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp, montadas em máquinas virtuais, serão exibidas como `nobody`. Certifique-se de especificar o ID de usuário correto `sapsys`para o <b>sid</b>adm e o ID de grupo para , ao embarcar em um novo [sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) para Arquivos Azure NetApp.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para banco de dados HANA em arquivos Do Azure NetApp

O throughput de um volume do Azure NetApp é uma função do tamanho do volume e do nível de serviço, conforme documentado no [nível de serviço para arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao projetar a infra-estrutura para SAP no Azure, você deve estar ciente de alguns requisitos mínimos de throughput de armazenamento pelo SAP, que se traduzem em características mínimas de throughput de:

- Habilitar leitura/gravação em /hana/log de 250 MB/seg com tamanhos de I/O de 1 MB  
- Habilite a atividade de leitura de pelo menos 400MB/seg para /hana/data para tamanhos de E / S de 16MB e 64MB  
- Habilite a atividade gravação de pelo menos 250 MB/seg para /hana/data para tamanhos de E / S de 16MB e 64MB  

Os [limites de throughput do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 TiB de cota de volume são:
- Nível de armazenamento premium - 64 MiB/s  
- Ultra Nível de armazenamento - 128 MiB/s  

> [!IMPORTANT]
> Independente da capacidade que você implanta em um único volume de NFS, espera-se que o throughput platela na faixa de 1,2-1,4 GB/seg de largura de banda alavancada por um consumidor em uma máquina virtual. Isso tem a ver com a arquitetura subjacente da oferta ANF e os limites de sessão Linux relacionados em torno do NFS. Os números de desempenho e throughput documentados no artigo Resultados de [teste de benchmark de desempenho para arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) foram conduzidos contra um volume NFS compartilhado com várias VMs de cliente e como resultado com várias sessões. Esse cenário é diferente do cenário que medimos no SAP. Onde medimos throughput de uma única VM contra um volume NFS. hospedado na ANF.

Para atender aos requisitos mínimos de throughput SAP `/hana/shared`para dados e log, e de acordo com as diretrizes para, os tamanhos recomendados seriam como:

| Volume | Tamanho<br /> Nível de armazenamento premium | Tamanho<br /> Ultra nível de armazenamento | Protocolo NFS suportado |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | Max (512 GB, 1xRAM) por 4 nódulos de trabalhador | Max (512 GB, 1xRAM) por 4 nódulos de trabalhador | v3 ou v4.1 |


> [!NOTE]
> As recomendações de dimensionamento de arquivos do Azure NetApp declaradas aqui são direcionadas para atender aos requisitos mínimos que o SAP expressa em relação aos seus provedores de infra-estrutura. Em implantações reais de clientes e cenários de carga de trabalho, isso pode não ser suficiente. Use essas recomendações como ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.  

Portanto, você pode considerar implantar throughput semelhante para os volumes ANF já listados para armazenamento em disco Ultra. Considere também os tamanhos para os tamanhos listados para os volumes para as diferentes SKUs VM, como feito nas tabelas de disco Ultra já.

> [!TIP]
> Você pode redimensionar os volumes de arquivos do Azure NetApp dinamicamente, sem a `unmount` necessidade dos volumes, parar as máquinas virtuais ou parar o SAP HANA. Isso permite que a flexibilidade atenda às demandas de throughput esperadas e imprevistos.

A documentação sobre como implantar uma configuração de escala SAP HANA com nó de espera usando volumes NFS v4.1 hospedados no ANF é publicada no [sap HANA scale-out com nó de espera em VMs Azure com Arquivos Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte:

- [Guia de alta disponibilidade do SAP HANA para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
