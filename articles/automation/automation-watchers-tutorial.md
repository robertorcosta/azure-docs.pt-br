---
title: Criar uma tarefa de observador na conta de Automação do Azure
description: Saiba como criar uma tarefa de observador na conta de Automação do Azure para inspecionar novos arquivos criados em uma pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617347"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar tarefas de observador de Automação do Azure para controlar alterações de arquivo em um computador local

O Azure Automation usa uma tarefa de observador para procurar eventos e desencadear ações com runbooks do PowerShell. A tarefa do observador contém duas partes, o observador e a ação. Um runbook de observador é executado em um intervalo definido na tarefa do observador e produz dados para um runbook de ação. 

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
* Livros de execução do PowerShell. Os runbooks do PowerShell Workflow não são suportados por tarefas de observador.

> [!NOTE]
> Tarefas de observadores não são suportadas na China Azure.

## <a name="import-a-watcher-runbook"></a>Importar um runbook observador

Este tutorial usa um runbook observador chamado **Watch-NewFile** para procurar novos arquivos em um diretório. O runbook observador recupera a última hora de gravação conhecido dos arquivos de uma pasta e examina todos os arquivos que sejam mais recentes do que essa marca-d'água.

Este processo de importação pode ser feito através da [Galeria PowerShell](https://www.powershellgallery.com).

1. Navegue até a página da galeria para [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na guia Automação do **Azure,** clique **em Implantar no Azure Automation**.

Você também pode importar este runbook para sua conta de automação do portal usando as seguintes etapas.

1. Abra a sua conta da Automação e clique na página Runbooks.
2. Clique **em Procurar galeria**.
3. Procure **o runbook Watcher**, selecione **O manual do Watcher que procura novos arquivos em um diretório**e clique em **Importar**.
  ![Importar o runbook de automação por meio da interface do usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Dê ao runbook um nome e uma descrição e clique em **OK** para importar o runbook para sua conta de Automação.
5. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique **em Sim** para publicar o manual.

## <a name="create-an-automation-variable"></a>Criar uma variável da Automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de data/hora que o runbook anterior lê e armazena de cada arquivo.

1. Selecione Variáveis em **Recursos Compartilhados** e clique **em + Adicionar uma variável**. **Variables**
1. Digite Watch-NewFileTimestamp para obter o nome.
1. Selecione DateTime para o tipo.
1. Clique **em Criar** para criar a variável Automação.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é usado em uma tarefa de observador para realizar ação nos dados passados para ela, provenientes de um runbook observador. Você deve importar um runbook de ação predefinido chamado **Process-NewFile** da [PowerShell Gallery](https://www.powershellgallery.com). 

Para criar um runbook de ação:

1. Navegue até a página da galeria para [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na guia Automação do **Azure,** clique **em Implantar no Azure Automation**.

Você também pode importar este runbook para sua conta de Automação no portal do Azure:

1. Navegue até sua conta de Automação e selecione **Runbooks** em **Automação de Processos**.
1. Clique **em Procurar galeria**.
1. Procure a **ação Watcher**, selecione A ação Watcher **que processa eventos desencadeados por um runbook de observadores**e clique em **Importar**.
  ![Importar runbook de ação por meio da interface do usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê ao runbook um nome e uma descrição e clique em **OK** para importar o runbook para sua conta de Automação.
1. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique **em Sim** para publicar o manual.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

Nesta etapa, você configura a tarefa watcher referenciando os runbooks de watcher e action definidos nas seções anteriores.

1. Navegue até sua conta de automação e selecione **tarefas do Watcher** em **Automação de Processos**.
1. Selecione a página de tarefas do Observador e clique **em + Adicione uma tarefa de observador**.
1. Digite **WatchMyFolder** como o nome.

1. Selecione **Configurar observador** e escolha o **runbook Watch-NewFile.**

1. Insira os seguintes valores para os parâmetros:

   * **FOLDERPATH** - Uma pasta no Hybrid Runbook Worker onde novos arquivos são criados, por exemplo, **d:\examplefiles**.
   * **EXTENSÃO** - Extensão para a configuração. Deixe em branco para processar todas as extensões de arquivo.
   * **REMALDIÇÃO** - Operação recursiva. Deixe esse valor como padrão.
   * **CONFIGURAÇÕES DE EXECUÇÃO** - Configuração para executar o runbook. Escolha o Hybrid Worker.

1. Clique **em OK**e, em seguida, **Selecione** para retornar à página Observador.
1. Selecione **Configurar ação** e escolha o **manual process-NewFile.**
1. Insira os seguintes valores para os parâmetros:

   * **EVENTDATA** - Dados do evento. Deixe em branco. Os dados são passados do runbook de observador.
   * **Configurações de execução** - Configuração para executar o runbook. Deixe como Azure, como este runbook é executado na Azure Automation.

1. Clique **em OK**e, em seguida, **Selecione** para retornar à página Observador.
1. Clique em **OK** para criar a tarefa de observador.

![Configurar ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Disparar um observador

Você deve executar um teste conforme descrito abaixo para garantir que a tarefa de observador funcione como esperado. 

1. Controle remoto no Híbrido Runbook Worker. 
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

1. Navegue até sua conta de automação e selecione **tarefas do Watcher** em **Automação de Processos**.
1. Selecione a tarefa de observador **WatchMyFolder**.
1. Clique em **Exibir fluxos de observador em** **Streams** para ver se o observador encontrou o novo arquivo e iniciou o runbook de ação.
1. Para ver os trabalhos do manual de ação, clique em **Exibir trabalhos de ação do observador**. Cada trabalho pode ser selecionado para visualizar os detalhes do trabalho.

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

