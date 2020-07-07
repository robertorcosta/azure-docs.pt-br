---
title: Botão Implantar no Azure
description: Use o botão para implantar modelos de Azure Resource Manager de um repositório GitHub.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: e403d095f7419ed13742dcfa8e8407a92ad10be0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744085"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Usar um botão de implantação para implantar modelos do repositório GitHub

Este artigo descreve como usar o botão **implantar no Azure** para implantar modelos de um repositório github. Você pode adicionar o botão diretamente ao arquivo README.md em seu repositório GitHub ou a uma página da Web que faz referência ao repositório. Esse método dá suporte apenas à implantação no nível do grupo de recursos.

## <a name="use-common-image"></a>Usar imagem comum

Para adicionar o botão à sua página da Web ou repositório, use a imagem a seguir:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão Implantar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para o modelo de implantação

Para criar a URL para o modelo, comece com a URL bruta para o modelo em seu repositório:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, codifique a URL. Você pode usar um codificador online ou executar um comando. O exemplo do PowerShell a seguir mostra como codificar por URL um valor.

```powershell
[uri]::EscapeDataString($url)
```

A URL de exemplo tem o seguinte valor quando a URL é codificada.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Cada link começa com a mesma URL base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adicione o link de modelo codificado por URL ao final da URL base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Você tem a URL completa para o link.

## <a name="create-deploy-to-azure-button"></a>Botão criar implantação no Azure

Por fim, coloque o link e a imagem juntos.

Para adicionar o botão com redução no arquivo README.md no repositório GitHub ou em uma página da Web, use:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Para HTML, use:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Implantar o modelo

Para testar a solução completa, selecione o seguinte botão:

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

O portal exibe um painel que permite que você forneça facilmente valores de parâmetro. Os parâmetros são preenchidos previamente com os valores padrão do modelo.

![Usar o portal para implantar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre modelos, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](template-syntax.md).
