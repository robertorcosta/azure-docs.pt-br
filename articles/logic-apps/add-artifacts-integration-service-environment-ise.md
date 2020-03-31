---
title: Adicionar recursos aos ambientes de serviços de integração
description: Adicione aplicativos lógicos, contas de integração, conectores personalizados e conectores gerenciados ao seu ambiente de serviço de integração (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164872"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicione recursos ao seu ambiente de serviço de integração (ISE) em Aplicativos lógicos do Azure

Depois de criar um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)adicione recursos como aplicativos lógicos, contas de integração e conectores para que eles possam acessar os recursos em sua rede virtual Do Azure. Por exemplo, conectores ISE gerenciados que ficam disponíveis depois de criar seu ISE não aparecem automaticamente no Logic App Designer. Antes de usar esses conectores ISE, você tem que adicionar e implantar manualmente [esses conectores no seu ISE para](#add-ise-connectors-environment) que eles apareçam no Logic App Designer.

> [!IMPORTANT]
> Para que aplicativos lógicos e contas de integração trabalhem juntos em um ISE, ambos devem usar o *mesmo ISE* que sua localização.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O ISE que você criou para executar seus aplicativos lógicos. Se você não tiver um ISE, [crie um ISE primeiro](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Para criar, adicionar ou atualizar recursos que são implantados em um ISE, você precisa ser designado para a função Proprietário ou Contribuinte nesse ISE, ou você tem permissões herdadas através da assinatura do Azure ou do grupo de recursos Azure associado ao ISE. Para pessoas que não possuem permissões de proprietário, contribuinte ou herdadas, elas podem ser atribuídas à função de Contribuinte do Ambiente de Serviço de Integração ou de Desenvolvedor do Ambiente de Serviço de Integração. Para obter mais informações sobre o RBAC (Role-Based Access Control, controle de acesso baseado em função), consulte [O que é o RBAC (Role-Based Access Control, controle de acesso baseado em função) para recursos do Azure](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Criar aplicativos lógicos

Para criar aplicativos lógicos que são executados no seu ambiente de serviço de integração (ISE), siga estas etapas:

1. Encontre e abra seu ISE, se ainda não estiver aberto. No menu ISE, em **Configurações,** selecione **Aplicativos Lógicos** > **Adicionar**.

   ![Adicionar novo aplicativo lógico ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Forneça informações sobre o aplicativo lógico que você deseja criar, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do aplicativo lógico para criar |
   | **Assinatura** | Sim | O nome da assinatura do Azure a ser usado |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Do Zure (novo ou existente) para usar |
   | **Local** | Sim | Em **Ambientes de serviço Integração,** selecione o ISE a ser usado, se não estiver já selecionado. <p><p> **Importante**: Para usar seus aplicativos lógicos com uma conta de integração, ambos devem usar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar**.

1. Continue [criando seu aplicativo lógico da maneira usual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Para obter diferenças na forma como os gatilhos e ações funcionam e como eles são rotulados quando você usa um ISE em comparação com o serviço de aplicativos lógicos de vários locatários, consulte [Isolado versus multi-inquilino na visão geral do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Para gerenciar aplicativos lógicos e conexões de API em seu ISE, consulte [Gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Criar contas de integração

Com base no [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, seu ISE inclui o uso específico da conta de integração sem custo adicional. Os aplicativos lógicos que existem em um ambiente de serviço de integração (ISE) podem referenciar apenas contas de integração que existem no mesmo ISE. Assim, para que uma conta de integração funcione com aplicativos lógicos em um ISE, tanto a conta de integração quanto os aplicativos lógicos devem usar o *mesmo ambiente* que sua localização. Para obter mais informações sobre contas de integração e ISEs, consulte [Contas de integração com ise](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Para criar uma conta de integração que use um ISE, siga estas etapas:

1. Encontre e abra seu ISE, se ainda não estiver aberto. No menu ISE, em **Configurações,** selecione **'Adicionar contas** > de**integração'.**

   ![Adicionar nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Forneça informações sobre o aplicativo lógico que você deseja criar, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome da conta de integração que você deseja criar |
   | **Assinatura** | Sim | O nome da assinatura do Azure que você deseja usar |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Do Zure (novo ou existente) para usar |
   | **Nível de preços** | Sim | O nível de preços a ser usado para a conta de integração |
   | **Local** | Sim | Em **Ambientes de serviço Integração,** selecione o mesmo ISE que seus aplicativos lógicos usam, se ainda não estiver selecionado. <p><p> **Importante**: Para usar sua conta de integração com aplicativos lógicos, ambos devem usar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar**.

1. [Vincule seu aplicativo lógico à sua conta de integração da maneira usual.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Continue adicionando recursos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [acordos.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Para gerenciar contas de integração em seu ISE, consulte [Gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Adicionar conectores ISE

Os conectores gerenciados pela Microsoft que ficam disponíveis depois de criar o ISE não aparecem automaticamente no seletor de conectores no Logic App Designer. Antes de usar esses conectores ISE, você tem que adicionar e implantar manualmente esses conectores no seu ISE para que eles apareçam no Logic App Designer.

1. No menu ISE, em **Configurações,** **selecione Conectores gerenciados**. Na barra de ferramentas, selecione **Adicionar**.

   ![Exibir conectores gerenciados](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. No Adicionar um novo painel **de conector gerenciado,** abra a lista **de conectores Find.** Selecione o conector ISE que você deseja usar, mas ainda não está implantado no seu ISE. Selecione **Criar**.

   ![Selecione o conector ISE que você deseja implantar em seu ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Apenas conectores ISE que são elegíveis, mas ainda não implantados no seu ISE, aparecem disponíveis para você selecionar. Os conectores que já estão implantados em seu ISE parecem indisponíveis para seleção.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Criar conectores personalizados

Para usar conectores personalizados em seu ISE, crie esses conectores personalizados diretamente dentro do seu ISE.

1. Encontre e abra seu ISE, se ainda não estiver aberto. No menu ISE, em **Configurações,** selecione **Conectores personalizados** > **adicionar**.

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, a assinatura do Azure e o grupo de recursos Do Zure (novo ou existente) para usar no seu conector personalizado.

1. Na lista **Localização,** na seção **'Integração de ambientes de serviços',** selecione o mesmo ISE que seus aplicativos lógicos usam e selecione **Criar**, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecione seu novo conector personalizado e, em seguida, selecione **Editar,** por exemplo:

   ![Selecione e edite o conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da maneira usual a partir de uma [definição openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerenciar conectores personalizados em seu ISE, consulte [Gerenciar seu ambiente de serviço de integração.](../logic-apps/ise-manage-integration-service-environment.md)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
