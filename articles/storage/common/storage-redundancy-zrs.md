---
title: Compilar aplicativos de Armazenamento do Microsoft Azure altamente disponíveis no ZRS (armazenamento com redundância de zona) | Microsoft Docs
description: O ZRS (armazenamento com redundância de zona) oferece uma maneira simples de compilar aplicativos altamente disponíveis. O ZRS protege contra falhas de hardware no datacenter e contra alguns desastres regionais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 53441f5fedb4fa5e68cd1d8230ffe3ee6122e55b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186670"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>ZRS (armazenamento com redundância de zona) para criar aplicativos de armazenamento do Azure altamente disponíveis

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Compatível com cobertura e disponibilidade regional

O ZRS atualmente dá suporte aos tipos de conta de armazenamento de uso geral V2 e de armazenamento de FileStorage padrão. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

O ZRS está disponível para BLOBs de blocos, blobs de páginas que não são de disco, compartilhamentos de arquivos padrão, tabelas e filas.

Para contas v2 de uso geral, o ZRS está geralmente disponível nas seguintes regiões:

- Sudeste da Ásia
- Norte da Europa
- Europa Ocidental
- França Central
- Leste do Japão
- Sul do Reino Unido
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA 2

Para contas de armazenamento de arquivo (compartilhamentos de arquivos Premium), o ZRS está geralmente disponível nas seguintes regiões:

- Europa Ocidental
- Leste dos EUA

