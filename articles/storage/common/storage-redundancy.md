---
title: Redundância de dados
titleSuffix: Azure Storage
description: Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. As configurações de redundância incluem o LRS (armazenamento com redundância local), o ZRS (armazenamento com redundância de zona), o GRS (armazenamento com redundância geográfica), o armazenamento com redundância geográfica com acesso de leitura (RA-GRS), o armazenamento com redundância de zona geográfica (GZRS) e o armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 65d898112396755bb2518cade0ac94c21bc52685
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117709"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

O armazenamento do Azure sempre armazena várias cópias de seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, interrupções de rede ou energia e desastres naturais em massa. A redundância garante que sua conta de armazenamento atenda ao [SLA (contrato de nível de serviço) do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/) , mesmo diante de falhas.

Ao decidir qual opção de redundância é melhor para seu cenário, considere as compensações entre custos menores e maior disponibilidade e durabilidade. Os fatores que ajudam a determinar qual opção de redundância você deve escolher incluem:  

- Como os dados são replicados na região primária
- Se os dados são replicados para um segundo local que está geograficamente distante para a região primária, para proteger contra desastres regionais
- Se o aplicativo requer acesso de leitura aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

## <a name="redundancy-in-the-primary-region"></a>Redundância na região primária

Os dados em uma conta de armazenamento do Azure são sempre replicados três vezes na região primária. O armazenamento do Azure oferece duas opções de como os dados são replicados na região primária:

- O **LRS (armazenamento com redundância local)** copia seus dados de forma síncrona três vezes em um único local físico na região primária. LRS é a opção de replicação menos cara, mas não é recomendada para aplicativos que exigem alta disponibilidade.
- O **ZRS (armazenamento com redundância de zona)** copia seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária. Para aplicativos que exigem alta disponibilidade, a Microsoft recomenda usar o ZRS na região primária e também replicar para uma região secundária.

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

O LRS (armazenamento com redundância local) Replica seus dados três vezes em um único local físico na região primária. O LRS fornece pelo menos 99,999999999% (11 noves) de durabilidade de objetos em um determinado ano.

