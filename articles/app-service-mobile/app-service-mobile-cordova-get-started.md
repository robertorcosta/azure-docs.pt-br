---
title: Crie um aplicativo Cordova
description: Siga este tutorial para começar a usar back-ends de aplicativos móveis do Azure para desenvolvimento do Apache Cordova
keywords: cordova,javascript,móvel,cliente
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 99fb4a4b07ecbd4a85abbc62ec52a0f5960654c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461565"
---
# <a name="create-an-apache-cordova-app"></a>Criar um aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral
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

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Crie uma conexão de banco de dados e configure o projeto cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Baixe e execute o aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Ferramentas visuais do estúdio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
