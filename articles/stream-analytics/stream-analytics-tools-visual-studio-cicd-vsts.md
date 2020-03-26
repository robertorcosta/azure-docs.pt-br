---
title: Implantar trabalhos do Azure Stream Analytics com CI/CD e o Azure DevOps
description: Este artigo descreve como implantar um trabalho do Stream Analytics com CI/CD usando o Azure DevOps Services.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d9360ff64206cdce208f9643cf8ca86515aaeb7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75354429"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Tutorial: Implantar um trabalho do Azure Stream Analytics com CI/CD usando o Azure Pipelines
Esse tutorial descreve como configurar a integração e a implantação contínua para um trabalho do Azure Stream Analytics usando o Azure Pipelines. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar um pipeline de build no Azure Pipelines
> * Criar um pipeline de lançamento no Azure Pipelines
> * Implantar e atualizar automaticamente um aplicativo

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho de **Desenvolvimento do Azure** ou **Armazenamento e processamento de dados**.
* Crie um [projeto do Stream Analytics no Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Criar uma organização do [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Configurar a dependência do pacote NuGet
Para realizar a compilação e a implantação automáticas em um computador aleatório, você precisa usar o pacote do NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Ele fornece as ferramentas de MSBuild, execução local e implantação que dão suporte ao processo de implantação e integração contínua de projetos do Stream Analytics para Visual Studio. Para obter mais informações, consulte [Ferramentas de CI/CD do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Adicione **packages.config** ao diretório do projeto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Azure Repos

Compartilhe os arquivos de origem do aplicativo em um projeto no Azure DevOps para que você possa gerar builds.  

1. Crie um novo repositório Git local para seu projeto escolhendo **Adicionar ao controle do código-fonte** e **Git** na barra de status no canto inferior direito do Visual Studio. 

2. Na exibição **Sincronização** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Enviar por Push para o Azure DevOps**.

   ![Enviar por push para o Azure DevOps Services, botão Publicar Repositório Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Verifique seu email e selecione sua conta na lista suspensa **Domínio do Azure DevOps Services**. Digite o nome do seu repositório e selecione **Publicar Repositório**.

   ![Enviar por push Repositório Git, botão Publicar Repositório](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    A publicação do repositório cria um novo projeto na sua organização com o mesmo nome do repositório local. Para criar o repositório em um projeto existente, clique em **Avançado** ao lado do nome do **Repositório** e escolha um projeto. Você pode exibir seu código no navegador escolhendo **Vê-lo na Web**.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Configurar entrega contínua com o Azure DevOps
Um pipeline de build do Azure Pipelines descreve um fluxo de trabalho composto por etapas de build que são executadas sequencialmente. Saiba mais sobre os [pipelines de build do Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Um pipeline de lançamento do Azure Pipelines descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usados juntos, o pipeline de lançamento e o pipeline de build executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre os [pipelines de lançamento](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) do Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Criar um pipeline de build
Abra um navegador da Web e navegue até o projeto que você acabou de criar no [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Na guia **Build e versão**, escolha **Builds**e **+Novo**.  Selecione **Git do Azure DevOps Services** e **Continuar**.
    
    ![Selecione a fonte do Git de DevOps no Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. Em **Selecionar um modelo**, clique em **Processo vazio** para começar com um pipeline vazio.
    
    ![Selecione o processo vazio nas opções de modelo no DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. Em **Gatilhos**, habilite a integração contínua marcando o status do gatilho **Habilitar a integração contínua**.  Selecione **Salvar e enfileirar** para iniciar uma compilação manualmente. 
    
    ![Habilite o status de gatilho de integração contínua](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Os builds também serão disparados no envio por push ou no check-in. Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Assim que verificar que o build é executado com êxito, defina um pipeline de lançamento que implante seu aplicativo em um cluster. Clique com o botão direito nas elipses ao lado do seu pipeline de build e escolha **Editar**.

5.  Em **Tarefas**, insira "Hospedado" como a **Fila do agente**.
    
    ![Selecione a fila de agentes no menu de Tarefas](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. Em **Fase 1**, clique em **+** e adicione uma tarefa do **NuGet**.
    
    ![Adicione uma tarefa do NuGet na fila de agentes](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Expanda **Avançado** e adicione `$(Build.SourcesDirectory)\packages` ao **Diretório de destino**. Mantenha os valores de configuração padrão restantes do NuGet.

   ![Configure a tarefa de restauração do NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. Em **Fase 1**, clique em **+** e adicione uma tarefa do **MSBuild**.

   ![Adicione uma tarefa do MSBuild na fila de agentes](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Altere os **Argumentos do MSBuild** para o seguinte:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurar a tarefa do MSBuild no DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. Em **Fase 1**, clique em **+** e adicione uma tarefa de **Implantação do Grupo de Recursos do Azure**. 
    
    ![Adicionar uma tarefa de Implantação do Grupo de Recursos do Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Expanda os **Detalhes do Azure** e preencha a configuração da seguinte forma:
    
    |**Configuração**  |**Valor sugerido**  |
    |---------|---------|
    |Subscription  |  Escolha sua assinatura.   |
    |Ação  |  Criar ou atualizar o grupo de recursos   |
    |Grupo de recursos  |  Insira um nome de grupo de recursos.   |
    |Modelo  | [O caminho da sua solução]\bin\Debug\Deploy\\[O nome do seu projeto].JobTemplate.json   |
    |Parâmetros de modelo  | [O caminho da sua solução]\bin\Debug\Deploy\\[O nome do seu projeto].JobTemplate.parameters.json   |
    |Substituir parâmetros de modelo  | Digite os parâmetros de modelo para substituir a na caixa de texto. Exemplo, –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Essa propriedade é opcional, mas seu build apresentará erros se parâmetros principais não forem substituídos.    |
    
    ![Defina propriedades para a Implantação do Grupo de Recursos do Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Clique em **Salvar e enfileirar** para testar o pipeline de build.
    
    ![Salve e enfileire o build no DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Falha no processo de build
Você poderá receber erros para os parâmetros de implantação nulos se não tiver substituído os parâmetros de modelo na tarefa **Implantação do Grupo de Recursos do Azure** de seu pipeline de build. Retorne ao pipeline de build e substitua os parâmetros nulos para resolver o erro.

   ![Falha no processo de build do DevOps Stream Analytics](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Confirmar e enviar alterações por push para disparar uma versão
Verifique se o pipeline de integração contínua está funcionando conferindo algumas alterações de código no Azure DevOps.    

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local escolhendo o ícone de alterações pendentes na barra de status no canto inferior direito.

1. Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

    ![Confirme as alterações do repositório no Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Escolha o ícone de barra de status de alterações não publicadas ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Azure DevOps.

    ![Envie as alterações por push do Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

O push das alterações para o Azure DevOps Services dispara um build automaticamente.  Quando o pipeline de build é concluída com êxito, uma versão é criada automaticamente e inicia a atualização do trabalho no cluster.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, poderá interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado.  
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar as ferramentas do Azure Stream Analytics para Visual Studio para configurar um processo de implantação e de integração contínua, prossiga para o artigo de configuração do pipeline de CI/CD:

> [!div class="nextstepaction"]
> [Integrar e desenvolver continuamente com ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
