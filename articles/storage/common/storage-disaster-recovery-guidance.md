---
title: Failover da conta de recuperação e armazenamento de desastres (visualização)
titleSuffix: Azure Storage
description: O armazenamento do Azure dá suporte ao failover de conta (versão prévia) para contas de armazenamento com redundância geográfica. Com o failover de conta, é possível iniciar o processo de failover da conta de armazenamento, se o ponto de extremidade primário ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365334"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Recuperação de desastres e failover de conta (visualização)

A Microsoft se empenha em garantir que os serviços do Azure estejam sempre disponíveis. No entanto, podem ocorrer interrupções imprevistas do serviço. Se o seu aplicativo exigir resiliência, a Microsoft recomenda o uso de armazenamento geo-redundante, para que seus dados sejam copiados para uma segunda região. Além disso, os clientes devem dispor de um plano de recuperação de desastre para lidar com uma interrupção regional do serviço. Uma parte importante de um plano de recuperação de desastre é a preparação de um failover para o ponto de extremidade secundário caso o ponto de extremidade primário fique indisponível.

O armazenamento do Azure dá suporte ao failover de conta (versão prévia) para contas de armazenamento com redundância geográfica. Com o failover de conta, é possível iniciar o processo de failover da conta de armazenamento, se o ponto de extremidade primário ficar indisponível. O failover atualiza o ponto de extremidade secundário para torná-lo um ponto de extremidade primário de sua conta de armazenamento. Quando o failover estiver concluído, os clientes poderão começar a gravar no novo ponto de extremidade primário.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Este artigo descreve os conceitos e o processo envolvidos em um failover de conta e mostra como preparar sua conta de armazenamento para recuperação com o mínimo de impacto para o cliente. Para saber como iniciar um failover de conta no portal do Azure ou no PowerShell, confira o artigo sobre como [iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Escolhendo a opção de redundância correta

O Azure Storage mantém várias cópias da sua conta de armazenamento para garantir durabilidade e alta disponibilidade. A opção de redundância que será escolhida para sua conta dependerá do grau de resiliência que você precisa. Para proteção contra interrupções regionais, escolha o armazenamento com redundância geográfica, com ou sem a opção de acesso de leitura proveniente da região secundária:  

**O armazenamento geo-redundante (GRS) ou o gzrs (preview) (visualização) copiaseus** dados de forma assíncrona em duas regiões geográficas que estão a pelo menos centenas de quilômetros de distância. Se a região primária sofrer uma interrupção, a região secundária funcionará como uma fonte redundante para seus dados. É possível iniciar um failover para transformar o ponto de extremidade secundário no ponto de extremidade primário.

**O armazenamento geo-redundante de acesso de leitura (RA-GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GZRS) (preview)** fornecem armazenamento geo-redundante com o benefício adicional do acesso à leitura ao ponto final secundário. Se ocorrer uma interrupção no ponto de extremidade primário, os aplicativos configurados para o RA-GRS e projetados para alta disponibilidade poderão continuar a ler os dados a partir do ponto de extremidade secundário. A Microsoft recomenda o RA-GRS para garantir a máxima resiliência de seus aplicativos.

Para obter mais informações sobre redundância no Azure Storage, consulte [a redundância do Azure Storage](storage-redundancy.md).

> [!WARNING]
> O Armazenamento com redundância geográfica envolve risco de perda de dados. Os dados são copiados para a região secundária de forma assíncrona, o que significa que há um atraso entre quando os dados gravados para a região primária são gravados para a região secundária. Em caso de paralisação, as operações de gravação para o ponto final primário que ainda não foram copiadas para o ponto final secundário serão perdidas.

## <a name="design-for-high-availability"></a>Projetando para a alta disponibilidade

É importante projetar seu aplicativo para ter uma alta disponibilidade desde o início. Consulte estes recursos do Azure para obter orientações sobre como projetar seu aplicativo e sobre o planejamento para a recuperação de desastre:

- [Projetando aplicativos resilientes para o Azure](/azure/architecture/checklist/resiliency-per-service): Uma visão geral dos conceitos-chave para arquitetar aplicações altamente disponíveis no Azure.
- [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service): Uma lista de verificação para verificar se seu aplicativo implementa as melhores práticas de design para alta disponibilidade.
- [Projetando aplicativos altamente disponíveis usando RA-GRS](storage-designing-ha-apps-with-ragrs.md): Orientação de design para aplicativos de construção para tirar proveito do RA-GRS.
- [Tutorial: Crie um aplicativo altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md): Um tutorial que mostra como construir um aplicativo altamente disponível que alterna automaticamente entre pontos finais à medida que falhas e recuperações são simuladas. 

Além disso, tenha em mente essas práticas recomendadas a fim de manter a alta disponibilidade de seus dados de armazenamento no Azure:

- **Discos:** Use [o Backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos VM usados por suas máquinas virtuais Do Azure. Considere também usar o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) para proteger suas VMs em caso de desastre regional.
- **Bolhas de bloco:** Ative a [exclusão suave](../blobs/storage-blob-soft-delete.md) para proteger contra exclusões e substituições em nível de objeto ou copiar blobs de bloco para outra conta de armazenamento em uma região diferente usando [AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)ou a [biblioteca Azure Data Movement](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
- **Arquivos:** Use [o AzCopy](storage-use-azcopy.md) ou [o Azure PowerShell](storage-powershell-guide-full.md) para copiar seus arquivos para outra conta de armazenamento em uma região diferente.
- **Tabelas:** use o [AzCopy](storage-use-azcopy.md) para exportar os dados de tabela para outra conta de armazenamento em uma região diferente.

## <a name="track-outages"></a>Acompanhar interrupções

Os clientes podem assinar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para acompanhar a integridade e o status do armazenamento do Azure e de outros serviços do Azure.

A Microsoft também recomenda que você projete seu aplicativo de forma a se preparar para a possibilidade de falhas de gravação. Seu aplicativo deve expor falhas de gravação de forma que o alerte para a possibilidade de uma interrupção na região primária.

## <a name="understand-the-account-failover-process"></a>Entendendo o processo de failover de conta

O failover de conta gerenciada pelo cliente (versão prévia) permite fazer failover da conta de armazenamento inteira para a região secundária se a primária ficar indisponível por algum motivo. Quando você força um failover para a região secundária, os clientes podem começar a gravar os dados no ponto de extremidade secundário depois que o failover estiver concluído. O failover normalmente leva cerca de uma hora.

### <a name="how-an-account-failover-works"></a>Como o failover de conta funciona

Em circunstâncias normais, um cliente grava dados em uma conta do Azure Storage na região principal, e esses dados são copiados assíncronamente para a região secundária. A imagem a seguir mostra o cenário quando a região primária está disponível:

![Os clientes gravam os dados na conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o ponto de extremidade primário ficar indisponível por algum motivo, o cliente não poderá mais gravar os dados na conta de armazenamento. A imagem a seguir mostra o cenário em que o ponto de extremidade primário ficou indisponível, mas nenhuma recuperação aconteceu ainda:

![O ponto de extremidade primário está indisponível, portanto, os clientes não podem gravar dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia o failover da conta no ponto de extremidade secundário. O processo de failover atualiza a entrada DNS fornecida pelo armazenamento do Azure para que o ponto de extremidade secundário se torne o novo ponto de extremidade primário de sua conta de armazenamento, conforme mostrado na imagem a seguir:

![O cliente inicia o failover da conta no ponto de extremidade secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

O acesso à gravação é restaurado nas contas de GRS e de RA-GRS depois que a entrada DNS for atualizada e as solicitações estejam sendo direcionadas para o novo ponto de extremidade primário. Os pontos de extremidade de armazenamento existentes para blobs, tabelas, filas e arquivos permanecem os mesmos após o failover.

> [!IMPORTANT]
> Depois que o failover estiver concluído, a conta de armazenamento é configurada para ser localmente redundante no novo ponto de extremidade primário. Para continuar a replicação no novo ponto de extremidade secundário, configure a conta para usar o armazenamento com redundância geográfica novamente (RA-GRS ou GRS).
>
> Tenha em mente que a conversão de uma conta de LRS para RA-GRS ou GRS tem um custo. Esse custo se aplica à atualização da conta de armazenamento na nova região primária para usar o RA-GRS ou o GRS após um failover.  

### <a name="anticipate-data-loss"></a>Prevendo a perda de dados

> [!CAUTION]
> Um failover de conta geralmente envolve alguma perda de dados. É importante entender as implicações de iniciar um failover de conta.  

Como os dados são escritos assíncronamente da região primária para a região secundária, há sempre um atraso antes que uma gravação para a região primária seja copiada para a região secundária. Se a região primária ficar indisponível, as gravações mais recentes podem ainda não ter sido copiadas para a região secundária.

Ao forçar um failover, todos os dados na região primária são perdidos uma vez que a região secundária se torna a nova região primária e a conta de armazenamento é configurada para ser localmente redundante. Todos os dados já copiados para o secundário são mantidos quando o failover acontece. No entanto, todos os dados escritos para o primário que também não foram copiados para o secundário são perdidos permanentemente.

A propriedade **Hora da última sincronização** indica o momento mais recente em que os dados da região primária foram certamente gravados na região secundária. Todos os dados gravados antes da hora da última sincronização estarão disponíveis na região secundária, enquanto os dados gravados após esse momento podem não terem sido gravados na região secundária e terem sido perdidos. Use essa propriedade caso ocorra uma interrupção para estimar a quantidade de perda de dados que poderá incorrer ao iniciar um failover de conta.

Como prática recomendada, projete seu aplicativo de modo que seja possível usar a hora da última sincronização para avaliar a estimativa de perda de dados. Por exemplo, se você estiver registrando todas as operações de gravação, será possível comparar o momento das últimas operações de gravação com a hora da última sincronização a fim de determinar quais gravações não foram sincronizadas na região secundária.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tendo cuidados ao realizar o fail back para a região primária original

Após realizar o failover da região primária para a região secundária, a conta de armazenamento estará configurada para ser localmente redundante na nova região primária. É possível configurar a conta para a redundância geográfica novamente, atualizando-a para usar o GRS ou o RA-GRS. Quando a conta é configurada para geo-redundância novamente após um failover, a nova região primária imediatamente começa a copiar dados para a nova região secundária, que era a principal antes do failover original. No entanto, pode levar algum tempo até que os dados existentes no primário sejam totalmente copiados para o novo secundário.

Depois que a conta de armazenamento for reconfigurada para a redundância geográfica, será possível iniciar outro failover da nova região primária de volta para a nova região secundária. Nesse caso, a região primária original antes do failover se tornará novamente a região primária e estará configurada para ser localmente redundante. Todos os dados na região primária após o failover (a região secundária original) serão perdidos. Se a maioria dos dados na conta de armazenamento não tiver sido copiada para o novo secundário antes de você falhar, você pode sofrer uma grande perda de dados.

Para evitar uma grande perda de dados, verifique o valor da propriedade **Hora da última sincronização** antes de realizar o fail back. Compare a hora da última sincronização com os momentos das últimas vezes que os dados foram gravados na nova região primária a fim de avaliar a perda de dados esperada. 

## <a name="initiate-an-account-failover"></a>Iniciar um failover da conta

É possível iniciar um failover de conta do portal do Azure, no PowerShell, na CLI do Azure ou na API de provedor de recursos do Armazenamento do Azure. Para obter mais informações sobre como iniciar um failover, confira o artigo sobre como [iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Sobre a visualização

O failover da conta está disponível na pré-visualização para todos os clientes que usam GRS ou RA-GRS com implantações do Azure Resource Manager. Há suporte para uso geral v1, uso geral v2 e os tipos de conta de armazenamento de Blob. O failover da conta está disponível atualmente em todas as regiões públicas. O failover da conta não está disponível em nuvens soberanas/nacionais no momento.

A versão prévia é destinada apenas para uso não produtivo. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

### <a name="additional-considerations"></a>Considerações adicionais

Examine as considerações adicionais descritas nesta seção para entender como seus aplicativos e serviços podem ser afetados quando você força um failover durante o período da versão prévia.

#### <a name="storage-account-containing-archived-blobs"></a>Conta de armazenamento contendo blobs arquivados

Contas de armazenamento que contêm blobs arquivados suportam failover de conta. Uma vez que o failover esteja concluído, para converter a conta de volta para GRS ou RA-GRS, todas as bolhas arquivadas precisam ser rehidratadas para um nível on-line primeiro.

#### <a name="storage-resource-provider"></a>Provedor de recursos de armazenamento

Depois que um failover for concluído, os clientes poderão ler e gravar novamente os dados do Azure Storage na nova região principal. No entanto, o provedor de recursos do Azure Storage não falha, portanto, as operações de gerenciamento de recursos ainda devem ocorrer na região principal. Se a região primária não estiver disponível, você não poderá executar operações de gerenciamento na conta de armazenamento.

Como o provedor de recursos do Azure Storage não falha, a propriedade [Local](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) retornará o local principal original após o failover ser concluído.

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

As máquinas virtuais (VMs) do Azure não realizarão o failover como parte de um failover de conta. Se a região primária ficar indisponível, e você fizer o failover para a região secundária, será preciso recriar todas as VMs após o failover. Além disso, há uma possível perda de dados associada ao failover da conta. A Microsoft recomenda a seguinte [orientação de alta disponibilidade](../../virtual-machines/windows/manage-availability.md) e [recuperação de desastres](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) específica para máquinas virtuais no Azure.

#### <a name="azure-unmanaged-disks"></a>Discos não gerenciados do Azure

Como prática recomendada, a Microsoft aconselha converter os discos não gerenciados em discos gerenciados. No entanto, se você precisar fazer o failover em uma conta que contenha discos não gerenciados anexados a VMs do Azure, será necessário desligar a VM antes de iniciar o failover.

Os discos não gerenciados são armazenadas como blobs de páginas no Armazenamento do Azure. Quando uma VM está em execução no Azure, todos os discos não gerenciados anexados à VM são concedidos. O failover de conta não poderá continuar se houver uma concessão em um blob. Para realizar o failover, siga estas etapas:

1. Antes de começar, anote os nomes de todos os discos não gerenciados, seus números de unidade lógica (LUN) e a VM à qual eles estão anexados. Isso facilitará anexar os discos novamente após o failover.
2. Desligue a VM.
3. Exclua a VM, mas mantenha os arquivos VHD para os discos não gerenciados. Anote a hora que você excluiu a VM.
4. Aguarde até que a **Hora da última sincronização** seja atualizada, ela deve posterior à hora em que você excluiu a VM. Esta etapa é importante porque se o ponto de extremidade secundário não foi totalmente atualizado com os arquivos VHD quando o failover ocorrer, a VM poderá não funcionar corretamente na nova região primária.
5. Inicie o failover da conta.
6. Aguarde até que o failover da conta esteja concluído e a região secundária tenha se tornado a nova região primária.
7. Crie uma VM na nova região primária e anexe novamente os VHDs.
8. Inicie a nova VM.

Tenha em mente que todos os dados armazenados em um disco temporário serão perdidos quando a VM for desligada.

### <a name="unsupported-features-and-services"></a>Recursos e serviços não suportados

Os seguintes recursos e serviços não são suportados para failover de conta para a versão de pré-visualização:

- A Sincronização de Arquivos do Azure não oferece suporte ao failover de conta de armazenamento. Não deve ser realizado o failover das contas de armazenamento que contêm compartilhamentos de arquivos do Azure que estejam sendo usadas como pontos de extremidade de nuvem na Sincronização de Arquivos do Azure. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas.
- As contas de armazenamento ADLS Gen2 (contas que têm namespace hierárquico ativado) não são suportadas no momento.
- Não é possível realizar failover em uma conta de armazenamento que contenha blob de blocos premium. As contas de armazenamento que dão suporte a blobs de bloco premium atualmente não são compatíveis com a redundância geográfica.
- Uma conta de armazenamento contendo qualquer [política de imutabilidade worm](../blobs/storage-blob-immutable-storage.md) habilitada para contêineres não pode ser falhada. As políticas de retenção ou retenção de tempo desbloqueadas/bloqueadas evitam o failover para manter a conformidade.
- Depois que o failover estiver concluído, os seguintes recursos podem parar de funcionar se originalmente [ativados: assinaturas de eventos,](../blobs/storage-blob-event-overview.md) [feed de alterações,](../blobs/storage-blob-change-feed.md) [políticas de ciclo de vida](../blobs/storage-lifecycle-management-concepts.md)e registro de análise de [armazenamento.](storage-analytics-logging.md)

## <a name="copying-data-as-an-alternative-to-failover"></a>Copiando dados como uma alternativa ao failover

Se sua conta de armazenamento estiver configurada para o RA-GRS, você terá acesso de leitura aos seus dados usando o ponto de extremidade secundário. Se você preferir não realizar o failover no caso de uma interrupção na região primária, será possível usar ferramentas como o [AzCopy](storage-use-azcopy.md), o [Azure PowerShell](storage-powershell-guide-full.md) ou a [Biblioteca de movimentação de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar os dados de sua conta de armazenamento na região secundária para outra conta de armazenamento em uma região não afetada. Você poderá, então, direcionar os aplicativos para essa conta de armazenamento para a disponibilidade de leitura e de gravação.

> [!CAUTION]
> Um failover de conta não deve ser usado como parte de sua estratégia de migração de dados.


## <a name="microsoft-managed-failover"></a>Failover gerenciado pela Microsoft

Em circunstâncias extremas em que uma região for perdida devido a um desastre significativo, a Microsoft poderá iniciar um failover regional. Nesse caso, nenhuma ação sua é necessária. Você não terá acesso para gravação na conta de armazenamento até que o failover gerenciado pela Microsoft seja concluído. Seus aplicativos poderão ler a partir da região secundária se sua conta de armazenamento estiver configurada para o RA-GRS. 

## <a name="see-also"></a>Confira também

- [Iniciar um failover de conta (versão prévia)](storage-initiate-account-failover.md)
- [Projetando aplicativos altamente disponíveis usando RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutorial: Construa um aplicativo altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md) 
