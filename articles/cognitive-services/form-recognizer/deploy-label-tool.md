---
title: Como implantar a ferramenta de rótulo de exemplo do reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes maneiras de implantar a ferramenta de rotulação de amostra do reconhecedor de formulário para ajudar com o aprendizado supervisionado.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207839"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implantar a ferramenta de rótulo de exemplo

A ferramenta de rotulagem de amostra do reconhecedor de formulário é um aplicativo que é executado em um contêiner do Docker. Ele fornece uma interface do usuário útil que pode ser usada para rotular manualmente documentos de formulário com a finalidade de aprendizado supervisionado. O guia de início rápido [treinar com rótulos](./quickstarts/label-tool.md) mostra como executar a ferramenta em seu computador local, que é o cenário mais comum. 

Este guia explicará maneiras alternativas de implantar e executar a ferramenta de rotulagem de exemplo. 

## <a name="deploy-with-azure-container-instances"></a>Implantar com instâncias de contêiner do Azure

Você pode executar a ferramenta rótulo em um contêiner do aplicativo Web Docker. Primeiro, [crie um novo recurso de aplicativo Web](https://ms.portal.azure.com/#create/Microsoft.WebSite) no portal do Azure. Preencha o formulário com os detalhes do grupo de recursos e da assinatura. Insira as seguintes informações nos campos obrigatórios:
* **Publicar**: contêiner do Docker
* **Operação** Sistema: Linux

Na próxima página, preencha os seguintes campos para a configuração do contêiner do Docker:

* **Opções**: contêiner único
* **Origem da imagem**: registro de contêiner do Azure
* **Tipo de acesso**: público
* **Imagem e marca**: MCR.Microsoft.com/Azure-cognitive-Services/Custom-Form/labeltool:Latest

As etapas a seguir são opcionais. Depois que o aplicativo terminar a implantação, você poderá executá-lo e acessar a ferramenta de rótulo online.

### <a name="connect-to-azure-ad-for-authorization"></a>Conectar-se ao Azure AD para autorização

É recomendável conectar seu aplicativo Web ao Azure Active Directory (AAD) para que somente alguém com suas credenciais possa entrar e usar o aplicativo. Siga as instruções em [configurar seu aplicativo do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) para se conectar ao AAD.

## <a name="next-steps"></a>Próximas etapas

Retorne ao guia [de início rápido treinamento para](./quickstarts/label-tool.md) aprender a usar a ferramenta para rotular manualmente os dados de treinamento e fazer aprendizado supervisionado.
