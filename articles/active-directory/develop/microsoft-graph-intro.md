---
title: API do Microsoft Graph | Microsoft Docs
description: A API do Microsoft Graph é uma API web RESTful que permite acessar os recursos de serviço sustais do Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136493"
---
# <a name="microsoft-graph-api"></a>API do Microsoft Graph

A API do Microsoft Graph é uma API web RESTful que permite acessar os recursos de serviço sustais do Microsoft Cloud. Depois de registrar seu aplicativo e obter tokens de autenticação para um usuário ou serviço, você pode fazer solicitações para a API do Microsoft Graph. Para obter mais informações, consulte [Visão geral do Microsoft Graph](https://docs.microsoft.com/graph/overview).

O Microsoft Graph expõe APIs rest e bibliotecas de clientes para acessar dados nos seguintes serviços da Microsoft 365:
- Serviços do Office 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Serviços de mobilidade corporativa e segurança: Análise avançada de ameaças, proteção avançada contra ameaças, diretório ativo do Azure, gerenciador de identidade e intune
- Serviços do Windows 10: atividades, dispositivos, notificações
- Dynamics 365 Business Central

## <a name="versions"></a>Versões

O Microsoft Graph atualmente suporta duas versões: v1.0 e beta. A versão v1.0 inclui APIs geralmente disponíveis. Use a versão v1.0 para todos os aplicativos de produção. O beta inclui APIs que estão atualmente em pré-visualização. Como podemos introduzir alterações de quebra em nossas APIs beta, recomendamos que você use a versão beta apenas para testar aplicativos que estão em desenvolvimento; não use APIs beta em seus aplicativos de produção. Para obter mais informações, consulte [Versioning, suporte e quebrando políticas de alteração para o Microsoft Graph](https://docs.microsoft.com/graph/versioning-and-support).

Para começar a usar as APIs beta, consulte [a referência de ponto final beta do Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Para começar a usar as APIs v1.0, consulte [a referência Microsoft Graph REST API v1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Introdução

Para ler ou escrever para um recurso, como um usuário ou uma mensagem de e-mail, você constrói uma solicitação que se parece com a seguinte:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Para obter mais informações sobre os elementos da solicitação construída, consulte [Use a API do Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)

As amostras quickstart estão disponíveis para mostrar como acessar o poder da API do Microsoft Graph. As amostras disponíveis acessam dois serviços com uma autenticação: conta Microsoft e Outlook. Cada quickstart acessa informações dos perfis dos usuários da conta da Microsoft e exibe eventos de seu calendário.
As partidas rápidas envolvem quatro etapas:
- Selecione sua plataforma
- Obtenha seu ID de aplicativo (ID do cliente)
- Compilar o exemplo
- Faça login e veja eventos em seu calendário

Quando você completa o quickstart, você tem um aplicativo que está pronto para ser executado. Para obter mais informações, consulte o [Microsoft Graph quickstart FAQ](https://docs.microsoft.com/graph/quick-start-faq). Para começar com as amostras, consulte [Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Ferramentas

O Microsoft Graph Explorer é uma ferramenta baseada na Web que você pode usar para construir e testar solicitações usando APIs do Microsoft Graph. Você pode acessar o `https://developer.microsoft.com/graph/graph-explorer`Microsoft Graph Explorer em: .

Postman é uma ferramenta que você também pode usar para construir e testar solicitações usando as APIs do Microsoft Graph. Você pode baixar Carteiro `https://www.getpostman.com/`em: . Para interagir com o Microsoft Graph em Carteiro, você usa a coleção Microsoft Graph no Carteiro. Para obter mais informações, consulte [Use Postman com a API do Microsoft Graph](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
