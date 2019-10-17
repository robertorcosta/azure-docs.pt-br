---
title: Usar o Portal do Azure para implantar recursos do Azure | Microsoft Docs
description: Use portal do Azure e o gerenciamento de recursos do Azure para implantar seus recursos em um grupo de recursos em sua assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: 249afcaad85d9031e0972d4fcfc185b5ff890f65
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390341"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implantar recursos com modelos do Resource Manager e o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](resource-group-overview.md) para implantar os recursos do Azure. Para saber mais sobre como gerenciar seus recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](manage-resources-portal.md).

A implantação de recursos do Azure usando o portal do Azure geralmente envolve duas etapas:

- Crie um grupos de recursos.
- Implante recursos no grupo de recursos.

Além disso, você também pode implantar um modelo de Azure Resource Manager para criar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

1. Para criar um novo grupo de recursos, selecione **grupos de recursos** no [portal do Azure](https://portal.azure.com).

   ![Selecionar grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em Grupo de Recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Selecione ou insira os seguintes valores de propriedade:

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: dê um nome ao grupo de recursos.
    - **Região**: especifique um local do Azure. É aí que o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Selecione **Examinar + criar**.
1. Examine os valores e, em seguida, selecione **criar**.
1. Selecione **Atualizar** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um grupo de recursos, você pode implantar recursos no grupo por meio do Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **criar um recurso** no [portal do Azure](https://portal.azure.com).

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar. Os recursos são organizados em categorias. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace. A captura de tela a seguir mostra que o servidor Ubuntu está selecionado.

   ![Selecionar tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar uma máquina virtual do Linux e implantá-la no grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

1. Sua implantação será iniciada. A implantação pode levar vários minutos. Alguns recursos levam mais tempo do que outros recursos. Quando a implantação for concluída, você verá uma notificação. Selecione **ir para o recurso** para abrir

   ![Exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)

1. Após implantar seus recursos, adicione mais deles ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> A interface do portal não oferece suporte ao referenciar um [segredo de um Key Vault](resource-manager-keyvault-parameter.md). Em vez disso, use [PowerShell](resource-group-template-deploy.md) ou [CLI do Azure](resource-group-template-deploy-cli.md) para implantar o modelo localmente ou em um URI externo.

1. Para implantar um modelo personalizado por meio do portal, selecione **criar um recurso**, Pesquisar **modelo**. e, em seguida, selecione **implantação de modelo**.

   ![Pesquisar implantação de modelo](./media/resource-group-template-deploy-portal/search-template.png)

1. Clique em **Criar**.
1. Você verá várias opções para criar um modelo:

    - Crie **seu próprio modelo no editor**: Crie um modelo usando o editor de modelos de Portal.  O editor é capaz de adicionar um esquema de modelo de recurso.
    - **Modelos comuns**: há quatro modelos comuns para criar uma máquina virtual Linux, uma máquina virtual do Windows, um aplicativo Web e um banco de dados SQL do Azure.
    - **Carregar um modelo de início rápido do GitHub**: use [modelos de início rápido](https://azure.microsoft.com/resources/templates/)existentes.

   ![Opções de exibição](./media/resource-group-template-deploy-portal/see-options.png)

    Este tutorial fornece a instrução para carregar um modelo de início rápido.

1. Em **carregar um modelo de início rápido do GitHub**, digite ou selecione **101-armazenamento-conta-criar**.

    Você tem duas opções:

    - **Selecionar modelo**: implantar o modelo.
    - **Editar modelo**: Edite o modelo de início rápido antes de implantá-lo.

1. Selecione **Editar modelo** para explorar o editor de modelo de Portal. O modelo é carregado no editor. Observe que há dois parâmetros: **storageAccountType** e **Location**.

   ![Criar modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Faça uma alteração secundária no modelo. Por exemplo, atualize a variável **storageAccountName** para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Clique em **Salvar**. Agora você vê a interface de implantação de modelo do Portal. Observe os dois parâmetros que você definiu no modelo.
1. Insira ou selecione os valores de propriedade:

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione **criar novo** e dê um nome.
    - **Local**: selecione um local do Azure.
    - **Tipo de conta de armazenamento**: Use o valor padrão.
    - **Local**: Use o valor padrão.
    - **Concordo com os termos e as condições declarados acima**: (selecionar)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Próximos passos

- Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](./resource-group-audit.md).
- Para solucionar erros de implantação, confira [View deployment operations](./resource-manager-deployment-operations.md) (Exibir operações de implantação).
- Para exportar um modelo de uma implantação ou grupo de recursos, consulte [exportar modelos de Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Para distribuir com segurança seu serviço em várias regiões, consulte [Deployment Manager do Azure](./deployment-manager-overview.md).
