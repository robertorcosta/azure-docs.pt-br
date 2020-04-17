---
title: Redundância de dados
titleSuffix: Azure Storage
description: Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. As configurações de redundância incluem lRS (LRS), armazenamento redundante de zona (ZRS), armazenamento geo-redundante (GRS), armazenamento geo-redundante de acesso de leitura (RA-GRS), armazenamento redundante em geo-zona (GZRS) (visualização) e armazenamento geo-redundante de acesso de leitura (RA-GZRS) (visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 78f7c935e64276e7f4862dad966b99bff6bd246d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481933"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

O Azure Storage sempre armazena várias cópias de seus dados para que ele seja protegido contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, quedas de rede ou energia e desastres naturais maciços. A redundância garante que sua conta de armazenamento atenda ao [Contrato de Nível de Serviço (SLA) para armazenamento azure,](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas.

Ao decidir qual opção de redundância é melhor para o seu cenário, considere as trocas entre custos mais baixos e maior disponibilidade e durabilidade. Os fatores que ajudam a determinar qual opção de redundância você deve escolher incluem:  

- Como seus dados são replicados na região primária
- Se seus dados são replicados para um segundo local que é geograficamente distante da região primária, para proteger contra desastres regionais
- Se seu aplicativo requer acesso à leitura dos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

## <a name="redundancy-in-the-primary-region"></a>Redundância na região primária

Os dados em uma conta do Azure Storage são sempre replicados três vezes na região principal. O Azure Storage oferece duas opções de como seus dados são replicados na região principal:

- **O LRS (Locally redundante storage, armazenamento redundante localmente)** copia seus dados de forma sincronizada três vezes dentro de um único local físico na região primária. O LRS é a opção de replicação mais barata, mas não é recomendado para aplicações que requerem alta disponibilidade.
- **O ZRS (Zone-redundante storage, armazenamento redundante de zona)** copia seus dados de forma sincronizada em três zonas de disponibilidade do Azure na região principal. Para aplicativos que exigem alta disponibilidade, a Microsoft recomenda o uso de ZRS na região primária e também a replicação para uma região secundária.

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

O LRS (Locally Redundante Storage, armazenamento redundante localmente) replica seus dados três vezes em um único local físico na região primária. A LRS fornece pelo menos 99,999999999% (11 noves) de durabilidade de objetos ao longo de um determinado ano.

O LRS é a opção de redundância de menor custo e oferece a menor durabilidade em comparação com outras opções. O LRS protege seus dados contra falhas no rack do servidor e na unidade. No entanto, se ocorrer um desastre como incêndio ou inundação dentro do data center, todas as réplicas de uma conta de armazenamento usando LRS podem ser perdidas ou irrecuperáveis. Para mitigar esse risco, a Microsoft recomenda o uso [do ZRS (Zone-Redundante Storage,](#zone-redundant-storage) armazenamento redundante de zona), [do armazenamento geo-redundante](#geo-redundant-storage) (GRS) ou [do armazenamento redundante em geo-zona (visualização)](#geo-zone-redundant-storage-preview) (GZRS).

Uma solicitação de gravação para uma conta de armazenamento que está usando LRS acontece de forma sincronizada. A operação de gravação retorna com sucesso somente após a gravação dos dados nas três réplicas.

LRS é uma boa escolha para os seguintes cenários:

- Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
- Se seu aplicativo estiver restrito a replicar dados apenas dentro de um país ou região devido a requisitos de governança de dados, você pode optar por LRS. Em alguns casos, as regiões emparelhadas em que os dados são geo-replicados podem estar em outro país ou região. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

O ZRS (Zone-redundante storage, armazenamento redundante por zona) replica seus dados de armazenamento do Azure de forma sincronizada em três zonas de disponibilidade do Azure na região principal. Cada zona de disponibilidade é um local físico separado com energia independente, resfriamento e rede. O ZRS oferece durabilidade para objetos de dados do Azure Storage de pelo menos 99,999999999999% (12 9's) durante um determinado ano.

Com o ZRS, seus dados ainda estão acessíveis para operações de leitura e gravação, mesmo que uma região fique indisponível. Se uma região ficar indisponível, o Azure realizará atualizações de rede, como o DNS reapontando. Essas atualizações podem afetar seu aplicativo se você acessar dados antes que as atualizações tenham sido concluídas. Ao projetar aplicativos para ZRS, siga práticas para o manuseio de falhas transitórias, incluindo a implementação de políticas de repetição com back-off exponencial.

Uma solicitação de gravação para uma conta de armazenamento que está usando zrs acontece de forma sincronizada. A operação de gravação retorna com sucesso somente após a gravação dos dados em todas as réplicas nas três regiões de disponibilidade.

A Microsoft recomenda o uso do ZRS na região primária para cenários que requerem consistência, durabilidade e alta disponibilidade. O ZRS oferece excelente desempenho, baixa latência e resiliência para seus dados se ele ficar temporariamente indisponível. No entanto, o ZRS por si só pode não proteger seus dados contra um desastre regional onde várias zonas são afetadas permanentemente. Para proteção contra desastres regionais, a Microsoft recomenda o uso [do armazenamento redundante em geo-zona](#geo-zone-redundant-storage-preview) (GZRS), que usa ZRS na região primária e também replica geo-replica seus dados para uma região secundária.

A tabela a seguir mostra quais tipos de contas de armazenamento suportam ZRS em quais regiões:

|    Tipo de conta de armazenamento    |    Regiões com suporte    |    Serviços com suporte    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Propósito geral v2<sup>1</sup>    | Sudeste da Ásia<br /> Leste da Austrália<br /> Norte da Europa<br />  Europa Ocidental<br /> França Central<br /> Leste do Japão<br /> Norte da África do Sul<br /> Sul do Reino Unido<br /> EUA Central<br /> Leste dos EUA<br /> Leste dos EUA 2<br /> Oeste dos EUA 2    |    Blobs de bloco<br /> Bolhas de página<sup>2</sup><br /> Ações de arquivo (padrão)<br /> Tabelas<br /> Filas<br /> |
|    BlockBlobArmazenamento<sup>1</sup>    | Europa Ocidental<br /> Leste dos EUA    |    Blobs de blocos somente    |
|    Armazenamento de arquivos    | Europa Ocidental<br /> Leste dos EUA    |    Somente arquivos azure    |

<sup>1</sup> O nível de arquivo não é suportado atualmente para contas ZRS.<br />
<sup>2</sup> Contas de armazenamento que contêm discos gerenciados do Azure para máquinas virtuais sempre usam LRS. Os discos não gerenciados do Azure também devem usar LRS. É possível criar uma conta de armazenamento para discos não gerenciados do Azure que usa GRS, mas não é recomendado devido a problemas potenciais com consistência sobre a replicação geosíncrona assíncrona. Nem discos gerenciados nem não gerenciados suportam ZRS ou GZRS. Para obter mais informações sobre discos gerenciados, consulte [Preços para discos gerenciados do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Para obter informações sobre quais regiões suportam o ZRS, consulte **Serviços de suporte por região** em Quais são as Zonas de Disponibilidade do [Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundância em uma região secundária

Para aplicativos que exigem alta disponibilidade, você pode optar por copiar adicionalmente os dados em sua conta de armazenamento para uma região secundária que está a centenas de quilômetros de distância da região primária. Se sua conta de armazenamento for copiada para uma região secundária, então seus dados serão duráveis mesmo no caso de uma paralisação regional completa ou um desastre no qual a região primária não é recuperável.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para obter mais informações sobre as regiões apoiadas pelo Azure, consulte [as regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).

O Azure Storage oferece duas opções para copiar seus dados para uma região secundária:

- **O GRS (Geo-redundante Storage, armazenamento geo-redundante)** copia seus dados sincronizadamente três vezes dentro de um único local físico na região primária usando LRS. Em seguida, ele copia seus dados assíncronamente para um único local físico na região secundária.
- **O GZRS (Preview)** copia seus dados de forma sincronizada em três zonas de disponibilidade do Azure na região principal usando ZRS. Em seguida, ele copia seus dados assíncronamente para um único local físico na região secundária.

A principal diferença entre GRS e GZRS é como os dados são replicados na região primária. Dentro do local secundário, os dados são sempre replicados sincronizadamente três vezes usando LRS.

Com GRS ou GZRS, os dados no local secundário não estão disponíveis para acesso à leitura ou gravação, a menos que haja um failover para a região secundária. Para ler acesso ao local secundário, configure sua conta de armazenamento para usar o armazenamento geo-redundante de acesso de leitura (RA-GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GZRS). Para obter mais informações, consulte [Leia acesso aos dados na região secundária](#read-access-to-data-in-the-secondary-region).

Se a região primária ficar indisponível, você pode optar por falhar na região secundária (visualização). Depois que o failover for concluído, a região secundária se torna a região primária, e você pode ler e gravar novamente dados. Para obter mais informações sobre recuperação de desastres e para saber como falhar na região secundária, consulte [Recuperação de desastres e failover de conta (visualização)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Como os dados são replicados na região secundária de forma assíncrona, uma falha que afeta a região primária pode resultar em perda de dados se a região primária não puder ser recuperada. O intervalo entre as gravações mais recentes para a região primária e a última gravação para a região secundária é conhecido como rpo (ponto de recuperação). O RPO indica o ponto no tempo em que os dados podem ser recuperados. O Azure Storage normalmente tem um RPO de menos de 15 minutos, embora não haja atualmente sla sobre quanto tempo leva para replicar dados para a região secundária.

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O GRS (Geo-redundante Storage, armazenamento geo-redundante) copia seus dados sincronizadamente três vezes dentro de um único local físico na região primária usando LRS. Em seguida, ele copia seus dados assíncronamente para um único local físico em uma região secundária que está a centenas de quilômetros de distância da região primária. O GRS oferece durabilidade para objetos de dados do Azure Storage de pelo menos 99,99999999999999999% (16 9's) durante um determinado ano.

Uma operação de gravação é primeiramente comprometida com o local principal e replicada usando LRS. A atualização é então replicada assíncronamente para a região secundária. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Armazenamento redundante em geo-zona (visualização)

O GZRS (Visualização) combina a alta disponibilidade fornecida pela redundância em zonas de disponibilidade com proteção contra paralisações regionais fornecidas pela georeplicação. Os dados em uma conta de armazenamento GZRS são copiados em três zonas de disponibilidade do [Azure](../../availability-zones/az-overview.md) na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais. A Microsoft recomenda o uso do GZRS para aplicativos que requerem consistência máxima, durabilidade e disponibilidade, excelente desempenho e resiliência para recuperação de desastres.

Com uma conta de armazenamento GZRS, você pode continuar a ler e gravar dados se uma zona de disponibilidade ficar indisponível ou for irrecuperável. Além disso, seus dados também são duráveis no caso de uma paralisação regional completa ou um desastre no qual a região primária não é recuperável. O GZRS foi projetado para fornecer pelo menos 99,999999999999999999% (16 9's) de durabilidade de objetos ao longo de um determinado ano.

Apenas contas de armazenamento v2 de uso geral suportam GZRS e RA-GZRS. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md). GZRS e RA-GZRS suportam blobs de bloco, blobs de página (exceto para discos VHD), arquivos, tabelas e filas.

GZRS e RA-GZRS estão atualmente disponíveis para visualização nas seguintes regiões:

- Sudeste da Ásia
- Norte da Europa
- Europa Ocidental
- Leste do Japão
- Sul do Reino Unido
- Leste dos EUA
- Leste dos EUA 2
- EUA Central
- Oeste dos EUA 2

A Microsoft continua a habilitar o GZRS e o RA-GZRS em regiões adicionais do Azure. Verifique regularmente a página [Atualizações de Serviços do Azure](https://azure.microsoft.com/updates/) para obter informações sobre regiões suportadas.

Para obter informações sobre preços de visualização de visualização, consulte os preços de visualização do GZRS para [Blobs,](https://azure.microsoft.com/pricing/details/storage/blobs) [Arquivos,](https://azure.microsoft.com/pricing/details/storage/files/)Filas e [Tabelas.](https://azure.microsoft.com/pricing/details/storage/queues/) [Tables](https://azure.microsoft.com/pricing/details/storage/tables/)

> [!IMPORTANT]
> A Microsoft recomenda o uso de recursos de visualização para cargas de trabalho de produção.

## <a name="read-access-to-data-in-the-secondary-region"></a>Leia o acesso aos dados na região secundária

O armazenamento geo-redundante (com GRS ou GZRS) replica seus dados em outro local físico na região secundária para proteger contra paralisações regionais. No entanto, esses dados estão disponíveis para serem lidos apenas se o cliente ou a Microsoft iniciar um failover da região primária para a secundária. Quando você habilita o acesso à leitura à região secundária, seus dados estarão disponíveis para serem lidos se a região primária ficar indisponível. Para acesso à leitura da região secundária, habilite o armazenamento geo-redundante de acesso de leitura (RA-GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projete seus aplicativos para ler acesso ao secundário

Se sua conta de armazenamento estiver configurada para acesso à leitura da região secundária, então você poderá projetar seus aplicativos para mudar perfeitamente para a leitura de dados da região secundária se a região primária ficar indisponível por qualquer motivo. A região secundária está sempre disponível para acesso à leitura, para que você possa testar seu aplicativo para ter certeza de que ele será lido a partir do secundário em caso de paralisação. Para obter mais informações sobre como projetar seus aplicativos para alta disponibilidade, consulte [Projetando aplicativos altamente disponíveis usando armazenamento geo-redundante de acesso de leitura](storage-designing-ha-apps-with-ragrs.md).

Quando o acesso à leitura do secundário é ativado, seus dados podem ser lidos a partir do ponto final secundário, bem como do ponto final principal da sua conta de armazenamento. O ponto final secundário anexa o sufixo *– secundário* ao nome da conta. Por exemplo, se o ponto final `myaccount.blob.core.windows.net`principal para o armazenamento `myaccount-secondary.blob.core.windows.net`Blob for, então o ponto final secundário é . As chaves de acesso da conta para sua conta de armazenamento são as mesmas para os pontos finais primários e secundários.

### <a name="check-the-last-sync-time-property"></a>Verificar a propriedade Horário da Última Sincronização

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária está frequentemente atrás da região primária. Se uma falha acontecer na região primária, é provável que todas as gravações para o primário ainda não tenham sido replicadas para o secundário.

Para determinar quais operações de gravação foram replicadas na região secundária, seu aplicativo pode verificar a propriedade **Last Sync Time** para sua conta de armazenamento. Todas as operações de gravação escritas na região primária antes do último tempo de sincronização foram replicadas com sucesso na região secundária, o que significa que estão disponíveis para serem lidas a partir do secundário. Quaisquer operações de gravação escritas na região primária após o último tempo de sincronização podem ou não ter sido replicadas para a região secundária, o que significa que elas podem não estar disponíveis para operações de leitura.

Você pode consultar o valor da **propriedade Last Sync Time** usando o Azure PowerShell, o Azure CLI ou uma das bibliotecas de clientes do Azure Storage. A última propriedade **Sync Time** é um valor de data/hora GMT. Para obter mais informações, [consulte Verifique a última propriedade Sync Time para obter uma conta de armazenamento](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Resumo das opções de redundância

A tabela a seguir mostra o quão duráveis e disponíveis seus dados estão em um determinado cenário, dependendo de qual tipo de redundância está em vigor para sua conta de armazenamento:

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (visualização)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Um nó dentro de um data center fica indisponível                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Ocorre uma paralisação em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Leia o acesso aos dados na região secundária se a região primária ficar indisponível | Não                              | Não                               | Sim (com RA-GRS)                                   | Sim (com RA-GZRS)                                 |
| Porcentagem de durabilidade dos objetos ao longo de um determinado ano<sup>1</sup>                                          | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Tipos de conta de armazenamento suportados<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| SLA de disponibilidade para solicitações de leitura<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para nível de acesso legal) para GRS<br /><br />Pelo menos 99,99% (99,9% para nível de acesso legal) para RA-GRS | Pelo menos 99,9% (99% para nível de acesso legal) para GZRS<br /><br />Pelo menos 99,99% (99,9% para nível de acesso legal) para RA-GZRS |
| SLA de disponibilidade para solicitações de gravação<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |

<sup>1</sup> Para obter informações sobre as garantias de armazenamento do Azure para durabilidade e disponibilidade, consulte o [SLA de armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Para obter informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento](storage-account-overview.md).

Todos os dados para todos os tipos de contas de armazenamento e [todos os níveis (incluindo arquivamento)](../blobs/storage-blob-storage-tiers.md) são copiados de acordo com a opção de redundância para a conta de armazenamento. Objetos que incluem blobs de bloco, bolhas de apêndice, bolhas de página, filas, tabelas e arquivos são copiados.

Para obter informações sobre preços para cada opção de redundância, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Atualmente, o Azure Premium Disk Storage suporta apenas o LRS (Locally Redundante Storage). As contas de armazenamento blob de bloco suportam o armazenamento localmente redundante (LRS) e o ZRS (Zone Redundante Storage, armazenamento redundante de zona) em determinadas regiões.

## <a name="data-integrity"></a>Integridade de dados

O Azure Storage verifica regularmente a integridade dos dados armazenados usando verificações cíclicas de redundância (CRCs). Se a corrupção de dados for detectada, ela será reparada usando dados redundantes. O Azure Storage também calcula as contas em todo o tráfego de rede para detectar a corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="see-also"></a>Confira também

- [Verifique a última propriedade Sync Time para obter uma conta de armazenamento](last-sync-time-get.md)
- [Alterar a opção de redundância para uma conta de armazenamento](redundancy-migration.md)
- [Projetando aplicativos altamente disponíveis usando o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Recuperação de desastres e failover de conta (visualização)](storage-disaster-recovery-guidance.md)
