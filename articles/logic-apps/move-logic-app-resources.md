---
title: Migrar aplicativos lógicos através de assinaturas, grupos de recursos ou regiões
description: Migrar aplicativos lógicos ou contas de integração para outras assinaturas, grupos de recursos ou locais (regiões) do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878724"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Mova recursos de aplicativos lógicos para outros grupos de recursos, regiões ou assinaturas do Azure

Para migrar seu aplicativo lógico ou recursos relacionados para outro grupo de recursos do Azure, região ou assinatura, você tem várias maneiras de concluir essas tarefas, como o portal Azure, OZure PowerShell, Azure CLI e Rest API. Antes de mover recursos, revise estas considerações: 

* Você pode mover apenas [tipos específicos de recursos de aplicativos lógicos](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) entre grupos de recursos do Azure ou assinaturas.

* Verifique os [limites](../logic-apps/logic-apps-limits-and-config.md) do número de recursos lógicos do aplicativo que você pode ter em sua assinatura do Azure e em cada região do Azure. Esses limites afetam se você pode mover tipos de recursos específicos quando a região permanece a mesma entre assinaturas ou grupos de recursos. Por exemplo, você pode ter apenas uma conta de integração de nível gratuito para cada região do Azure em cada assinatura do Azure.

* Quando você move recursos, o Azure cria novos IDs de recursos. Portanto, certifique-se de usar os novos IDs e atualizar quaisquer scripts ou ferramentas que estejam associadas aos recursos movidos.

* Depois de migrar aplicativos lógicos entre assinaturas, grupos de recursos ou regiões, você deve recriar ou reautorizar quaisquer conexões que exijam Autenticação Aberta (OAuth).

* Você pode mover um [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) apenas para outro grupo de recursos que existe na mesma região do Azure ou assinatura do Azure. Você não pode mover um ISE para um grupo de recursos que existe em uma região diferente do Azure ou assinatura do Azure. Além disso, após tal movimento, você deve atualizar todas as referências ao ISE em seus fluxos de trabalho de aplicativos lógicos, contas de integração, conexões e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

* A mesma assinatura do Azure que foi usada para criar o aplicativo lógico ou conta de integração que você deseja mover

