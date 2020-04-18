---
title: Extensão da Política do Azure para Visual Studio Code
description: Aprenda a usar a extensão de política do Azure para visual studio code para procurar aliases do Resource Manager.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641017"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Use a extensão da Política do Azure para o Visual Studio Code

> Aplica-se à versão de extensão da Diretiva do Azure **0.0.21** e mais recente

Aprenda a usar a extensão de Política do Azure para visual Studio Code para procurar [pseudônimos](../concepts/definition-structure.md#aliases) e revisar recursos e políticas. Primeiro, descreveremos como instalar a extensão de Política do Azure no Visual Studio Code. Então vamos mostrar como procurar pseudônimos.

A extensão de Política do Azure para Visual Studio Code pode ser instalada em todas as plataformas suportadas pelo Visual Studio Code. Esse suporte inclui Windows, Linux e macOS.

> [!NOTE]
> As alterações feitas localmente nas políticas visualizadas na extensão de Política do Azure para o Visual Studio Code não são sincronizadas com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Instale a extensão da Política do Azure

Depois de atender aos pré-requisitos, você pode instalar a extensão azure Policy para Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.

1. Na barra de menus, vá para **Exibir** > **extensões**.

1. Na caixa de pesquisa, **digite Azure Policy**.

1. Selecione **Azure Policy** nos resultados da pesquisa e, em seguida, **selecione Instalar**.

1. Selecione **Recarregar** quando necessário.

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure

Para um usuário nacional de nuvem, siga estas etapas para definir primeiro o ambiente Azure:

1. Selecione **Arquivo\Preferências\Configurações**.

1. Pesquisar na seguinte string: _Azure: Cloud_

1. Selecione a nuvem de nação da lista:

   ![Defina o login padrão da nuvem do Azure para visual studio code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Conecte-se a uma conta do Azure

Para avaliar recursos e procurar pseudônimos, você deve se conectar à sua conta do Azure. Siga estas etapas para se conectar ao Azure a partir do Visual Studio Code:

1. Faça login no Azure a partir da extensão da Política do Azure ou da Paleta de Comando.

   - Extensão da Política do Azure

     A partir da extensão Azure Policy, **selecione Entrar no Azure**.

     ![Login na nuvem do Azure para visual studio code da extensão da Política do Azure](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta de Comandos

     Na barra de menus, vá para **Exibir** > **paleta de**comando e **digite Azure: Faça login**.

     ![Login na nuvem do Azure para Visual Studio Code da Paleta de Comando](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Siga o sinal nas instruções para entrar no Azure. Depois de conectado, o nome da conta do Azure é mostrado na barra de status na parte inferior da janela Visual Studio Code.

## <a name="select-subscriptions"></a>Selecione assinaturas

Quando você faz login pela primeira vez, apenas os recursos e políticas de assinatura padrão são carregados pela extensão de Diretiva do Azure. Para adicionar ou remover assinaturas de exibir recursos e políticas, siga estas etapas:

1. Inicie o comando de assinatura na Paleta de Comando ou no rodapé da janela.

   - Paleta de comando: 

     Na barra de menus, vá para **Exibir** > **paleta de**comando e **digite Azure: Selecione Assinaturas**.

   - Rodapé de janela

     No rodapé da janela na parte inferior da tela, selecione o segmento que corresponde **ao \<Azure: sua conta\>**.

1. Use a caixa de filtro para encontrar rapidamente assinaturas pelo nome. Em seguida, verifique ou remova a verificação de cada assinatura para definir as assinaturas mostradas pela extensão da Diretiva do Azure. Quando terminar de adicionar ou remover assinaturas para exibir, selecione **OK**.

## <a name="search-for-and-view-resources"></a>Pesquise e visualize recursos

A extensão a zure Policy lista recursos nas assinaturas selecionadas pelo Provedor de Recursos e por grupo de recursos no painel **Recursos.** A visão de árvore inclui os seguintes agrupamentos de recursos dentro da assinatura selecionada ou no nível de assinatura:

- **Provedores de recursos**
  - Cada provedor de recursos registrado com recursos e recursos infantis relacionados que têm aliases de política
- **Grupos de recursos**
  - Todos os recursos do grupo de recursos em que estão

Por padrão, a extensão filtra a parte 'Provedor de recursos' por recursos e recursos existentes que possuem aliases de diretiva. Altere esse comportamento nas **configurações** > **extensões** > **a política do Azure** para ver todos os provedores de recursos sem filtragem.

Clientes com centenas ou milhares de recursos em uma única assinatura podem preferir uma maneira pesquisável de localizar seus recursos. A extensão da Diretiva do Azure permite a busca de um recurso específico com as seguintes etapas:

1. Inicie a interface de pesquisa a partir da extensão Azure Policy ou da Paleta de Comando.

   - Extensão da Política do Azure

     Na extensão Azure Policy, paire sobre o painel **Recursos** e selecione a elipse e selecione **Recursos de pesquisa**.

   - Paleta de comando:

     Na barra de menus, vá para **Exibir** > **paleta de**comando e insira **Recursos: Recursos de pesquisa**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura procurar.

1. Use o filtro para selecionar qual grupo de recursos procurar que faça parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual recurso exibir. O filtro funciona tanto para o nome do recurso quanto para o tipo de recurso.

## <a name="discover-aliases-for-resource-properties"></a>Descubra aliases para propriedades de recursos

Quando um recurso é selecionado, seja através da interface de pesquisa ou selecionando-o na visão de árvore, a extensão Azure Policy abre o arquivo JSON representando esse recurso e todos os valores de propriedade do Gerenciador de Recursos.

Uma vez que um recurso esteja aberto, pairando sobre o nome ou valor da propriedade do Gerenciador de recursos exibe o alias de Diretiva Do Azure se existir. Neste exemplo, o recurso `Microsoft.Compute/virtualMachines` é um tipo de recurso e a propriedade **properties.storageProfile.imageReference.offer** é pairada sobre. Pairar mostra os pseudônimos correspondentes.

![O hover de extensão da diretiva do Azure mostra alias de propriedade do Resource Manager](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Pesquisar e visualizar políticas e atribuições

A extensão de diretiva do Azure lista os tipos de diretiva e as atribuições de diretiva como uma visão de árvore para as assinaturas selecionadas a serem exibidas no painel **Políticas.** Clientes com centenas ou milhares de políticas ou atribuições em uma única assinatura podem preferir uma maneira pesquisável de localizar suas políticas ou atribuições. A extensão da Diretiva do Azure permite pesquisar uma política ou atribuição específica com as seguintes etapas:

1. Inicie a interface de pesquisa a partir da extensão Azure Policy ou da Paleta de Comando.

   - Extensão da Política do Azure

     A partir da extensão Azure Policy, paire sobre o painel **Políticas** e selecione a elipse e selecione **'Políticas de pesquisa ''''''''''''''''''''''''''''''''''''''''''''''''''**

   - Paleta de comando:

     Na barra de menus, vá para **Exibir** > **paleta de**comando e insira **Políticas: Políticas de pesquisa**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura procurar.

1. Use o filtro para selecionar qual tipo de política ou atribuição para pesquisar que faz parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual política ou para exibir. O filtro funciona para _exibirNome_ para a definição de diretiva ou atribuição de diretiva.

Ao selecionar uma diretiva ou atribuição, seja através da interface de pesquisa ou selecionando-a na exibição de árvores, a extensão Azure Policy abre o JSON que representa a política ou a atribuição e todos os valores de propriedade do Gerenciador de Recursos. A extensão pode validar o esquema JSON de política azure aberta.

## <a name="sign-out"></a>Sair

Na barra de menus, vá para **Exibir** > **paleta de**comando e, em seguida, **digite Azure: Sign Out**.

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não compatíveis.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)