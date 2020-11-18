---
title: Tutorial – Limpar recursos | Azure
description: Neste tutorial, você aprenderá a limpar os recursos do Azure alocados ao criar o aplicativo Web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428125"
---
# <a name="tutorial-clean-up-resources"></a>Tutorial: limpar recursos

Se você concluiu todas as etapas neste tutorial de várias partes, isso significa que você criou um serviço de aplicativo, um plano de hospedagem do serviço de aplicativo e uma conta de armazenamento em um grupo de recursos.  Você também criou um registro de aplicativo no Azure AD.  Quando não for mais necessário, exclua esses recursos e o registro de aplicativo para que você não continue a acumular encargos.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Excluir os recursos do Azure criados durante as etapas do tutorial

## <a name="delete-the-resource-group"></a>Exclua o grupo de recursos
No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** no menu do portal e selecione o grupo de recursos que contém o serviço de aplicativo e o plano do serviço de aplicativo.

Selecione **Excluir grupo de recursos** para excluir o grupo de recursos e todos os recursos que ele contém.

Excluir grupo de recursos

Este comando pode levar vários minutos para ser executado.

## <a name="delete-the-app-registration"></a>Excluir o registro do aplicativo
No menu do portal, selecione **Azure Active Directory**, **Registros de aplicativo** e o aplicativo que você criou.
:::image type="content" alt-text="Selecionar registro do aplicativo" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Na visão geral do registro de aplicativo, selecione **Excluir**.
:::image type="content" alt-text="Excluir registro do aplicativo" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Excluir os recursos do Azure criados durante as etapas do tutorial

Saiba como conectar um [aplicativo .NET Core](tutorial-dotnetcore-sqldb-app.md), [aplicativo Python](tutorial-python-postgresql-app.md), [aplicativo Java](tutorial-java-spring-cosmosdb.md) ou [aplicativo Node.js](tutorial-nodejs-mongodb-app.md) a um banco de dados.