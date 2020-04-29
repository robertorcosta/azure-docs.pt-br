---
title: SAP HANA configurações de armazenamento de máquina virtual do Azure | Microsoft Docs
description: Recomendações de armazenamento para VM que têm SAP HANA implantadas neles.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255610"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento de máquina virtual do SAP HANA no Azure

O Azure fornece diferentes tipos de armazenamento que são adequados para VMs do Azure que estão executando o SAP HANA. Os **SAP Hana tipos de armazenamento do Azure certificados** que podem ser considerados para a lista de implantações SAP Hana como: 

- SSD Premium do Azure  
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber mais sobre esses tipos de disco, consulte o artigo [selecionar um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/). 

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independentemente do tipo de armazenamento do Azure escolhido, o sistema de arquivos usado nesse armazenamento precisa ter suporte do SAP para o sistema operacional específico e o DBMS. [Nota de suporte SAP #405827](https://launchpad.support.sap.com/#/notes/405827) lista os sistemas de arquivos com suporte para diferentes sistemas operacionais e bancos de dados, incluindo SAP Hana. Isso se aplica a todos os volumes SAP HANA pode acessar para leitura e gravação para qualquer tarefa. Especificamente usando o NFS no Azure para SAP HANA, restrições adicionais de versões de NFS se aplicam conforme mencionado posteriormente neste artigo 


As condições mínimas de SAP HANA certificadas para os diferentes tipos de armazenamento são: 

- O Azure SSD Premium-/Hana/log deve ser armazenado em cache com o [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)do Azure. O volume/Hana/data pode ser colocado em SSD Premium sem o Acelerador de Gravação do Azure ou no ultra Disk
- Ultra Disk do Azure pelo menos para o volume/Hana/log. O volume/Hana/data pode ser colocado em qualquer SSD Premium sem Acelerador de Gravação do Azure ou para obter tempos de reinicialização mais rápidos
- Volumes **NFS v 4.1** sobre Azure NetApp Files para/Hana/log **e** /Hana/Data. O volume de/Hana/Shared pode usar o protocolo NFS v3 ou NFS v 4.1. O protocolo NFS v 4.1 é obrigatório para volumes/Hana/data e/Hana/log.

Alguns dos tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar/Hana/data no armazenamento Premium e/Hana/log pode ser colocado em armazenamento de ultra disco para obter a baixa latência necessária. No entanto, se você usar um volume NFS v 4.1 baseado em seja para/Hana/data, será necessário usar outro volume NFS v 4.1 com base em seja para/Hana/log. **Não há suporte**para o uso de NFS sobre seja para um dos volumes (como/Hana/Data) e armazenamento Premium do Azure ou ultra Disk para o outro volume (como/Hana/log).

No mundo local, você raramente teve de se preocupar com os subsistemas de e/s e seus recursos. O motivo era que o fornecedor do dispositivo precisava certificar-se de que os requisitos mínimos de armazenamento fossem atendidos para o SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você deve estar ciente de alguns desses requisitos de SAP emitidos. Algumas das características mínimas de taxa de transferência que são necessárias do SAP estão resultando na necessidade de:

- Habilitar leitura/gravação em **/Hana/log** de 250 MB/s com tamanhos de e/s de 1 MB
- Habilite a atividade de leitura de pelo menos 400MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB
- Habilite a atividade gravação de pelo menos 250 MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB

Uma vez que a baixa latência de armazenamento é crítica aos sistemas DBMS, mesmo que o DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é **obrigatório** aproveitar os discos Premium do Azure para volumes **/Hana/data** e **/Hana/log** . Para alcançar a taxa de transferência mínima **hana/log** e **hana/data** conforme desejado pelo SAP, você precisa para criar um RAID 0 utilizando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E usar os volumes RAID como **hana/data** e **hana/log** volumes. 

**Recomendação: como tamanhos de distribuição para o RAID 0, a recomendação é usar:**

- 256 KB para **/Hana/data**
- 32KB para **hana/log**

> [!IMPORTANT]
> O tamanho de distribuição para/Hana/data foi alterado de recomendações anteriores chamando 64 KB ou 128 KB para 256 KB com base nas experiências do cliente com versões mais recentes do Linux. O tamanho de 256 KB fornece um desempenho ligeiramente melhor

> [!NOTE]
> Não é necessário configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento Premium e Standard do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem rendimento de E/S suficiente.

A acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em discos de Armazenamento Premium do Azure de 3 x P30, ele deverá fornecer três vezes a IOPS e três vezes a taxa de transferência de armazenamento de um único disco P30 do Armazenamento Premium do Azure.

Considere a taxa de transferência de E/S geral ao dimensionar ou escolher uma VM. A taxa de transferência de armazenamento de VM geral está documentada no artigo [Tamanhos da máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo Agendador de E/S do Linux
O Linux possui vários modos de agendamento de E/S diferentes. A recomendação comum por meio de fornecedores do Linux e do SAP é reconfigurar o modo do Agendador de e/s para volumes de disco do modo **Kyber** ou do **data limite do MQ** para o modo **NOOP** (não-multifila) ou **None** para (Multiqueue). Os detalhes são referenciados no [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com armazenamento Premium e Acelerador de Gravação do Azure para máquinas virtuais da série M do Azure
O Azure Acelerador de Gravação é uma funcionalidade disponível para VMs da série M do Azure exclusivamente. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. Para o SAP HANA, acelerador de gravação deve ser usado em relação a **hana/log** somente no volume. Portanto, **/Hana/data** e **/Hana/log** são volumes separados com o Azure acelerador de gravação que dão suporte apenas ao volume **/Hana/log** . 

> [!IMPORTANT]
> Ao usar o armazenamento Premium do Azure, o uso do [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) do Azure ou o volume/Hana/log é obrigatório. Acelerador de Gravação está disponível apenas para VMs de armazenamento Premium e série M e série Mv2.

As recomendações de cache abaixo pressupõem que as características de E/S para o SAP HANA sejam listadas como:

- Praticamente, não há nenhuma carga de trabalho de leitura nos arquivos de dados do HANA. As exceções são E / Ss de tamanho grande após o reinício da instância do HANA ou quando os dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, em grande parte, o cache de leitura não faz sentido pois, na maioria dos casos, todos os volumes de arquivos de dados precisam ser lidos na íntegra.
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Os pontos de salvamento de gravação são assíncronos e não impedem nenhuma transação de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. No entanto, a recuperação de pane devem ser situações excepcionais
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são E/Ss grandes ao executar backups de log de transações, recuperação de pane ou na fase de reinicialização de uma instância do HANA.  
- A carga principal no arquivo de log de refazer do SAP HANA são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações precisam ser persistidas em disco de forma confiável

**Recomendação: como resultado desses padrões de e/s observados por SAP HANA, o cache para os diferentes volumes usando o armazenamento Premium do Azure deve ser definido como:**

- **hana/data** -sem cache
- **/Hana/log** -sem cache-exceção para a série M e Mv2 em que acelerador de gravação deve ser habilitada sem cache de leitura. 
- **/Hana/Shared** -cache de leitura

### <a name="production-recommended-storage-solution"></a>Solução de armazenamento recomendada para produção

> [!IMPORTANT]
> Certificação do SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de gravação do Azure para o **hana/log** volume. Como resultado, as implantações de SAP HANA de cenário de produção em máquinas virtuais da série M do Azure devem ser configurados com o Acelerador de gravação do Azure para o **hana/log** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho pelo SAP e a taxa de transferência máxima de armazenamento que os diferentes tipos de VM fornecem.

**Recomendação: as configurações recomendadas para cenários de produção são semelhantes a:**

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x p30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o **hana/log** volume precisam ser implantados como discos gerenciados.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Acelerador de Gravação do Azure podem ser encontradas no artigo [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Gravação do Azure podem ser localizados na mesma documentação.

**Recomendação: você precisa usar Acelerador de Gravação para discos que formam o volume/Hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuração com consciência de custos do Armazenamento do Microsoft Azure
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também usam para hospedar SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que podem não atender a todos os critérios mínimos de armazenamento para SAP HANA ou que não são oficialmente suportados com SAP HANA pela SAP. Mas até o momento essas VMs parecem funcionar adequadamente para cenários de não produção. O **/Hana/data** e o **/Hana/log** são combinados em um volume. Como resultado, o uso do Azure Acelerador de Gravação pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários com suporte do SAP, verifique se um determinado tipo de VM tem suporte para SAP HANA pela SAP na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma mudança das recomendações anteriores para uma solução econômica, é mudar do [azure HDD Standard disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para melhor desempenho e mais confiável [discos de SSD standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho pelo SAP e a taxa de transferência máxima de armazenamento que os diferentes tipos de VM fornecem.

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
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x p30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se você precisar de alterações no volume **/Hana/backup** , que foi dimensionado para manter os backups que representam o dobro do volume de memória, sinta-se à vontade para se ajustar.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima NÃO seriam benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), pois ele utiliza uma combinação do Armazenamento Premium do Azure e Armazenamento Standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. É necessário escolher o Armazenamento Premium para todos os discos acima listados como o Armazenamento Standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA da VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos que o SAP expressa para seus provedores de infraestrutura. Em cenários de carga de trabalho e implantações de clientes reais, houve situações em que essas recomendações ainda não forneciam recursos suficientes. Essas poderiam ser situações em que um cliente precisava de um recarregamento mais rápido dos dados após uma reinicialização do HANA ou em que as configurações de backup demandavam mais alta largura de banda para o armazenamento. Outros casos incluíram **/hana/log**, onde 5000 IOPS não foram suficientes para a carga de trabalho específica. Portanto, considere essas recomendações como um ponto de partida e adapte-se com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento do Azure ultra Disk para SAP HANA
A Microsoft está no processo de distribuir um novo tipo de armazenamento do Azure chamado [ultra Disk do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). A diferença significativa entre o armazenamento do Azure oferecido até o momento e o ultra Disk é que os recursos de disco não estão mais vinculados ao tamanho do disco. Como um cliente, você pode definir esses recursos para o ultra Disk:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a IOPS de 160K (o máximo também depende dos tipos de VM)
- Taxa de transferência de armazenamento de 300 MB/seg a 2.000 MB/s

O ultra Disk oferece a possibilidade de definir um único disco que atenda a seu tamanho, IOPS e intervalo de taxa de transferência de disco. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM sobre o armazenamento Premium do Azure para construir volumes que atendam aos requisitos de taxa de transferência de armazenamento e IOPS. Você pode executar uma combinação de configuração entre o ultra Disk e o armazenamento Premium. Como resultado, você pode limitar o uso de ultra Disk para os volumes críticos de desempenho/Hana/data e/Hana/log e abranger os outros volumes com o armazenamento Premium do Azure

Outras vantagens do ultra Disk podem ser a melhor latência de leitura em comparação com o armazenamento Premium. A latência de leitura mais rápida pode ter vantagens quando você deseja reduzir os tempos de inicialização do HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento de ultra disco também podem ser sentidas quando o HANA está escrevendo pontos de salvamento. Como os discos de armazenamento Premium para/Hana/data geralmente não são Acelerador de Gravação armazenados em cache, a latência de gravação para/Hana/data no armazenamento Premium em comparação com o ultra Disk é maior. Pode ser esperado que o salvamento de pontos com o ultra Disk esteja com um desempenho melhor no ultra Disk.

> [!IMPORTANT]
> O ultra Disk ainda não está presente em todas as regiões do Azure e também ainda não está dando suporte a todos os tipos de VM listados abaixo. Para obter informações detalhadas em que o ultra Disk está disponível e quais famílias de VM têm suporte, consulte o artigo [quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de ultra Disk pura
Nessa configuração, você mantém os volumes/Hana/data e/Hana/log separadamente. Os valores sugeridos são derivados dos KPIs que o SAP tem para certificar tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho pelo SAP e a taxa de transferência máxima de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | volume/Hana/data | taxa de transferência de e/s de/Hana/data | IOPS de/Hana/data | volume/Hana/log | taxa de transferência de e/s de/Hana/log | IOPS de/Hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2\.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M64ls | 512 GiB | 1.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 1.200 MBps |9.000 | 512 GB | 800 MBps  | 3\.000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 1200 MBps |9.000 | 512 GB | 800 MBps  | 3\.000 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 1.000 MBps | 9.000 | 512 GB | 400 MBps  | 2\.500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 1.000 MBps | 9.000 | 512 GB | 400 MBps  | 2\.500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.500 MBps | 9.000 | 512 GB | 800 MBps  | 3\.000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 9.000 | 512 GB | 800 MBps  | 3\.000 |   

Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o ultra Disk do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de ultra disco não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os serviços de backup do Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento consciente de custo com configuração de ultra Disk pura
Nessa configuração, você os volumes/Hana/data e/Hana/log no mesmo disco. Os valores sugeridos são derivados dos KPIs que o SAP tem para certificar tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho pelo SAP e a taxa de transferência máxima de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume para/Hana/data e/log | /Hana/data e taxa de transferência de e/s de log | IOPS de/Hana/data e log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 1.200 GB | 1.200 MBps | 9.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9.500 | 
| M64ls | 512 GiB | 1.000 MB/s | 1.100 GB | 900 MBps | 10.000 | 
| M64s | 1.000 GiB | 1.000 MB/s |  1.700 GB | 900 MBps | 10.000 | 
| M64ms | 1.750 GiB | 1.000 MB/s | 2.600 GB | 900 MBps | 10.000 | 
| M128s | 2.000 GiB | 2.000 MB/s |2.900 GB | 1.800 MBps |12.000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 5.300 GB | 1.800 MBps |12.000 |  
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4.000 GB | 900 MBps | 10.000 |  
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.700 GB | 900 MBps | 10.000 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.700 GB | 1, 800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 15.000 GB | 1.800 MBps | 12.000 |    

Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o ultra Disk do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v 4.1 no Azure NetApp Files
O Azure NetApp Files fornece compartilhamentos NFS nativos que podem ser usados para volumes/Hana/Shared,/Hana/data e/Hana/log. O uso de compartilhamentos NFS baseados em seja para os volumes/Hana/data e/Hana/log requer o uso do protocolo v 4.1 NFS. Não há suporte para o protocolo de NFS v3 para o uso de volumes/Hana/data e/Hana/log ao basear os compartilhamentos em seja. 

> [!IMPORTANT]
> O protocolo NFS v3 implementado em Azure NetApp Files **não** tem suporte para ser usado para/Hana/data e/Hana/log. O uso do NFS 4,1 é obrigatório para volumes/Hana/data e/Hana/log de um ponto de vista funcional. Enquanto para o volume/Hana/Shared, o NFS v3 ou o protocolo NFS v 4.1 podem ser usados a partir de um ponto de vista funcional.

### <a name="important-considerations"></a>Considerações importantes
Ao considerar Azure NetApp Files para o SAP NetWeaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB.  
- O tamanho mínimo do volume é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, nas quais Azure NetApp Files volumes serão montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files.
- A taxa de transferência de um volume do Azure NetApp é uma função da cota de volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes da NetApp do Azure do HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.  
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (leitura&gravação, somente leitura, etc.). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente Azure NetApp Files recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atento às possíveis implicações de latência em algumas regiões do Azure.  
- É importante ter as máquinas virtuais implantadas de perto para o armazenamento do Azure NetApp para baixa latência. 
- A ID de usuário para <b>Sid</b>ADM e a ID de `sapsys` grupo para nas máquinas virtuais devem corresponder à configuração no Azure NetApp files. 

> [!IMPORTANT]
> Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre a ID de usuário para <b>Sid</b>ADM e a ID `sapsys` de grupo para entre a máquina virtual e a configuração da NetApp do Azure, as permissões para arquivos nos volumes do Azure NetApp, montadas em `nobody`máquinas virtuais, serão exibidas como. Certifique-se de especificar a ID de usuário correta para <b>Sid</b>ADM e a ID `sapsys`de grupo para, ao integrar [um novo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) para Azure NetApp files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para o banco de dados HANA no Azure NetApp Files

A taxa de transferência de um volume do Azure NetApp é uma função do tamanho do volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao projetar a infraestrutura para o SAP no Azure, você deve estar ciente de alguns requisitos mínimos de taxa de transferência de armazenamento pelo SAP, que são convertidos em características de taxa de transferência mínima do:

- Habilitar leitura/gravação em/Hana/log de 250 MB/s com tamanhos de e/s de 1 MB  
- Habilite a atividade de leitura de pelo menos 400MB/seg para /hana/data para tamanhos de E / S de 16MB e 64MB  
- Habilite a atividade gravação de pelo menos 250 MB/seg para /hana/data para tamanhos de E / S de 16MB e 64MB  

Os [limites de taxa de transferência de Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 Tib de cota de volume são:
- Camada de armazenamento Premium-64 MiB/s  
- Camada de armazenamento ultra-128 MiB/s  

> [!IMPORTANT]
> Independentemente da capacidade que você implanta em um único volume NFS, a taxa de transferência é esperada para limite no intervalo de largura de banda de 1,2 a 1,4 GB/s utilizada por um consumidor em uma máquina virtual. Isso tem a ver com a arquitetura subjacente da oferta de seja e limites de sessão do Linux relacionados em relação ao NFS. Os números de desempenho e taxa de transferência, conforme documentado no artigo [resultados do teste de desempenho de benchmark para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) foram realizados em um volume NFS compartilhado com várias VMs cliente e como resultado com várias sessões. Esse cenário é diferente para o cenário que medimos no SAP. Onde medimos a taxa de transferência de uma única VM em um volume NFS. hospedado em seja.

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log, e de `/hana/shared`acordo com as diretrizes para, os tamanhos recomendados seriam semelhantes a:

| Volume | Tamanho<br /> Camada de armazenamento Premium | Tamanho<br /> Camada de armazenamento ultra | Protocolo NFS com suporte |
| --- | --- | --- |
| /Hana/log | 4 TiB | 2 TiB | 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) por 4 nós de trabalho | Max (512 GB, 1xRAM) por 4 nós de trabalho | V3 ou v 4.1 |


> [!NOTE]
> As recomendações de dimensionamento Azure NetApp Files declaradas aqui estão direcionadas para atender aos requisitos mínimos do SAP expressos em direção aos seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, isso pode não ser suficiente. Use essas recomendações como um ponto de partida e adapte-se com base nos requisitos de sua carga de trabalho específica.  

Portanto, você pode considerar implantar uma taxa de transferência semelhante para os volumes seja, conforme listado para o armazenamento de ultra disco, já. Além disso, considere os tamanhos dos tamanhos listados para os volumes para os diferentes SKUs de VM, conforme feito nas tabelas de ultra Disk já.

> [!TIP]
> Você pode redimensionar Azure NetApp Files volumes dinamicamente, sem a necessidade `unmount` dos volumes, parar as máquinas virtuais ou parar SAP Hana. Isso permite flexibilidade para atender ao seu aplicativo demandas de produtividade esperadas e imprevistas.

A documentação sobre como implantar uma configuração de expansão SAP HANA com o nó em espera usando volumes NFS v 4.1 hospedados em seja é publicada em [SAP Hana expansão com o nó em espera em VMs do Azure com Azure NetApp files em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte:

- [SAP Hana guia de alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
