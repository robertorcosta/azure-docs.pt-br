---
title: Criar uma conta de Serviços de Mídia do Azure
description: Este tutorial orienta você pelas etapas de criação de uma conta dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23a26e01c8329fa384d29443a143b173c58a2c6e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539089"
---
# <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Para iniciar a criptografia, codificação, analise, gerenciamento e streaming de conteúdo de mídia no Azure, você precisa criar uma conta dos Serviços de Mídia. A conta dos Serviços de Mídia precisa estar associada a uma ou mais contas de armazenamento. Este artigo descreve as etapas para criar uma nova conta dos serviços de mídia do Azure.

> [!NOTE]
> A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.

 Você pode usar o portal do Azure ou a CLI para criar uma conta de serviços de mídia. Escolha a guia para o método que você deseja usar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
O portal do Azure fornece uma maneira de criar rapidamente uma conta dos serviços de mídia do Azure. Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure.

No momento, você pode usar o [portal do Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-events-outputs-concept.md)dos serviços de mídia v3, 
* Exibir (não gerenciar) os [ativos](assets-concept.md)v3, 
* [Obtenha informações sobre como acessar APIs](./access-api-howto.md). 

Para todas as outras tarefas de gerenciamento (por exemplo, [transformações e trabalhos](transforms-jobs-concept.md) e [proteção de conteúdo](content-protection-overview.md)), use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Usar o portal do Azure para criar uma conta de serviços de mídia

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **+ criar um recurso**  >  **Media**Media  >  **Services**.
1. Na seção **criar uma conta dos serviços de mídia** , insira os valores necessários.

    | Nome | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos serviços de mídia. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.|
    |**Assinatura**|Se você tiver mais de uma assinatura, selecione uma na lista de assinaturas do Azure às quais você tem acesso.|
    |**Grupo de recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localidade**|Selecione a região geográfica que será usada para armazenar os registros de mídia e de metadados para sua conta de serviços de mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. |
    |**Conta de armazenamento**|Selecione uma conta de armazenamento para fornecer o armazenamento de BLOBs do conteúdo de mídia da sua conta de serviços de mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.<br/><br/>Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Você pode usar o portal do Azure para adicionar contas de armazenamento secundárias. Para obter mais informações, consulte [contas de armazenamento do Azure com contas dos serviços de mídia do Azure](storage-account-concept.md).<br/><br/>A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.|

1. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
1. Clique em **Criar** na parte inferior do formulário.

    Quando sua conta de serviços de mídia for criada, um ponto de extremidade de streaming **padrão** será adicionado à sua conta no estado **parado** . Para começar a transmitir seu conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a [criptografia dinâmica](content-protection-overview.md), o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **executando** . 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Confira também

* [CLI do Azure](/cli/azure/ams?view=azure-cli-latest)
* [Anexar um armazenamento secundário a uma conta dos serviços de mídia](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
