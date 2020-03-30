---
title: Backup do Azure do SAP HANA no nível do arquivo | Microsoft Docs
description: Há duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure, e este artigo aborda o Backup do Azure do SAP HANA no nível do arquivo
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271395"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup do Azure do SAP HANA no nível do arquivo

## <a name="introduction"></a>Introdução

Este artigo é um artigo relacionado ao [guia backup do SAP HANA no Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide), que fornece uma visão geral e informações sobre como começar e mais detalhes sobre os instantâneos de armazenamento e serviço de backup do Azure. 

Diferentes tipos de VM no Azure permitem que um número diferente de VHDs sejam anexados. Os detalhes exatos estão documentados em [Tamanhos de máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). Para os testes referidos nesta documentação usamos uma VM GS5 Azure, que permite 64 discos de dados conectados. Para sistemas SAP HANA maiores, um número significativo de discos já pode estar ocupado com arquivos de log e de dados, possivelmente em combinação com a distribuição de software para obter a taxa de transferência de E/S de disco ideal. Para obter mais detalhes sobre as configurações sugeridas de disco para implantações SAP HANA em VMs Do Zure, leia o artigo [Configurações de armazenamento de máquinavirtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). As sugestões também incluem recomendações de espaço em disco para backups locais.

O modo padrão de gerenciamento de backup/restauração no nível do arquivo é com um backup baseado em arquivo via declarações do SAP HANA Studio ou do SAP HANA SQL. Para obter mais informações, leia o artigo [SAP HANA SQL and System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio. Ao escolher o tipo de &quot;arquivo,&quot;, é preciso especificar um caminho no sistema de arquivos no qual o SAP HANA gravará os arquivos de backup. A restauração funciona da mesma maneira.

Embora essa opção pareça simples e direta, há algumas considerações. Uma VM do Azure tem uma limitação do número de discos de dados que podem ser anexados. Talvez não haja capacidade para armazenar arquivos de backup do SAP HANA nos sistemas de arquivos da VM, dependendo dos requisitos de tamanho do banco do dedos e de taxa de transferência do disco, que podem envolver a distribuição de software entre vários discos de dados. Posteriormente, este artigo fornece várias opções para mover esses arquivos de backup e gerenciar as restrições de tamanho de arquivo e desempenho ao lidar com terabytes de dados.

Outra opção, que oferece mais liberdade com relação à capacidade total, é o Armazenamento de Blobs do Azure. Embora um único blob também esteja restrito a 1 TB, a capacidade total de um único contêiner de blob é atualmente de 500 TB. Além disso, ele oferece aos clientes a opção de selecionar o chamado armazenamento de blobs &quot;estático&quot;, que tem um bom custo-benefício. Consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) para obter detalhes sobre o armazenamento de bolhas legais.

Para obter mais segurança, use uma conta de armazenamento com replicação geográfica para armazenar os backups do SAP HANA. Consulte [a redundância do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter detalhes sobre redundância de armazenamento e replicação de armazenamento.

É possível colocar VHDs dedicados para backups do SAP HANA em uma conta de armazenamento de backup dedicada com replicação geográfica. Ou é possível copiar os VHDs que armazenam os backups do SAP HANA em uma conta de armazenamento com replicação geográfica ou para uma conta de armazenamento em uma região diferente.

## <a name="azure-blobxfer-utility-details"></a>Detalhes do utilitário blobxfer do Azure

Para armazenar diretórios e arquivos no armazenamento do Azure, é possível usar a CLI ou o PowerShell, ou desenvolver uma ferramenta usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/). Há também um utilitário pronto para uso, o AzCopy, para copiar dados para o armazenamento Do Zure. (consulte [Transferir dados com o utilitário de linha de comando AzCopy](../../../storage/common/storage-use-azcopy.md)).

