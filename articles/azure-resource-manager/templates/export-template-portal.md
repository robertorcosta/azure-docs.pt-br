---
title: Modelo de exportação no portal Azure
description: Use o portal Azure para exportar um modelo do Azure Resource Manager a partir de recursos em sua assinatura.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273729"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportação única e multi-recursos para um modelo no portal Azure

Para ajudar na criação de modelos do Azure Resource Manager, você pode exportar um modelo a partir de recursos existentes. O modelo exportado ajuda você a entender a sintaxe JSON e as propriedades que implantam seus recursos. Para automatizar implantações futuras, comece com o modelo exportado e modifique-o para o seu cenário.

O Gerenciador de recursos permite que você escolha um ou mais recursos para exportar para um modelo. Você pode se concentrar exatamente nos recursos necessários no modelo.

Este artigo mostra como exportar modelos através do portal. Você também pode usar [a Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação certa

Há duas maneiras de exportar um modelo:

* **Exportar de grupo de recursos ou recursos**. Essa opção gera um novo modelo a partir dos recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Você pode exportar um grupo inteiro de recursos ou recursos específicos dentro desse grupo de recursos.

* **Exportar antes da implantação ou do histórico**. Esta opção recupera uma cópia exata de um modelo usado para implantação.

Dependendo da opção escolhida, os modelos exportados têm qualidades diferentes.

| A partir de grupo de recursos ou recurso | Antes da implantação ou da história |
| --------------------- | ----------------- |
| Template é o instantâneo do estado atual dos recursos. Ele inclui quaisquer alterações manuais que você fez após a implantação. | O modelo só mostra o estado dos recursos no momento da implantação. Todas as alterações manuais que você fez após a implantação não estão incluídas. |
| Você pode selecionar quais recursos de um grupo de recursos para exportar. | Todos os recursos para uma implantação específica estão incluídos. Você não pode escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados em um momento diferente. |
| O modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que você normalmente não definiria durante a implantação. Você pode querer remover ou limpar essas propriedades antes de reutilizar o modelo. | O modelo inclui apenas as propriedades necessárias para a implantação. O modelo está pronto para uso. |
| O modelo provavelmente não inclui todos os parâmetros necessários para reutilização. A maioria dos valores de propriedade são codificados no modelo. Para reimplantar o modelo em outros ambientes, você precisa adicionar parâmetros que aumentam a capacidade de configurar os recursos.  Você pode desmarcar **Incluir parâmetros** para que você possa criar seus próprios parâmetros. | O modelo inclui parâmetros que facilitam a reimplantação em diferentes ambientes. |

Exportar o modelo de um grupo de recursos ou recurso, quando:

* Você precisa capturar alterações nos recursos que foram feitos após a implantação original.
* Você deseja selecionar quais recursos são exportados.

Exportar o modelo antes da implantação ou do histórico, quando:

* Você quer um modelo fácil de reutilizar.
* Você não precisa incluir alterações que fez após a implantação original.

## <a name="limitations"></a>Limitações

Ao exportar de um grupo de recursos ou recurso, o modelo exportado é gerado a partir [dos esquemas publicados](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) para cada tipo de recurso. Ocasionalmente, o esquema não tem a versão mais recente para um tipo de recurso. Verifique seu modelo exportado para ter certeza de que ele inclui as propriedades necessárias. Se necessário, edite o modelo exportado para usar a versão de API que você precisa.

O recurso modelo de exportação não suporta a exportação de recursos da Fábrica de Dados do Azure. Para saber como você pode exportar recursos da Fábrica de Dados, consulte [Copiar ou clonar uma fábrica de dados na Fábrica de Dados do Azure](https://aka.ms/exportTemplateViaAdf).

Para exportar recursos criados através de um modelo clássico de implantação, você deve [migrá-los para o modelo de implantação do Gerenciador de recursos](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Modelo de exportação de um grupo de recursos

Para exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que deseja exportar.

1. Selecione um ou mais recursos selecionando as caixas de seleção.  Para selecionar todos, selecione a caixa de seleção à esquerda de **Name**. O item **menu modelo Exportar** só fica ativado depois de ter selecionado pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

    Na captura de tela, apenas a conta de armazenamento é selecionada.
1. Selecione **Exportar modelo**.

1. O modelo exportado é exibido e está disponível para download e implantação.

   ![Mostrar modelo](./media/export-template-portal/show-template.png)

   **Incluir parâmetros** é selecionado por padrão.  Quando selecionado, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se você quiser criar seus próprios parâmetros, alterne esta caixa de seleção para não incluí-los.

## <a name="export-template-from-a-resource"></a>Modelo de exportação a partir de um recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que deseja exportar.

1. Selecione o recurso que deseja exportar para abrir o recurso.

1. Para esse recurso, selecione **Exportar modelo** no painel esquerdo.

   ![Recurso de exportação](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é exibido e está disponível para download e implantação. O modelo contém apenas o único recurso. **Incluir parâmetros** é selecionado por padrão.  Quando selecionado, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se você quiser criar seus próprios parâmetros, alterne esta caixa de seleção para não incluí-los.

## <a name="export-template-before-deployment"></a>Modelo de exportação antes da implantação

1. Selecione o serviço Azure que deseja implantar.

1. Preencha os valores para o novo serviço.

1. Depois de passar na validação, mas antes de iniciar a implantação, selecione **Baixar um modelo para automação**.

   ![Baixar o modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é exibido e está disponível para download e implantação.


## <a name="export-template-after-deployment"></a>Modelo de exportação após implantação

Você pode exportar o modelo que foi usado para implantar recursos existentes. O modelo que você recebe é exatamente o que foi usado para implantação.

1. Selecione o grupo de recursos que deseja exportar.

1. Selecione o link em **Implantações**.

   ![Selecione o histórico de implantação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implantações do histórico de implantação.

   ![Selecionar a implantação](./media/export-template-portal/select-details.png)

1. Selecione **Modelo**. O modelo usado para esta implantação é exibido e está disponível para download.

   ![Selecionar modelo](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a exportar modelos com [a Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Para aprender a sintaxe do modelo Resource Manager, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
- Para saber como desenvolver modelos, consulte os [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
