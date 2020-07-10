---
title: Acompanhar arquivos atualizados com uma tarefa do observador da Automação do Azure
description: Este artigo informa como criar uma tarefa de observador na conta de Automação do Azure para inspecionar novos arquivos criados em uma pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 38963a8e1bfdbde50439ed871aa33e9aaa830d35
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185646"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Rastrear os arquivos atualizados com uma tarefa do observador

A Automação do Azure usa a tarefa de observador para cuidar de eventos e disparar ações com runbooks do PowerShell. A tarefa do observador contém duas partes, o observador e a ação. Um runbook do observador é executado em um intervalo definido na tarefa do observador e envia dados para um runbook de ação. 

> [!NOTE]
> Não há suporte para tarefas do observador no Azure China 21Vianet.

> [!IMPORTANT]
> A partir de maio de 2020, usar Aplicativos Lógicos do Azure será a maneira com suporte para monitorar eventos, agendar tarefas recorrentes e disparar ações. Consulte [Agendar e executar tarefas automatizadas, processos e fluxos de trabalho recorrentes com Aplicativos Lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

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
* [Conta de automação](./index.yml) para manter os runbooks de observador e de ação e a tarefa do observador.
* Um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) no qual a tarefa do observador é executada.
* Runbooks do PowerShell. Os runbooks de fluxo de trabalho do PowerShell não são suportados por tarefas do observador.

## <a name="import-a-watcher-runbook"></a>Importar um runbook observador

Este tutorial usa um runbook observador chamado **Watch-NewFile** para procurar novos arquivos em um diretório. O runbook observador recupera a última hora de gravação conhecido dos arquivos de uma pasta e examina todos os arquivos que sejam mais recentes do que essa marca-d'água.

Esse processo de importação pode ser feito por meio da [Galeria do PowerShell](https://www.powershellgallery.com).

1. Navegue até a página da galeria para [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na guia **Automação do Azure**, clique em **Implantar na Automação do Azure**.

Você também pode seguir as etapas abaixo para importar esse runbook na sua conta de automação do portal.

1. Abra a sua conta da Automação e clique na página Runbooks.
2. Clique em **Procurar na galeria**.
3. Pesquise **Runbook do observador**, selecione **Runbook de observador que procura novos arquivos em um diretório** e clique em **Importar**.
  ![Importar o runbook de automação por meio da interface do usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Dê um nome e uma descrição ao runbook e clique em **OK** para importar o runbook na sua conta da Automação.
5. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique em **Sim** para publicar o runbook.

## <a name="create-an-automation-variable"></a>Criar uma variável da Automação

Uma [variável de automação](./shared-resources/variables.md) é usada para armazenar os carimbos de data/hora que o runbook anterior lê e armazena de cada arquivo.

1. Selecione **Variáveis** em **Recursos compartilhados** e clique em **+ Adicionar uma variável**.
1. Digite "Watch-NewFileTimestamp" para o nome.
1. Selecione DateTime para o tipo.
1. Clique em **Criar** para criar a variável de Automação.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é usado em uma tarefa de observador para realizar ação nos dados passados para ela, provenientes de um runbook observador. Você precisa importar um runbook de ação predefinido chamado **Process-NewFile** da [Galeria do PowerShell](https://www.powershellgallery.com). 

Para criar um runbook de ação:

1. Navegue até a página de galeria do [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na guia **Automação do Azure**, clique em **Implantar na Automação do Azure**.

Você também pode importar esse runbook para sua conta de Automação do portal do Azure:

1. Navegue até sua conta de Automação e selecione **Runbooks** em **Automação de processo**.
1. Clique em **Procurar na galeria**.
1. Pesquise a **Ação do observador**, selecione **Ação de observador que processa eventos disparados por um runbook observador** e clique em **Importar**.
  ![Importar runbook de ação por meio da interface do usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê um nome e uma descrição ao runbook e clique em **OK** para importar o runbook na sua conta da Automação.
1. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique em **Sim** para publicar o runbook.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

Nesta etapa, você configura a tarefa de observador, referenciando os runbooks de ação e observador definidos nas seções anteriores.

1. Navegue até a sua conta da Automação e selecione **Tarefas de observador** em **Automação do processo**.
1. Selecione a página Tarefas do observador e clique em **+ Adicionar uma tarefa de observador**.
1. Digite **WatchMyFolder** como nome.

1. Selecione **Configurar observador** e escolha o runbook **Watch-NewFile**.

1. Insira os seguintes valores para os parâmetros:

   * **FOLDERPATH** - Uma pasta do Hybrid Runbook Worker em que novos arquivos são criados, por exemplo, **d:\examplefiles**.
   * **EXTENSION** - Extensão da configuração. Deixe em branco para processar todas as extensões de arquivo.
   * **RECURSE** - Operação recursiva. Deixe este valor como o padrão.
   * **RUN SETTINGS** - Configuração para executar o runbook. Escolha o Hybrid Worker.

1. Clique em **OK** e, sem seguida, em **Selecionar** para voltar à página do observador.
1. Selecione **Configurar ação** e escolha o runbook **Process-NewFile**.
1. Insira os seguintes valores para os parâmetros:

   * **EVENTDATA** - Dados do evento. Deixe em branco. Os dados são passados do runbook de observador.
   * **Run Settings** - Configuração para executar o runbook. Mantenha Azure, pois esse runbook é executado na Automação do Azure.

1. Clique em **OK** e, sem seguida, em **Selecionar** para voltar à página do observador.
1. Clique em **OK** para criar a tarefa de observador.

![Configurar ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Disparar um observador

Você precisa executar um teste, como descrito abaixo, para garantir que a tarefa do observador funcione como o esperado. 

1. Remoto no Hybrid Runbook Worker. 
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

1. Navegue até a sua conta da Automação e selecione **Tarefas de observador** em **Automação do processo**.
1. Selecione a tarefa de observador **WatchMyFolder**.
1. Clique em **Exibir fluxos do observador** em **Fluxos** para ver se o observador encontrou o novo arquivo e iniciou o runbook de ação.
1. Para ver os trabalhos do runbook de ação, clique em **Exibir trabalhos de ação do observador**. Cada trabalho pode ser selecionado para exibir os respectivos detalhes.

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
> [Criar runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md)