Portanto, o blobxfer foi usado para copiar arquivos de backup do SAP HANA. Ele é um software livre, usado por muitos clientes em ambientes de produção e disponível no [GitHub](https://github.com/Azure/blobxfer). Essa ferramenta permite a cópia dos dados diretamente no Armazenamento de Blobs do Azure ou no compartilhamento de arquivos do Azure. Ele também oferece uma gama de recursos úteis, como hash md5, ou paralelismo automático ao copiar um diretório com vários arquivos.

## <a name="sap-hana-backup-performance"></a>Desempenho de backup do SAP HANA
Neste capítulo, são discutidas considerações de desempenho. Os números alcançados podem não representar o estado mais recente, uma vez que há desenvolvimento constante para obter melhor throughput para o armazenamento Azure. Como resultado, você deve realizar testes individuais para sua configuração e região do Azure.

![Essa captura de tela é do console de backup do SAP HANA no SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Esta captura de tela mostra o console de backup SAP HANA do SAP HANA Studio. Levou cerca de 42 minutos para realizar um backup de 230 GB em um único disco de armazenamento Azure Standard HDD conectado ao HANA VM usando o sistema de arquivos XFS em um único disco.

![Esta captura de tela é do YaST na VM de teste do SAP HANA](media/sap-hana-backup-file-level/one-backup-disk.png)

Esta captura de tela é do YaST na VM de teste do SAP HANA. Você pode ver o disco único de 1 TB para backup SAP HANA. Demorou aproximadamente 42 minutos para fazer backup de 230 GB. Além disso, cinco discos de 200 GB foram anexados e o RAID de software md0 foi criado, com distribuição nesses cinco discos de dados do Azure.

![Repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexados](media/sap-hana-backup-file-level/five-backup-disks.png)

A repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexado reduziu o tempo de backup de 42 minutos para 10 minutos. Os discos foram anexados sem armazenamento em cache para a VM. Este exercício demonstra a importância do throughput de gravação de disco para alcançar um bom tempo de backup. Você pode mudar para o armazenamento SSD Padrão Azure ou armazenamento Premium Azure para acelerar ainda mais o processo para um desempenho ideal. Em geral, o armazenamento HDD padrão do Azure não é recomendado e foi usado apenas para fins de demonstração. A recomendação é usar um mínimo de armazenamento SSD Padrão Azure ou Armazenamento Premium Azure para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar arquivos de backup do SAP HANA para o Armazenamento de Blobs do Azure
Os números de desempenho, números de duração de backup e números de duração de cópia mencionados podem não representar o estado mais recente da tecnologia Azure. A Microsoft está constantemente melhorando o armazenamento do Azure para oferecer mais throughput e latências mais baixas. Portanto, os números são apenas para fins de demonstração. Você precisa testar para sua necessidade individual na região Azure de sua escolha para ser capaz de julgar com método é o melhor para você.

Outra opção para armazenar rapidamente arquivos de backup do SAP HANA é o Armazenamento de Blobs do Azure. Um único recipiente blob tem um limite de cerca de 500 TB, o suficiente para sistemas SAP HANA, usando M32ts, M32ls, M64ls e GS5 VM tipos de Azure, para manter backups SAP HANA suficientes. Os clientes &quot;têm&quot; &quot;a&quot; escolha entre armazenamento de bolhas quente e fria (veja [armazenamento Azure Blob: níveis de acesso quente, frio e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)).

Com a ferramenta blobxfer, é fácil copiar os arquivos de backup do SAP HANA diretamente no Armazenamento de Blobs do Azure.

![Aqui é possível ver os arquivos de um backup completo de arquivos do SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Você pode ver os arquivos de um backup completo de arquivo SAP HANA. Dos quatro arquivos, o maior tem aproximadamente 230 GB de tamanho.

![Demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Sem usar um hash md5 no teste inicial, demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure.

O console do backup do HANA Studio permite restringir o tamanho máximo dos arquivos de backup do HANA. No ambiente de amostra, ele melhorou o desempenho tendo vários arquivos de backup menores, em vez de um grande arquivo de 230 GB.

Definir o limite de tamanho do arquivo de backup no lado HANA não&#39;melhora o tempo de backup, porque os arquivos são gravados sequencialmente. O limite de tamanho do arquivo foi definido como 60 GB, portanto o backup criou quatro arquivos de dados grandes, em vez de um único arquivo de 230 GB. O uso de vários arquivos de backup pode se tornar uma necessidade para fazer backup de bancos de dados HANA se seus alvos de backup tiverem limitações nos tamanhos de arquivos de tamanhos blob.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB, o que resultou em19 arquivos de backup. Essa configuração reduziu o tempo usado pelo blobxfer para copiar os 230 GB para o Armazenamento de Blobs do Azure de 3000 segundos até 875 segundos.

Como você está explorando a cópia de backups realizados em discos locais para outros locais, como o armazenamento blob do Azure, tenha em mente que a largura de banda usada por um eventual processo de cópia paralela está contabilizando a rede ou a cota de armazenamento do seu tipo de VM individual. Como resultado, você precisa equilibrar a duração da cópia em relação à rede e largura de banda de armazenamento que a carga de trabalho normal em execução na VM está exigindo. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar arquivos de backup do SAP HANA para o compartilhamento NFS

O Microsoft Azure oferece ações nfs nativas através [do Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Você pode criar diferentes volumes de dezenas de TBs em capacidade para armazenar e gerenciar backups. Você também pode fazer o instantâneo desses volumes com base na tecnologia da NetApp. O Azure NetApp Files (ANF) é oferecido em três níveis de serviço diferentes que dão a diferentes throughput de armazenamento. Para obter mais detalhes, leia os níveis de serviço do [artigo para arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Você pode criar e montar um volume NFS a partir de ANF conforme descrito no artigo [Quickstart: Configure arquivos do Azure NetApp e crie um volume NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal).

Além de usar volumes NFS nativos do Azure através da ANF, existem várias possibilidades de criar implantações próprias que fornecem ações NFS no Azure. Todos têm a desvantagem que você precisa para implantar e gerenciar essas soluções você mesmo. Algumas dessas possibilidades estão documentadas nestes artigos:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

As ações nfs criadas por meios descritos acima podem ser usadas para executar diretamente backups HANA contra ou para copiar backups que foram realizados contra discos locais para essas ações NFS.

> [!NOTE]
> SAP HANA suporta NFS v3 e NFS v4.x. Qualquer outro formato como SMB com sistema de arquivos CIFS não é suportado para gravar backups HANA contra. Veja também [a nota de suporte sap #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar arquivos de backup do SAP HANA para os Arquivos do Azure

É possível montar um compartilhamento de Arquivos do Azure dentro de uma VM do Linux do Azure. O artigo [Como usar o armazenamento de arquivos Azure com o Linux](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) fornece detalhes sobre como executar a configuração. Para obter limitações de arquivos Azure ou arquivos premium do Azure, leia o artigo [Azure Files scalability and performance targets](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets).

> [!NOTE]
> SMB com sistema de arquivos CIFS não é suportado pelo SAP HANA para escrever backups HANA contra. Veja também a [nota de suporte sap #1820529](https://launchpad.support.sap.com/#/notes/1820529). Como resultado, você só pode usar esta solução como destino final de um backup de banco de dados HANA que foi conduzido diretamente contra discos conectados locais
> 

Em um teste realizado contra o Azure Files, não o Azure Premium Files levou cerca de 929 segundos para copiar 19 arquivos de backup com um volume total de 230 GB. Esperamos que o tempo usando arquivos Premium do Azure seja muito melhor. No entanto, você precisa ter em mente que você precisa equilibrar os interesses de uma cópia rápida com os requisitos que sua carga de trabalho tem sobre a largura de banda da rede. Uma vez que cada tipo de VM do Azure impõe a cota de largura de banda da rede, você precisa ficar dentro do intervalo dessa cota com sua carga de trabalho mais a cópia dos arquivos de backup.

![Esta figura mostra que demorou aproximadamente 929 segundos para copiar os 19 arquivos de backup do SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Armazenar arquivos de backup SAP HANA em arquivos Azure pode ser uma opção interessante. Especialmente com a latência melhorada e o throughput de Arquivos Premium do Azure.

## <a name="next-steps"></a>Próximas etapas
* [Guia de backup do SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
