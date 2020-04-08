---
title: Editar e gerenciar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
description: Editar, atualizar, gerenciar, adicionar ao controle de origem e implantar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803183"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerenciar aplicativos lógicos com Visual Studio

Embora você possa criar, editar, gerenciar e implantar aplicativos lógicos no [portal Azure,](https://portal.azure.com)você também pode usar o Visual Studio quando quiser adicionar seus aplicativos lógicos ao controle de origem, publicar diferentes versões e criar modelos [do Azure Resource Manager](../azure-resource-manager/management/overview.md) para vários ambientes de implantação. Com o Microsoft Visual Studio Cloud Explorer, é possível localizar e gerenciar os aplicativos lógicos junto com outros recursos do Azure. Por exemplo, é possível abrir, baixar, editar, executar, exibir histórico de execução, desabilitar e habilitar aplicativos lógicos que já estejam implantados no Portal do Azure. Se você não tiver experiência em trabalhar com Aplicativo Lógico do Azure no Visual Studio, aprenda [como criar aplicativos lógicos com Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Você também pode [gerenciar seus aplicativos lógicos no portal Azure](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> A implantação ou publicação de um aplicativo lógico do Visual Studio substitui a versão desse aplicativo no Portal do Azure. Portanto, se você fizer alterações no Portal do Azure que deseja manter, certifique-se de [atualizar o aplicativo lógico no Visual Studio](#refresh) do Portal do Azure, antes da próxima implantação ou publicação pelo Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Baixe e instale essas ferramentas, caso você ainda não as tenha:

  * [Visual Studio 2019, 2017 ou 2015 – Community Edition ou superior](https://aka.ms/download-visual-studio). Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.
    > Para obter mais informações, consulte [Gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Para instalar o Cloud Explorer para o Visual Studio 2015, [baixe Cloud Explorer do Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Para obter mais informações, consulte [Gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * As Ferramentas dos Aplicativos Lógicos do Azure mais recentes para a extensão do Visual Studio para a versão que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Reinicie o Visual Studio após concluir a instalação.

* Acesso à Web durante o uso do Designer de Aplicativos Lógicos inserido

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Localizar aplicativos lógicos

No Visual Studio, é possível localizar todos os aplicativos lógicos associados à sua assinatura do Azure e que estejam implantados no Portal do Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Exibir**, selecione **Cloud Explorer**.

1. No Cloud Explorer, selecione **Gerenciamento de contas**. Selecione a assinatura do Azure associada aos seus aplicativos lógicos e, em seguida, **selecione Aplicar**. Por exemplo:

   ![Selecione "Gerenciamento de contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Se você estiver pesquisando por **Grupos de Recursos** ou **Tipos de Recursos**, siga estas etapas:

   * **Grupos de Recursos**: na sua assinatura do Azure, o Cloud Explorer mostra todos os grupos de recursos associados a essa assinatura. Expanda o grupo de recursos que contém seu aplicativo lógico e selecione seu aplicativo lógico.

   * **Tipos de Recursos**: na sua assinatura do Azure, expanda **Aplicativos Lógicos**. Após o Cloud Explorer mostrar todos os aplicativos lógicos implementados associados à sua assinatura, selecione o aplicativo lógico.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, você pode abrir aplicativos lógicos criados e implantados diretamente através do portal Azure ou como projetos do Azure Resource Group com o Visual Studio.

1. Abra o Cloud Explorer e localize o aplicativo lógico.

1. No menu de atalho do aplicativo lógico, selecione **Abrir com o Logic App Editor**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

   Esse exemplo mostra aplicativos lógicos por tipo de recurso para que os aplicativos lógicos apareçam na seção **Aplicativos Lógicos**.

   ![Abrir o aplicativo lógico implantado do Portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois que o aplicativo lógico for aberto no Logic Apps Designer, na parte inferior do designer, você pode selecionar **O Code View** para que você possa rever a estrutura de definição do aplicativo lógico subjacente. Se você quiser criar um modelo de implantação para o aplicativo lógico, saiba [como baixar um modelo do Azure Resource Manager ](#download-logic-app) para esse aplicativo lógico. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar pelo Azure

É possível baixar aplicativos lógicos pelo [Portal do Azure](https://portal.azure.com) e salvá-los como modelos do [Azure Resource Manager](../azure-resource-manager/management/overview.md). Em seguida, você poderá editar localmente os modelos com o Visual Studio e personalizar aplicativos lógicos para diferentes ambientes de implantação.  O download de aplicativos lógicos *parametriza * automaticamente suas definições dentro dos [modelos do Resource Manager](../azure-resource-manager/templates/overview.md), que também usam JSON (JavaScript Object Notation).

1. No Visual Studio, abra o Cloud Explorer. Encontre e selecione o aplicativo lógico que deseja baixar do Azure.

1. No menu de atalho sinuoso, selecione **Abrir com o Logic App Editor**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

   O Designer do Aplicativo Lógico é aberto e mostra o aplicativo lógico. Para rever a definição e a estrutura subjacentes do app lógico, na parte inferior do designer, selecione **'Exibição de código '**.

1. Na barra de ferramentas do designer, selecione **Download**.

   ![Baixe o aplicativo lógico do portal Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando for solicitado um local, navegue para esse local e salve o modelo do Resource Manager para a definição de aplicativo lógico no formato de arquivo .json (JSON).

   A definição de aplicativo lógico aparece na subseção `resources` dentro do modelo do Resource Manager. Agora é possível editar a definição de aplicativo lógico e o modelo do Resource Manager com o Visual Studio. Você também pode adicionar o modelo como um [projeto do Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a uma solução do Visual Studio. Conheça [os projetos do Azure Resource Group para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Link para conta de integração

Para criar aplicativos lógicos para cenários de integração empresarial (Business-to-Business) B2B, você pode vincular seu aplicativo lógico a uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) criada anteriormente que existe na mesma região do seu aplicativo lógico. Uma conta de integração contém artefatos B2B, como parceiros de negociação, acordos, esquemas e mapas, e permite que seu aplicativo lógico use conectores B2B para validação XML e codificação ou decodificação de arquivos planos. Embora você possa [criar este link usando o portal Azure,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)você também pode usar o Visual Studio depois de atender aos [pré-requisitos,](#requirements)e seu aplicativo lógico existe como um arquivo JSON (.json) dentro de um [projeto do Azure Resource Group.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Conheça [os projetos do Azure Resource Group para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. No Visual Studio, abra o projeto Azure Resource Group que contém seu aplicativo lógico.

1. No Solution Explorer, abra o menu de atalho **do aplicativo de lógica<>.json** e selecione Abrir com o Logic App **Designer**. (Teclado: Ctrl + L)

   ![Abra o arquivo .json do aplicativo de lógica com o Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tem esse comando no Visual Studio 2019, verifique se você tem as últimas atualizações para o Visual Studio e a extensão Azure Logic Apps Tools.

1. Certifique-se de que o Logic App Designer tenha foco selecionando a guia ou a superfície do designer para que a janela Propriedades mostre a propriedade **Da Conta de Integração** para o seu aplicativo lógico.

   ![Janela de propriedades - propriedade "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, no menu **Exibir,** selecione **''Janela propriedades'.** (Teclado: Pressione F4)

1. Abra a lista de propriedades **da Conta de Integração** e selecione a conta de integração que deseja vincular ao seu aplicativo lógico, por exemplo:

   ![Abra a lista de propriedades "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Quando terminar, lembre-se de salvar sua solução do Visual Studio.

Quando você define a propriedade **Integration Account** no Visual Studio e salva seu aplicativo lógico como um modelo do Azure Resource Manager, esse modelo também inclui uma declaração de parâmetro para a conta de integração selecionada. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [Visão geral: Automatize a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Alterar o local de implantação

No Visual Studio, se o seu aplicativo lógico existe como um arquivo JSON (.json) dentro de um [projeto do Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) que você usa para automatizar a implantação, esse aplicativo lógico é definido como um tipo de local e um local específico. Este local é uma região Azure ou um ambiente de [serviço de integração existente (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Para alterar o tipo de localização ou localização do seu aplicativo lógico, você tem que abrir o arquivo de definição de fluxo de trabalho (.json) do Solution Explorer usando o Logic App Designer. Você não pode alterar essas propriedades usando o Cloud Explorer.

> [!IMPORTANT]
> Alterar o tipo de localização de **Região** para Ambiente de [**Serviço de Integração**](connect-virtual-network-vnet-isolated-environment-overview.md) afeta o modelo de [preços](logic-apps-pricing.md#fixed-pricing) do seu aplicativo lógico que é usado para faturamento, [limites,](logic-apps-limits-and-config.md#integration-account-limits)suporte à conta de [integração](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e assim por diante. Antes de selecionar um tipo de local diferente, certifique-se de entender o impacto resultante em seu aplicativo lógico.

1. No Visual Studio, abra o projeto Azure Resource Group que contém seu aplicativo lógico.

1. No Solution Explorer, `<logic-app-name>.json` abra o menu de atalho do arquivo e selecione **Abrir com o Logic App Designer**. (Teclado: Ctrl + L)

   ![Abra o arquivo .json do aplicativo de lógica com o Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tem esse comando no Visual Studio 2019, verifique se você tem as últimas atualizações para o Visual Studio e a extensão Azure Logic Apps Tools.

1. Certifique-se de que o Logic App Designer tenha foco selecionando a guia ou a superfície do designer para que a janela Propriedades mostre as propriedades **De sacem tipo de local** e **local** para o seu aplicativo lógico. O tipo de localização do projeto é definido como **Região** ou **Ambiente de Serviço de Integração.**

   ![Janela de propriedades - "Escolher tipo de local" & propriedades "Localização"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, no menu **Exibir,** selecione **''Janela propriedades'.** (Teclado: Pressione F4)

1. Para alterar o tipo de local, abra a lista **'Escolher tipo de local'** e selecione o tipo de local que deseja.

   Por exemplo, se o tipo de local for **Ambiente de Serviço de Integração,** você pode selecionar **Região**.

   ![Propriedade "Escolher tipo de localização" - alterar o tipo de local](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Para alterar o local específico, abra a lista de propriedades **Localização.** Com base no tipo de localização, selecione o local que deseja, por exemplo:

   * Selecione uma região diferente do Azure:

     ![Abra a lista de propriedades "Localização", selecione outra região do Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecione uma ISE diferente:

     ![Abrir a lista de propriedades "Localização", selecione outro ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Quando terminar, lembre-se de salvar sua solução do Visual Studio.

Quando você altera o tipo de localização ou localização no Visual Studio e salva seu aplicativo lógico como um modelo do Azure Resource Manager, esse modelo também inclui declarações de parâmetros para esse tipo de local e local. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [Visão geral: Automatize a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar pelo Azure

Se você editar o aplicativo lógico no Portal do Azure e quiser manter essas alterações, atualize a versão do aplicativo no Visual Studio com essas alterações.

* No Visual Studio, na barra de ferramentas logic app designer, selecione **Atualizar**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Atualizar**.

![Atualizar aplicativo lógico com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicativos lógicos

Quando estiver pronto para implantar as atualizações do seu aplicativo lógico do Visual Studio para o Azure, na barra de ferramentas logic app designer, **selecione Publicar**.

![Publique aplicativo lógico atualizado para o portal Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente o aplicativo lógico

É possível disparar um aplicativo lógico manualmente implantado no Azure a partir do Visual Studio. Na barra de ferramentas Logic App Designer, selecione **Run Trigger**.

![Executar manualmente o gatilho para o seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Examinar o histórico de execuções

Para verificar o status e diagnosticar problemas com execuções de aplicativos lógicos, você pode revisar os detalhes, como entradas e saídas, dessas execuções no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Abrir histórico de execução**.

   ![Abra o histórico de execução do seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Para visualizar os detalhes de uma execução específica, clique duas vezes em uma execução. Por exemplo:

   ![Exibir informações sobre execução específica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, selecione o cabeçalho da coluna para essa propriedade.

1. Expanda as etapas cujas entradas e saídas você deseja revisar, por exemplo:

   ![Exibir entradas e saídas para cada etapa](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

Sem excluir o aplicativo lógico, será possível parar o acionamento do gatilho na próxima vez quando a condição do gatilho for atendida. Desabilitar o aplicativo lógico evita que o mecanismo dos Aplicativos Lógicos crie e execute instâncias de fluxo de trabalho futuras para o aplicativo lógico. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Desabilitar**.

![Desative seu aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Ao desabilitar um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes continuarão até que sejam concluídas, e isso poderá demorar algum tempo.

Para reativar seu aplicativo lógico, no Cloud Explorer, abra o menu de atalho do seu aplicativo lógico e selecione **Habilitar**.

![Habilite o aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Excluir seu aplicativo lógico

Para excluir o aplicativo lógico do Portal do Azure, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Excluir**.

![Exclua seu aplicativo lógico do portal Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído. 

## <a name="troubleshooting"></a>Solução de problemas

Quando você abre seu projeto de aplicativo lógico no Designer de Aplicativos Lógicos, talvez não obtenha a opção de selecionar sua assinatura do Azure. Em vez disso, seu aplicativo lógico é aberto com uma assinatura do Azure que não é aquela que você deseja usar. Esse comportamento ocorre porque, depois de abrir o arquivo .json de um aplicativo lógico, o Visual Studio armazena em cache a primeira assinatura selecionada para uso futuro. Para resolver esse problema, tente uma destas etapas:

* Renomeie o arquivo .json do aplicativo lógico. O cache de assinatura depende do nome do arquivo.

* Para remover assinaturas previamente selecionadas para *todos os* aplicativos lógicos em sua solução, exclua a pasta de configurações ocultas do Visual Studio (.vs) no diretório da sua solução. Esse local armazena suas informações de assinatura.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar aplicativos lógicos implantados com Visual Studio. Em seguida, saiba mais sobre como personalizar as definições de aplicativos lógicos para implantação:

> [!div class="nextstepaction"]
> [Criar definições de aplicativo lógico em JSON](../logic-apps/logic-apps-author-definitions.md)
