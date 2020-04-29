---
title: Criar uma conta de Serviços de Mídia do Azure
description: Este tutorial orienta você pelas etapas de criação de uma conta dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79478793"
---
# <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Para iniciar a criptografia, codificação, analise, gerenciamento e streaming de conteúdo de mídia no Azure, você precisa criar uma conta dos Serviços de Mídia. A conta dos Serviços de Mídia precisa estar associada a uma ou mais contas de armazenamento.

> [!NOTE]
> A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.

Este artigo descreve as etapas para criar uma nova conta dos serviços de mídia do Azure. Escolha entre as guias a seguir.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O portal do Azure fornece uma maneira de criar rapidamente uma conta dos serviços de mídia do Azure. Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure.

No momento, você pode usar o [portal do Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-events-outputs-concept.md)dos serviços de mídia v3, 
* Exibir (não gerenciar) os [ativos](assets-concept.md)v3, 
* [Obtenha informações sobre como acessar APIs](access-api-portal.md). 

Para todas as outras tarefas de gerenciamento (por exemplo, [transformações e trabalhos](transforms-jobs-concept.md) e [proteção de conteúdo](content-protection-overview.md)), use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.

Este artigo mostra como criar uma conta dos Serviços de Mídia usando o portal do Azure.

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **+ criar um recurso** > **Media** > Media**Services**.
1. Na seção **criar uma conta dos serviços de mídia** , insira os valores necessários.
    
    | Name | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos serviços de mídia. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.|
    |**Assinatura**|Se você tiver mais de uma assinatura, selecione uma na lista de assinaturas do Azure às quais você tem acesso.|
    |**Grupo de recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Local**|Selecione a região geográfica que será usada para armazenar os registros de mídia e de metadados para sua conta de serviços de mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. |
    |**Conta de armazenamento**|Selecione uma conta de armazenamento para fornecer o armazenamento de BLOBs do conteúdo de mídia da sua conta de serviços de mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.<br/><br/>Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Você pode usar o portal do Azure para adicionar contas de armazenamento secundárias. Para obter mais informações, consulte [contas de armazenamento do Azure com contas dos serviços de mídia do Azure](storage-account-concept.md).<br/><br/>A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.|
    
1. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
1. Clique em **Criar** na parte inferior do formulário.

    Quando sua conta de serviços de mídia for criada, um ponto de extremidade de streaming **padrão** será adicionado à sua conta no estado **parado** . Para começar a transmitir seu conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a [criptografia dinâmica](content-protection-overview.md), o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **executando** . 

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando a seguir. Um grupo de recursos do Azure é um contêiner lógico em que recursos, como contas dos Serviços de Mídia do Azure e contas de armazenamento associadas, são implantados e gerenciados.

Você pode substituir `amsResourceGroup` por seu valor.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. Para saber mais sobre como as contas de armazenamento são usadas nos Serviços de Mídia, confira [Contas de armazenamento](storage-account-concept.md).

Você deve ter uma conta de armazenamento **principal** e pode ter qualquer número de contas de armazenamento **secundárias** associadas à sua conta de serviços de mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente blob não são permitidas como **Primárias**. Se quiser saber mais sobre as contas de armazenamento, confira [Opções de conta de Armazenamento do Azure](../../storage/common/storage-account-options.md). 

Neste exemplo, criamos uma conta de uso geral v2, LRS Padrão. Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. Para obter mais informações, confira [Contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
O comando a seguir cria uma conta de armazenamento que será associada à conta dos Serviços de Mídia. No script a seguir, você pode substituir `storageaccountforams` pelo seu valor. `amsResourceGroup`deve corresponder ao valor que você deu para o grupo de recursos na etapa anterior. O nome da conta de armazenamento deve ter comprimento menor que 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

O comando da CLI do Azure a seguir cria uma nova conta dos Serviços de Mídia. Você pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor que você forneceu para sua conta de armazenamento `amsResourceGroup` ) e (deve corresponder ao valor que você deu para o grupo de recursos).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Confira também

* [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Anexar um armazenamento secundário a uma conta dos serviços de mídia](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
