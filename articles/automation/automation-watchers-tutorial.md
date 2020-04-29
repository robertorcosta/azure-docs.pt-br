---
title: Criar uma tarefa de observador na conta de Automação do Azure
description: Saiba como criar uma tarefa de observador na conta de Automação do Azure para inspecionar novos arquivos criados em uma pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617347"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar tarefas de observador de Automação do Azure para controlar alterações de arquivo em um computador local

A automação do Azure usa uma tarefa do observador para procurar eventos e disparar ações com runbooks do PowerShell. A tarefa do Inspetor contém duas partes, o Inspetor e a ação. Um runbook do observador é executado em um intervalo definido na tarefa do Inspetor e gera dados para um runbook de ação. 

Este tutorial orienta na criação de uma tarefa de observador para monitorar quando um novo arquivo é adicionado a um diretório. Você aprenderá como:

> [!div class="checklist"]
> * Importar um runbook observador
> * Criar uma variável da Automação
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Disparar um observador
> * Inspecionar a saída

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter os runbooks de observador e de ação e a tarefa do observador.
* Um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) no qual a tarefa do observador é executada.
* Runbooks do PowerShell. Os runbooks de fluxo de trabalho do PowerShell não têm suporte nas tarefas do Inspetor.

> [!NOTE]
> Não há suporte para tarefas do Inspetor no Azure China.

## <a name="import-a-watcher-runbook"></a>Importar um runbook observador

Este tutorial usa um runbook observador chamado **Watch-NewFile** para procurar novos arquivos em um diretório. O runbook observador recupera a última hora de gravação conhecido dos arquivos de uma pasta e examina todos os arquivos que sejam mais recentes do que essa marca-d'água.

Esse processo de importação pode ser feito por meio do [Galeria do PowerShell](https://www.powershellgallery.com).

1. Navegue até a página da galeria para [Watch-newfile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na guia **automação do Azure** , clique em **implantar na automação do Azure**.

Você também pode importar esse runbook para sua conta de automação do portal usando as etapas a seguir.

1. Abra a sua conta da Automação e clique na página Runbooks.
2. Clique em **procurar na Galeria**.
3. Pesquise o **runbook do Inspetor**, selecione **runbook do inspetor que procura por novos arquivos em um diretório**e clique em **importar**.
  ![Importar o runbook de automação por meio da interface do usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Dê um nome e uma descrição ao runbook e clique em **OK** para importar o runbook para sua conta de automação.
5. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique em **Sim** para publicar o runbook.

## <a name="create-an-automation-variable"></a>Criar uma variável da Automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de data/hora que o runbook anterior lê e armazena de cada arquivo.

1. Selecione **variáveis** em **recursos compartilhados** e clique em **+ Adicionar uma variável**.
1. Digite Watch-NewFileTimestamp para o nome.
1. Selecione DateTime para o tipo.
1. Clique em **criar** para criar a variável de automação.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é usado em uma tarefa de observador para realizar ação nos dados passados para ela, provenientes de um runbook observador. Você deve importar um runbook de ação predefinido chamado **process-NewFile** do [Galeria do PowerShell](https://www.powershellgallery.com). 

Para criar um runbook de ação:

1. Navegue até a página da galeria para [process-newfile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na guia **automação do Azure** , clique em **implantar na automação do Azure**.

Você também pode importar esse runbook para sua conta de automação do portal do Azure:

1. Navegue até sua conta de automação e selecione **Runbooks** em **automação de processo**.
1. Clique em **procurar na Galeria**.
1. Pesquisar **ação do observador**, selecione **ação do observador que processa eventos disparados por um runbook do Inspetor**e clique em **importar**.
  ![Importar runbook de ação por meio da interface do usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê um nome e uma descrição ao runbook e clique em **OK** para importar o runbook para sua conta de automação.
1. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique em **Sim** para publicar o runbook.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

Nesta etapa, você configura a tarefa do inspetor que faz referência aos runbooks do Inspetor e da ação definidos nas seções anteriores.

1. Navegue até sua conta de automação e selecione **tarefas do Inspetor** em automação do **processo**.
1. Selecione a página tarefas do Inspetor e clique em **+ Adicionar uma tarefa do Inspetor**.
1. Insira **WatchMyFolder** como o nome.

1. Selecione **Configurar Inspetor** e escolha o runbook **Watch-NewFile** .

1. Insira os seguintes valores para os parâmetros:

   * **FOLDERPATH** -uma pasta na Hybrid runbook Worker onde novos arquivos são criados, por exemplo, **d:\examplefiles**.
   * **Extensão-extensão** para a configuração. Deixe em branco para processar todas as extensões de arquivo.
   * **Recurse** -operação recursiva. Deixe esse valor como o padrão.
   * **Configurações de execução** -configuração para executar o runbook. Escolha o Hybrid Worker.

1. Clique em **OK**e, em seguida, **selecione** para retornar à página do Inspetor.
1. Selecione **Configurar ação** e escolha o runbook **process-NewFile** .
1. Insira os seguintes valores para os parâmetros:

   * **EVENTDATA** -dados de evento. Deixe em branco. Os dados são passados do runbook de observador.
   * **Configurações de execução** -configuração para executar o runbook. Deixe como o Azure, pois esse runbook é executado na automação do Azure.

1. Clique em **OK**e, em seguida, **selecione** para retornar à página do Inspetor.
1. Clique em **OK** para criar a tarefa do Inspetor.

![Configurar ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Disparar um observador

Você deve executar um teste conforme descrito abaixo para garantir que a tarefa do Inspetor funcione conforme o esperado. 

1. Remoto para o Hybrid Runbook Worker. 
2. Abra o **PowerShell** e crie um arquivo de teste na pasta.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

O exemplo a seguir mostra a saída esperada.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspecionar a saída

1. Navegue até sua conta de automação e selecione **tarefas do Inspetor** em automação do **processo**.
1. Selecione a tarefa do Inspetor **WatchMyFolder**.
1. Clique em **Exibir fluxos do Inspetor** em **fluxos** para ver se o Inspetor encontrou o novo arquivo e iniciou o runbook de ação.
1. Para ver os trabalhos de runbook de ação, clique em **exibir trabalhos de ação do observador**. Cada trabalho pode ser selecionado para exibir os detalhes do trabalho.

   ![Trabalhos de ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/WatcherActionJobs.png)

A saída esperada, quando o novo arquivo é encontrado, pode ser vista no exemplo a seguir:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar um runbook observador
> * Criar uma variável da Automação
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Disparar um observador
> * Inspecionar a saída

Siga este link para saber mais sobre como criar seu próprio runbook.

> [!div class="nextstepaction"]
> [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

