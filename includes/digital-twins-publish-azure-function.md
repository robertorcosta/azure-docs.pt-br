---
author: baanders
description: incluir um arquivo do Visual Studio no processo de publicação de uma função do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 069a29f49172cf3bad6b7f7b6aca28f32f5d63b3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683820"
---
Para publicar o projeto em um aplicativo de funções do Azure, clique com o botão direito do mouse em *Gerenciador de Soluções*, selecione o projeto, depois clique em **Publicar**.

> [!IMPORTANT] 
> Ao fazer uma publicação em um aplicativo de funções do Azure, sua assinatura incorrerá em encargos adicionais, mesmo que você use os Gêmeos Digitais do Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: publicar projeto":::

Na página *Publicar* que aparece, deixe a seleção de destino padrão **Azure** e pressione *Avançar*. 

Para um destino específico, escolha **Aplicativo de Funções do Azure (Windows)** e pressione *Avançar*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Publicar função do Azure no Visual Studio: destino específico":::

Na página da *instância do Azure Functions*, escolha sua assinatura. Isso deve popular uma caixa com os *grupos de recursos* em sua assinatura.

Selecione o grupo de recursos de sua instância e clique em *+* para criar uma função do Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Publicar função do Azure no Visual Studio: instância do Azure Functions (antes do aplicativo de funções)":::

Na janela *Aplicativo de Funções (Windows) – Criar*, preencha os campos da seguinte maneira:
* **Nome** é o nome do plano de consumo que o Azure usará para hospedar o aplicativo do Azure Functions. Ele também se tornará o nome do aplicativo de funções que contém sua função real. Você pode escolher seu valor exclusivo ou deixar a sugestão padrão.
* **Assinatura** deve corresponder à assinatura que você deseja usar 
* Da mesma forma, o **Grupo de recursos** deve corresponder ao que você deseja usar
* Deixe o **Tipo de plano** como *Consumo*
* Selecione **Local** correspondente ao local de seu grupo de recursos
* Crie um recurso do **Armazenamento do Azure** usando o link *Novo...* . Defina o local de modo que corresponda ao seu grupo de recursos, use os outros valores padrão e selecione "OK".

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Publicar função do Azure no Visual Studio: Aplicativo de Funções (Windows) – Criar":::

Em seguida, selecione **Criar**.

Isso deve levar você de volta à página da *instância do Azure Functions*, em que seu novo aplicativo de funções agora está visível abaixo do grupo de recursos. Selecione *Concluir*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publicar função do Azure no Visual Studio: instância do Azure Functions (após o aplicativo de funções)":::

No painel *Publicar* que é aberto na janela principal do Visual Studio, verifique se todas as informações parecem corretas e selecione **Publicar**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Publicar função do Azure no Visual Studio: publicar":::

> [!NOTE]
> Você verá um pop-up como este: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Publicar função do Azure no Visual Studio: publicar credenciais" border="false":::
> Selecione **Tentativa de recuperar credenciais do Azure** e escolha **Salvar**.
>
> Se você vir um aviso para *Versão das Funções de Atualização no Azure* ou se *Sua versão do runtime de funções não corresponder à versão em execução no Azure*:
>
> siga os prompts para atualizar para a versão mais recente do Azure Functions runtime. Esse problema poderá ocorrer caso esteja usando uma versão anterior do Visual Studio.

Para obter acesso aos Gêmeos Digitais do Azure, o aplicativo de funções precisará de uma identidade gerenciada pelo sistema e permissões para acessar sua instância dos Gêmeos Digitais do Azure. Você vai configurar tudo isso a seguir.
