---
title: 'Início Rápido: Nova atribuição de política com os modelos'
description: Neste início rápido, você usa um modelo do Resource Manager para criar uma atribuição de política para identificar recursos que não estão em conformidade.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 646e2e064618e70fd9e9e747926f0886d85fde26
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187776"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Início Rápido: Criar uma atribuição de política para identificar recursos sem conformidade usando um modelo do Resource Manager

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, você criará uma atribuição de política e atribuirá uma definição de política interna chamada _Auditar VMs que não usam discos gerenciados_. Para ver uma lista parcial das políticas internas disponíveis, confira [Exemplos do Azure Policy](./samples/index.md).

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

O recurso definido no modelo inclui:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Implantar o modelo

> [!NOTE]
> o serviço Azure Policy é gratuito. Para saber mais, confira [Visão geral do Azure Policy](./overview.md).

1. Selecione a imagem a seguir para entrar no portal do Azure e abrir o modelo:

   [![Implantar o modelo de política no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecione ou insira os valores a seguir:

   | Nome | Valor |
   |------|-------|
   | Subscription | Selecione sua assinatura do Azure. |
   | Resource group | Selecione **Criar**, especifique um nome e, em seguida, selecione **OK**. Na captura de tela, o nome do grupo de recursos é _mypolicyquickstart\<Data em MMDD\>rg_. |
   | Location | Selecione uma região. Por exemplo, **Centro dos EUA**. |
   | Nome de atribuição de política | Especifique um nome de atribuição de política. Será possível usar a exibição de definição de política se você desejar. Por exemplo, **Auditar VMs que não usam discos gerenciados**. |
   | Nome do Rg | Especifique um nome de grupo de recursos ao qual você deseja atribuir a política. Neste início rápido, use o valor padrão **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** é uma função de modelo que recupera o grupo de recursos. |
   | ID de definição de política | Especifique **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Concordo com os termos e condições acima | (Selecionar) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- para encontrar mais exemplos de modelo, confira [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, acesse [Referência de modelo do Azure](/azure/templates/microsoft.authorization/allversions).
- Para saber como desenvolver modelos do Resource Manager, confira a [Documentação do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Para conhecer a implantação de nível de assinatura, confira [Create resource groups and resources at the subscription level](../../azure-resource-manager/templates/deploy-to-subscription.md) (Criar grupos de recursos e recursos no nível da assinatura).

## <a name="validate-the-deployment"></a>Validar a implantação

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize as **VMs de auditoria que não usam a atribuição de política de discos gerenciados** que você criou.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Página de visão geral de conformidade de política" border="false":::

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão em **Recursos sem conformidade**.

Para saber mais, confira [How compliance works](./how-to/get-compliance-data.md#how-compliance-works) (Como a conformidade funciona).

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estas etapas:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página e localize as **VMs de auditoria que não usam a atribuição  de política de discos gerenciados** que você criou.

1. Clique com o botão direito do mouse na atribuição de política **Auditar VMs que não usam discos gerenciados** e selecione **Excluir atribuição**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Excluir uma atribuição da página de visão geral de conformidade" border="false":::

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você atribuiu uma definição de política interna a um escopo e avaliou seu relatório de conformidade. A definição de política valida que todos os recursos no escopo estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)