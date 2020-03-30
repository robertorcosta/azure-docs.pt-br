---
title: Use o Visual Studio Code para criar modelos
description: Saiba como instalar e usar o Visual Studio Code e a extensão Azure Resource Manager Tools.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273625"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Use o Visual Studio Code para criar modelos do Azure Resource Manager

Visual Studio Code é um editor leve, multiplataforma e de código aberto. A extensão Azure Resource Manager Template Tools é um plugin para o desenvolvimento de modelos do Gerenciador de Recursos. A extensão adiciona suporte ao idioma para modelos para dar-lhe intellisense, destaque de sintaxe, ajuda em linha e muitas outras funções de idioma. Juntos, eles fornecem experiência recomendada de desenvolvimento de modelos.

## <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

O visual studio Code suporta MacOS, Windows e Linux.  Pode ser instalado a partir do [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Instale a extensão resource manager tools

1. Abra o Visual Studio Code.
1. Selecione **Extensões** no menu esquerdo. Ou no menu **Exibir,** selecione **Extensões** para abrir o painel Extensões.

    ![Instale a extensão visual studio code resource manager tools](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Procure o **Gerenciador de Recursos**.
1. Selecione **Instalar** em **Ferramentas de Gerenciador de Recursos do Azure**.

## <a name="the-extension-features"></a>Os recursos de extensão

### <a name="colorization-for-template-language-expressions"></a>Colorização para expressões de linguagem de modelo

Parâmetros, variáveis, funções, nomes e expressões são codificados por cores, conforme mostrado na captura de tela a seguir:

![Visual Studio Code Resource Manager ferramentas extensões de coloração](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

A visualização do contorno do modelo facilita a navegação através de grandes modelos.

### <a name="intellisense"></a>Intellisense

A extensão de modelo do Gerenciador de recursos conhece possíveis conclusões para nomes de funções, parâmetros, variáveis e referências. As sugestões do IntelliSense aparecem conforme você digita. Se você continuar digitando caracteres, a lista de membros (variáveis, métodos, etc.) será filtrada para incluir apenas membros contendo seus caracteres digitados. Pressionar **Guia** ou **Inserir** insere o membro selecionado.

- Nomes de função incorporados

    ![Visual Studio Code Resource Manager ferramentas funções intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Referências de parâmetros

    ![Visual Studio Code Resource Manager ferramentas extensões parâmetros intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Referências variáveis

    ![Visual Studio Code Resource Manager ferramentas extensões intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- propriedades resourceGroup()

    ![Visual Studio Code Resource Manager ferramentas funções intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Além disso, o intellisense também trabalha com propriedades de assinatura e propriedades de referências a variáveis que são objetos.

### <a name="signature-help-for-function-parameters"></a>Ajuda de assinatura para parâmetros de função

Ao pairar sobre nomes de função, a extensão mostra a ajuda de assinatura para parâmetros de função.

![Visual Studio Code Resource Manager ferramentas funções de assinatura de extensões](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ir para definição para referências variáveis e parâmetros

Você pode pular para a definição com **Ctrl+Click**, ou usando ![o menu de contexto como mostrado na captura de tela: As extensões das ferramentas do Visual Studio Code Resource Manager vão para a definição](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Você pode abrir a definição para o lado com **Ctrl+Alt+Click**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Espiar para definições de variáveis e parâmetros

Para abrir o editor, use o menu de contexto como mostrado na captura de tela anterior.

A captura de tela a seguir mostra o editor peek:

![Visual Studio Code Resource Manager ferramentas extensões peek editor](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Encontre todas as referências para variáveis e parâmetros

Para encontrar todas as referências, use o menu de contexto como mostrado na captura de tela anterior.

A captura de tela a seguir mostra como as referências são destacadas:

![As extensões de ferramentas do Visual Studio Code Resource Manager encontram todas as referências](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Renomear todas as referências para variáveis e parâmetros

Para renomear todas as referências para variáveis e parâmetros, use o menu de contexto como mostrado na captura de tela anterior.

### <a name="hover-for-parameter-description"></a>Passar o tempo para a descrição do parâmetro

![Visual Studio Code Resource Manager ferramentas extensões hover definição](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Correspondência de chaves

Os suportes correspondentes são destacados assim que o cursor estiver perto de um deles. Quando você clica em uma cinta, a cinta correspondente também é destacada como mostrado na captura de tela a seguir:

![Visual Studio Code Resource Manager ferramentas extensões brace matching](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Mostrar erros e avisos

Os erros identificados pela extensão incluem:

- Referências de parâmetros indefinidos
- Referências variáveis indefinidas
- Nomes de funções não reconhecidos
- referência () uso da função em definição variável
- Número incorreto de argumentos em funções

Os avisos incluem:

- Parâmetros não utilizados
- Variáveis não utilizadas

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Azure Resource Manager, consulte [Tutorial: Crie e implante seu primeiro modelo de Gerenciador de recursos do Azure](template-tutorial-create-first-template.md).
- Para passar por um rápido começo usando o Visual Studio Code, consulte [Quickstart: Crie modelos do Azure Resource Manager usando o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)
