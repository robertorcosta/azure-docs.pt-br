---
title: Prepare apis web de chamada de aplicativos móveis para produção | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a criar um aplicativo móvel que chama APIs da Web. (Preparar aplicativos para produção.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882532"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparar aplicativos móveis para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo móvel antes de movê-lo para a produção.

## <a name="handle-errors"></a>Tratar erros

À medida que você prepara um aplicativo móvel para produção, várias condições de erro podem ocorrer. Os principais casos que você vai lidar são falhas silenciosas e recuos na interação. Outras condições que você deve considerar incluem situações sem rede, paralisações de serviços, requisitos para consentimento de admin e outros casos específicos de cenário.

Para cada tipo de MSAL (Microsoft Authentication Library, biblioteca de autenticação da Microsoft), você pode encontrar código de exemplo e conteúdo wiki que descreve como lidar com condições de erro:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Mitigar e investigar questões

Para melhor diagnosticar problemas em seu aplicativo, colete dados. Para obter informações sobre os tipos de dados que você pode coletar, consulte [Login em aplicativos MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Aqui estão algumas sugestões para coleta de dados:

- Os usuários podem pedir ajuda quando tiverem problemas. Uma prática recomendada é capturar e armazenar temporariamente registros. Forneça um local onde os usuários possam carregar os logs. A MSAL fornece extensões de registro para capturar informações detalhadas sobre autenticação.

- Se a telemetria estiver disponível, habilite-a através do MSAL para coletar dados sobre como os usuários se inscrevem no seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Para testar amostras adicionais, consulte [aplicativos de clientes públicos desktop e móveis](sample-v2-code.md#desktop-and-mobile-public-client-apps).