LRS é a opção de redundância de menor custo e oferece a menor durabilidade em comparação com outras opções. O LRS protege seus dados contra falhas de unidade e rack do servidor. No entanto, se um desastre, como incêndio ou inundação, ocorrer no data center, todas as réplicas de uma conta de armazenamento usando LRS poderão ser perdidas ou irrecuperáveis. Para atenuar esse risco, a Microsoft recomenda o uso de [armazenamento com redundância de zona](#zone-redundant-storage) (ZRS), [armazenamento com redundância geográfica](#geo-redundant-storage) (GRS) ou [armazenamento com redundância de zona geográfica](#geo-zone-redundant-storage) (GZRS).

Uma solicitação de gravação para uma conta de armazenamento que está usando o LRS ocorre de forma síncrona. A operação de gravação retorna com êxito somente depois que os dados são gravados em todas as três réplicas.

LRS é uma boa opção para os seguintes cenários:

- Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
- Se seu aplicativo estiver restrito a replicar dados somente em um país ou região devido a requisitos de governança de dados, você poderá optar por LRS. Em alguns casos, as regiões emparelhadas entre as quais os dados são replicados geograficamente podem estar em outro país ou região. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

O ZRS (armazenamento com redundância de zona) replica os dados do armazenamento do Azure de forma síncrona em três zonas de disponibilidade do Azure na região primária. Cada zona de disponibilidade é um local físico separado com energia, resfriamento e rede independentes. O ZRS oferece durabilidade para objetos de dados de armazenamento do Azure de pelo menos 99,9999999999% (12 noves) em um determinado ano.

Com o ZRS, seus dados ainda estarão acessíveis para operações de leitura e gravação, mesmo se uma zona ficar indisponível. Se uma zona ficar indisponível, o Azure assumirá as atualizações de rede, como o desapontador de DNS. Essas atualizações podem afetar seu aplicativo se você acessar os dados antes que as atualizações sejam concluídas. Ao criar aplicativos para ZRS, siga as práticas para tratamento de falhas transitórias, incluindo a implementação de políticas de repetição com retirada exponencial.

Uma solicitação de gravação para uma conta de armazenamento que está usando o ZRS ocorre de forma síncrona. A operação de gravação retorna com êxito somente depois que os dados são gravados em todas as réplicas entre as três zonas de disponibilidade.

A Microsoft recomenda usar o ZRS na região primária para cenários que exigem consistência, durabilidade e alta disponibilidade. O ZRS fornece desempenho excelente, baixa latência e resiliência para seus dados se eles ficarem temporariamente indisponíveis. No entanto, ZRS sozinho pode não proteger seus dados contra um desastre regional em que várias zonas são permanentemente afetadas. Para proteção contra desastres regionais, a Microsoft recomenda o uso de GZRS ( [armazenamento com redundância de zona geográfica](#geo-zone-redundant-storage) ), que usa o ZRS na região primária e também Replica geograficamente seus dados para uma região secundária.

A tabela a seguir mostra quais tipos de contas de armazenamento dão suporte a ZRS em quais regiões:

|    Tipo de conta de armazenamento    |    Regiões com suporte    |    Serviços com suporte    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Uso geral v2<sup>1</sup>    | Sudeste da Ásia<br /> Leste da Austrália<br /> Norte da Europa<br />  Europa Ocidental<br /> França Central<br /> Leste do Japão<br /> Norte da África do Sul<br /> Sul do Reino Unido<br /> EUA Central<br /> Leste dos EUA<br /> Leste dos EUA 2<br /> Oeste dos EUA 2    |    Blobs de bloco<br /> Blobs de páginas<sup>2</sup><br /> Compartilhamentos de arquivos (padrão)<br /> Tabelas<br /> Filas<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa Ocidental<br /> Leste dos EUA    |    Blobs de blocos somente    |
|    Armazenamento de    | Europa Ocidental<br /> Leste dos EUA    |    Somente arquivos do Azure    |

<sup>1</sup> atualmente, a camada de arquivo morto não tem suporte para contas ZRS.<br />
<sup>2</sup> as contas de armazenamento que contêm o Azure Managed disks para máquinas virtuais sempre usam lRS. Os discos não gerenciados do Azure também devem usar LRS. É possível criar uma conta de armazenamento para discos não gerenciados do Azure que usa GRS, mas não é recomendável devido a problemas potenciais com consistência em relação à replicação geográfica assíncrona. Os discos gerenciados ou não não gerenciados dão suporte a ZRS ou GZRS. Para obter mais informações sobre discos gerenciados, consulte [preços para Azure Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Para obter informações sobre quais regiões dão suporte a ZRS, consulte **suporte de serviços por região** em [o que são zonas de disponibilidade do Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundância em uma região secundária

Para aplicativos que exigem alta disponibilidade, você pode optar por copiar, além disso, os dados em sua conta de armazenamento para uma região secundária que está a centenas de quilômetros de distância da região primária. Se sua conta de armazenamento for copiada para uma região secundária, os dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para obter mais informações sobre regiões com suporte do Azure, consulte [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).

O armazenamento do Azure oferece duas opções para copiar seus dados para uma região secundária:

- O **armazenamento com redundância geográfica (GRS)** copia seus dados de forma síncrona três vezes em um único local físico na região primária usando lRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária.
- O **GZRS (armazenamento com redundância de zona geográfica)** copia seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária usando ZRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária.

A principal diferença entre GRS e GZRS é como os dados são replicados na região primária. No local secundário, os dados são sempre replicados três vezes de forma síncrona, usando LRS.

Com GRS ou GZRS, os dados no local secundário não estão disponíveis para acesso de leitura ou gravação, a menos que haja um failover para a região secundária. Para acesso de leitura para o local secundário, configure sua conta de armazenamento para usar o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de acesso de leitura (RA-GZRS). Para obter mais informações, consulte [acesso de leitura aos dados na região secundária](#read-access-to-data-in-the-secondary-region).

Se a região primária ficar indisponível, você poderá optar por fazer failover para a região secundária. Após a conclusão do failover, a região secundária se tornará a região primária e você poderá ler e gravar os dados novamente. Para obter mais informações sobre a recuperação de desastres e sobre como fazer failover para a região secundária, consulte [recuperação de desastre e failover da conta de armazenamento](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Como os dados são replicados para a região secundária de forma assíncrona, uma falha que afeta a região primária poderá resultar em perda de dados se a região primária não puder ser recuperada. O intervalo entre as gravações mais recentes na região primária e a última gravação na região secundária é conhecido como o RPO (objetivo de ponto de recuperação). O RPO indica o ponto no tempo em que os dados podem ser recuperados. O armazenamento do Azure normalmente tem um RPO de menos de 15 minutos, embora atualmente não haja SLA quanto ao tempo necessário para replicar dados para a região secundária.

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O armazenamento com redundância geográfica (GRS) copia seus dados de forma síncrona três vezes em um único local físico na região primária usando LRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico em uma região secundária que está a centenas de quilômetros de distância da região primária. O GRS oferece durabilidade para objetos de dados de armazenamento do Azure de pelo menos 99.99999999999999% (16 9 ' s) em um determinado ano.

Uma operação de gravação é confirmada primeiro no local principal e replicada usando LRS. Em seguida, a atualização é replicada assincronamente para a região secundária. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

### <a name="geo-zone-redundant-storage"></a>Armazenamento com redundância de zona geográfica

O GZRS (armazenamento com redundância de zona geográfica) combina a alta disponibilidade fornecida pela redundância entre zonas de disponibilidade com proteção contra interrupções regionais fornecidas pela replicação geográfica. Os dados em uma conta de armazenamento GZRS são copiados em três [zonas de disponibilidade do Azure](../../availability-zones/az-overview.md) na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais. A Microsoft recomenda o uso do GZRS para aplicativos que exigem consistência máxima, durabilidade e disponibilidade, excelente desempenho e resiliência para recuperação de desastres.

Com uma conta de armazenamento GZRS, você pode continuar lendo e gravando dados se uma zona de disponibilidade ficar indisponível ou não puder ser recuperada. Além disso, seus dados também são duráveis no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável. O GZRS foi projetado para fornecer pelo menos a durabilidade de objetos de 99.99999999999999% (16 9) em um determinado ano.

Somente as contas de armazenamento de uso geral v2 dão suporte a GZRS e RA-GZRS. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md). GZRS e RA-GZRS dão suporte a blobs de blocos, blobs de páginas (exceto discos VHD), arquivos, tabelas e filas.

Há suporte para GZRS e RA-GZRS nas seguintes regiões:

- Sudeste da Ásia
- Norte da Europa
- Europa Ocidental
- Leste do Japão
- Sul do Reino Unido
- EUA Central
- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA 2

Para obter informações sobre preços, consulte detalhes de preços para [BLOBs](https://azure.microsoft.com/pricing/details/storage/blobs), [arquivos](https://azure.microsoft.com/pricing/details/storage/files/), [filas](https://azure.microsoft.com/pricing/details/storage/queues/)e [tabelas](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Acesso de leitura aos dados na região secundária

O armazenamento com redundância geográfica (com GRS ou GZRS) Replica seus dados para outro local físico na região secundária para proteger contra interrupções regionais. No entanto, esses dados estarão disponíveis para serem lidos somente se o cliente ou a Microsoft iniciar um failover da região primária para a secundária. Quando você habilita o acesso de leitura para a região secundária, seus dados ficam disponíveis para serem lidos se a região primária ficar indisponível. Para acesso de leitura à região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de acesso de leitura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projetar seus aplicativos para acesso de leitura ao secundário

Se sua conta de armazenamento estiver configurada para acesso de leitura à região secundária, você poderá projetar seus aplicativos para alternar diretamente para a leitura de dados da região secundária se a região primária ficar indisponível por qualquer motivo. A região secundária está sempre disponível para acesso de leitura, de modo que você pode testar seu aplicativo para certificar-se de que ele será lido do secundário no caso de uma interrupção. Para obter mais informações sobre como projetar seus aplicativos para alta disponibilidade, consulte [usar redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md).

Quando o acesso de leitura ao secundário está habilitado, seus dados podem ser lidos do ponto de extremidade secundário, bem como do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário acrescenta o sufixo *– secundário* ao nome da conta. Por exemplo, se o ponto de extremidade primário para o armazenamento de BLOBs for `myaccount.blob.core.windows.net` , o ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net` . As chaves de acesso da conta para sua conta de armazenamento são as mesmas para os pontos de extremidade primários e secundários.

### <a name="check-the-last-sync-time-property"></a>Verificar a propriedade Horário da Última Sincronização

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária geralmente está atrás da região primária. Se ocorrer uma falha na região primária, é provável que todas as gravações no primário ainda não tenham sido replicadas para o secundário.

Para determinar quais operações de gravação foram replicadas para a região secundária, seu aplicativo pode verificar a propriedade **hora da última sincronização** para sua conta de armazenamento. Todas as operações de gravação gravadas na região primária antes da hora da última sincronização foram replicadas com êxito para a região secundária, o que significa que elas estão disponíveis para serem lidas a partir do secundário. Qualquer operação de gravação gravada na região primária após a hora da última sincronização pode ou não ter sido replicada para a região secundária, o que significa que elas podem não estar disponíveis para operações de leitura.

Você pode consultar o valor da última propriedade de **hora de sincronização** usando Azure PowerShell, CLI do Azure ou uma das bibliotecas de cliente de armazenamento do Azure. A propriedade **hora da última sincronização** é um valor de data/hora GMT. Para obter mais informações, consulte [verificar a propriedade hora da última sincronização de uma conta de armazenamento](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Resumo das opções de redundância

A tabela a seguir mostra como os dados duráveis e disponíveis estão em um determinado cenário, dependendo de qual tipo de redundância está em vigor para sua conta de armazenamento:

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Um nó dentro de um data center se torna indisponível                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Ocorre uma interrupção em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Acesso de leitura aos dados na região secundária se a região primária ficar indisponível | Não                              | Não                               | Sim (com RA-GRS)                                   | Sim (com RA-GZRS)                                 |
| Porcentagem de durabilidade dos objetos em um determinado ano<sup>1</sup>                                          | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Tipos de conta de armazenamento com suporte<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| SLA de disponibilidade para solicitações de leitura<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso fria) para GRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GRS | Pelo menos 99,9% (99% para a camada de acesso fria) para GZRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GZRS |
| SLA de disponibilidade para solicitações de gravação<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |

<sup>1</sup> para obter informações sobre as garantias de armazenamento do Azure quanto à durabilidade e disponibilidade, consulte o [SLA do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> para obter informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento](storage-account-overview.md).

Todos os dados de todos os tipos de contas de armazenamento são copiados de acordo com a opção de redundância para a conta de armazenamento. Os objetos, incluindo BLOBs de blocos, blobs de acréscimo, blobs de página, filas, tabelas e arquivos são copiados. Os dados em todas as camadas, incluindo a camada de arquivo, são copiados. Para obter mais informações sobre camadas de BLOB, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

Para obter informações sobre preços para cada opção de redundância, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> O Azure Premium Armazenamento em Disco atualmente dá suporte apenas ao LRS (armazenamento localmente redundante). As contas de armazenamento de blobs de blocos dão suporte a LRS (armazenamento com redundância local) e a ZRS (armazenamento com redundância de zona) em determinadas regiões.

## <a name="data-integrity"></a>Integridade de dados

O armazenamento do Azure verifica regularmente a integridade dos dados armazenados usando verificações de redundância cíclica (CRCs). Se a corrupção de dados for detectada, ela será reparada usando dados redundantes. O armazenamento do Azure também calcula somas de verificação em todo o tráfego de rede para detectar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="see-also"></a>Veja também

- [Verificar a propriedade hora da última sincronização de uma conta de armazenamento](last-sync-time-get.md)
- [Alterar a opção de redundância para uma conta de armazenamento](redundancy-migration.md)
- [Use a redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
- [Recuperação de desastres e failover de conta de armazenamento](storage-disaster-recovery-guidance.md)
