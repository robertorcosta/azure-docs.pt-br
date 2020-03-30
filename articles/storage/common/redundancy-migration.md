---
title: Alterar a forma como uma conta de armazenamento é replicada
titleSuffix: Azure Storage
description: Saiba como alterar a forma como os dados de uma conta de armazenamento existente são replicados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337085"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Alterar a forma como uma conta de armazenamento é replicada

O Azure Storage sempre armazena várias cópias de seus dados para que ele seja protegido contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, quedas de rede ou energia e desastres naturais maciços. A redundância garante que sua conta de armazenamento atenda ao [Contrato de Nível de Serviço (SLA) para armazenamento azure,](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas.

O Azure Storage oferece os seguintes tipos de replicação:

- Armazenamento com redundância local (LRS)
- Armazenamento com redundância de zona (ZRS)
- Armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso de leitura (RA-GRS)
- GZRS (Geo-zone-redundante storage, armazenamento redundante em geozona) ou armazenamento redundante de zona geográfica de acesso à leitura (RA-GZRS) (visualização)

Para obter uma visão geral de cada uma dessas opções, consulte [a redundância de armazenamento do Azure](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Alternar entre tipos de replicação

Você pode mudar uma conta de armazenamento de um tipo de replicação para qualquer outro tipo, mas alguns cenários são mais simples do que outros. Se você quiser adicionar ou remover a replicação geográfica ou ler o acesso à região secundária, você pode usar o portal Azure, PowerShell ou Azure CLI para atualizar a configuração de replicação. No entanto, se você quiser alterar a forma como os dados são replicados na região primária, movendo-se de LRS para ZRS ou vice-versa, então você deve realizar uma migração manual.

A tabela a seguir fornece uma visão geral de como mudar de cada tipo de replicação para outro:

| Comutação | ... para LRS | ... para GRS/RA-GRS | ... para ZRS | ... para GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... da LRS</b> | N/D | Use o portal Azure, powershell ou CLI para alterar a configuração de replicação<sup>1</sup> | Realize uma migração manual <br /><br />Solicite uma migração ao vivo | Realize uma migração manual <br /><br /> OU <br /><br /> Mude para GRS/RA-GRS primeiro e, em seguida, solicite uma migração ao vivo<sup>1</sup> |
| <b>... de GRS/RA-GRS</b> | Use o portal Azure, powershell ou CLI para alterar a configuração de replicação | N/D | Realize uma migração manual <br /><br /> OU <br /><br /> Mude para LRS primeiro e, em seguida, solicite uma migração ao vivo | Realize uma migração manual <br /><br /> Solicite uma migração ao vivo |
| <b>... da ZRS</b> | Realize uma migração manual | Realize uma migração manual | N/D | Use o portal Azure, powershell ou CLI para alterar a configuração de replicação<sup>1</sup> |
| <b>... de GZRS/RA-GZRS</b> | Realize uma migração manual | Realize uma migração manual | Use o portal Azure, powershell ou CLI para alterar a configuração de replicação | N/D |

<sup>1</sup> Incorre em uma carga única de saída.

> [!CAUTION]
> Se você executou um [failover de conta](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) para sua conta (RA-)GRS ou (RA-)GZRS, ela será configurada para ser localmente redundante na nova região primária. A migração ao vivo para ZRS ou GZRS para tais contas LRS não é suportada. Você precisará realizar a [migração manual](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs).

## <a name="change-the-replication-setting"></a>Alterar a configuração de replicação

Você pode usar o portal Azure, PowerShell ou Azure CLI para alterar a configuração de replicação de uma conta de armazenamento, desde que você não esteja alterando a forma como os dados são replicados na região principal. Se você está migrando de LRS na região primária para ZRS na região primária ou vice-versa, então você deve realizar uma [migração manual](#perform-a-manual-migration-to-zrs) ou uma [migração ao vivo](#request-a-live-migration-to-zrs).

Alterar a forma como sua conta de armazenamento é replicada não resulta em tempo de inatividade para seus aplicativos.

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a opção de redundância para sua conta de armazenamento no portal Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Selecione a **configuração configuração.**
1. Atualize a **configuração de replicação.**

![Captura de tela mostrando como alterar a opção de replicação no portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para alterar a opção de redundância da sua conta de armazenamento com o `-SkuName` PowerShell, ligue para o comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e especifique o parâmetro:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para alterar a opção de redundância para sua conta de armazenamento com o Azure CLI, ligue para o comando [az storage account update](/cli/azure/storage/account#az-storage-account-update) e especifique o `--sku` parâmetro:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Realize uma migração manual para ZRS

Se você quiser alterar a forma como os dados em sua conta de armazenamento são replicados na região principal, movendo-se de LRS para ZRS ou vice-versa, então você pode optar por realizar uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração ao vivo. Você controla o tempo de uma migração manual, então use esta opção se precisar que a migração seja concluída até uma determinada data.

Quando você realiza uma migração manual de LRS para ZRS na região primária ou vice-versa, a conta de armazenamento de destino pode ser geo-redundante e também pode ser configurada para acesso à leitura da região secundária. Por exemplo, você pode migrar uma conta LRS para uma conta GZRS ou RA-GZRS em uma etapa.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se o seu aplicativo exigir alta disponibilidade, a Microsoft também oferece uma opção de migração ao vivo. Uma migração ao vivo é uma migração no local sem tempo de inatividade.

Com uma migração manual, você copia os dados da sua conta de armazenamento existente para uma nova conta de armazenamento que usa ZRS na região principal. Para realizar uma migração manual, você pode usar uma das seguintes opções:

- Copie os dados usando uma ferramenta existente, como o AzCopy, uma das bibliotecas de clientes do Azure Storage ou uma ferramenta confiável de terceiros.
- Se você estiver familiarizado com Hadoop ou HDInsight, você pode anexar a conta de armazenamento de origem e a conta de armazenamento de destino ao seu cluster. Em seguida, paralelize o processo de cópia de dados com uma ferramenta como DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Solicite uma migração ao vivo para zrs

Se você precisar migrar sua conta de armazenamento de LRS ou GRS para ZRS na região principal sem tempo de inatividade do aplicativo, você pode solicitar uma migração ao vivo da Microsoft. Durante uma migração ao vivo, você pode acessar dados em sua conta de armazenamento e sem perda de durabilidade ou disponibilidade. O SLA de armazenamento do Azure é mantido durante o processo de migração. Não há perda de dados associada a uma migração ao vivo. Pontos finais de serviço, chaves de acesso, assinaturas de acesso compartilhadas e outras opções de conta permanecem inalteradas após a migração.

O ZRS suporta apenas contas v2 de uso geral, portanto, certifique-se de atualizar sua conta de armazenamento antes de enviar uma solicitação de migração ao vivo para O ZRS. Para obter mais informações, consulte [Atualizar para uma conta de armazenamento v2 de uso geral](storage-account-upgrade.md). Uma conta de armazenamento deve conter dados a serem migrados via migração ao vivo.

A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação de LRS ou GRS. Se sua conta usa o RA-GRS, você precisa primeiro alterar o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Essa etapa intermediária remove o terminal secundário somente leitura fornecido pelo RA-GRS antes da migração.

Enquanto a Microsoft lida com seu pedido de migração ao vivo prontamente, não há garantia de quando uma migração ao vivo será concluída. Se você precisar que seus dados sejam migrados para o ZRS até uma determinada data, a Microsoft recomenda que você execute uma migração manual. Geralmente, quanto mais dados você tiver em sua conta, mais tempo levará para migrar esses dados.

Você deve realizar uma migração manual se:

- Você deseja migrar seus dados para uma conta de armazenamento ZRS que está localizada em uma região diferente da conta de origem.
- Sua conta de armazenamento é uma conta de blob de página premium ou blob de bloco.
- Você deseja migrar dados de ZRS para LRS, GRS ou RA-GRS.
- Sua conta de armazenamento inclui dados no nível de arquivamento.

Você pode solicitar a migração ao vivo por meio do [Portal de Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que você deseja converter em ZRS.

1. Selecione **nova solicitação de suporte**
2. Preencha o **Básico** com base nas informações da sua conta. Na seção **Serviço**, selecione **Gerenciamento de Contas de Armazenamento** e o recurso que você deseja converter em ZRS.
3. Selecione **Avançar**.
4. Especifique os seguintes valores na seção **Problema**:
    - **Severidade**: deixe o valor padrão como-está.
    - **Tipo de problema**: selecione **migração de dados**.
    - **Categoria**: Selecione **Migrar para ZRS**.
    - **Título**: Tipo de titúlo descritivo, por exemplo, **migração de conta do ZRS**.
    - **Detalhes**: Digite detalhes adicionais na caixa **Detalhes,** por exemplo, gostaria de migrar \_ \_ para ZRS de [LRS, GRS] na região.
5. Selecione **Avançar**.
6. Verifique se as informações de contato estão corretas na **informações de contato** folha.
7. Selecione **Criar**.

Uma pessoa de suporte entrará em contato com você e fornecerá toda a assistência necessária.

> [!NOTE]
> A migração ao vivo não é suportada atualmente para compartilhamentos de arquivos premium. Apenas a cópia manual ou a movimentação de dados são suportadas no momento.
>
> As contas de armazenamento GZRS não suportam atualmente o nível de arquivamento. Consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para obter mais detalhes.
>
> Os discos gerenciados estão disponíveis apenas para LRS e não podem ser migrados para ZRS. Você pode armazenar instantâneos e imagens para discos gerenciados padrão de SSD no armazenamento HDD padrão e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para obter informações sobre integração com conjuntos de disponibilidade, consulte [Introdução aos discos gerenciados do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Mudar de ZRS Classic

> [!IMPORTANT]
> A Microsoft irá substituir e migrar as contas do ZRS clássico em 31 de março de 2021. Mais detalhes serão fornecidos aos clientes do ZRS Classic antes da reprovação.
>
> Depois que o ZRS se tornar geralmente disponível em uma determinada região, os clientes não poderão mais criar contas ZRS Classic a partir do portal Azure naquela região. O uso do Microsoft PowerShell e do Azure CLI para criar contas do ZRS Classic é uma opção até que o ZRS Classic seja descontinuado. Para obter informações sobre onde o ZRS está disponível, consulte [a redundância do Azure Storage](storage-redundancy.md).

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie o failover para o secundário. Uma conta do ZRS Classic não pode ser convertida para ou de LRS, GRS ou RA-GRS. As contas do ZRS Classic também não suportam métricas ou registros.

As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta ZRS para ou a partir de uma conta LRS, GRS, RA-GRS ou ZRS Classic, use uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, PowerShell ou Azure CLI. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.

Você também pode atualizar sua conta de armazenamento ZRS Classic para ZRS usando o portal Azure, PowerShell ou Azure CLI em regiões onde o ZRS está disponível.

# <a name="portal"></a>[Portal](#tab/portal)

Para atualizar para ZRS no portal Azure, navegue até as configurações de **configuração** da conta e escolha **Upgrade**:

![Atualize o ZRS Classic para ZRS no Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para atualizar para ZRS usando o PowerShell, chame o seguinte comando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para atualizar para ZRS usando o Azure CLI, ligue para o seguinte comando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Custos associados à alteração de como os dados são replicados

Os custos associados à alteração da forma como os dados são replicados dependem do seu caminho de conversão. Encomenda de pelo menos para as mais caras, as ofertas de redundância do Azure Storage incluem LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS.

Por exemplo, passar *de* LRS para qualquer outro tipo de replicação incorrerá em cobranças adicionais porque você está se movendo para um nível de redundância mais sofisticado. Migrar *para* GRS ou RA-GRS incorrerá em uma carga de largura de banda de saída porque seus dados (em sua região primária) estão sendo replicados para sua região secundária remota. Essa carga é um custo único na configuração inicial. Depois que os dados são copiados, não há mais taxas de migração. Para obter detalhes sobre encargos de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se você migrar sua conta de armazenamento de GRS para LRS, não haverá custo adicional, mas seus dados replicados serão excluídos do local secundário.

> [!IMPORTANT]
> Se você migrar sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta será cobrada como RA-GRS por mais 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Confira também

- [Redundância de armazenamento azure](storage-redundancy.md)
- [Verifique a última propriedade Sync Time para obter uma conta de armazenamento](last-sync-time-get.md)
- [Projetando aplicativos altamente disponíveis usando armazenamento geo-redundante de acesso de leitura](storage-designing-ha-apps-with-ragrs.md)
