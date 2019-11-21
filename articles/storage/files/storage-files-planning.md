---
title: Planejando uma implantação de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227876"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planejando uma implantação de Arquivos do Azure

O [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB padrão no setor. Já que o Arquivos do Azure é totalmente gerenciado, implantá-lo em cenários de produção é muito mais fácil do que implantar e gerenciar um servidor de arquivos ou um dispositivo NAS. Este artigo aborda os tópicos a serem considerados ao implantar um compartilhamento de Arquivos do Azure para uso em produção dentro de sua organização.

## <a name="management-concepts"></a>Conceitos de gerenciamento

 O diagrama a seguir ilustra as construções do gerenciamento do Arquivos do Azure:

![Estrutura do arquivo](./media/storage-files-introduction/files-concepts.png)

* **Conta de Armazenamento**: todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter detalhes sobre a capacidade da conta de armazenamento.

* **Compartilhamento** : um compartilhamento do armazenamento de Arquivos é um compartilhamento de arquivos SMB no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento pai. An account can contain an unlimited number of shares and a share can store an unlimited number of files, up to the total capacity of the file share. The total capacity for premium and standard file shares is 100 TiB.

* **Diretório**: uma hierarquia opcional de diretórios.

* **Arquivo**: um arquivo no compartilhamento. Um arquivo pode ter até 1 TiB de tamanho.

* **Formato de URL**: para solicitações a um compartilhamento de Arquivos do Azure feitas com o protocolo REST de Arquivo, os arquivos são endereçáveis usando o seguinte formato de URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso a dados

O Arquivos do Azure oferece dois métodos de acesso a dados internos e práticos, que você pode usar separadamente ou combinados entre si para acessar seus dados:

1. **Acesso à nuvem direto**: qualquer compartilhamento de Arquivos do Azure pode ser montado por [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e/ou [Linux](storage-how-to-use-files-linux.md) com o protocolo SMB padrão do setor ou por meio da API REST de Arquivo. Com o SMB, leituras e gravações para arquivos no compartilhamento são feitas diretamente no compartilhamento de arquivos no Azure. Para montar uma VM no Azure, o cliente SMB no sistema operacional deve dar suporte ao menos ao SMB 2.1. Para montar localmente, por exemplo, na estação de trabalho do usuário, o cliente SMB com suporte pela estação de trabalho deverá, por sua vez, dar suporte ao menos ao SMB 3.0 (com criptografia). Além de SMB, novos aplicativos ou serviços podem acessar diretamente o compartilhamento de arquivos por meio de REST de Arquivo, que fornece uma interface de programação de aplicativo fácil e escalonável para desenvolvimento de software.
2. **Sincronização de Arquivos do Azure**: com a Sincronização de Arquivos do Azure, os compartilhamentos podem ser replicados no Windows Servers local ou no Azure. Seus usuários acessariam o compartilhamento de arquivos por meio do Windows Server, por exemplo, por meio de um compartilhamento NFS ou SMB. Isso é útil para cenários nos quais os dados serão acessados e modificados longe de um datacenter do Azure, como em um cenário de filial. Os dados podem ser replicados entre vários pontos de extremidade do Windows Server, por exemplo, entre várias filiais. Finalmente, dados podem ser divididos em camadas para arquivos do Azure, de modo que todos os dados ainda serão acessíveis por meio do servidor, mas ele não terá uma cópia completa dos dados. Em vez disso, os dados são recuperados diretamente quando abertos pelo usuário.

A tabela a seguir ilustra como os usuários e aplicativos podem acessar o compartilhamento de Arquivos do Azure:

| | Acesso direto à nuvem | Sincronização de arquivos do Azure |
|------------------------|------------|-----------------|
| Quais protocolos você precisa usar? | O Arquivos do Azure dá suporte a SMB 2.1, a SMB 3.0 e à API REST de arquivo. | Acessar o compartilhamento de arquivos do Azure por meio de qualquer protocolo com suporte no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde você está executando a carga de trabalho? | **No Azure**: o Arquivos do Azure oferece acesso direto aos seus dados. | **Localmente, com rede lenta**: clientes Windows, Linux e macOS podem montar um compartilhamento de arquivos do Windows local localmente como um cache rápido do seu compartilhamento de Arquivos do Azure. |
| De que nível de ACLs você precisa? | Nível de compartilhamento e de arquivo. | Nível de compartilhamento, de arquivo e de usuário. |

## <a name="data-security"></a>Segurança dos dados

O Arquivos do Azure tem várias opções integradas para garantir a segurança dos dados:

* Suporte para criptografia em ambos os protocolos over-the-wire: criptografia SMB 3.0 e REST de arquivo via HTTPS. Por padrão: 
    * Clients that support SMB 3.0 encryption send and receive data over an encrypted channel.
    * Clients that do not support SMB 3.0 with encryption can communicate intra-datacenter over SMB 2.1 or SMB 3.0 without encryption. Os clientes do SMB não têm permissão para se comunicar no interior do datacenter via SMB 2.1 ou SMB 3.0 sem criptografia.
    * Os clientes podem se comunicar por REST de arquivo via HTTP ou HTTPS.
* Criptografia em repouso ([criptografia do serviço de Armazenamento do Microsoft Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): a Criptografia do Serviço de Armazenamento (SSE) é habilitada para todas as contas de armazenamento. Os dados em repouso são criptografados com chaves totalmente gerenciadas. Criptografia em repouso não aumenta os custos de armazenamento nem reduz o desempenho. 
* Optional requirement of encrypted data in-transit: when selected, Azure Files rejects access to the data over unencrypted channels. Especificamente, são permitidas somente HTTPS e SMB 3.0 com conexões de criptografia.

    > [!Important]  
    > A exigência de transferência segura de dados fará com que clientes SMB mais antigos não sejam capazes de se comunicar com o SMB 3.0 com criptografia. Para obter mais informações, consulte [Montar no Windows](storage-how-to-use-files-windows.md), [Montar no Linux](storage-how-to-use-files-linux.md) e [Montar no macOS](storage-how-to-use-files-mac.md).

Para segurança máxima, recomendamos habilitar ambas a criptografia em repouso e a criptografia de dados em trânsito sempre que você estiver usando clientes modernos para acessar seus dados. Por exemplo, se você precisa montar um compartilhamento em uma VM Windows Server 2008 R2, que só dá suporte a SMB 2.1, você precisa permitir tráfego não criptografado para sua conta de armazenamento, já que o SMB 2.1 não dá suporte a criptografia.

Se você estiver usando a Sincronização de Arquivos do Azure para acessar o compartilhamento de Arquivos do Azure, usaremos sempre HTTPS e SMB 3.0 com criptografia para sincronizar seus dados aos Windows Servers, independentemente de você exigir ou não criptografia de dados em repouso.

## <a name="file-share-performance-tiers"></a>Níveis de desempenho do compartilhamento de arquivos

Azure Files offers two performance tiers: standard and premium.

### <a name="standard-file-shares"></a>Compartilhamentos de arquivos padrão

Standard file shares are backed by hard disk drives (HDDs). Standard file shares provide reliable performance for IO workloads that are less sensitive to performance variability such as general-purpose file shares and dev/test environments. Os compartilhamentos de arquivos padrão estão disponíveis apenas em um modelo de cobrança pago conforme o uso.

> [!IMPORTANT]
> If you want to use file shares larger than 5 TiB, see the [Onboard to larger file shares (standard tier)](#onboard-to-larger-file-shares-standard-tier) section for steps to onboard, as well as regional availability and restrictions.

### <a name="premium-file-shares"></a>Premium file shares

Premium file shares are backed by solid-state drives (SSDs). Premium file shares provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. This makes them suitable for a wide variety of workloads like databases, web site hosting, and development environments. Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Premium file shares use a deployment model separate from standard file shares.

Azure Backup is available for premium file shares and Azure Kubernetes Service supports premium file shares in version 1.13 and above.

If you'd like to learn how to create a premium file share, see our article on the subject: [How to create an Azure premium file storage account](storage-how-to-create-premium-fileshare.md).

Currently, you cannot directly convert between a standard file share and a premium file share. If you would like to switch to either tier, you must create a new file share in that tier and manually copy the data from your original share to the new share you created. You can do this using any of the Azure Files supported copy tools, such as Robocopy or AzCopy.

> [!IMPORTANT]
> Premium file shares are available with LRS in most regions that offer storage accounts and with ZRS in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the [products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) page for Azure. To find out what regions support ZRS, see [Support coverage and regional availability](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> To help us prioritize new regions and premium tier features, please fill out this [survey](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Compartilhamentos provisionados

Os compartilhamentos de arquivos Premium são provisionados com base em uma taxa de GiB/IOPS/transferência fixa. Para cada GiB provisionado, o compartilhamento emitirá um IOPS e uma taxa de transferência de 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB, com um IOPS e uma taxa de transferência mínimos.

Com base no melhor esforço, todos os compartilhamentos poderão acumular até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Os novos compartilhamentos começam com o crédito de intermitência completa com base na capacidade provisionada.

Shares must be provisioned in 1 GiB increments. Minimum size is 100 GiB, next size is 101 GiB and so on.

> [!TIP]
> Baseline IOPS = 1 * provisioned GiB. (Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Up to a max of 100,000 IOPS).
>
> egress rate = 60 MiB/s + 0.06 * provisioned GiB
>
> ingress rate = 40 MiB/s + 0.04 * provisioned GiB

Provisioned share size is specified by share quota. Share quota can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/Throughput scale changes will be effective within a few minutes after the size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

The following table illustrates a few examples of these formulae for the provisioned share sizes:

|Capacity (GiB) | IOPS de linha de base | Burst IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Up to 1,500   | 90   | 60   |
|1\.024       | 1\.024   | Up to 3,072   | 122   | 81   |
|5\.120       | 5\.120   | Up to 15,360  | 368   | 245   |
|10,240      | 10,240  | Up to 30,720  | 675 | 450   |
|33,792      | 33,792  | Up to 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | Up to 100,000 | 3\.132 | 2,088   |
|102,400     | 100 mil | Up to 100,000 | 6,204 | 4,136   |

> [!NOTE]
> File shares performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. To achieve maximum performance scale, spread the load across multiple VMs. Please refer [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

#### <a name="bursting"></a>Bursting

Premium file shares can burst their IOPS up to a factor of three. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee, file shares can burst *up to* the limit.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 100 baseline IOPS. If actual traffic on the share was 40 IOPS for a specific 1-second interval, then the 60 unused IOPS are credited to a burst bucket. These credits will then be used later when operations would exceed the baseline IOPs.

> [!TIP]
> Size of the burst bucket = Baseline IOPS * 2 * 3600.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst. Shares can continue to burst as long as credits are remaining, though shares smaller than 50 TiB will only stay at the burst limit for up to an hour. Shares larger than 50 TiB can technically exceed this one hour limit, up to two hours but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is bursting.
- Remaining constant, when there are either no credits or baseline IOPS are in use.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## <a name="file-share-redundancy"></a>Redundância de compartilhamento de arquivo

Azure Files standard shares supports four data redundancy options: locally redundant storage (LRS), zone redundant storage (ZRS), geo-redundant storage (GRS), and geo-zone-redundant storage (GZRS) (preview).

Azure Files premium shares support both LRS and ZRS, ZRS is currently available in a smaller subset of regions.

As seções a seguir descrevem as diferenças entre as opções de redundância diferentes:

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

> [!Warning]  
> Se estiver usando o compartilhamento de arquivos do Azure como um ponto de extremidade de nuvem em uma conta de armazenamento GRS, você não deve iniciar o failover da conta de armazenamento. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas. No caso de perda de uma região do Azure, a Microsoft disparará o failover da conta de armazenamento de modo que seja compatível com a Sincronização de Arquivos do Azure.

O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

If you opt for read-access geo-redundant storage (RA-GRS), you should know that Azure File does not support read-access geo-redundant storage (RA-GRS) in any region at this time. File shares in the RA-GRS storage account work like they would in GRS accounts and are charged GRS prices.

O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.

For a storage account with GRS enabled, all data is first replicated with locally redundant storage (LRS). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação com esse nível é fornecida por LRS; Para obter mais informações, consulte [Armazenamento com redundância local (LRS): redundância de dados de baixo custo para Armazenamento do Microsoft Azure](../common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* Geo-zone-redundant storage (GZRS) (preview) provides high availability together with maximum durability by replicating data synchronously across three Azure availability zones and then replicating data asynchronously to the secondary region. You can also enable read access to the secondary region. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year. For more information on GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](../common/storage-redundancy-gzrs.md).
* Zone-redundant storage (ZRS) provides highly availability with synchronous replication and may be a better choice for some scenarios than GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso entre a hora em que dados são gravados na região primária e quando são replicados na região secundária. Caso ocorra um desastre na região, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover na região secundária. Em caso de failover, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de recuperação de desastre](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboard to larger file shares (standard tier)

This section only applies to the standard file shares. All premium file shares are available with 100 TiB capacity.

### <a name="restrictions"></a>Restrições

- LRS/ZRS to GRS/GZRS account conversion will not be possible for any storage account with large file shares enabled.

### <a name="regional-availability"></a>Disponibilidade regional

Standard file shares are available in all regions up to 5 TiB. In certain regions, they are available with a 100 TiB limit, those regions are listed in the following table:

|Região |Supported redundancy |
|-------|---------|
|Austrália Oriental |LRS     |
|Sudeste da Austrália|LRS |
|Canadá Central  |LRS     |
|Leste do Canadá     |LRS     |
|Índia Central  |LRS     |
|Central US*   |LRS     |
|Ásia Oriental      |LRS     |
|East US*        |LRS     |
|East US 2*      |LRS     |
|França Central |LRS, ZRS|
|Sul da França   |LRS     |
|Europa Setentrional   |LRS     |
|Sul da Índia    |LRS     |
|Sudeste Asiático |LRS, ZRS|
|Centro-Oeste dos EUA|LRS     |
|West Europe*    |LRS, ZRS|
|West US*        |LRS     |
|Oeste dos EUA 2      |LRS, ZRS|

\* Supported for new accounts, not all existing accounts have completed the upgrade process. You can check if your existing storage accounts have completed the upgrade process by attempting to [Enable large file shares](storage-files-how-to-create-large-file-share.md).

To help us prioritize new regions and features, please fill out this [survey](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Enable and create larger file shares

To begin using larger file shares, see our article [How to enable and create large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Today, the maximum size for an Azure file share is 100 TiB. Devido a essa limitação atual, você deve considerar o crescimento esperado dos dados durante a implantação de um compartilhamento de Arquivos do Azure.

It is possible to sync multiple Azure file shares to a single Windows File Server with Azure File Sync. This allows you to ensure that older, large file shares that you may have on-premises can be brought into Azure File Sync. For more information, see [Planning for an Azure File Sync Deployment](storage-files-planning.md).

## <a name="data-transfer-method"></a>Método de transferência de dados

Há muitas opções fáceis para transferência de dados em massa de um arquivo de compartilhamento existente, tal como um compartilhamento de arquivos local, para o Arquivos do Azure. Alguns populares incluem (lista não exaustiva):

* **Sincronização de Arquivos do Azure**: como parte de uma primeira sincronização entre um compartilhamento de Arquivos do Azure (um "Ponto de Extremidade da Nuvem") e um namespace de diretório do Windows (um "Ponto de Extremidade de Servidor"), a sincronização de arquivos do Azure replicará todos os dados do compartilhamento de arquivos existente para o Arquivos do Azure.
* **[Importação/Exportação do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : o serviço de Importação/Exportação do Azure permite a você transferir com segurança grandes quantidades de dados para um compartilhamento de Arquivos do Azure por meio do envio de unidades de disco rígido para um data center do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : o Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. Robocopy pode ser usado para transferir dados para arquivos do Azure montando o compartilhamento de arquivos localmente e, em seguida, usando a localização montada como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : o AzCopy é um utilitário de linha de comando projetado para copiar dados de e para os Arquivos do Azure e o Armazenamento de Blobs do Azure, usando comandos simples com o desempenho ideal.

## <a name="next-steps"></a>Próximos passos
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
