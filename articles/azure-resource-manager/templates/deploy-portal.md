---
title: Implantar recursos com o portal Azure
description: Use o portal Azure e o Azure Resource Manage para implantar seus recursos em um grupo de recursos em sua assinatura.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153430"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Implantar recursos com modelos ARM e portal Azure

Saiba como usar o [portal Azure](https://portal.azure.com) com [modelos ARM (Azure Resource Manager)](overview.md) para implantar seus recursos do Azure. Para saber mais sobre o gerenciamento de seus recursos, consulte [Gerenciar recursos do Azure usando o portal Azure](../management/manage-resources-portal.md).

A implantação de recursos do Azure usando o portal Azure geralmente envolve duas etapas:

- Crie um grupos de recursos.
- Implantar recursos para o grupo de recursos.

Além disso, você também pode implantar um modelo ARM para criar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione grupos de **recursos** do [portal Azure](https://portal.azure.com).

   ![Selecionar grupos de recursos](./media/deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/deploy-portal/add-resource-group.png)

1. Selecione ou insira os seguintes valores de propriedade:

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: Dê um nome ao grupo de recursos.
    - **Região**: Especifique uma localização do Azure. É aqui que o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/deploy-portal/set-group-properties.png)

1. Selecione **Revisão + criar**.
1. revisar os valores e, em seguida, selecionar **Criar**.
1. Selecione **Atualizar** antes de ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um grupo de recursos, você pode implantar recursos para o grupo a partir do Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, **selecione Criar um recurso** no [portal Azure](https://portal.azure.com).

   ![Novo recurso](./media/deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar. Os recursos são organizados em categorias. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace. A captura de tela a seguir mostra que o Ubuntu Server está selecionado.

   ![Selecionar tipo de recurso](./media/deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar uma máquina virtual Linux e implantá-la no grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/deploy-portal/select-existing-group.png)

   Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

1. Sua implantação será iniciada. A implantação pode levar vários minutos. Alguns recursos levam mais tempo do que outros recursos. Quando a implantação for concluída, você verá uma notificação. Selecione **Ir para o recurso** para abrir

   ![Exibir notificação](./media/deploy-portal/view-notification.png)

1. Após implantar seus recursos, adicione mais deles ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, consulte [Entender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).

> [!NOTE]
> A interface do portal não oferece suporte ao referenciar um [segredo de um Key Vault](key-vault-parameter.md). Em vez disso, use [PowerShell](deploy-powershell.md) ou [CLI do Azure](deploy-cli.md) para implantar o modelo localmente ou em um URI externo.

1. Para implantar um modelo personalizado através do portal, **selecione Criar um recurso,** procurar **modelo**. e, em seguida, selecione **a implantação de Modelo**.

   ![Pesquisar implantação de modelo](./media/deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você vê várias opções para criar um modelo:

    - **Construa seu próprio modelo no editor:** crie um modelo usando o editor de modelos do portal.  O editor é capaz de adicionar um esquema de modelo de recurso.
    - **Modelos comuns**: Existem quatro modelos comuns para a criação de uma máquina virtual Linux, uma máquina virtual do Windows, um aplicativo web e um banco de dados Azure SQL.
    - **Carregar um modelo de quickstart do GitHub**: use um [modelo de partida rápida](https://azure.microsoft.com/resources/templates/)existente .

   ![Opções de exibição](./media/deploy-portal/see-options.png)

    Este tutorial fornece a instrução para carregar um modelo de partida rápida.

1. Em **Carregar um modelo de inicialinicial rápido do GitHub,** digite ou selecione **101-storage-account-create**.

    Você tem duas opções:

    - **Selecione modelo**: implante o modelo.
    - **Editar modelo**: editar o modelo quickstart antes de implantá-lo.

1. Selecione **Editar modelo** para explorar o editor de modelos de portal. O modelo é carregado no editor. Observe que existem dois parâmetros: **armazenamentoAccountType** e **localização**.

   ![Criar modelo](./media/deploy-portal/show-json.png)

1. Faça uma pequena alteração no modelo. Por exemplo, atualize a variável **storageAccountName** para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Salvar**. Agora você vê a interface de implantação do modelo de portal. Observe os dois parâmetros definidos no modelo.
1. Digite ou selecione os valores da propriedade:

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: Selecione **Criar novo** e dar um nome.
    - **Localização**: Selecione uma localização do Azure.
    - **Tipo de conta de armazenamento**: Use o valor padrão.
    - **Localização**: Use o valor padrão.
    - **Concordo com os termos e as condições declarados acima**: (selecionar)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Próximas etapas

- Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](../management/view-activity-logs.md).
- Para solucionar erros de implantação, confira [View deployment operations](deployment-history.md) (Exibir operações de implantação).
- Para exportar um modelo de um grupo de implantação ou de recursos, consulte [Exportar modelos DE BRAÇO](export-template-portal.md).
- Para implementar seu serviço com segurança em várias regiões, consulte [O Gerenciador de Implantação do Azure](deployment-manager-overview.md).
