---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5ef61502d0b2fccc92ca606992e965b45764baa0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744207"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Criar um WebJob disparado manualmente

1. Foi obtido com a [portal do Azure](https://portal.azure.com).
1. Vá para o **serviço de aplicativo** do seu <abbr title="Seu recurso de aplicativo pode ser um aplicativo Web, um aplicativo de API ou um aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

    ![Selecionar o WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs**, selecione **Adicionar**.

   ![Página do WebJob](../media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

    ![Captura de tela que mostra as configurações que precisam ser definidas para a criação de um WebJob disparado manualmente.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Configuração      | Valor de exemplo   | Descrição  |
    | ------------ | ----------------- | ------------ |
   | <abbr title="Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de `-` e `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script.">Carregar arquivos</abbr> | ConsoleApp.zip |
    | <abbr title="Os tipos incluem contínuo, disparado.">Tipo</abbr> | Disparado | 
    | <abbr title="Os tipos incluem agendado ou manual">Gatilhos</a> | Manual | |

4. Clique em **OK**. 

   O novo WebJob é exibido na página **WebJobs**.

   ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Para executar um WebJob manual**, clique com o botão direito do mouse em seu nome na lista e clique em **executar**.
   
    ![Executar o Trabalho Web](../media/web-sites-create-web-jobs/runondemand.png)

