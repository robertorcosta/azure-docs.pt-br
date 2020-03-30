---
title: Implantação contínua para Azure Functions
description: Use os recursos de implantação contínua do Azure App Service para publicar suas funções.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277018"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions

Você pode usar funções do Azure para implantar seu código continuamente usando [a integração de controle de origem](functions-deployment-technologies.md#source-control). A integração de controle de origem permite um fluxo de trabalho no qual uma atualização de código aciona a implantação no Azure. Se você é novo no Azure Functions, comece revisando a visão geral das funções do [Azure](functions-overview.md).

A implantação contínua é uma boa opção para projetos onde você integra contribuições múltiplas e frequentes. Quando você usa a implantação contínua, você mantém uma única fonte de verdade para o seu código, o que permite que as equipes colaborem facilmente. Você pode configurar a implantação contínua nas funções do Azure a partir dos seguintes locais de código-fonte:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Balde de bits](https://bitbucket.org/)

A unidade de implantação para funções no Azure é o aplicativo de função. Todas as funções em um aplicativo de função são implantadas ao mesmo tempo. Depois de habilitar a implantação contínua, o acesso ao código de função no portal Azure é configurado *como somente leitura* porque a fonte da verdade está definida para estar em outro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para implantação contínua

Para que a implantação contínua seja bem sucedida, sua estrutura de diretório deve ser compatível com a estrutura básica de pasta que o Azure Functions espera.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A implantação contínua ainda não é suportada para aplicativos Linux em execução em um plano de consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurar a implantação contínua

Para configurar a implantação contínua de um aplicativo de função existente, complete essas etapas. As etapas demonstram integração com um repositório do GitHub, mas etapas semelhantes se aplicam aos Repos ida e volta do Azure ou outros repositórios de código-fonte.

1. Em seu aplicativo de função no [portal Azure,](https://portal.azure.com)selecione **Recursos da plataforma** > **Centro de Implantação**.

    ![Centro de implantação aberto](./media/functions-continuous-deployment/platform-features.png)

2. Em **Deployment Center,** selecione **GitHub**e selecione **Autorizar**. Se você já autorizou o GitHub, selecione **Continuar**. 

    ![Centro de implantação de serviços de aplicativos do Azure](./media/functions-continuous-deployment/github.png)

3. No GitHub, selecione o botão **Autorizar AzureAppService.** 

    ![Autorizar o serviço de aplicativos do Azure](./media/functions-continuous-deployment/authorize.png)
    
    No **Centro de Implantação** no portal Azure, selecione **Continuar**.

4. Selecione um dos seguintes provedores de compilação:

    * **Serviço de aplicativo serviço de construção serviço**: Melhor quando você não precisa de uma compilação ou se você precisa de uma compilação genérica.
    * **Azure Pipelines (Preview)**: Melhor quando você precisa de mais controle sobre a compilação. Este provedor está atualmente em pré-visualização.

    ![Selecione um provedor de compilação](./media/functions-continuous-deployment/build.png)

5. Configure informações específicas para a opção de controle de origem especificada. Para o GitHub, você deve inserir ou selecionar valores para **Organização,** **Repositório**e **Ramo.** Os valores são baseados na localização do seu código. Em seguida, selecione **Continuar**.

    ![Configurar o GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Revise todos os detalhes e selecione **Concluir** para concluir sua configuração de implantação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo é concluído, todo o código da fonte especificada é implantado no seu aplicativo. Nesse ponto, as alterações na fonte de implantação desencadeiam uma implantação dessas alterações no seu aplicativo de função no Azure.

## <a name="deployment-scenarios"></a>Cenários de implantação

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua

Se você já escreveu funções no [portal Azure](https://portal.azure.com) e deseja baixar o conteúdo do seu aplicativo antes de mudar para a implantação contínua, vá para a guia **Visão Geral** do seu aplicativo de função. Selecione o botão **Debaixar o conteúdo** do aplicativo.

![Baixar conteúdo do aplicativo](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, você não poderá mais editar seus arquivos de origem no portal Funções.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas do Azure Functions](functions-best-practices.md)
