---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77029143"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta seção, você criará um aplicativo de funções e os recursos relacionados em sua assinatura do Azure e, em seguida, implantará seu código. 

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    ![Publicar seu projeto no Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos prompts:

    + **Selecione a assinatura**: Escolha a assinatura a ser usada. Essa opção não será exibida caso você possua apenas uma assinatura.

    + **Selecione o aplicativo de funções no Azure**: Escolha `+ Create new Function App` (não `Advanced`). Este artigo não dá suporte ao [fluxo de publicação avançado](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure. 
    
    + **Insira um nome exclusivo globalmente para o aplicativo de funções**: Digite um nome que seja válido em um caminho de URL. O nome que você digitar é validado para ter certeza de que ele é exclusivo no Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Selecione um runtime**: Escolha a versão do Python em que você está executando localmente. É possível usar o comando `python --version` para verificar sua versão.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecione um runtime**: Escolha a versão do Node.js em que você está executando localmente. É possível usar o comando `node --version` para verificar sua versão.
    ::: zone-end

    + **Selecione uma localização para novos recursos**:  Para obter um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de você. 
    
1.  Quando concluído, os seguintes recursos do Azure serão criados em sua assinatura:

    + **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** : Contém todos os recursos criados do Azure. O nome é baseado no nome de seu aplicativo de funções.
    + **[Conta de armazenamento](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : Uma conta de armazenamento padrão é criada com um nome exclusivo baseado no nome de seu aplicativo de funções.
    + **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** : Um plano de consumo é criado na região Oeste dos EUA para hospedar seu aplicativo de funções sem servidor.
    + **Aplicativo de funções**: Seu projeto é implantado e executado no novo aplicativo de funções.
    + **Application Insights**: uma instância, que está conectada ao seu aplicativo de funções, é criada com base no nome da função.

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. 
    
1. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou. Se você perder a notificação, selecione o ícone de sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)
