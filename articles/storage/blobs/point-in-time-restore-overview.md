---
title: Restauração pontual para blobs de blocos (versão prévia)
titleSuffix: Azure Storage
description: A restauração pontual para BLOBs de blocos fornece proteção contra exclusão acidental ou corrupção, permitindo que você restaure uma conta de armazenamento para seu estado anterior em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1187b01fa623264055edecf21ea5c9d35d59a152
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068295"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Restauração pontual para blobs de blocos (versão prévia)

A restauração pontual fornece proteção contra exclusão acidental ou corrupção, permitindo que você restaure dados de blob de blocos para um estado anterior. A restauração pontual é útil em cenários em que um usuário ou aplicativo exclui acidentalmente dados ou onde um erro de aplicativo corrompe os dados. A restauração pontual também permite cenários de teste que exigem a reversão de um conjunto de dados para um estado conhecido antes da execução de outros testes.

Para saber como habilitar a restauração pontual para uma conta de armazenamento, consulte [habilitar e gerenciar a restauração pontual para BLOBs de blocos (versão prévia)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Como funciona a restauração pontual

Para habilitar a restauração pontual, você cria uma política de gerenciamento para a conta de armazenamento e especifica um período de retenção. Durante o período de retenção, você pode restaurar blobs de blocos do estado atual para um estado em um ponto anterior no tempo.

Para iniciar uma restauração pontual, chame a operação [restaurar intervalos de blob](/rest/api/storagerp/storageaccounts/restoreblobranges) e especifique um ponto de restauração em hora UTC. Você pode especificar intervalos lexicográfica de contêineres e nomes de blob a serem restaurados ou omitir o intervalo para restaurar todos os contêineres na conta de armazenamento. Há suporte para até 10 intervalos lexicográfica por operação de restauração.

O armazenamento do Azure analisa todas as alterações feitas nos BLOBs especificados entre o ponto de restauração solicitado, especificado em hora UTC e o momento atual. A operação de restauração é atômica, portanto, ela é bem-sucedida completamente na restauração de todas as alterações ou falha. Se houver BLOBs que não podem ser restaurados, a operação falhará e as operações de leitura e gravação para os contêineres afetados serão retomadas.

Somente uma operação de restauração pode ser executada em uma conta de armazenamento por vez. Uma operação de restauração não pode ser cancelada quando está em andamento, mas uma segunda operação de restauração pode ser executada para desfazer a primeira operação.

A operação **restaurar intervalos de blob** retorna uma ID de restauração que identifica exclusivamente a operação. Para verificar o status de uma restauração pontual, chame a operação **obter status da restauração** com a ID de restauração retornada da operação **restaurar intervalos de blob** .

Tenha em mente as seguintes limitações nas operações de restauração:

- Um bloco que foi carregado por meio do [bloco Put](/rest/api/storageservices/put-block) ou [Put bloco da URL](/rest/api/storageservices/put-block-from-url), mas não confirmado via [lista de blocos Put](/rest/api/storageservices/put-block-list), não faz parte de um blob e, portanto, não é restaurado como parte de uma operação de restauração.
- Um blob com uma concessão ativa não pode ser restaurado. Se um blob com uma concessão ativa estiver incluído no intervalo de BLOBs a serem restaurados, a operação de restauração falhará atomicamente.
- Os instantâneos não são criados ou excluídos como parte de uma operação de restauração. Somente o blob de base é restaurado para seu estado anterior.
- Se um blob tiver se movido entre as camadas quente e fria no período entre o momento atual e o ponto de restauração, o blob será restaurado para sua camada anterior. No entanto, um blob que foi movido para a camada de arquivo não será restaurado.

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [Excluir contêiner](/rest/api/storageservices/delete-container) durante a versão prévia de restauração pontual, esse contêiner não poderá ser restaurado com uma operação de restauração. Durante a versão prévia, em vez de excluir um contêiner, exclua blobs individuais se você quiser restaurá-los.

### <a name="prerequisites-for-point-in-time-restore"></a>Pré-requisitos para restauração pontual

A restauração pontual requer que os seguintes recursos de armazenamento do Azure estejam habilitados:

- [Exclusão reversível](soft-delete-overview.md)
- [Feed de alterações (versão prévia)](storage-blob-change-feed.md)
- [Controle de versão de BLOB](versioning-overview.md)

Habilite esses recursos para a conta de armazenamento antes de habilitar a restauração pontual. Registre-se nas versões prévias do feed de alterações e do controle de versão do blob antes de habilitá-las.

### <a name="retention-period-for-point-in-time-restore"></a>Período de retenção para restauração pontual

Ao habilitar a restauração pontual para uma conta de armazenamento, você especifica um período de retenção. Blobs de blocos na sua conta de armazenamento podem ser restaurados durante o período de retenção.

O período de retenção começa quando você habilita a restauração pontual. Tenha em mente que você não pode restaurar BLOBs para um estado antes do início do período de retenção. Por exemplo, se você habilitou a restauração pontual em 1º de maio com uma retenção de 30 dias, em 15 de maio, você pode restaurar para um máximo de 15 dias. Em 1º de junho, você pode restaurar dados entre 1 e 30 dias.

O período de retenção para a restauração pontual deve ser pelo menos um dia menor do que o período de retenção especificado para exclusão reversível. Por exemplo, se o período de retenção de exclusão reversível for definido como 7 dias, o período de retenção de restauração pontual poderá ser entre 1 e 6 dias.

### <a name="permissions-for-point-in-time-restore"></a>Permissões para restauração pontual

Para iniciar uma operação de restauração, um cliente deve ter permissões de gravação para todos os contêineres na conta de armazenamento. Para conceder permissões para autorizar uma operação de restauração com o Azure Active Directory (Azure AD), atribua a função de **colaborador da conta de armazenamento** à entidade de segurança no nível da conta de armazenamento, do grupo de recursos ou da assinatura.

## <a name="about-the-preview"></a>Sobre a visualização

A restauração pontual tem suporte apenas para contas de armazenamento v2 de uso geral. Somente os dados nas camadas de acesso quentes e frias podem ser restaurados com a restauração pontual.

As regiões a seguir dão suporte à restauração pontual no modo de visualização:

- Canadá Central
- Leste do Canadá
- França Central

A visualização inclui as seguintes limitações:

- Não há suporte para a restauração de blobs de blocos Premium.
- A restauração de blobs na camada de acesso aos arquivos não é compatível. Por exemplo, se um blob na camada de acesso frequente foi movido para a camada de acesso aos arquivos dois dias atrás e uma operação de restauração a um ponto três dias atrás, o blob não será restaurado para a camada de acesso frequente.
- Não há suporte para a restauração Azure Data Lake Storage Gen2 namespaces simples e hierárquicos.
- Não há suporte para a restauração de contas de armazenamento usando chaves fornecidas pelo cliente.

> [!IMPORTANT]
> A versão prévia de restauração pontual é destinada apenas para uso fora de produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

### <a name="register-for-the-preview"></a>Registre-se para a versão prévia

Para se registrar para a versão prévia, execute os seguintes comandos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for Blob versioning
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Verificar status do registro

O registro para a restauração pontual é automático e deve levar menos de 10 minutos. Para verificar o status do seu registro, execute os seguintes comandos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>Preços e cobrança

A cobrança pela restauração pontual depende da quantidade de dados processados para executar a operação de restauração. A quantidade de dados processados é baseada no número de alterações ocorridas entre o ponto de restauração e o momento atual. Por exemplo, supondo uma taxa relativamente constante de alteração para bloquear dados de BLOB em uma conta de armazenamento, uma operação de restauração que volta no tempo 1 dia custaria 1/10 de uma restauração que volta no tempo de 10 dias.

Para estimar o custo de uma operação de restauração, examine o log do feed de alterações para estimar a quantidade de dados que foram modificados durante o período de restauração. Por exemplo, se o período de retenção do feed de alterações for de 30 dias e o tamanho do feed de alterações for de 10 MB, a restauração para um ponto de 10 dias antes custaria aproximadamente um terço do preço listado para uma conta do LRS nessa região. Restaurar para um ponto de 27 dias antes custaria aproximadamente nove décimos do preço listado.

Para obter mais informações sobre os preços da restauração pontual, consulte preço do [blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Fazer perguntas ou fornecer comentários

Para fazer perguntas sobre a visualização de restauração pontual ou para fornecer comentários, entre em contato com a Microsoft em pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Próximas etapas

- [Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-manage.md)
- [Suporte ao feed de alterações no Armazenamento de Blobs do Azure (versão prévia)](storage-blob-change-feed.md)
- [Habilitar exclusão reversível para blobs](soft-delete-enable.md)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
