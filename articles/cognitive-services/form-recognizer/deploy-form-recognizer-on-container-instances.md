---
title: Executar contêiner de reconhecimento de formulário em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Reconhecerr de formulário em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879523"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Implantar o contêiner Reconhecimento de formulário em instâncias de contêiner do Azure

Saiba como implantar o contêiner Cognitive Services [Form Recognizer](form-recognizer-container-howto.md) em Instâncias [de Contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure . Este procedimento demonstra a criação de um recurso Azure Form Recognizer. Em seguida, discutimos puxar a imagem do contêiner associado. Finalmente, destacamos a capacidade de exercitar a orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

> [!IMPORTANT]
> Os recipientes Do Reconhecimento de Formulário atualmente usam a versão 1.0 da API do Reconhecimento de Formulário. Você pode acessar a versão mais recente da API usando o serviço gerenciado em vez disso.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Primeiro, é necessário preencher e enviar o [formulário de solicitação de acesso a Contêineres do Reconhecimento de Formulários dos Serviços Cognitivos](https://aka.ms/FormRecognizerContainerRequestAccess) para solicitar acesso ao contêiner. Fazer isso também fará sua inscrição na Pesquisa Visual Computacional. Você não precisa se inscrever no formulário de solicitação da Pesquisa Visual Computacional separadamente. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
