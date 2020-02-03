---
title: Implantar um trabalho de Azure Stream Analytics usando o pacote NPM de CI/CD
description: Este artigo descreve como usar Azure Stream Analytics pacote de NPM CI/CD para configurar um processo de implantação e integração contínua.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962128"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Implantar um trabalho de Azure Stream Analytics usando o pacote NPM de CI/CD 

Você pode usar o pacote Azure Stream Analytics CI/CD NPM para configurar um processo de implantação e integração contínua para seus trabalhos de Stream Analytics. Este artigo descreve como usar o pacote NPM em geral com qualquer sistema de CI/CD, bem como instruções específicas para implantação com o Azure Pipelines.

Para obter mais informações sobre a implantação com o PowerShell, consulte [implantar com um arquivo de modelo do Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Você também pode aprender mais sobre como [usar um objeto como um parâmetro em um modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Criar o projeto de VS Code

Você pode habilitar a integração e a implantação contínuas para trabalhos de Azure Stream Analytics usando o pacote NPM- **streamanalytics-cICD** . O pacote NPM fornece as ferramentas para gerar Azure Resource Manager modelos de [Stream Analytics projetos de Visual Studio Code](quick-create-vs-code.md). Ele pode ser usado no Windows, no macOS e no Linux sem instalar Visual Studio Code.

Você pode [baixar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) por meio do comando `npm install -g azure-streamanalytics-cicd`. Essa é a abordagem recomendada, que também pode ser usada em uma tarefa do PowerShell ou CLI do Azure script de um pipeline de compilação no **Azure pipelines**.

Depois de instalar o pacote, use o comando a seguir para gerar os modelos de Azure Resource Manager. O argumento **ScriptPath** é o caminho absoluto para o arquivo **asaql** em seu projeto. Verifique se os arquivos asaproj. JSON e JobConfig. JSON estão na mesma pasta com o arquivo de script. Se o **outputPath** não for especificado, os modelos serão colocados na pasta **Deploy** na pasta **bin** do projeto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (no macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um Stream Analytics Visual Studio Code projeto é compilado com êxito, ele gera os dois arquivos de modelo de Azure Resource Manager a seguir na pasta **bin/[Debug/Retail]/Deploy** : 

*  Arquivo de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Arquivo de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros padrão no arquivo Parameters. JSON são das configurações no seu projeto Visual Studio Code. Se você deseja implantar em outro ambiente, apenas substitua os parâmetros adequadamente.

> [!NOTE]
> Para todas as credenciais, os valores padrão são definidos como nulo. Eles **precisam** ser definidos antes de serem implantados na nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implantar com Azure Pipelines

Esta seção fornece detalhes sobre como criar [Azure pipelines criar](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [liberar](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) pipelines usando o NPM.

Abra um navegador da Web e navegue até seu Azure Stream Analytics Visual Studio Code projeto.

1. Em **pipelines** no menu de navegação à esquerda, selecione **Builds**. Em seguida, selecione **novo pipeline**

   ![Criar novo pipeline do Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecione **usar o editor clássico** para criar um pipeline sem YAML.

3. Selecione o tipo de origem, o projeto de equipe e o repositório. Depois selecione **Continuar**.

   ![Selecionar Azure Stream Analytics projeto](./media/setup-cicd-vs-code/select-repo.png)

4. Na página **escolher um modelo** , selecione **trabalho vazio**.

### <a name="add-npm-task"></a>Adicionar tarefa NPM

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Insira "NPM" na pesquisa de tarefas e selecione **NPM**.

   ![Selecionar tarefa NPM](./media/setup-cicd-vs-code/search-npm.png)

2. Dê um nome de **exibição**à tarefa. Altere a opção de **comando** para *personalizado* e insira o comando a seguir em **comando e argumentos**. Deixe as opções padrão restantes.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Inserir configurações para a tarefa NPM](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Adicionar tarefa de linha de comando

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Pesquise por **linha de comando**.

2. Dê à tarefa um **nome de exibição** e insira o script a seguir. Modifique o script com o nome do seu repositório e o nome do projeto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Inserir configurações para tarefa de linha de comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Tarefa adicionar arquivos de cópia

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Procure por **arquivos de cópia**. Em seguida, insira as configurações a seguir.

   |Parâmetro|Entrada|
   |-|-|
   |Nome de exibição|Copiar arquivos para: $ (Build. artifactstagingdirectory)|
   |Pasta de Origem|`$(system.defaultworkingdirectory)`| 
   |Conteúdo| `**\Deploy\**` |
   |Pasta de destino| `$(build.artifactstagingdirectory)`|

   ![Inserir configurações para copiar tarefa](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Adicionar tarefa publicar artefatos de compilação

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Pesquise **artefatos de compilação de publicação** e selecione a opção com o ícone de seta preta. 

2. Não altere nenhuma das configurações padrão.

### <a name="save-and-run"></a>Salvar e executar

Depois de concluir a adição das tarefas NPM, linha de comando, copiar arquivos e publicar artefatos de compilação, selecione **salvar & fila**. Quando solicitado, insira um comentário salvar e selecione **salvar e executar**.

## <a name="release-with-azure-pipelines"></a>Versão com Azure Pipelines

Abra um navegador da Web e navegue até seu Azure Stream Analytics Visual Studio Code projeto.

1. Em **pipelines** no menu de navegação à esquerda, selecione **versões**. Em seguida, selecione **novo pipeline**.

2. Selecione **Iniciar com um trabalho vazio**.

3. Na caixa **artefatos** , selecione **+ Adicionar um artefato**. Em **origem**, selecione o pipeline de compilação que você acabou de criar e selecione **Adicionar**.

   ![Inserir artefato de pipeline de compilação](./media/setup-cicd-vs-code/build-artifact.png)

4. Altere o nome do **estágio 1** para **implantar o trabalho no ambiente de teste**.

5. Adicione um novo estágio e nomeie-o **como implantar trabalho no ambiente de produção**.

### <a name="add-tasks"></a>Adicionar tarefas

1. Na lista suspensa tarefas, selecione **implantar trabalho para o ambiente de teste**. 

2. Selecione a **+** ao lado de **trabalho do Agent** e pesquise implantação do grupo de *recursos do Azure*. Insira os parâmetros s seguir:

   |Configuração|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAJob*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de teste que conterá seu trabalho de Stream Analytics.|
   |Local|Escolha o local do seu grupo de recursos de teste.|
   |Local do modelo| *Artefato vinculado*|
   |Modelo| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parâmetros de modelo|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Substituir parâmetros de modelo|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Modo de implantação|Incremental|

3. Na lista suspensa tarefas, selecione **implantar trabalho no ambiente de produção**.

4. Selecione a **+** ao lado de **trabalho do Agent** e pesquise implantação do grupo de *recursos do Azure*. Insira os parâmetros s seguir:

   |Configuração|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAJob*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de produção que conterá seu trabalho de Stream Analytics.|
   |Local|Escolha o local do seu grupo de recursos de produção.|
   |Local do modelo| *Artefato vinculado*|
   |Modelo| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parâmetros de modelo|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Substituir parâmetros de modelo|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Modo de implantação|Incremental|

### <a name="create-release"></a>Criar versão

Para criar uma versão, selecione **criar versão** no canto superior direito.

![Criar uma versão usando Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Recursos adicionais

Para usar a Identidade Gerenciada para o Azure Data Lake Storage Gen1 como coletor de saída, você precisará fornecer Acesso à entidade de serviço usando o PowerShell antes da implantação no Azure. Saiba mais sobre como [implantar o ADLS Gen1 com a Identidade Gerenciada e o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Próximos passos

* [Início rápido: criar um trabalho de Azure Stream Analytics nuvem no Visual Studio Code (visualização)](quick-create-vs-code.md)
* [Testar Stream Analytics consultas localmente com Visual Studio Code (versão prévia)](visual-studio-code-local-run.md)
* [Explorar Azure Stream Analytics com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)
