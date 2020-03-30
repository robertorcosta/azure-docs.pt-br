---
title: Botão Implantar no Azure
description: Use o botão para implantar modelos do Azure Resource Manager a partir de um repositório do GitHub.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109055"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Use um botão de implantação para implantar modelos do repositório GitHub

Este artigo descreve como usar o botão **Implantar para o Azure** para implantar modelos a partir de um repositório do GitHub. Você pode adicionar o botão diretamente ao arquivo README.md em seu repositório GitHub ou a uma página da Web que faz referência ao repositório.

## <a name="use-common-image"></a>Use imagem comum

Para adicionar o botão à sua página web ou repositório, use a seguinte imagem:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão Implantar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para implantar modelo

Para criar a URL para o seu modelo, comece com a URL bruta para o modelo em seu repo:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, url codificá-lo. Você pode usar um codificador on-line ou executar um comando. O exemplo do PowerShell a seguir mostra como codificar um valor de URL.

```powershell
[uri]::EscapeDataString($url)
```

O exemplo de URL tem o seguinte valor quando a URL codificada.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Cada link começa com a mesma URL base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adicione seu link de modelo codificado por URL ao final da URL base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Você tem sua URL completa para o link.

## <a name="create-deploy-to-azure-button"></a>Criar implantar no botão Azure

Finalmente, coloque o link e a imagem juntos.

Para adicionar o botão com Markdown no arquivo README.md no repositório do GitHub ou em uma página da Web, use:

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

O portal exibe um painel que permite fornecer facilmente valores de parâmetros. Os parâmetros são pré-preenchidos com os valores padrão do modelo.

![Use o portal para implantar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre modelos, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
