---
title: Acessar a API dos Serviços de Mídia do Azure – CLI do Azure | Microsoft Docs
description: Siga as etapas deste artigo de instruções para acessar a API dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 06ca26ee9a8af7f49aba6a48a1831d30d5886851
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896335"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Acessar a API dos Serviços de Mídia do Azure com a CLI do Azure
 
Para usar a autenticação de entidade de serviço do Azure AD para se conectar à API dos serviços de mídia do Azure, seu aplicativo precisa solicitar um token do Azure AD que tenha os seguintes parâmetros:

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia
* URI de recurso dos Serviços de Mídia REST
* Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente

> [!IMPORTANT]
> Para obter uma explicação detalhada, consulte [acessando as APIs do Media Services V3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Este artigo mostra como usar a CLI do Azure para criar um aplicativo e uma entidade de serviço do Azure AD e obter os valores necessários para acessar os recursos dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Consulte também

- [Dimensionar as unidades reservadas para mídia – CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta dos Serviços de Mídia – CLI](create-account-cli-how-to.md) 
- [Redefinir as credenciais da conta – CLI](cli-reset-account-credentials.md)
- [Criar ativos – CLI](cli-create-asset.md)
- [Carregar um arquivo – CLI](cli-upload-file-asset.md)
- [Criar transformações – CLI](cli-create-transform.md)
- [Codificar com uma transformação personalizada-CLI](custom-preset-cli-howto.md)
- [Criar trabalhos – CLI](cli-create-jobs.md)
- [Criar uma EventGrid – CLI](job-state-events-cli-how-to.md)
- [Publicar um ativo – CLI](cli-publish-asset.md)
- [Filtro – CLI](filters-dynamic-manifest-cli-howto.md)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Próximos passos

O ponto de extremidade de streaming do qual você deseja transmitir conteúdo deve estar no estado executando. O comando da CLI a seguir inicia o ponto de extremidade de streaming padrão:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

