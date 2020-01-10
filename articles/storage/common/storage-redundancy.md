---
title: Redundância de dados no Armazenamento do Azure | Microsoft Docs
description: Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. As opções de redundância incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica), armazenamento com redundância geográfica com acesso de leitura (RA-GRS), armazenamento com redundância de zona geográfica (GZRS) e com acesso de leitura com redundância de zona geográfica armazenamento (RA-GZRS) (visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7517c4d9b3f9b58d9cf745f5001078837e1fbfea
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748167"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A redundância do Armazenamento do Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região ou entre regiões separadas geograficamente.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

O armazenamento do Azure verifica regularmente a integridade dos dados armazenados usando verificações de redundância cíclica (CRCs). Se a corrupção de dados for detectada, ela será reparada usando dados redundantes. O armazenamento do Azure também calcula somas de verificação em todo o tráfego de rede para detectar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="choosing-a-redundancy-option"></a>Escolher uma opção de redundância

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de redundância:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

A tabela a seguir fornece uma visão geral rápida do escopo de durabilidade e disponibilidade que cada estratégia de replicação fornecerá a você para um determinado tipo de evento (ou evento de impacto semelhante).

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (visualização)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilidade de nó dentro de um datacenter                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Uma interrupção em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Acesso de leitura aos dados (em uma região remota e replicada geograficamente) no caso de indisponibilidade em toda a região | Não                              | Não                               | Sim (com RA-GRS)                                   | Sim (com RA-GZRS)                                 |
| Projetado para fornecer \_\_ durabilidade de objetos em um determinado ano<sup>1</sup>                                          | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Tipos de conta de armazenamento com suporte<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| SLA de disponibilidade para solicitações de leitura<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso fria) para GRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GRS | Pelo menos 99,9% (99% para a camada de acesso fria) para GZRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GZRS |
| SLA de disponibilidade para solicitações de gravação<sup>1</sup>  | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |

<sup>1</sup> para obter informações sobre as garantias de armazenamento do Azure quanto à durabilidade e disponibilidade, consulte o [SLA do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).   

<sup>2</sup> para obter informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento](storage-account-overview.md).

Todos os dados de todos os tipos de contas de armazenamento são replicados, incluindo BLOBs de blocos, blobs de acréscimo, blobs de páginas, filas, tabelas e arquivos.

Para obter informações sobre cada opção de redundância de preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> O Azure Premium Armazenamento em Disco atualmente dá suporte apenas ao LRS (armazenamento localmente redundante). O armazenamento de blobs de blocos Premium do Azure dá suporte a LRS (armazenamento com redundância local) e ZRS (armazenamento com redundância de zona) em determinadas regiões.

## <a name="changing-replication-strategy"></a>Alterar estratégia de replicação

Você pode alterar a estratégia de replicação da sua conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ou uma das bibliotecas de cliente de [armazenamento do Azure](https://docs.microsoft.com/azure/index#pivot=sdkstools). Alterar o tipo de replicação da sua conta de armazenamento não resulta em tempo de inatividade.

> [!NOTE]
> No momento, você não pode usar as bibliotecas de cliente de armazenamento do portal do Azure ou do Azure para converter sua conta em ZRS, GZRS ou RA-GZRS. Para migrar sua conta para o ZRS, consulte [armazenamento com redundância de zona (ZRS) para criar aplicativos de armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md) para obter detalhes. Para migrar GZRS ou RA-GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](storage-redundancy-zrs.md) para obter detalhes.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Há algum custo para alterar a estratégia de replicação da minha conta?

Depende do caminho de conversão. Ordenando da menor para a mais cara, as ofertas de redundância de armazenamento do Azure LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS. Por exemplo, passar *de* LRS para qualquer outro tipo de replicação incorrerá em encargos adicionais, pois você está mudando para um nível de redundância mais sofisticado. Migrar *para* grs ou ra-grs incorrerá em um encargo de largura de banda de saída, pois seus dados (em sua região primária) estão sendo replicados para sua região secundária remota. Essa cobrança é um custo único na configuração inicial. Depois que os dados são copiados, não há nenhuma cobrança adicional de migração. Você é cobrado somente pela replicação de novas ou atualizações nos dados existentes. Para obter detalhes sobre encargos de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se você migrar sua conta de armazenamento de GRS para LRS, não haverá nenhum custo adicional, mas os dados replicados serão excluídos do local secundário.

Se você migrar sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta será cobrada como RA-GRS por um adicional 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Consulte também

- [Visão geral da conta de armazenamento](storage-account-overview.md)
- [Armazenamento redundante local (LRS): Redundância de dados de baixo custo para o Armazenamento do Azure](storage-redundancy-lrs.md)
- [ZRS (armazenamento com redundância de zona): aplicativos de Armazenamento do Microsoft Azure altamente disponíveis](storage-redundancy-zrs.md)
- [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)
- [Armazenamento com redundância de zona geográfica (GZRS) para alta disponibilidade e durabilidade máxima (versão prévia)](storage-redundancy-gzrs.md)
- [Escalabilidade e metas de desempenho para contas de armazenamento Standard](scalability-targets-standard-account.md)
- [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Opções de redundância Armazenamento do Microsoft Azure e armazenamento com redundância geográfica com acesso de leitura](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
