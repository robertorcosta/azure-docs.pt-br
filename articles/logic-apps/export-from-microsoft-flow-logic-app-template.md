---
title: A exportação flui do Power Automate para o Azure Logic Apps
description: Migrar flui do Power Automate para o Azure Logic Apps exportando como modelos do Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428875"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implantar no Aplicativos Lógicos do Azure

Para ampliar e expandir os recursos do seu fluxo, você pode migrar esse fluxo do [Power Automate](https://flow.microsoft.com) para [o Azure Logic Apps](../logic-apps/logic-apps-overview.md). Você pode exportar seu fluxo como um modelo do Azure Resource Manager para um aplicativo lógico, implantar esse modelo de aplicativo lógico em um grupo de recursos do Azure e, em seguida, abrir esse aplicativo lógico no Logic App Designer.

> [!NOTE]
> Nem todos os conectores Power Automate estão disponíveis no Azure Logic Apps. Você pode importar fluxos que tenham [conectores equivalentes](../connectors/apis-list.md) em Aplicativos azure Logic. Por exemplo, o gatilho button, o conector Aprovação e o conector de notificação são específicos para o Power Automate.
>
> Os fluxos baseados em OpenAPI exportados do Power Automate não são suportados atualmente para implantação como modelos de aplicativos lógicos. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O fluxo que você deseja exportar do Power Automate

## <a name="export-a-flow"></a>Exportar um fluxo

1. Faça login no [Power Automate](https://flow.microsoft.com)e selecione **Meus fluxos**. Encontre e selecione seu fluxo. Na barra de ferramentas, selecione o botão elipses **( ...**). Selecione o modelo De aplicativos lógicos de **exportação** > **(.json)**.

   ![Exportar fluxo](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salve seu modelo no local que você deseja.

Para obter mais informações, consulte [Crescer até a Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implantar modelo usando o portal Azure

1. Faça login no [portal Azure](https://portal.azure.com) com sua conta no Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, digite "implantação de modelo". Selecione **''''Implante', implantar á implantação usando modelos personalizados]** e, em seguida, selecione **Criar**.

   ![Selecione "Implantação de modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implantação personalizada,** **selecione Construa seu próprio modelo no editor**.

   ![Selecione "Construa seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na **barra de ferramentas Editar modelo,** selecione **'Carregar arquivo ''''Editar'.** Encontre e selecione o modelo JSON que você exportou do Power Automate e selecione **Abrir**.

   ![Selecione "Carregar arquivo"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Depois que o editor mostrar o JSON, parâmetros e recursos em seu modelo, **selecione Salvar**.
  
   ![Salvar modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora especifique esses parâmetros de entrada para o modelo:

   * Assinatura do Azure para usar para faturamento
   * Grupo de recursos do Azure
   * Localização para o grupo de recursos do Azure
   * Nome para o recurso do aplicativo lógico
   * Localização para o recurso do aplicativo lógico, se diferente do grupo de recursos do Azure
   * O nome para quaisquer conexões criadas anteriormente que o aplicativo lógico pode reutilizar

      Se você está criando seu primeiro aplicativo lógico, todas as conexões são criadas como novas, para que você possa aceitar os nomes padrão. Caso contrário, você pode especificar os nomes para conexões criadas anteriormente, que você pode usar em vários aplicativos lógicos.

   Depois de fornecer essas informações para o modelo, revise e concorde com os Termos e Condições do Mercado Azure para criar os recursos e faturar sua assinatura do Azure de acordo e, em seguida, selecione **Compra**.
  
   ![Especificar parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   O Azure implanta seu modelo como um aplicativo lógico para o seu grupo de recursos especificado. Todos os aplicativos lógicos que você migra do Power Automate são implantados em um estado desativado.

1. Antes de ativar seu aplicativo lógico, autorize quaisquer novas conexões seguindo estas etapas:

   1. Abra o aplicativo lógico que você criou. No menu do aplicativo lógico, selecione **Logic app designer**.

      Cada conexão que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso:](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar novo**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Faça login em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

1. Salve seu aplicativo lógico. Quando estiver pronto para ativar seu aplicativo lógico, no menu do aplicativo lógico, selecione **Visão geral**e selecione **Ativar**.

   ![Habilitar aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar executar fluxos de trabalho duplicados, certifique-se de desativar ou excluir seu fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implantar modelo usando o Visual Studio

Se você configurou o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicativos lógicos, você pode implantar seu modelo exportado do Visual Studio para o Azure Logic Apps.

1. No Visual Studio, abra o arquivo de modelo que você exportou do Power Automate.

1. No Visual Studio, crie um projeto do Azure Resource Group e selecione o modelo **logic app** seguindo as etapas do [Quickstart: Crie tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps - Visual Studio,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)por exemplo:

   ![Criar um projeto do grupo de recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. A partir do Solution Explorer, abra o arquivo **LogicApp.json,** se o arquivo ainda não estiver aberto.

1. Copie o conteúdo do modelo exportado e sobreescreva o conteúdo no arquivo **LogicApp.json.**

1. Antes de implantar seu aplicativo lógico, autorize quaisquer novas conexões seguindo estas etapas:

   1. Abra o menu de atalho **logicApp.json** e selecione **Abrir com o Logic App Designer**.

      ![Modelo aberto com Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione o grupo de recursos e assinaturas do Azure que você deseja usar para implantar seu aplicativo lógico.

      ![Selecione o grupo de recursos e assinaturas do Azure](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Depois que seu aplicativo lógico aparecer no designer, quaisquer conexões que exijam autorização mostram ícones de aviso:

      ![Conexões com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar novo**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Faça login em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

   1. Salve sua solução antes de implantar o aplicativo lógico.

1. No Solution Explorer, abra o menu de atalho do projeto e **selecione Implantar** > **novo**. Se solicitado, entre com sua conta do Azure.

1. Quando solicitado, confirme a assinatura do Azure, o grupo de recursos Do Azure e quaisquer outras configurações que você deseja usar para implantação, como um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md) para usar para passar valores de parâmetro de modelo e, em seguida, selecione **'Implantar**'

   ![Confirmar configurações de implantação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça o nome do recurso do aplicativo lógico no Azure e selecione **Salvar**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Ao iniciar a implantação, o status da implantação do aplicativo é exibido na janela **Saída** do Visual Studio. Se o status não aparecer, abra a lista **Mostrar saída de** e selecione o grupo de recursos do Azure. Por exemplo: 

   ![Janela de Saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se alguma conexão em seu aplicativo lógico precisar de entrada de você, uma janela PowerShell será aberta em segundo plano e solicita quaisquer senhas ou chaves secretas necessárias. Depois de inserir essas informações, a implantação continuará.

   ![Conexões autenticadas](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após o término da implantação, seu aplicativo lógico é publicado, mas não é ativado no portal Azure.

1. Quando estiver pronto para ativar seu aplicativo lógico no portal Azure, encontre e abra seu aplicativo lógico no Logic App Designer. No menu do aplicativo lógico, selecione **Visão geral**e selecione **Habilitar**.

1. Para evitar executar fluxos de trabalho duplicados, certifique-se de desativar ou excluir seu fluxo original.

Para obter mais informações sobre essas etapas de implantação, consulte [Quickstart: Crie tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores para aplicativos azure logic](../connectors/apis-list.md)
* Saiba mais sobre [os aplicativos azure logic](../logic-apps/logic-apps-overview.md)
