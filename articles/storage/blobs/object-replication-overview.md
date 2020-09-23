---
title: Visão geral da replicação de objeto
titleSuffix: Azure Storage
description: A replicação de objetos copia de modo assíncrono os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Use a replicação de objeto para minimizar a latência em solicitações de leitura para aumentar a eficiência das cargas de trabalho de computação, otimizar a distribuição de dados e minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ee76c1692049d5b25e85b6780fbcf78f7ebfdd2f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987045"
---
# <a name="object-replication-for-block-blobs"></a>Replicação de objeto para BLOBs de blocos

A replicação de objetos copia de modo assíncrono os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Alguns cenários com suporte da replicação de objeto incluem:

- **Redução da latência.** A replicação de objeto pode reduzir a latência de solicitações de leitura, permitindo que os clientes consumam dados de uma região que esteja próxima fisicamente.
- **Aumento da eficiência das cargas de trabalho de computação.** Com a replicação de objeto, as cargas de trabalho de computação podem processar os mesmos conjuntos de blobs de blocos em regiões diferentes.
- **Otimização da distribuição de dados.** É possível processar ou analisar dados em um único local e, em seguida, replicar apenas os resultados em regiões adicionais.
- **Otimização dos custos.** Após a replicação dos dados, você poderá reduzir os custos movendo-os para a camada de arquivo usando políticas de gerenciamento do ciclo de vida.

O diagrama a seguir mostra como a replicação de objeto replica blobs de blocos de uma conta de armazenamento de origem em uma região para contas de destino em duas regiões diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama mostrando como funciona a replicação de objeto":::

Para saber como configurar a replicação de objeto, consulte [Configurar a replicação de objeto](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Pré-requisitos para replicação do objeto

A replicação de objeto requer que os seguintes recursos de armazenamento do Azure também estejam habilitados:

- [Feed de alterações](storage-blob-change-feed.md): deve estar habilitado na conta de origem. Para saber como habilitar o feed de alterações, consulte [habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Controle de versão de blob](versioning-overview.md): deve ser habilitado nas contas de origem e de destino. Para saber como habilitar o controle de versão, consulte [habilitar e gerenciar o controle de versão de blob](versioning-enable.md).

A habilitação do feed de alterações e do controle de versão de blob pode incorrer em custos adicionais. Para ter mais detalhes, confira a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="object-replication-policies-and-rules"></a>Políticas e regras da replicação de objeto

Ao configurar a replicação de objeto, você cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contêiner de origem e um contêiner de destino e indicam quais blobs de blocos no contêiner de origem serão replicados.

Depois de configurar a replicação de objeto, o Armazenamento do Azure verifica periodicamente o feed de alterações da conta de origem e replica de forma assíncrona quaisquer operações de gravação ou exclusão na conta de destino. A latência de replicação depende do tamanho do blob de blocos que está sendo replicado.

> [!IMPORTANT]
> Como os dados do blob de blocos são replicados de forma assíncrona, a conta de origem e a conta de destino não ficam em sincronização imediatamente. Atualmente, não há SLA sobre o tempo necessário para replicar dados para a conta de destino.

### <a name="replication-policies"></a>Políticas de replicação

Ao configurar a replicação de objeto, uma política de replicação é criada na conta de origem e de destino por meio do provedor de Armazenamento do Azure. A política de replicação é identificada por uma ID de política. A política nas contas de origem e de destino deve ter a mesma ID de política para que a replicação ocorra.

Uma conta de armazenamento pode servir como a conta de origem para até duas contas de destino. As contas de origem e de destino podem estar na mesma região ou em regiões diferentes. Eles também podem residir em assinaturas diferentes e em locatários diferentes do Azure Active Directory (Azure AD). Somente uma política de replicação pode ser criada para cada par de conta de origem/conta de destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Armazenamento do Azure replicará os blobs de um contêiner de origem para um contêiner de destino. Você pode especificar até 10 regras de replicação para cada política de replicação. Cada regra de replicação define um único contêiner de origem e de destino, e cada contêiner de origem e destino pode ser usado em apenas uma regra.

Quando você cria uma regra de replicação, por padrão, somente os novos blobs de blocos adicionados subsequentemente ao contêiner de origem são copiados. Você pode especificar que os blobs de blocos novos e existentes sejam copiados ou pode definir um escopo de cópia personalizado que copia blobs de blocos criados a partir de um tempo especificado em diante.

Você também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar blobs de blocos por prefixo. Quando você especifica um prefixo, somente os blobs que correspondem a esse prefixo no contêiner de origem serão copiados para o contêiner de destino.

Os contêineres de origem e de destino devem existir para que você possa especificá-los em uma regra. Depois de criar a política de replicação, o contêiner de destino torna-se somente leitura. Qualquer tentativa de gravar no contêiner de destino falha com o código de erro 409 (conflito). No entanto, você pode chamar a operação [definir camada de blob](/rest/api/storageservices/set-blob-tier) em um blob no contêiner de destino para movê-lo para a camada de arquivo morto. Para obter mais informações sobre a camada de arquivo, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Cobrança

A replicação de objeto incorre em custos adicionais de transações de leitura e gravação em relação às contas de origem e de destino, bem como encargos de saída para a replicação de dados da conta de origem para a conta de destino e leitura de encargos para processar o feed de alterações.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a replicação de objeto](object-replication-configure.md)
- [Suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
