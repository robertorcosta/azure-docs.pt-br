---
title: Configurações de armazenamento de máquina virtual do SAP HANA no Azure | Microsoft Docs
description: Recomendações de armazenamento para VMs que têm o SAP HANA implantado nelas.
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
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682592"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento de máquina virtual do SAP HANA no Azure

O Azure fornece diferentes tipos de armazenamento principais adequados a máquinas virtuais do Azure que executam o SAP HANA. Os **tipos de armazenamento do Azure certificados pelo SAP HANA** que podem ser considerados para implantações do SAP HANA são listados como: 

- SSD Premium do Azure  
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber mais sobre esses tipos de disco, confira nosso artigo [Selecionar um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/). 

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independentemente do tipo de armazenamento do Azure escolhido, o sistema de arquivos usado nesse armazenamento precisa ter suporte do SAP para o sistema operacional específico e o DBMS. [Nota de suporte nº 405827 do SAP](https://launchpad.support.sap.com/#/notes/405827) lista os sistemas de arquivos com suporte para diferentes sistemas operacionais e bancos de dados, incluindo o SAP HANA. Isso se aplica a todos os volumes que o SAP HANA pode acessar para leitura e gravação de qualquer tarefa. Especificamente usando o NFS no Azure para o SAP HANA, restrições adicionais de versões do NFS se aplicam conforme mencionado mais adiante neste artigo 


As condições mínimas certificadas pelo SAP HANA para os diferentes tipos de armazenamento são: 

- SSD Premium do Azure – /hana/log é necessário para ser armazenado em cache dom o [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) do Azure. The volume /hana/data pode ser colocado no SSD Premium sem o Acelerador de Gravação do Azure ou no Disco Ultra
- Disco Ultra do Azure pelo menos para o volume /hana/log. O volume /hana/data pode ser colocado em qualquer SSD Premium sem o Acelerador de Gravação do Azure ou para obter um disco Ultra com tempos de reinicialização mais rápidos
- Os volumes **NFS v4.1** baseados no Azure NetApp Files para /hana/log **e** /hana/data. O volume /hana/shared pode usar o protocolo NFS v3 ou NFS v4.1. O protocolo NFS v4.1 é obrigatório para volumes /hana/data e /hana/log.

Alguns tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar /hana/data no Armazenamento Premium e /hana/log pode ser colocado no armazenamento de disco Ultra para obter a latência baixa necessária. No entanto, se você usar um volume NFS v4.1 baseado no ANF para /hana/data, será necessário usar outro volume NFS v4.1 com base no ANF para /hana/log. O uso do NFS baseado no ANF para um dos volumes (como /hana/data) e o Armazenamento Premium do Azure ou o disco Ultra para o outro volume (como /hana/log) **não tem suporte**.

No mundo local, você raramente precisava preocupar-se com os subsistemas de E/S e as funcionalidades. O motivo era que o fornecedor do dispositivo precisava certificar-se de que os requisitos mínimos de armazenamento fossem atendidos para o SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você deve estar ciente de alguns desses requisitos emitidos pelo SAP. Algumas das características mínimas de taxa de transferência que o SAP exige resultam na necessidade de:

- Habilitar a leitura/gravação em **/hana/log** de 250 MB/s com tamanhos de E/S de 1 MB
- Habilite a atividade de leitura de pelo menos 400MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB
- Habilite a atividade gravação de pelo menos 250 MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB

Uma vez que a baixa latência de armazenamento é crítica aos sistemas DBMS, mesmo que o DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é **obrigatório** usar os Discos Premium do Azure para os volumes **/hana/data** e **/hana/log**. Para alcançar a taxa de transferência mínima **hana/log** e **hana/data** conforme desejado pelo SAP, você precisa para criar um RAID 0 utilizando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E usar os volumes RAID como **hana/data** e **hana/log** volumes. 

> [!IMPORTANT]
>Os três FileSystems do SAP HANA /data, /log e /shared não devem ser colocados em um grupo de volume padrão ou raiz.  É altamente recomendável seguir as diretrizes de fornecedores do Linux, que costumam criar grupos de volumes individuais para /data, /log e /shared.

**Recomendação: com tamanhos de faixa para RAID 0, o recomendável é usar:**

- 256 KB para **/hana/data**
- 32KB para **hana/log**

> [!IMPORTANT]
> O tamanho de faixa para /hana/data foi alterado das recomendações anteriores que chamam 64 KB ou 128 KB para 256 KB com base nas experiências do cliente com versões mais recentes do Linux. O tamanho de 256 KB fornece um desempenho ligeiramente melhor

> [!NOTE]
> Não é necessário configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento Premium e Standard do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem rendimento de E/S suficiente.

A acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em discos de Armazenamento Premium do Azure de 3 x P30, ele deverá fornecer três vezes a IOPS e três vezes a taxa de transferência de armazenamento de um único disco P30 do Armazenamento Premium do Azure.

Considere a taxa de transferência de E/S geral ao dimensionar ou escolher uma VM. A taxa de transferência de armazenamento de VM geral está documentada no artigo [Tamanhos da máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo Agendador de E/S do Linux
O Linux possui vários modos de agendamento de E/S diferentes. A recomendação comum por meio de fornecedores do Linux e do SAP é reconfigurar o modo de agendador de E/S para volumes de disco do modo **mq-deadline** ou **kyber** para o modo **noop** (não multifila) ou **none** (multifila). Detalhes são referenciados na [Nota SAP Nº 1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com Armazenamento Premium e Acelerador de Gravação do Azure para máquinas virtuais da Série M do Azure
O Acelerador de Gravação do Azure é uma funcionalidade disponível exclusivamente para VMs da Série M do Azure. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. Para o SAP HANA, acelerador de gravação deve ser usado em relação a **hana/log** somente no volume. Portanto, **/hana/data** e **/gana/log** são volumes separados com o Acelerador de Gravação do Azure que dão suporte apenas ao volume **/hana/log**. 

> [!IMPORTANT]
> Ao usar o Armazenamento Premium do Azure, o uso do [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) do Azure ou o volume /hana/log é obrigatório. O Acelerador de Gravação está disponível apenas para Armazenamento Premium e VMs da série M e Mv2.

As recomendações de cache abaixo pressupõem que as características de E/S para o SAP HANA sejam listadas como:

- Praticamente, não há nenhuma carga de trabalho de leitura nos arquivos de dados do HANA. As exceções são E / Ss de tamanho grande após o reinício da instância do HANA ou quando os dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, em grande parte, o cache de leitura não faz sentido pois, na maioria dos casos, todos os volumes de arquivos de dados precisam ser lidos na íntegra.
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Os pontos de salvamento de gravação são assíncronos e não impedem nenhuma transação de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. No entanto, a recuperação de pane devem ser situações excepcionais
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são E/Ss grandes ao executar backups de log de transações, recuperação de pane ou na fase de reinicialização de uma instância do HANA.  
- A carga principal no arquivo de log de refazer do SAP HANA são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações precisam ser persistidas em disco de forma confiável

**Recomendação: Como resultado desses padrões de E/S observados pelo SAP HANA, o cache dos diferentes volumes usando o Armazenamento Premium do Azure deve ser definido como:**

- **hana/data** -sem cache
- **/hana/log** – sem cache – exceção para a série M e Mv2 em que Acelerador de Gravação deve ser habilitado sem cache de leitura. 
- **hana/shared** - ler o cache

### <a name="production-recommended-storage-solution"></a>Solução de armazenamento recomendada para produção

> [!IMPORTANT]
> Certificação do SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de gravação do Azure para o **hana/log** volume. Como resultado, as implantações de SAP HANA de cenário de produção em máquinas virtuais da série M do Azure devem ser configurados com o Acelerador de gravação do Azure para o **hana/log** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho do SAP e a taxa de transferência de armazenamento máxima que os diferentes tipos de VM fornecem.

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
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumenta a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o **hana/log** volume precisam ser implantados como discos gerenciados.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Acelerador de Gravação do Azure podem ser encontradas no artigo [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Gravação do Azure podem ser localizados na mesma documentação.

**Recomendação: você precisa usar o Acelerador de Gravação para discos que formam o volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuração com consciência de custos do Armazenamento do Microsoft Azure
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também utilizam para hospedar o SAP HANA em VMs do Azure. Talvez existam alguns tipos de VMs que não atendam a todos os critérios de armazenamento mínimos do SAP HANA ou que não sejam oficialmente compatíveis com o SAP HANA pelo SAP. Mas até o momento essas VMs parecem funcionar adequadamente para cenários de não produção. O **/hana/data** e o **/hana/log** são combinados em um volume. Como resultado, o uso do Acelerador de Gravação do Azure pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários com suporte do SAP, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma alteração das recomendações anteriores para uma solução econômica é migrar de [discos HDD Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para [discos SSD Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) mais confiáveis e com melhor desempenho

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho do SAP e a taxa de transferência de armazenamento máxima que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1\.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1\.000 GiB | 1\.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2\.000 GiB | 2\.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se você precisar de alterações no volume **/hana/backu**p, que foi dimensionado para manter backups que representam o dobro do volume da memória, sinta-se à vontade para fazer ajustes.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumenta a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima NÃO seriam benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), pois ele utiliza uma combinação do Armazenamento Premium do Azure e Armazenamento Standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. É necessário escolher o Armazenamento Premium para todos os discos acima listados como o Armazenamento Standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA da VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos que o SAP expressa para seus provedores de infraestrutura. Em cenários de carga de trabalho e implantações de clientes reais, houve situações em que essas recomendações ainda não forneciam recursos suficientes. Essas poderiam ser situações em que um cliente precisava de um recarregamento mais rápido dos dados após uma reinicialização do HANA ou em que as configurações de backup demandavam mais alta largura de banda para o armazenamento. Outros casos incluíram **/hana/log**, onde 5000 IOPS não foram suficientes para a carga de trabalho específica. Portanto, considere essas recomendações como um ponto de partida e adapte-se com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento de disco Ultra do Azure para SAP HANA
A Microsoft está no processo de distribuir um novo tipo de armazenamento do Azure chamado [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) do Azure. A diferença significativa entre o armazenamento do Azure oferecido até o momento e o disco Ultra é que as funcionalidades de disco não estão mais vinculadas ao tamanho do disco. Como um cliente, você pode definir esses recursos para o disco Ultra:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a 160 mil IOPS (o máximo também depende dos tipos de VM)
- Taxa de transferência de armazenamento de 300 MB/s a 2.000 MB/s

O disco Ultra oferece a possibilidade de definir um único disco que atenda a seu tamanho, ao IOPS e ao intervalo de taxa de transferência de disco. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM com base no Armazenamento Premium do Azure para construir volumes que atendam aos requisitos de taxa de transferência de armazenamento e IOPS. Você pode executar uma combinação de configuração entre o disco Ultra e o Armazenamento Premium. Como resultado, você pode limitar o uso do disco Ultra para os volumes críticos de desempenho /hana/data e/hana/log e abranger os outros volumes com o Armazenamento Premium do Azure

Outras vantagens do disco Ultra pode ser a melhor latência de leitura em comparação com o Armazenamento Premium. A latência de leitura mais rápida pode ter vantagens quando você deseja reduzir os tempos de inicialização do HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento de disco Ultra também podem ser sentidas quando o HANA está escrevendo pontos de salvamento. Como os discos de Armazenamento Premium para /hana/data geralmente não são Acelerador de Gravação armazenados em cache, a latência de gravação em /hana/data no Armazenamento Premium em comparação com o disco Ultra é maior. Pode ser esperado que a gravação do ponto de salvamento com o disco Ultra está tendo melhor desempenho no disco Ultra.

> [!IMPORTANT]
> O disco Ultra ainda não está presente em todas as regiões do Azure e também ainda não está dando suporte a todos os tipos de VM listados abaixo. Para obter informações detalhadas sobre onde o disco Ultra está disponível para quais famílias de VM há suporte, consulte o artigo [Quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de disco Ultra pura
Nessa configuração, você mantém os volumes /hana/data e /hana/log separadamente. Os valores sugeridos são derivados dos KPIs que o SAP precisa certificar para tipos de VM para SAP HANA e as configurações de armazenamento, conforme recomendado no [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho do SAP e a taxa de transferência de armazenamento máxima que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume /hana/data | Taxa de transferência de E/S /hana/data | IOPS /hana/data | Volume /hana/log | Taxa de transferência de E/S /hana/log | IOPS /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2\.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M64ls | 512 GiB | 1\.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBps |9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 1\.200 MBps |9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3\.000 |   

Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o Disco Ultra do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de disco Ultra não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os serviços de Backup do Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento econômica com configuração de disco Ultra pura
Nessa configuração, você mantém os volumes /hana/data e /hana/log no mesmo disco. Os valores sugeridos são derivados dos KPIs que o SAP precisa certificar para tipos de VM para SAP HANA e as configurações de armazenamento, conforme recomendado no [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho do SAP e a taxa de transferência de armazenamento máxima que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume para /hana/data e /log | Taxa de transferência de E/S /hana/data e log | IOPS /hana/data e log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9\.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9\.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9\.500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10.000 | 
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10.000 | 
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10.000 | 
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 Mbps |12.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 Mbps |12.000 |  
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10.000 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10.000 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.700 GB | 1\.800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 15.000 GB | 1\.800 Mbps | 12.000 |    

Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o Disco Ultra do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v4.1 em Azure NetApp Files
O Azure NetApp Files fornece compartilhamentos NFS nativos que podem ser usados para volumes /hana/shared, /hana/data e /hana/log. O uso de compartilhamentos NFS baseados em ANF para os volumes /hana/data e /hana/log requer o uso do protocolo NFS v4.1. Não há suporte para o protocolo NFS v3 para o uso de volumes /hana/data e /hana/log ao basear os compartilhamentos no ANF. 

> [!IMPORTANT]
> **Não** há suporte para o protocolo NFS v3 implementado no Azure NetApp Files para ser usado para /hana/data e /hana/log. O uso do NFS 4.1 é obrigatório para volumes /hana/data e /hana/log de um ponto de vista funcional. Enquanto que, para o volume /hana/shared, o protocolo NFS v3 ou NFS v4.1 pode ser usado de um ponto de vista funcional.

### <a name="important-considerations"></a>Considerações importantes
Ao considerar Azure NetApp Files para o SAP Netweaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é de 4 TiB.  
- O tamanho mínimo do volume é 100 GiB
- O Azure NetApp Files e todas as máquinas virtuais, em que os volumes do Azure NetApp Files serão montados, devem estar na mesma Rede Virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede, delegada ao Azure NetApp Files.
- A taxa de transferência de um volume do Azure NetApp é uma função da cota de volume e do nível de serviço, conforme documentado no [Nível de serviço para Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes Azure NetApp do HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.  
- Azure NetApp Files oferece a [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos e o tipo de acesso (leitura e gravação, somente leitura etc.). 
- O recurso do Azure NetApp Files ainda não tem reconhecimento de zona. No momento, o recurso do Azure NetApp Files não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure.  
- É importante que as máquinas virtuais sejam implantadas bem próximas ao armazenamento do Azure NetApp para ter baixa latência. 
- A ID de Usuário para <b>sid</b>adm e a ID de Grupo para `sapsys` nas máquinas virtuais devem corresponder com a configuração no Azure NetApp Files. 

> [!IMPORTANT]
> Para cargas de trabalho do SAP HANA, baixa latência é fundamental. Trabalhe com seu representante da Microsoft para verificar se as máquinas virtuais e os volumes do Azure NetApp Files foram implantados bem próximos.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre a ID de usuário para <b>sid</b>adm e a ID de Grupo para `sapsys` entre a máquina virtual e a configuração do Azure NetApp, as permissões dos arquivos em volumes do Azure NetApp, montadas em máquinas virtuais, serão exibidas como `nobody`. Especifique a ID de Usuário correta para <b>sid</b>adm e a ID do Grupo para `sapsys`, ao [integrar um novo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ao Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionar o banco de dados do HANA no Azure NetApp Files

A taxa de transferência de um volume do Azure NetApp é uma função do tamanho de volume e do nível de serviço, conforme documentado no [Nível de serviço para Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao criar a infraestrutura para o SAP no Azure, você deve estar ciente de alguns requisitos de taxa de transferência mínima de armazenamento do SAP, que são convertidos em características de taxa de transferência mínima de:

- Habilitar a leitura/gravação em /hana/log de 250 MB/s com tamanhos de E/S de 1 MB  
- Habilitar a atividade de leitura de pelo menos 400 MB/s para /hana/data para tamanhos de E/S de 16 MB e 64 MB  
- Habilitar a atividade gravação de pelo menos 250 MB/s para /hana/data com tamanhos de E/S de 16 MB e 64 MB  

Os [limites de taxa de transferência do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 TiB de cota de volume são:
- Camada de Armazenamento Premium – 64 MiB/s  
- Camada de Armazenamento Ultra – 128 MiB/s  

> [!IMPORTANT]
> Independentemente da capacidade que você implanta em um único volume NFS, espera-se que a taxa de transferência fique estável no intervalo de largura de banda de 1,2 a 1,4 GB/s utilizada por um consumidor em uma máquina virtual. Isso tem a ver com a arquitetura subjacente da oferta do ANF e com os limites de sessão do Linux relacionados em relação ao NFS. Os números de desempenho e taxa de transferência, conforme documentado no artigo [Resultados do teste de benchmark de desempenho para Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) foram realizados em um volume NFS compartilhado com várias VMs de cliente e como resultado com várias sessões. Esse cenário é diferente para o cenário que medimos no SAP. Nele, medimos a taxa de transferência de uma única VM com relação a um volume NFS. hospedado no ANF.

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log e, de acordo com as diretrizes de `/hana/shared`, os tamanhos recomendados seriam semelhantes a:

| Volume | Tamanho<br /> Camada de armazenamento Premium | Tamanho<br /> Camada de armazenamento Ultra | Protocolo do NFS com suporte |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared | Max(512 GB, 1xRAM) por 4 nós de trabalho | Max(512 GB, 1xRAM) por 4 nós de trabalho | v3 ou v4.1 |


> [!NOTE]
> As recomendações de dimensionamento do Azure NetApp Files declaradas aqui estão visando os requisitos mínimos que o SAP expressa para seus provedores de infraestrutura. Em implantações de clientes e cenários de carga de trabalho reais, isso pode não ser suficiente. Use essas recomendações como um ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.  

Portanto, você pode considerar implantar uma taxa de transferência semelhante para os volumes do ANF conforme já listado para o armazenamento de disco Ultra. Além disso, considere os tamanhos listados para os volumes dos diferentes SKUs de VM, como já foi feito nas tabelas do disco Ultra.

> [!TIP]
> Você pode redimensionar os volumes do Azure NetApp Files dinamicamente, sem a necessidade de `unmount` os volumes, parar as máquinas virtuais ou parar o SAP HANA. Isso oferece flexibilidade para você atender às demandas de taxa de transferência tanto esperadas quanto imprevistas do seu aplicativo.

A documentação sobre como implantar uma configuração de expansão do SAP HANA com o nó em espera usando volumes NFS v4.1 hospedados no ANF foi publicada na [Expansão do SAP HANA com o nó em espera em VMs do Azure com o Azure NetApp Files no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte:

- [Guia de alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
