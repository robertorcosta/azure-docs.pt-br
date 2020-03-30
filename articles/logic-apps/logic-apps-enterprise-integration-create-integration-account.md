---
title: Criar ou gerenciar contas de integração B2B
description: Criar, vincular e gerenciar contas de integração para integração corporativa com aplicativos azure logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270323"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Crie e gerencie contas de integração para integrações corporativas B2B em Aplicativos de Lógica do Azure

Antes de criar [soluções de integração corporativa e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) usando [os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você precisa criar uma conta de integração, que é um recurso separado do Azure que fornece uma solução segura, escalonável e o contêiner gerenciável para os artefatos de integração que você define e usa com seus fluxos de trabalho de aplicativo lógico.

Por exemplo, você pode criar, armazenar e gerenciar artefatos B2B, como parceiros comerciais, acordos, mapas, esquemas, certificados e configurações em lote. Além disso, antes que seu aplicativo lógico possa trabalhar com esses artefatos e usar os conectores Logic Apps B2B, você deve [vincular sua conta de integração](#link-account) ao seu aplicativo lógico. Tanto sua conta de integração quanto o aplicativo lógico devem existir no *mesmo* local ou região.

> [!TIP]
> Para criar uma conta de integração dentro de um [ambiente de serviço de integração,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)consulte Criar contas de [integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Este tópico mostra como executar essas tarefas:

* Criar sua conta de integração.
* Vincular sua conta de integração a um aplicativo lógico.
* Alterar o nível de preços para sua conta de integração.
* Desvincule sua conta de integração de um aplicativo lógico.
* Mover sua conta de integração para outra assinatura ou grupo de recursos do Azure.
* Excluir sua conta de integração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Criar conta de integração

Para esta tarefa, você pode usar o portal Azure seguindo as etapas desta seção, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)ou [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, digite "conta de integração" como seu filtro e selecione **Conta de Integração**.

   ![Criar nova conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Em **Conta de Integração,** selecione **Criar**.

   ![Escolha “Adicionar” para criar a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Forneça essas informações sobre sua conta de integração:

   ![Forneça detalhes da conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*nome da conta de integração*> | O nome da sua conta de integração, que pode`-`conter apenas`_`letras, números,`(` `)`hífens`.`( ), sublinha (), parênteses ( , ) e períodos ( ). Este exemplo usa "Fabrikam-Integração". |
   | **Assinatura** | Sim | <*Nome de assinatura do Azure*> | O nome e a ID da assinatura do Azure |
   | **Grupo de recursos** | Sim | <*Nome do grupo azure-resource*> | O nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) para usar para organizar recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome "FabrikamIntegration-RG". |
   | **Nível de preços** | Sim | <*nível de preços*> | A camada de preços para a conta de integração, que você pode alterar mais tarde. Para este exemplo, selecione **Livre**. Para saber mais, consulte esses tópicos: <p>- [Modelo de preços de Aplicativos lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Preços de Aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Local** | Sim | <*Região Azul*> | A região onde armazenar seus metadados da conta de integração. Selecione o mesmo local do seu aplicativo lógico ou crie seus aplicativos lógicos no mesmo local que sua conta de integração. Para este exemplo, use "West US". <p>**Nota:** Para criar uma conta de integração dentro de um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selecione esse ISE como o local. Para obter mais informações, consulte [Criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Análise de log** | Não | Off, On | Mantenha a configuração **off** para este exemplo. |
   |||||

1. Quando terminar, selecione **Criar**.

   Após a conclusão da implantação, o Azure abre sua conta de integração.

   ![Azure abre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Antes que seu aplicativo lógico possa usar sua conta de integração, siga os próximos passos para vincular sua conta de integração e aplicativo lógico juntos.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Vincular ao aplicativo lógico

Para dar aos seus aplicativos lógicos acesso a uma conta de integração que contém seus artefatos B2B, você deve primeiro vincular sua conta de integração ao seu aplicativo lógico. Tanto o app lógico quanto a conta de integração devem existir na mesma região. Para completar esta tarefa, você pode usar o portal Azure. Se você usa o Visual Studio e seu aplicativo lógico está em um [projeto do Azure Resource Group,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)você pode [vincular seu aplicativo lógico a uma conta de integração usando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No [portal Azure,](https://portal.azure.com)abra um aplicativo lógico existente ou crie um novo aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**. Em **Conta Integração,** abra a lista **Selecionar uma conta Integração.** Selecione a conta de integração para vincular ao seu aplicativo lógico.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para finalizar a vinculação, **selecione Salvar**.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Depois que sua conta de integração é vinculada com sucesso, o Azure mostra uma mensagem de confirmação.

   ![O Azure confirma a vinculação com êxito](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Agora, seu aplicativo lógico pode usar os artefatos em sua conta de integração mais os conectores B2B, como validação XML e codificação ou decodificação de arquivos planos.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Alterar tipo de preço

Para aumentar os [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) para uma conta de integração, você pode [atualizar para um nível de preço mais alto,](#upgrade-pricing-tier)se disponível. Por exemplo, você pode atualizar do nível Livre para o nível Básico ou Nível Padrão. Você também pode [fazer o downgrade para um nível inferior,](#downgrade-pricing-tier)se disponível. Para obter mais informações sobre preços, consulte estes tópicos:

* [Preços de Aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Nível de preços de upgrade

Para fazer essa alteração, você pode usar o portal Azure seguindo as etapas desta seção ou o [Azure CLI](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa principal do Azure, digite "contas de integração" como seu filtro e selecione **Contas de Integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja mover. No menu da conta de integração, selecione **Visão geral**.

   ![No menu da conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione **Atualizar o nível de preços**, que lista quaisquer níveis mais altos disponíveis. Quando você seleciona um nível, a alteração imediatamente entra em vigor.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>CLI do Azure

1. Se você ainda não fez isso, [instale os pré-requisitos do Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. No portal Azure, abra o ambiente Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, digite o `skuName` comando de recurso [ **az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)e defina para o nível mais alto que você deseja.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por exemplo, se você tiver o `skuName` nível `Standard`Básico, você pode definir para:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nível de preços de downgrade

Para fazer essa mudança, use o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Se você ainda não fez isso, [instale os pré-requisitos do Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. No portal Azure, abra o ambiente Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, digite o `skuName` comando de recurso [ **az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) e defina para o nível inferior que você deseja.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por exemplo, se você tiver o `skuName` nível `Basic`Padrão, você pode definir para:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desvincular do aplicativo lógico

Se você quiser vincular seu aplicativo lógico a outra conta de integração, ou não usar mais uma conta de integração com seu aplicativo lógico, exclua o link usando o Azure Resource Explorer.

1. Abra a janela do seu navegador e vá para [o Azurehttps://resources.azure.com)Resource Explorer (](https://resources.azure.com). Faça login com as mesmas credenciais de conta do Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Na caixa de pesquisa, digite o nome do seu aplicativo lógico para que você possa encontrar e selecionar seu aplicativo lógico.

   ![Localize e selecione o aplicativo lógico](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na barra de título do explorador, **selecione Ler/Gravar**.

   ![Ativar o modo de "Leitura/Gravação"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na guia **Dados,** selecione **Editar**.

   ![Na guia "Dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. No editor, encontre `integrationAccount` o objeto e exclua essa propriedade, que tem esse formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo: 

   ![Encontre o objeto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na guia **Dados,** selecione **Colocar** para salvar suas alterações.

   ![Para salvar alterações, selecione "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. No portal do Azure, encontre e selecione o aplicativo lógico. Nas **configurações**do fluxo de trabalho do aplicativo, verifique se a propriedade **da conta Integração** agora parece vazia.

   ![Verifique se a conta de integração não está vinculada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover a conta de integração

Você pode mover sua conta de integração para outro grupo de recursos do Azure ou assinatura do Azure. Quando você move recursos, o Azure cria novos IDs de recursos, então certifique-se de usar os novos IDs e atualizar quaisquer scripts ou ferramentas associadas aos recursos movidos. Se você quiser alterar a assinatura, você também deve especificar um grupo de recursos existente ou novo.

Para esta tarefa, você pode usar o portal Azure seguindo as etapas desta seção ou o [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa principal do Azure, digite "contas de integração" como seu filtro e selecione **Contas de Integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja mover. No menu da conta de integração, selecione **Visão geral**.

   ![No menu da conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Ao lado do **grupo Resource** ou do nome **da assinatura,** selecione **alterar**.

   ![Alterar grupo de recursos ou assinatura](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecione todos os recursos relacionados que você também deseja mover.

1. Com base na sua seleção, siga estas etapas para alterar o grupo de recursos ou a assinatura:

   * Grupo de recursos: Na lista **de grupos de recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo de recursos**.

   * Assinatura: Na lista **de assinaturas,** selecione a assinatura de destino. Na lista **de grupos de recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo de recursos**.

1. Para reconhecer o seu entendimento de que quaisquer scripts ou ferramentas associados aos recursos movidos não funcionarão até que você os atualize com os novos IDs de recurso, selecione a caixa de confirmação e selecione **OK**.

1. Depois de terminar, certifique-se de atualizar todos os scripts com os novos IDs de recursos para seus recursos movidos.  

## <a name="delete-integration-account"></a>Excluir conta de integração

Para esta tarefa, você pode usar o portal Azure seguindo as etapas desta seção, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)ou [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa principal do Azure, digite "contas de integração" como seu filtro e selecione **Contas de Integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja excluir. No menu da conta de integração, selecione **Visão geral**.

   ![No menu da conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione **Excluir**.

   ![No painel "Visão geral", selecione "Excluir"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar se deseja excluir sua conta de integração, selecione **Sim**.

   ![Para confirmar a exclusão, selecione "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Próximas etapas

* [Crie parceiros comerciais em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Crie acordos entre parceiros em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
