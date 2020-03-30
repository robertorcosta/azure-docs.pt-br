---
title: Contas de armazenamento do Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de armazenamento do Azure para usar com o Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499827"
---
# <a name="azure-storage-accounts"></a>Contas de armazenamento do Azure

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia.

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É fortemente recomendável usar contas de armazenamento no mesmo local que a conta do Serviço de Mídia para evitar custos adicionais de latência e saída de dados.

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). Blob apenas contas não são permitidas como **primárias**.

Recomendamos que você use gpv2, para que você possa aproveitar os recursos e desempenho mais recentes. Para saber mais sobre as contas de armazenamento, consulte [Visão geral da conta de Armazenamento do Microsoft Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Apenas o nível de acesso quente é suportado para uso com o Azure Media Services, embora os outros níveis de acesso possam ser usados para reduzir os custos de armazenamento em conteúdo que não está sendo usado ativamente.

Existem diferentes SKUs que você pode escolher para sua conta de armazenamento. Para obter mais informações, confira [Contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deve considerar `--sku Standard_RAGRS`, o que fornece replicação geográfica para a continuidade de negócios.

## <a name="assets-in-a-storage-account"></a>Ativos em uma conta de armazenamento

Nos Serviços de Mídia v3, as APIs de armazenamento são usadas para carregar arquivos em ativos. Para obter mais informações, consulte [Ativos no Azure Media Services v3](assets-concept.md).

> [!Note]
> Não tente alterar o conteúdo de contêineres blob gerados pelo SDK de Serviços de Mídia sem usar APIs de Serviços de Mídia.

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger seus ativos em repouso, os ativos devem ser criptografados pela criptografia do lado do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia v3:

|Opção de criptografia|Descrição|Serviços de Mídia v3|
|---|---|---|
|Criptografia de armazenamento de serviços de mídia| Criptografia AES-256, chave gerenciada pelos Serviços de Mídia. |Não apoiado. <sup>(1)</sup>|
|[Criptografia de serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do lado do servidor oferecida pelo Azure Storage, chave gerenciada pelo Azure ou pelo cliente.| Com suporte.|
|[Criptografia do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do lado do cliente oferecida pelo armazenamento Azure, chave gerenciada pelo cliente no Key Vault.|Sem suporte.|

<sup>1</sup> No Media Services v3, a criptografia de armazenamento (criptografia AES-256) só é suportada para retrocompatibilidade quando seus ativos foram criados com o Media Services v2, o que significa que o V3 funciona com ativos criptografados de armazenamento existentes, mas não permite a criação de novos.

## <a name="storage-account-errors"></a>Erros de conta de armazenamento

O estado "Desconectado" de uma conta de Serviços de Mídia indica que a conta não tem mais acesso a uma ou mais contas de armazenamento anexadas, devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Mídia exigem chaves de acesso de armazenamento atualizadas para realizar várias tarefas na conta.

Veja a seguir os principais cenários que fariam com que a conta de Serviços de Mídia não tivesse acesso às contas de armazenamento anexadas.

|Problema|Solução|
|---|---|
|A conta de Serviços de Mídia ou as contas de armazenamento anexadas foram migradas para assinaturas separadas. |Migre a conta(s) de armazenamento ou serviços de mídia para que todos estejam na mesma assinatura. |
|A conta de Serviços de Mídia está usando uma conta de armazenamento anexada em uma assinatura diferente, já que ela era uma conta Serviços de Mídia inicial compatível com este cenário. Todas as primeiras contas do Media Services foram convertidas em contas modernas baseadas no Azure Resources Manager e terão um estado desconectado. |Migre a conta de armazenamento ou serviços de mídia para que todos estejam na mesma assinatura.|

## <a name="azure-storage-firewall"></a>Firewall de armazenamento azure

O Azure Media Services não suporta contas de armazenamento com o firewall de armazenamento Azure ou [pontos finais privados](https://docs.microsoft.com/azure/storage/common/storage-network-security) ativados.

## <a name="next-steps"></a>Próximas etapas

Para saber como anexar uma conta de armazenamento para sua conta de Serviços de Mídia, consulte [Criar uma conta](create-account-cli-quickstart.md).
