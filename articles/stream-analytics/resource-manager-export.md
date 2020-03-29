---
title: Exportar um modelo de gerenciador de recursos do Azure Stream Analytics
description: Este artigo descreve como exportar um modelo do Azure Resource Manager para o seu trabalho no Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968920"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportar um modelo de gerenciador de recursos do Azure Stream Analytics

[Os modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) permitem que você implemente a infra-estrutura como código. O modelo é um arquivo JSON (JavaScript Object Notation, notação de objeto sustal) que define a infra-estrutura e a configuração de seus recursos. Você especifica os recursos a serem implantados e as propriedades desses recursos.

Você pode reimplantar um trabalho do Azure Stream Analytics exportando o modelo do Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Abra um emprego em VS Code

Antes de exportar um modelo, você deve primeiro abrir um trabalho de Stream Analytics existente no Visual Studio Code. 

Para exportar um trabalho para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** no portal Azure. Na página **Consulta,** selecione **Abrir no Visual Studio**. Em seguida, selecione **Visual Studio Code**.

![Trabalho de Open Stream Analytics em Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Para obter mais informações sobre como usar o Visual Studio Code para gerenciar trabalhos do Stream Analytics, consulte o [Visual Studio Code quickstart](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Compilar o script 

O próximo passo é compilar o script de trabalho para um modelo do Azure Resource Manager. Antes de compilar o script, certifique-se de que seu trabalho tenha pelo menos uma entrada e uma saída configuradas. Se nenhuma entrada ou saída estiver configurada, você precisa configurar a entrada e a saída primeiro.

1. No Visual Studio Code, navegue até o arquivo *Transformation.asaql* do seu trabalho.

   ![Arquivo Transformation.asaql em Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Clique com o botão direito do mouse no arquivo *Transformation.asaql* e selecione **ASA: Compilar script** no menu.

1. Observe que uma pasta **Implantar** é exibida no espaço de trabalho do Stream Analytics.

1. Explorar o arquivo *JobTemplate.json,* que é o modelo de gerenciamento de recursos do Azure usado para implantar.

## <a name="complete-the-parameters-file"></a>Complete o arquivo de parâmetros

Em seguida, complete o arquivo de parâmetros do modelo de gerenciamento de recursos do Azure.

1. Abra o arquivo *JobTemplate.parameters.json* localizado na pasta **Implantar** do seu espaço de trabalho do Stream Analytics no Visual Studio Code.

1. Observe que as teclas de entrada e saída são nulas. Substitua os valores nulos pelas teclas de acesso reais para os recursos de entrada e saída.

1. Salve o arquivo de parâmetros.

## <a name="deploy-using-templates"></a>Implantar usando modelos

Você está pronto para implantar seu trabalho no Azure Stream Analytics usando os modelos do Azure Resource Manager gerados na seção anterior.

Em uma janela PowerShell, execute o seguinte comando. Certifique-se de reaplce os arquivos *ResourceGroupName,* *TemplateFile*e *TemplateParameterFile* com o nome do grupo de recursos real e os caminhos completos do arquivo para os arquivos *JobTemplate.json* e *JobTemplate.parameters.json* na **pasta Implantar** do seu trabalho.

Se você não tiver o Azure PowerShell configurado, siga as etapas no [módulo Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Próximas etapas

* [Test Azure Stream Analytics trabalhos localmente com entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com o Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)