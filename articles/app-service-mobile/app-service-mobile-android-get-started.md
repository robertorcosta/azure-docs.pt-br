---
title: Criar um aplicativo Android
description: Siga este tutorial para começar a usar o aplicativo móvel do Azure back-ends para desenvolvimento de aplicativos Android.
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9be9402bff1a1263de3c6f21b78899464c50c823
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459559"
---
# <a name="create-an-android-app"></a>Criar um aplicativo Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android móvel usando um back-end de aplicativo móvel do Azure.  Você criará um novo back-end do aplicativo móvel e um aplicativo Android simples com *Lista de tarefas pendentes* que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Android sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Prerequisites
Para concluir este tutorial, você precisará do seguinte:

* [Ferramentas para desenvolvedores do Android](https://developer.android.com/sdk/index.html), que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.
* SDK do Android móveis do Azure.
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end de aplicativo móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Executar o aplicativo Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
