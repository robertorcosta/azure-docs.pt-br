---
title: Gerenciar aplicativos lógicos no portal do Azure
description: Edite, habilite, desabilite ou exclua aplicativos lógicos usando o portal do Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415935"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gerenciar aplicativos lógicos no portal do Azure

Você pode gerenciar aplicativos lógicos usando o [portal do Azure](https://portal.azure.com) ou o [Visual Studio](manage-logic-apps-with-visual-studio.md). Este artigo mostra como editar, desabilitar, habilitar ou excluir aplicativos lógicos no portal do Azure. Se você for novo nos aplicativos lógicos do Azure, consulte [o que são os aplicativos lógicos do Azure](logic-apps-overview.md)?

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo lógico existente. Para saber como criar um aplicativo lógico no portal do Azure, consulte [início rápido: criar seu primeiro fluxo de trabalho usando aplicativos lógicos do Azure-portal do Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Localizar aplicativos lógicos

Para localizar e abrir seu aplicativo lógico, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

1. Na barra do Azure Search, insira `logic apps`e selecione **aplicativos lógicos**.

   ![Encontrar e selecionar "Aplicativos Lógicos"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na página **aplicativos lógicos** , localize e selecione o aplicativo lógico que você deseja gerenciar.

   Depois que o painel **visão geral** do aplicativo lógico for aberto, você poderá filtrar a lista que aparece na página **aplicativos lógicos** das seguintes maneiras:

   * Pesquisar por aplicativos lógicos por nome
   * Filtrar aplicativos lógicos por assinatura, grupo de recursos, local e marcas
   * Agrupar aplicativos lógicos por grupo de recursos, tipo, assinatura e local

## <a name="view-logic-app-properties"></a>Exibir Propriedades do aplicativo lógico

1. Na portal do Azure, [Localize e abra seu aplicativo lógico](#find-logic-app).

1. No menu do aplicativo lógico, em **configurações**, selecione **Propriedades**.

1. No painel **Propriedades** , você pode exibir e copiar as seguintes informações sobre seu aplicativo lógico:

   * **Nome**
   * **ID do recurso**
   * **Grupo de recursos**
   * **Local**
   * **Tipo** 
   * **Nome da assinatura**
   * **ID da assinatura**
   * **Ponto de extremidade de acesso**
   * **Endereços IP de saída do tempo de execução**
   * **Endereços IP do ponto de extremidade de acesso**
   * **Endereços IP de saída do conector**

## <a name="disable-or-enable-logic-apps"></a>Desabilitar ou habilitar aplicativos lógicos

Você pode habilitar ou desabilitar um [único aplicativo lógico](#disable-enable-single-logic-app) ou [vários aplicativos lógicos ao mesmo tempo](#disable-or-enable-multiple-logic-apps) no portal do Azure. Você também pode [habilitar ou desabilitar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Desabilitar seu aplicativo lógico afeta suas instâncias de fluxo de trabalho e é executado das seguintes maneiras:

* Todas as execuções em andamento e pendentes continuam até que sejam concluídas. Dependendo do número dessas execuções, esse processo pode levar algum tempo.

* O mecanismo de aplicativos lógicos não criará ou executará novas instâncias de fluxo de trabalho.

* O gatilho não será acionado na próxima vez que suas condições forem atendidas.

* O estado do gatilho lembra o ponto em que o aplicativo lógico foi interrompido. Portanto, se você reabilitar o aplicativo lógico, o gatilho será acionado para todos os itens não processados desde a última execução.

  Para impedir que o aplicativo lógico seja acionado em itens não processados desde a última execução, limpe o estado do gatilho antes de reabilitar o aplicativo lógico:

  1. Na portal do Azure, [Localize e abra seu aplicativo lógico](#find-logic-app).

  1. Edite qualquer parte do gatilho do aplicativo lógico.

  1. Salve suas alterações. Esta etapa redefine o estado atual do gatilho.

  1. [Habilite novamente seu aplicativo lógico](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico único

1. Na portal do Azure, [Localize e abra seu aplicativo lógico](#find-logic-app).

1. No menu do seu aplicativo lógico, selecione **visão geral**. Escolha uma destas opções:

   * Na barra de ferramentas, selecione **Desabilitar**.

     ![Desabilitar aplicativo lógico único no portal do Azure](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se seu aplicativo lógico já estiver desabilitado, você verá apenas a opção **habilitar** .

   * Na barra de ferramentas, selecione **habilitar**.

     ![Habilitar aplicativo lógico único no portal do Azure](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se seu aplicativo lógico já estiver habilitado, você verá apenas a opção **desabilitar** . 

   O portal do Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se a operação foi bem-sucedida ou falhou.

   ![Notificação para confirmar o status da operação](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Desabilitar ou habilitar vários aplicativos lógicos

1. Na portal do Azure, [Localize os aplicativos lógicos](#find-logic-app) que você deseja desabilitar ou habilitar.

1. Para verificar se um aplicativo lógico está habilitado ou desabilitado no momento, na página **aplicativos lógicos** , examine a coluna **status** desse aplicativo lógico. 

   ![Coluna de status de aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se a coluna **status** não estiver visível, na barra de ferramentas **aplicativos lógicos** , selecione **tentar visualização**.

   ![Ativar visualização](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Na coluna caixa de seleção, selecione os aplicativos lógicos que você deseja desabilitar ou habilitar. Na barra de ferramentas, selecione **desabilitar** ou **habilitar**.

   ![Habilitar ou desabilitar vários aplicativos lógicos no portal do Azure](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando a caixa de confirmação for exibida, selecione **Sim** para continuar.

   O portal do Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se a operação foi bem-sucedida ou falhou.

## <a name="delete-logic-apps"></a>Excluir aplicativos lógicos

Você pode [excluir um único aplicativo lógico](#delete-single-logic-app) ou [excluir vários aplicativos lógicos ao mesmo tempo](#delete-multiple-logic-apps) no portal do Azure. Você também pode [excluir seu aplicativo lógico no Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Excluir seu aplicativo lógico afeta suas instâncias de fluxo de trabalho das seguintes maneiras:

* Todas as execuções em andamento e pendentes continuam até que sejam concluídas. Dependendo do número dessas execuções, esse processo pode levar algum tempo.

* O mecanismo de aplicativos lógicos não criará ou executará novas instâncias de fluxo de trabalho.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Excluir aplicativo lógico único

1. Na portal do Azure, [Localize e abra seu aplicativo lógico](#find-logic-app).

1. No menu do seu aplicativo lógico, selecione **visão geral**. Na barra de ferramentas do aplicativo lógico, selecione **excluir**.

   ![Na barra de ferramentas do aplicativo lógico, selecione "excluir"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando a caixa de confirmação for exibida, insira o nome do aplicativo lógico e selecione **excluir**.

   ![Confirmar para excluir seu aplicativo lógico](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   O portal do Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se a operação foi bem-sucedida ou falhou.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Excluir vários aplicativos lógicos

1. Na portal do Azure, [Localize os aplicativos lógicos que você deseja excluir](#find-logic-app).

1. Na coluna caixa de seleção, selecione os aplicativos lógicos que você deseja excluir. Na barra de ferramentas, selecione **Excluir**.

   ![Excluir vários aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando a caixa de confirmação for exibida `yes`, insira e selecione **excluir**.

   ![Confirmar para excluir seus aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   O portal do Azure mostra uma notificação na barra de ferramentas principal do Azure que confirma se a operação foi bem-sucedida ou falhou.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Gerenciar versões do aplicativo lógico

Você pode usar o portal do Azure para controle de versão de seus aplicativos lógicos. Você pode encontrar o histórico de versão do aplicativo lógico e promover versões anteriores.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Localizar e exibir versões anteriores

1. Na portal do Azure, [localize o aplicativo lógico que você deseja gerenciar](#find-logic-app).

1. No menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione **versões**.

   ![No menu do seu aplicativo lógico, selecione "versões" em "ferramentas de desenvolvimento"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Selecione a **versão** do seu aplicativo lógico para gerenciar na lista. Você pode inserir o identificador de **versão** na barra de pesquisa para filtrar a lista.

1. Na página **versão do histórico** , você verá os detalhes da versão anterior no modo somente leitura. Você pode selecionar entre os modos **Designer** de aplicativos lógicos e **modo de exibição de código** .

   ![Página de versão do histórico para o aplicativo lógico com exibição de código e exibição do designer de aplicativos lógicos](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Promover versões anteriores

1. No histórico de versão do aplicativo lógico, [Localize e selecione a versão que você deseja promover](#find-version-history).

1. Na página **versão do histórico** , selecione **promover**.

   ![Botão promover no histórico de versão do aplicativo lógico](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na página **Designer de aplicativos lógicos** que é aberta, edite a versão que você está promovendo conforme necessário. Você pode alternar entre os modos de exibição de **Designer** e de **código** . Você também pode atualizar **parâmetros**, **modelos**e **conectores**.

   ![Página do designer de aplicativos lógicos para promover uma versão anterior](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Para salvar as atualizações e concluir a promoção da versão anterior, selecione **salvar**. (Ou, para cancelar as alterações, selecione **descartar**.) 

   Quando você [Exibir o histórico de versão do aplicativo lógico](#find-version-history) novamente, a versão promovida aparecerá na parte superior da lista e terá um novo identificador.

## <a name="next-steps"></a>Próximas etapas

* [Monitorar aplicativos lógicos](monitor-logic-apps.md)
