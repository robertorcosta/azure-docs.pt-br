---
title: Tutorial – criar um aplicativo Web seguro no Serviço de Aplicativo do Azure | Azure
description: Neste tutorial, você aprenderá a criar um aplicativo Web usando o Serviço de Aplicativo do Azure, a habilitar a autenticação, a chamar o armazenamento do Azure e a chamar o Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428138"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutorial: habilitar a autenticação no Serviço de Aplicativo e no armazenamento de acesso e no Microsoft Graph

Este tutorial descreve um cenário de aplicativo comum em que você aprende a:

- [(A) Configurar a autenticação para um aplicativo Web](scenario-secure-app-authentication-app-service.md) e limitar o acesso para usuários em sua organização.
- [(B) Acessar com segurança o armazenamento do Azure](scenario-secure-app-access-storage.md) em nome do aplicativo Web usando identidades gerenciadas.
- (C) Acessar dados no Microsoft Graph [em nome do usuário conectado](scenario-secure-app-access-microsoft-graph-as-user.md) ou [em nome do aplicativo Web](scenario-secure-app-access-microsoft-graph-as-app.md) usando identidades gerenciadas.
- [Limpe os recursos](scenario-secure-app-clean-up-resources.md) que você criou para esse tutorial.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Cenários de aplicativos na plataforma de identidade da Microsoft" border="false":::

Para começar, saiba como habilitar a autenticação para um aplicativo Web.

> [!div class="nextstepaction"]
> [Configurar a autenticação para um aplicativo Web](scenario-secure-app-authentication-app-service.md)
