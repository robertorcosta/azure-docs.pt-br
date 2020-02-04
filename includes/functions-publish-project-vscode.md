---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842072"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta seção, você criará um aplicativo de funções e os recursos relacionados em sua assinatura do Azure e, em seguida, implantará seu código. 

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    ![Publicar seu projeto no Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos prompts:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----- |
    | Selecionar uma assinatura | Sua assinatura | Mostrado quando você tem várias assinaturas. |
    | Selecione o Aplicativo de Funções no Azure | + Criar Aplicativo de Funções | Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure. |
    | Insira um nome exclusivo globalmente para o aplicativo de funções | Nome exclusivo | Caracteres válidos para um nome de aplicativo de funções são `a-z`, `0-9` e `-`. |
    | Selecione uma localização para novos recursos | Região | Selecione uma [região](https://azure.microsoft.com/regions/) perto de você. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----- |
    | Selecionar uma assinatura | Sua assinatura | Mostrado quando você tem várias assinaturas. |
    | Selecione o Aplicativo de Funções no Azure | + Criar Aplicativo de Funções | Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure. |
    | Insira um nome exclusivo globalmente para o aplicativo de funções | Nome exclusivo | Caracteres válidos para um nome de aplicativo de funções são `a-z`, `0-9` e `-`. |
    | Selecione um runtime | Sua versão | Escolha a versão da linguagem em que você está executando localmente. |
    | Selecione uma localização para novos recursos | Região | Selecione uma [região](https://azure.microsoft.com/regions/) perto de você. | 

    ::: zone-end

    
1.  Quando concluído, os seguintes recursos do Azure serão criados em sua assinatura:

    + **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** : Contém todos os recursos criados do Azure. O nome é baseado no nome de seu aplicativo de funções.
    + **[Conta de armazenamento](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : Uma conta de armazenamento padrão é criada com um nome exclusivo baseado no nome de seu aplicativo de funções.
    + **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** : Um plano de consumo é criado na região Oeste dos EUA para hospedar seu aplicativo de funções sem servidor.
    + **Aplicativo de funções**: Seu projeto é implantado e executado no novo aplicativo de funções.
    + **[Application Insights]()** : uma instância, que está conectada ao seu aplicativo de funções, é criada com base no nome da função.

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. 
    
1. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)

1. De volta na área **Azure: Functions** na barra lateral, expanda o novo aplicativo de funções em sua assinatura. Expanda **Funções**, clique com o botão direito do mouse (Windows) ou Ctrl + clique (MacOS) em **HttpExample** e escolha **Copiar URL de função**.

    ![Copiar a URL da função para o novo gatilho HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