A Microsoft continua a habilitar o ZRS em mais regiões do Azure. Verifique a página [Atualizações de Serviço do Azure](https://azure.microsoft.com/updates/) regularmente para obter informações sobre novas regiões.

**Limitações conhecidas**

- A camada de arquivo morto não tem suporte atualmente em contas ZRS. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para obter mais detalhes.
- Os discos gerenciados não dão suporte a ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona fica indisponível?

Seus dados ainda podem ser acessados por operações de leitura e de gravação, mesmo em caso de não disponibilidade de uma zona. A Microsoft recomenda que você continue seguindo as práticas recomendadas para tratamento de falhas transitórias. Essas práticas incluem implementar políticas de repetição com retirada exponencial.

Quando uma zona não estiver disponível, o Azure realiza atualizações da rede, como a reposição de DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando seus dados antes que as atualizações sejam concluídas.

O ZRS não pode proteger seus dados contra um desastre regional em que várias zonas permanentemente são afetadas. Em vez disso, o ZRS oferece resiliência para seus dados se ele ficar indisponível temporariamente. Para proteção contra desastres regionais, a Microsoft recomenda o uso de armazenamento com redundância geográfica (GRS). Para obter mais informações sobre GRS, consulte [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Convertendo em replicação ZRS

Migrando para ou do LRS, GRS e RA-GRS é simples. Use o portal do Azure ou a API de provedor de recursos de armazenamento para alterar o tipo de redundância da sua conta. Azure, em seguida, replicar os dados adequadamente. 

A migração de dados para o ZRS requer uma estratégia diferente. Migração de ZRS envolve a movimentação física de dados de um carimbo de armazenamento único para vários carimbos dentro de uma região.

Há duas opções principais para a migração para o ZRS: 

- Copie ou mova dados manualmente para uma nova conta do ZRS de uma conta existente.
- Solicite uma migração ao vivo.

> [!IMPORTANT]
> Atualmente, a migração ao vivo não tem suporte para compartilhamentos de arquivos premium. No momento, só há suporte para copiar ou mover dados manualmente.

Se você precisar que a migração seja concluída em uma determinada data, considere executar uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração ao vivo. Com uma migração manual, você está no controle do tempo.

Para executar uma migração manual, você tem opções:
- Use ferramentas existentes como o AzCopy, uma das bibliotecas cliente do Armazenamento do Azure ou ferramentas de terceiros confiáveis.
- Se você estiver familiarizado com o Hadoop ou o HDInsight, anexe a conta de origem e de destino (ZRS) ao seu cluster. Em seguida, paralelize o processo de cópia de dados com uma ferramenta como DistCp.
- Crie seu próprio ferramental usando uma das bibliotecas cliente do Armazenamento do Azure.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se o seu aplicativo exigir alta disponibilidade, a Microsoft também oferece uma opção de migração ao vivo. Uma migração ao vivo é uma migração no local sem tempo de inatividade. 

Durante uma migração ao vivo, você pode usar sua conta de armazenamento enquanto seus dados são migrados entre os carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que você tem normalmente.

Tenha em mente as seguintes restrições sobre a migração ao vivo:

- Enquanto a Microsoft lida com seu pedido de migração ao vivo prontamente, não há garantia de quando uma migração ao vivo será concluída. Se você precisar que seus dados sejam migrados para o ZRS até uma determinada data, a Microsoft recomenda que você execute uma migração manual. Geralmente, quanto mais dados você tiver em sua conta, mais tempo levará para migrar esses dados. 
- A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação de LRS ou GRS. Se sua conta usa o RA-GRS, você precisa primeiro alterar o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Essa etapa intermediária remove o terminal secundário somente leitura fornecido pelo RA-GRS antes da migração.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região. Se você quiser migrar os dados para uma conta do ZRS localizada em uma região diferente da conta de origem, você deve executar uma migração manual.
- Apenas os tipos de conta de armazenamento padrão suportam a migração ao vivo. As contas de armazenamento premium devem ser migradas manualmente.
- Não há suporte para a migração dinâmica de ZRS para LRS, GRS ou RA-GRS. Você precisará mover os dados manualmente para uma conta de armazenamento nova ou existente.
- Os discos gerenciados estão disponíveis somente para LRS e não podem ser migrados para o ZRS. Você pode armazenar instantâneos e imagens para SSD Standard Managed Disks no armazenamento HDD Standard e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para integração com conjuntos de disponibilidade, consulte [introdução aos Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Contas LRS ou GRS com dados de arquivo morto não podem ser migradas para ZRS.

Você pode solicitar a migração ao vivo por meio do [Portal de Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que você deseja converter em ZRS.
1. Selecione **Nova solicitação de suporte**
2. Preencha o **Básico** com base nas informações da sua conta. Na seção **Serviço**, selecione **Gerenciamento de Contas de Armazenamento** e o recurso que você deseja converter em ZRS. 
3. Selecione **Avançar**. 
4. Especifique os seguintes valores na seção **Problema**: 
    - **Severidade**: deixe o valor padrão como-está.
    - **Tipo de problema**: selecione **migração de dados**.
    - **Categoria**: selecione **migrar para ZRS**.
    - **Título**: Tipo de titúlo descritivo, por exemplo, **migração de conta do ZRS**.
    - **Detalhes**: digite detalhes adicionais na caixa de **detalhes** , por exemplo, eu gostaria de migrar para ZRS de [lRS, grs] na região do \_\_. 
5. Selecione **Avançar**.
6. Verifique se as informações de contato estão corretas na **informações de contato** folha.
7. Selecione **Criar**.

Uma pessoa de suporte entrará em contato com você e fornecerá toda a assistência necessária.

## <a name="live-migration-to-zrs-faq"></a>Perguntas frequentes sobre migração ao vivo para ZRS

**Devo planejar qualquer tempo de inatividade durante a migração?**

Não há nenhum tempo de inatividade causado pela migração. Durante uma migração ao vivo, você pode continuar usando sua conta de armazenamento enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que você tem normalmente.

**Há alguma perda de dados associada à migração?**

Não há perda de dados associada à migração. Durante o processo de migração, você tem o mesmo nível de SLA de durabilidade e disponibilidade que você tem normalmente.

**As atualizações são necessárias para os aplicativos quando a migração for concluída?**

Depois que a migração for concluída, o tipo de replicação das contas será alterado para "armazenamento com redundância de zona (ZRS)". Pontos de extremidade de serviço, chaves de acesso, SAS e quaisquer outras opções de configuração de conta permanecem inalterados e intactos.

**Posso solicitar uma migração dinâmica de minhas contas de uso geral v1 para ZRS?**

O ZRS só dá suporte a contas v2 de uso geral, portanto, antes de enviar uma solicitação de migração ao vivo para o ZRS, atualize suas contas para uso geral v2. Confira [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [atualize para uma conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para obter mais detalhes.

**Posso solicitar uma migração dinâmica de minhas contas de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para ZRS?**

Antes de enviar uma solicitação de migração ao vivo para ZRS, verifique se seus aplicativos ou cargas de trabalho não exigem mais acesso ao ponto de extremidade secundário somente leitura e altere o tipo de replicação de suas contas de armazenamento para o armazenamento com redundância geográfica (GRS). Consulte [alterando a estratégia de replicação](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) para obter mais detalhes.

**Posso solicitar uma migração dinâmica de minhas contas de armazenamento para ZRS para outra região?**

Se desejar migrar seus dados para uma conta do ZRS localizada em uma região diferente da região da conta de origem, você deverá executar uma migração manual.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS clássico: é uma opção herdada para redundância de blobs de bloco
> [!NOTE]
> A Microsoft irá substituir e migrar as contas do ZRS clássico em 31 de março de 2021. Mais detalhes serão fornecidos aos clientes do ZRS Classic antes da reprovação. 
>
> Depois que o ZRS se tornar [disponível](#support-coverage-and-regional-availability) em uma região, os clientes não poderão criar contas ZRS clássicas do portal nessa região. O uso do Microsoft PowerShell e do Azure CLI para criar contas do ZRS Classic é uma opção até que o ZRS Classic seja descontinuado.

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie o failover para o secundário. Uma conta do ZRS Classic não pode ser convertida para ou de LRS, GRS ou RA-GRS. As contas do ZRS Classic também não suportam métricas ou registros.

As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta do ZRS para ou de uma conta do LRS, ZRS Classic, GRS ou RA-GRS, use uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, Azure PowerShell ou Azure CLI. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.

Você também pode atualizar suas contas ZRS clássicas para ZRS no portal ou usando Azure PowerShell ou CLI do Azure nas regiões em que ZRS está disponível. Para atualizar para o ZRS no portal do Azure, navegue até a seção de **configuração** da conta e escolha **Atualizar**:

![Atualizar ZRS clássico para ZRS no portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Para atualizar para o ZRS usando o PowerShell, chame o seguinte comando:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Para atualizar para o ZRS usando a CLI, chame o seguinte comando:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Consulte também
- [Replicação de Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)
