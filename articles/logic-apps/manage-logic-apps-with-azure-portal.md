---
title: Gerenciar aplicativos lógicos no portal Azure
description: Editar, ativar, desativar ou excluir aplicativos lógicos usando o portal Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/06/2020
ms.openlocfilehash: a35f34ed27299077dca78630511ea3c291caff60
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804969"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gerenciar aplicativos lógicos no portal Azure

Você pode gerenciar aplicativos lógicos usando o [portal Azure](https://portal.azure.com) ou [o Visual Studio](manage-logic-apps-with-visual-studio.md). Este artigo mostra como editar, desativar, ativar ou excluir aplicativos lógicos no portal Azure. Se você é novo no Azure Logic Apps, veja [o que é o Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo lógico existente. Para saber como criar um aplicativo lógico no portal Azure, consulte [Quickstart: Crie seu primeiro fluxo de trabalho usando o Azure Logic Apps - portal Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Localizar aplicativos lógicos

Para encontrar e abrir seu aplicativo lógico, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

1. Na barra de pesquisa do `logic apps`Azure, digite e selecione **Logic Apps**.

   ![Encontrar e selecionar "Aplicativos Lógicos"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na página **Logic Apps,** encontre e selecione o aplicativo lógico que deseja gerenciar.

   Depois que o painel **visão geral** do aplicativo lógico for aberto, você pode filtrar a lista que aparece na página **Aplicativos lógicos** desta forma:

   * Procure por aplicativos lógicos pelo nome
   * Filtrar aplicativos lógicos por assinatura, grupo de recursos, localização e tags
   * Aplicativos lógicos de grupo por grupo de recursos, tipo, assinatura e localização

## <a name="view-logic-app-properties"></a>Ver propriedades do aplicativo lógico

1. No portal Azure, [encontre e abra seu aplicativo lógico](#find-logic-app).

1. No menu do aplicativo lógico, em **Configurações,** selecione **Propriedades**.

1. No painel **Propriedades,** você pode visualizar e copiar as seguintes informações sobre o seu aplicativo lógico:

   * **Nome**
   * **ID de recursos**
   * **Grupo de recursos**
   * **Localidade**
   * **Tipo** 
   * **Nome da assinatura**
   * **ID de assinatura**
   * **Ponto final de acesso**
   * **Endereços IP de saída em tempo de execução**
   * **Acesso endereços IP de ponto final**
   * **Endereços IP de saída do conector**

## <a name="disable-or-enable-logic-apps"></a>Desativar ou ativar aplicativos lógicos

Você pode ativar ou desativar um [único aplicativo de lógica](#disable-enable-single-logic-app) ou aplicativos de lógica múltipla ao mesmo [tempo](#disable-or-enable-multiple-logic-apps) no portal Azure. Você também pode [ativar ou desativar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Desativar seu aplicativo lógico afeta suas instâncias de fluxo de trabalho e é executado desta maneira:

* Todos os trabalhos em andamento e pendentes continuam até que terminem. Dependendo do número dessas corridas, esse processo pode levar algum tempo.

* O mecanismo Logic Apps não criará ou executará novas instâncias de fluxo de trabalho.

* O gatilho não disparará da próxima vez que suas condições forem atendidas.

* O estado de gatilho lembra o ponto em que o aplicativo lógico foi interrompido. Então, se você reativar o aplicativo lógico, o gatilho será acionado para todos os itens não processados desde a última execução.

  Para impedir que seu aplicativo lógico seja acionado em itens não processados desde a última execução, limpe o estado do gatilho antes de reativar o aplicativo lógico:

  1. No portal Azure, [encontre e abra seu aplicativo lógico](#find-logic-app).

  1. Edite qualquer parte do gatilho do aplicativo lógico.

  1. Salve suas alterações. Esta etapa redefine o estado atual do seu gatilho.

  1. [Reative seu aplicativo lógico](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Desativar ou ativar um aplicativo de lógica única

1. No portal Azure, [encontre e abra seu aplicativo lógico](#find-logic-app).

1. No menu do aplicativo lógico, selecione **Visão geral**. Escolha entre essas opções:

   * Na barra de ferramentas, selecione **Desabilitar**.

     ![Desativar aplicativo de lógica única no portal Azure](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se o seu aplicativo lógico já estiver desativado, você só verá a opção **Ativar.**

   * Na barra de ferramentas, selecione **Ativar**.

     ![Habilite o aplicativo de lógica única no portal Azure](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se o seu aplicativo lógico já estiver habilitado, você só verá a opção **Desativar.** 

   O portal Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se sua operação foi bem sucedida ou não.

   ![Notificação para confirmar o status da operação](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Desativar ou ativar vários aplicativos lógicos

1. No portal Azure, [encontre os aplicativos lógicos](#find-logic-app) que você deseja desativar ou ativar.

1. Para verificar se um aplicativo lógico está habilitado ou desativado, na página **Logic Apps,** revise a coluna **Status** para esse aplicativo lógico. 

   ![Coluna de status de Aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se a coluna **Status** não estiver visível, na barra de **ferramentas Logic Apps,** selecione **'Tentar visualizar '''Visualizar'.**

   ![Ativar visualização](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Na coluna caixa de seleção, selecione os aplicativos lógicos que deseja desativar ou ativar. Na barra de ferramentas, **selecione Desativar** ou **Habilitar**.

   ![Habilite ou desative vários aplicativos lógicos no portal Azure](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando a caixa de confirmação aparecer, selecione **Sim** para continuar.

   O portal Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se sua operação foi bem sucedida ou não.

## <a name="delete-logic-apps"></a>Excluir aplicativos lógicos

Você pode [excluir um único aplicativo lógico](#delete-single-logic-app) ou excluir vários aplicativos [lógicos ao mesmo tempo](#delete-multiple-logic-apps) no portal Azure. Você também pode [excluir seu aplicativo lógico no Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

A exclusão do aplicativo lógico afeta as instâncias do fluxo de trabalho desta maneira:

* Todos os trabalhos em andamento e pendentes continuam até que terminem. Dependendo do número dessas corridas, esse processo pode levar algum tempo.

* O mecanismo Logic Apps não criará ou executará novas instâncias de fluxo de trabalho.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Excluir aplicativo de lógica única

1. No portal Azure, [encontre e abra seu aplicativo lógico](#find-logic-app).

1. No menu do aplicativo lógico, selecione **Visão geral**. Na barra de ferramentas do seu aplicativo lógico, selecione **Excluir**.

   ![Na barra de ferramentas do aplicativo lógico, selecione "Excluir"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando a caixa de confirmação aparecer, digite o nome do aplicativo lógico e **selecione Excluir**.

   ![Confirme para excluir seu aplicativo lógico](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   O portal Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se sua operação foi bem sucedida ou não.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Exclua vários aplicativos lógicos

1. No portal Azure, [encontre os aplicativos lógicos que você deseja excluir](#find-logic-app).

1. Na coluna caixa de seleção, selecione os aplicativos lógicos que deseja excluir. Na barra de ferramentas, selecione **Excluir**.

   ![Exclua vários aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando a caixa de `yes`confirmação aparecer, digite e **selecione Excluir**.

   ![Confirme para excluir seus aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   O portal Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se sua operação foi bem sucedida ou não.

## <a name="next-steps"></a>Próximas etapas

* [Monitorar aplicativos lógicos](monitor-logic-apps.md)
