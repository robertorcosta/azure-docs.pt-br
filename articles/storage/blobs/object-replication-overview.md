---
title: Visão geral da replicação de objeto (versão prévia)
titleSuffix: Azure Storage
description: A replicação de objeto (versão prévia) copia de maneira assíncrona os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Use a replicação de objeto para minimizar a latência em solicitações de leitura para aumentar a eficiência das cargas de trabalho de computação, otimizar a distribuição de dados e minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: edbce017b1090a029d49c9f2f8812e7e65133fcb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073124"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replicação de objeto para blobs de blocos (versão prévia)

A replicação de objeto (versão prévia) copia de maneira assíncrona os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Alguns cenários com suporte da replicação de objeto incluem:

- **Redução da latência.** A replicação de objeto pode reduzir a latência de solicitações de leitura, permitindo que os clientes consumam dados de uma região que esteja próxima fisicamente.
- **Aumento da eficiência das cargas de trabalho de computação.** Com a replicação de objeto, as cargas de trabalho de computação podem processar os mesmos conjuntos de blobs de blocos em regiões diferentes.
- **Otimização da distribuição de dados.** É possível processar ou analisar dados em um único local e, em seguida, replicar apenas os resultados em regiões adicionais.
- **Otimização dos custos.** Após a replicação dos dados, você poderá reduzir os custos movendo-os para a camada de arquivo usando políticas de gerenciamento do ciclo de vida.

O diagrama a seguir mostra como a replicação de objeto replica blobs de blocos de uma conta de armazenamento de origem em uma região para contas de destino em duas regiões diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama mostrando como funciona a replicação de objeto":::

Para saber como configurar a replicação de objeto, consulte [Configurar a replicação de objeto (versão prévia)](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="object-replication-policies-and-rules"></a>Políticas e regras da replicação de objeto

Ao configurar a replicação de objeto, você cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contêiner de origem e um contêiner de destino e indicam quais blobs de blocos no contêiner de origem serão replicados.

Depois de configurar a replicação de objeto, o Armazenamento do Azure verifica periodicamente o feed de alterações da conta de origem e replica de forma assíncrona quaisquer operações de gravação ou exclusão na conta de destino. A latência de replicação depende do tamanho do blob de blocos que está sendo replicado.

> [!IMPORTANT]
> Como os dados do blob de blocos são replicados de forma assíncrona, a conta de origem e a conta de destino não ficam em sincronização imediatamente. Atualmente, não há SLA sobre o tempo necessário para replicar dados para a conta de destino.

### <a name="replications-policies"></a>Políticas de replicação

Ao configurar a replicação de objeto, uma política de replicação é criada na conta de origem e de destino por meio do provedor de Armazenamento do Azure. A política de replicação é identificada por uma ID de política. A política nas contas de origem e de destino deve ter a mesma ID de política para que a replicação ocorra.

Uma conta de armazenamento pode servir como a conta de origem para até duas contas de destino. E uma conta de destino pode não ter mais do que duas contas de origem. As contas de origem e de destino podem estar em regiões diferentes. É possível configurar políticas de replicação separadas para replicar dados para cada uma das contas de destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Armazenamento do Azure replicará os blobs de um contêiner de origem para um contêiner de destino. Você pode especificar até 10 regras de replicação para cada política de replicação. Cada regra define um único contêiner de origem e de destino, e cada contêiner de origem e de destino pode ser usado em apenas uma regra.

Quando você cria uma regra de replicação, por padrão, somente os novos blobs de blocos adicionados subsequentemente ao contêiner de origem são copiados. Também é possível especificar que os blobs de blocos novos e existentes sejam copiados, ou você pode definir um escopo de cópia personalizado que copia blobs de blocos criados a partir de um tempo especificado em diante.

Você também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar blobs de blocos por prefixo. Quando você especifica um prefixo, somente os blobs que correspondem a esse prefixo no contêiner de origem serão copiados para o contêiner de destino.

Os contêineres de origem e de destino devem existir para que você possa especificá-los em uma regra. Depois de criar a política de replicação, o contêiner de destino torna-se somente leitura. Qualquer tentativa de gravar no contêiner de destino falha com o código de erro 409 (conflito). No entanto, você pode chamar a operação [definir camada de blob](/rest/api/storageservices/set-blob-tier) em um blob no contêiner de destino para movê-lo para a camada de arquivo morto. Para obter mais informações sobre a camada de arquivo, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>Sobre a visualização

A replicação de objeto tem suporte apenas para contas de armazenamento v2 de uso geral. A replicação de objeto está disponível em versão prévia nas seguintes regiões:

- França Central
- Leste do Canadá
- Canadá Central
- Leste dos EUA 2
- EUA Central

As contas de origem e de destino devem residir em uma dessas regiões para usar a replicação de objeto. As contas podem estar em duas regiões diferentes.

Durante a versão prévia, não há custos adicionais associados à replicação de dados entre contas de armazenamento.

> [!IMPORTANT]
> A versão prévia de replicação de objeto é destinada apenas para uso não produtivo. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

### <a name="prerequisites-for-object-replication"></a>Pré-requisitos para replicação do objeto

A replicação de objeto exige que os seguintes recursos de Armazenamento do Azure estejam habilitados: 
- [Feed de alteração](storage-blob-change-feed.md)
- [Controle de versão](versioning-overview.md)

Antes de configurar a replicação de objeto, habilite seus pré-requisitos. O feed de alterações deve ser habilitado na conta de origem, e o controle de versão do blob deve ser habilitado na conta de origem e de destino. Para saber mais sobre como habilitar esses recursos, confira estes artigos:

- [Habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)

Registre-se nas versões prévias do feed de alterações e do controle de versão do blob antes de habilitá-las.

A habilitação do feed de alterações e do controle de versão de blob pode incorrer em custos adicionais. Para ter mais detalhes, confira a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Registre-se para a versão prévia

Você pode se registrar na versão prévia de replicação de objeto usando PowerShell ou a CLI do Azure. Registre-se também nas versões prévia do feed de alterações e do controle de versão do blob, caso ainda não tenha feito isso.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registrar para a versão prévia com o PowerShell, execute os seguintes comandos:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registrar para a versão prévia com a CLI do Azure, execute os seguintes comandos:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Verificar status do registro

Verifique o status de suas solicitações de registro usando o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o status de suas solicitações de registro usando o PowerShell, execute os seguintes comandos:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o status de suas solicitações de registro usando a CLI do Azure, execute os seguintes comandos:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Fazer perguntas ou fornecer comentários

Para fazer perguntas sobre a versão prévia de replicação de objeto ou para fornecer comentários, entre em contato com a Microsoft em AzureStorageFeedback@microsoft.com. Ideias e sugestões sobre o Armazenamento do Azure são sempre bem-vindos no [fórum de comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Próximas etapas

- [Configurar a replicação de objeto (versão prévia)](object-replication-configure.md)
- [Suporte ao feed de alterações no Armazenamento de Blobs do Azure (versão prévia)](storage-blob-change-feed.md)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
