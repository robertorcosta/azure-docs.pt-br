---
title: Criar um aplicativo Cordova nos Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Docs
description: Siga este tutorial para começar a usar back-ends de aplicativos móveis do Azure para desenvolvimento do Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,móvel,cliente
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a1baa26fee46a4bba5db9652cf1e65e9d473284a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388840"
---
# <a name="create-an-apache-cordova-app"></a>Criar um aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Apache Cordova móvel usando um back-end de aplicativo móvel do Azure.  Você criará um novo back-end do aplicativo móvel e um aplicativo Apache Cordova simples com *Lista de tarefas pendentes* que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Apache Cordova sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Um computador com o [Visual Studio Community 2017] ou mais recente.
* [Ferramentas do Visual Studio para Apache Cordova].
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

Você também pode ignorar o Visual Studio e usar a linha de comando do Apache Cordova diretamente.  Usar a linha de comando é útil ao concluir o tutorial em um computador Mac.  Compilar aplicativos de cliente do Apache Cordova usando a linha de comando não é coberto por este tutorial.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end de aplicativo móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Baixe e execute o aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