* Permissões do proprietário de recursos para mover e configurar os recursos que você deseja. Saiba mais sobre [o Controle de Acesso Baseado em Papéis (RBAC).](../role-based-access-control/built-in-roles.md#owner)

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Mover recursos entre assinaturas

Para mover um recurso, como um aplicativo lógico ou uma conta de integração, para outra assinatura do Azure, você pode usar o portal Azure, Azure PowerShell, Azure CLI ou REST API. Essas etapas abrangem o portal Azure, que você pode usar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. No [portal Azure](https://portal.azure.com), encontre e selecione o recurso de aplicativo lógico que você deseja mover.

1. Na página **Visão geral** do recurso, ao lado **de Assinatura,** selecione o link **de alteração.**

1. Na página **de recursos do Move,** selecione o recurso do aplicativo lógico e todos os recursos relacionados que você deseja mover.

1. Na lista **de assinaturas,** selecione a assinatura de destino.

1. Na lista **de grupos de recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**.

1. Para confirmar o seu entendimento de que quaisquer scripts ou ferramentas associados aos recursos movidos não funcionarão até que você os atualize com os novos IDs de recurso, selecione a caixa de confirmação e selecione **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Mover recursos entre grupos de recursos

Para mover um recurso, como um aplicativo lógico, conta de integração ou [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)para outro grupo de recursos do Azure, você pode usar o portal Azure, Azure PowerShell, Azure CLI ou REST API. Essas etapas abrangem o portal Azure, que você pode usar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de realmente mover recursos entre grupos, você pode testar se você pode mover seu recurso com sucesso para outro grupo. Para obter mais informações, consulte [Validar seu movimento](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. No [portal Azure](https://portal.azure.com), encontre e selecione o recurso de aplicativo lógico que você deseja mover.

1. Na página **Visão geral** do recurso, ao lado do **grupo Recurso,** selecione o link **de alteração.**

1. Na página **de recursos do Move,** selecione o recurso do aplicativo lógico e todos os recursos relacionados que você deseja mover.

1. Na lista **de grupos de recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**.

1. Para confirmar o seu entendimento de que quaisquer scripts ou ferramentas associados aos recursos movidos não funcionarão até que você os atualize com os novos IDs de recurso, selecione a caixa de confirmação e selecione **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Mover recursos entre regiões

Quando você quer mover um aplicativo lógico para uma região diferente, suas opções dependem da maneira como você criou seu aplicativo lógico. Com base na opção escolhida, você deve recriar ou reautorizar as conexões em seu aplicativo lógico.

* No portal Azure, recrie o aplicativo lógico na nova região e reconfigure as configurações do fluxo de trabalho. Para economizar tempo, você pode copiar a definição e conexões do fluxo de trabalho subjacente do aplicativo de origem para o aplicativo de destino. Para visualizar o "código" por trás de um aplicativo lógico, na barra de ferramentas logic app designer, selecione **Exibição de código**.

* Usando o Visual Studio e o Azure Logic Apps Tools for Visual Studio, você pode [abrir e baixar seu aplicativo lógico](../logic-apps/manage-logic-apps-with-visual-studio.md) no portal Azure como um modelo do [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Este modelo está praticamente pronto para implantação e inclui as definições de recursos para o seu aplicativo lógico, incluindo o próprio fluxo de trabalho e conexões. O modelo também declara parâmetros para os valores a serem usados na implantação. Dessa forma, você pode mudar mais facilmente onde e como você implanta o aplicativo lógico, com base em suas necessidades. Para especificar a localização e outras informações necessárias para implantação, você pode usar um arquivo de parâmetros separado.

* Se você criou e implantou seu aplicativo lógico usando ferramentas de integração contínua (CI) e Entrega Contínua (CD), como o Azure Pipelines no Azure DevOps, você pode implantar seu aplicativo em outra região usando essas ferramentas.

Para obter mais informações sobre modelos de implantação de aplicativos lógicos, consulte esses tópicos:

* [Visão geral: Automatize a implantação de aplicativos de lógica do Azure usando modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Encontre, abra e baixe seu aplicativo lógico do portal Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Crie modelos do Azure Resource Manager para aplicativos de lógica do Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implantar modelos do Azure Resource Manager para aplicativos de lógica do Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Recursos relacionados

Alguns recursos do Azure, como os recursos de gateway de dados no Azure, podem existir em uma região que difere dos aplicativos lógicos que usam esses recursos. No entanto, outros recursos do Azure, como contas de integração vinculadas, devem existir na mesma região que seus aplicativos lógicos. Com base no seu cenário, certifique-se de que seus aplicativos lógicos possam acessar os recursos que seus aplicativos esperam existir na mesma região.

Por exemplo, para vincular um aplicativo lógico a uma conta de integração, ambos os recursos devem existir na mesma região. Em cenários como recuperação de desastres, você geralmente quer contas de integração que tenham a mesma configuração e artefatos. Em outros cenários, você pode precisar de contas de integração com diferentes configurações e artefatos.

Os conectores personalizados no Azure Logic Apps são visíveis para os autores e usuários dos conectores que têm a mesma assinatura do Azure e o mesmo inquilino do Azure Active Directory. Esses conectores estão disponíveis na mesma região onde os aplicativos lógicos são implantados. Para obter mais informações, veja [Compartilhar conectores personalizados em sua organização](https://docs.microsoft.com/connectors/custom-connectors/share).

O modelo que você recebe do Visual Studio inclui apenas as definições de recursos para o seu aplicativo lógico e suas conexões. Então, se o seu aplicativo lógico usa outros recursos, por exemplo, uma conta de integração e artefatos B2B, como parceiros, acordos e esquemas, você deve exportar o modelo dessa conta de integração usando o portal Dozure. Este modelo inclui as definições de recursos tanto para a conta de integração quanto para artefatos. No entanto, o modelo não está totalmente parametrizado. Então, você deve parametrizar manualmente os valores que deseja usar para implantação.

### <a name="export-templates-for-integration-accounts"></a>Modelos de exportação para contas de integração

1. No [portal Azure,](https://portal.azure.com)encontre e abra sua conta de integração.

1. No menu da sua conta de integração, em **Configurações,** selecione **Modelo exportar .**

1. Na barra de ferramentas, selecione **Baixar**e salve o modelo.

1. Abra e edite o modelo para parametrizar os valores necessários para implantação.

## <a name="next-steps"></a>Próximas etapas

[Mova os recursos do Azure para novos grupos de recursos ou assinaturas](../azure-resource-manager/management/move-resource-group-and-subscription.md)
