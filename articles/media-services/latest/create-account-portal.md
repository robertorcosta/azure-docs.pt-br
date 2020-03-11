---
title: Criar uma conta do Serviços de Mídia do Azure com o portal do Azure
description: Este tutorial orienta você pelas etapas de criação de uma conta do Serviços de Mídia do Azure com o portal do Azure.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: c90278eccf889595378d3b6b07de2468910c660c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080869"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Usar o portal do Azure para criar uma conta de serviços de mídia

O portal do Azure fornece uma maneira de criar rapidamente uma conta dos serviços de mídia do Azure. Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure.

No momento, você pode usar o [portal do Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-events-outputs-concept.md)dos serviços de mídia v3, 
* Exibir (não gerenciar) os [ativos](assets-concept.md)v3, 
* [Obtenha informações sobre como acessar APIs](access-api-portal.md). 

Para todas as outras tarefas de gerenciamento (por exemplo, [transformações e trabalhos](transforms-jobs-concept.md) e [proteção de conteúdo](content-protection-overview.md)), use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.

Este artigo mostra como criar uma conta dos Serviços de Mídia usando o portal do Azure.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **+ criar um recurso** > **mídia** > **serviços de mídia**.
1. Na seção **criar uma conta dos serviços de mídia** , insira os valores necessários.
    
    | {1&gt;Nome&lt;1} | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos serviços de mídia. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.|
    |**Assinatura**|Se você tiver mais de uma assinatura, selecione uma na lista de assinaturas do Azure às quais você tem acesso.|
    |**Grupo de recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localidade**|Selecione a região geográfica que será usada para armazenar os registros de mídia e de metadados para sua conta de serviços de mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. |
    |**Conta de armazenamento**|Selecione uma conta de armazenamento para fornecer o armazenamento de BLOBs do conteúdo de mídia da sua conta de serviços de mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.<br/><br/>Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Você pode usar o portal do Azure para adicionar contas de armazenamento secundárias. Para obter mais informações, consulte [contas de armazenamento do Azure com contas dos serviços de mídia do Azure](storage-account-concept.md).<br/><br/>A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.|
    
1. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
1. Clique em **Criar** na parte inferior do formulário.
   
    Quando a conta é criada com êxito, a página de visão geral é carregada. Na tabela ponto de extremidade de streaming, a conta terá um ponto de extremidade de streaming padrão no estado **parado** . 

    Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para começar a transmitir seu conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a [criptografia dinâmica](content-protection-overview.md), o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **executando** . 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se você planeja acessar a API dos serviços de mídia programaticamente, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](access-api-portal.md).

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

