---
title: Implantar um trabalho do Azure Stream Analytics usando o pacote npm CI/CD
description: Este artigo descreve como usar o pacote npm do Azure Stream Analytics CI/CD para configurar um processo contínuo de integração e implantação.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962128"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Implantar um trabalho do Azure Stream Analytics usando o pacote npm CI/CD 

Você pode usar o pacote de npm do Azure Stream Analytics PARA configurar um processo contínuo de integração e implantação para seus trabalhos de Stream Analytics. Este artigo descreve como usar o pacote npm em geral com qualquer sistema de CI/CD, bem como instruções específicas para implantação com a Azure Pipelines.

Para obter mais informações sobre a implantação com o Powershell, consulte [implantar com um arquivo de modelo do Gerenciador de recursos e com o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Você também pode aprender mais sobre como [usar um objeto como parâmetro em um modelo de Gerenciador de recursos](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Construa o projeto DE CÓDIGO VS

Você pode habilitar integração e implantação contínuas para trabalhos do Azure Stream Analytics usando o pacote **asa-streamanalytics-cicd** npm. O pacote npm fornece as ferramentas para gerar modelos do Azure Resource Manager de [projetos do Stream Analytics Visual Studio Code](quick-create-vs-code.md). Ele pode ser usado no Windows, macOS e Linux sem instalar o Visual Studio Code.

Você pode [baixar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) através do `npm install -g azure-streamanalytics-cicd` comando. Esta é a abordagem recomendada, que também pode ser usada em uma tarefa de script PowerShell ou Azure CLI de um pipeline de construção em **Azure Pipelines**.

Depois de instalar o pacote, use o seguinte comando para produzir os modelos do Azure Resource Manager. O argumento **scriptPath** é o caminho absoluto para o arquivo **asaql** em seu projeto. Certifique-se de que os arquivos asaproj.json e JobConfig.json estão na mesma pasta com o arquivo de script. Se o **outputPath** não for especificado, os modelos serão colocados na pasta **Implantar** na pasta **bin** do projeto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (no macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um projeto do Stream Analytics Visual Studio Code é criado com sucesso, ele gera os dois seguintes arquivos de modelo do Azure Resource Manager a pasta **bin/[Debug/Retail]/Deploy:** 

*  Arquivo de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Arquivo de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros padrão no arquivo parameters.json são das configurações do projeto Visual Studio Code. Se você deseja implantar em outro ambiente, apenas substitua os parâmetros adequadamente.

> [!NOTE]
> Para todas as credenciais, os valores padrão são definidos como nulo. Eles **precisam** ser definidos antes de serem implantados na nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implantação com o Azure Pipelines

Esta seção detalha como criar a [compilação](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [liberação](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) de pipelines do Azure Pipelines usando npm.

Abra um navegador da Web e navegue até o projeto Azure Stream Analytics Visual Studio Code.

1. Em **Pipelines** no menu de navegação à esquerda, **selecione Builds**. Em seguida, selecione **Novo pipeline**

   ![Criar novo pipeline Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecione **Use o editor clássico** para criar um pipeline sem YAML.

3. Selecione seu tipo de origem, projeto de equipe e repositório. Em seguida, **selecione Continuar**.

   ![Selecione o projeto Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na **página Escolher um modelo,** selecione **Esvaziar o trabalho**.

### <a name="add-npm-task"></a>Adicionar tarefa npm

1. Na página **Tarefas,** selecione o sinal de acrescido ao lado **do trabalho do Agente 1**. Digite "npm" na pesquisa de tarefas e selecione **npm**.

   ![Selecione a tarefa npm](./media/setup-cicd-vs-code/search-npm.png)

2. Dê à tarefa um **nome de exibição**. Altere a opção **Comando** para *personalizado* e digite o seguinte comando em **Comando e argumentos**. Deixe as opções padrão restantes.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Digite configurações para a tarefa npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Adicionar tarefa de linha de comando

1. Na página **Tarefas,** selecione o sinal de acrescido ao lado **do trabalho do Agente 1**. Procure a **linha de comando**.

2. Dê à tarefa um **nome de exibição** e digite o seguinte script. Modifique o script com seu nome de repositório e nome do projeto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Digite configurações para tarefa de linha de comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Adicionar tarefa de arquivos de cópia

1. Na página **Tarefas,** selecione o sinal de acrescido ao lado **do trabalho do Agente 1**. Pesquisar **arquivos Copy**. Em seguida, digite as seguintes configurações.

   |Parâmetro|Entrada|
   |-|-|
   |Nome de exibição|Copiar arquivos para: $(build.artifactstagingdirectory)|
   |Pasta de Origem|`$(system.defaultworkingdirectory)`| 
   |Conteúdo| `**\Deploy\**` |
   |Pasta de destino| `$(build.artifactstagingdirectory)`|

   ![Digite configurações para tarefa de cópia](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Adicionar publicar tarefa de construir artefatos

1. Na página **Tarefas,** selecione o sinal de acrescido ao lado **do trabalho do Agente 1**. Procure **publicar artefatos de construção** e selecione a opção com o ícone de seta preta. 

2. Não altere nenhuma das configurações padrão.

### <a name="save-and-run"></a>Salvar e Executar

Depois de terminar de adicionar o npm, a linha de comando, copiar arquivos e publicar tarefas de artefatos de compilação, **selecione Salvar & fila**. Quando for solicitado, digite um comentário salvar e **selecione Salvar e executar**.

## <a name="release-with-azure-pipelines"></a>Lançamento com Azure Pipelines

Abra um navegador da Web e navegue até o projeto Azure Stream Analytics Visual Studio Code.

1. Em **Pipelines** no menu de navegação à esquerda, **selecione Releases**. Em seguida, selecione **Novo pipeline**.

2. Selecione **começar com um trabalho vazio**.

3. Na caixa **Artefatos,** selecione **+ Adicione um artefato**. Em **Source,** selecione o pipeline de compilação que você criou e selecione **Adicionar**.

   ![Digite o artefato do gasoduto de construção](./media/setup-cicd-vs-code/build-artifact.png)

4. Alterar o nome da **Fase 1** para Implantar o trabalho para testar **o ambiente**.

5. Adicione um novo estágio e nomeie-o **Implantar trabalho ao ambiente de produção**.

### <a name="add-tasks"></a>Adicionar tarefas

1. A partir da parada de tarefas, **selecione Implantar trabalho para testar o ambiente**. 

2. Selecione **+** o trabalho ao lado **do Agente** e procure a *implantação do grupo de recursos do Azure*. Insira os parâmetros s seguir:

   |Configuração|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAJob*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Resource group| Escolha um nome para o grupo de recursos de teste que contenha seu trabalho no Stream Analytics.|
   |Location|Escolha o local do seu grupo de recursos de teste.|
   |Local do modelo| *Artefato ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parâmetros de modelo|($(Build.ArtifactStagingStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Substituir parâmetros de modelo|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Modo de implantação|Incremental|

3. A partir da parada de tarefas, **selecione Implantar trabalho para o ambiente de produção**.

4. Selecione **+** o trabalho ao lado **do Agente** e procure a *implantação do grupo de recursos do Azure*. Insira os parâmetros s seguir:

   |Configuração|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAJob*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Resource group| Escolha um nome para o grupo de recursos de produção que contenha seu trabalho no Stream Analytics.|
   |Location|Escolha a localização do seu grupo de recursos de produção.|
   |Local do modelo| *Artefato ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parâmetros de modelo|($(Build.ArtifactStagingStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Substituir parâmetros de modelo|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Modo de implantação|Incremental|

### <a name="create-release"></a>Criar versão

Para criar uma versão, selecione **Criar versão** no canto superior direito.

![Crie uma versão usando o Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Recursos adicionais

Para usar a Identidade Gerenciada para o Azure Data Lake Storage Gen1 como coletor de saída, você precisará fornecer Acesso à entidade de serviço usando o PowerShell antes da implantação no Azure. Saiba mais sobre como [implantar o ADLS Gen1 com a Identidade Gerenciada e o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Próximas etapas

* [Quickstart: Crie um trabalho na nuvem do Azure Stream Analytics no Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Test Stream Analytics consulta localmente com Visual Studio Code (Preview)](visual-studio-code-local-run.md)
* [Explore o Azure Stream Analytics com o Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
