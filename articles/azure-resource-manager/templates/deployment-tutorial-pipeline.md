---
title: Integração contínua com o Azure Pipelines
description: Saiba como criar, testar e implantar continuamente modelos do Azure Resource Manager.
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1c56ce913a1b63bab90f5dd5aaada382abbf493
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084321"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines

No [tutorial anterior](./deployment-tutorial-linked-template.md), você implantou um modelo vinculado.  Neste tutorial, você saberá como usar o Azure Pipelines para criar e implantar continuamente os projetos de modelo do Azure Resource Manager.

O Azure DevOps fornece serviços de desenvolvedor para capacitar as equipes a fim de planejar o trabalho, colaborar com o desenvolvimento de código e criar e implantar aplicativos. Os desenvolvedores podem trabalhar na nuvem usando o Azure DevOps Services. O Azure DevOps fornece um conjunto integrado de recursos que pode ser acessado por meio do seu navegador da Web ou do cliente do IDE. O Azure Pipelines é um desses recursos. O Azure Pipelines é um serviço CI (integração contínua) e CD (entrega contínua) cheio de recursos. Ele funciona com seu provedor Git de preferência e pode ser implantado na maioria dos principais serviços de nuvem. É possível automatizar o build, o teste e a implantação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

> [!NOTE]
> Escolha um nome de projeto. Ao percorrer o tutorial, substitua qualquer **AzureRmPipeline** pelo nome do projeto.
> Esse nome do projeto é usado para gerar nomes de recurso.  Um dos recursos é uma conta de armazenamento. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O nome deve ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com "armazenamento" acrescentado, e o nome do projeto deve ter entre 3 e 11 caracteres. Portanto, o nome do projeto deve atender aos requisitos de nome da conta de armazenamento e ter menos de 11 caracteres.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório GitHub
> * Criar um projeto de DevOps do Azure
> * Criar um pipeline do Azure
> * Verificar a implantação do pipeline
> * Atualizar o modelo e reimplantar
> * Limpar os recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* **Uma conta do GitHub**, em que você a usa para criar um repositório para seus modelos. Se não tiver uma, poderá [criá-la gratuitamente](https://github.com). Para saber mais sobre como usar os repositórios GitHub, confira [Criar repositórios GitHub](/azure/devops/pipelines/repos/github).
* **Instalar o Git**. Essa instrução tutorial usa o *Git Bash* ou o *Git Shell*. Para obter instruções, confira [Instalar o Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização do Azure DevOps**. Se não tiver uma, poderá criá-la gratuitamente. Confira [Criar uma coleção de projeto ou organização]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* (opcional) **Visual Studio Code com a extensão de Ferramentas do Resource Manager**. Confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Preparar um repositório GitHub

O GitHub é usado para armazenar o código-fonte do seu projeto incluindo modelos do Resource Manager. Para ver outros repositórios compatíveis, confira [repositórios compatíveis com o Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se você não tiver uma conta do GitHub, confira [Pré-requisitos](#prerequisites).

1. Entre no [GitHub](https://github.com).
1. Selecione sua imagem de conta no canto superior direito e, em seguida, selecione **Seus repositórios**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar repositório GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **Novo**, um botão verde.
1. No **Nome do repositório**, insira um nome do repositório.  Por exemplo, **AzureRmPipeline-repo**. Substitua qualquer **AzureRmPipeline** pelo nome do projeto. É possível selecionar **público** ou **privado** para percorrer este tutorial. E, em seguida, selecione **Criar repositório**.
1. Anote a URL. A URL do repositório está o seguinte formato: **https://github.com/ [NomeDaSuaConta]/[NomeDoSeuRepositorio]** .

Esse repositório é conhecido como um *repositório remoto*. Cada um dos desenvolvedores do mesmo projeto pode clonar seu próprio *repositório local* e mesclar as alterações no repositório remoto.

### <a name="clone-the-remote-repository"></a>Clonar o repositório remoto

1. Abra o Git Shell ou o Git Bash.  Consulte [Pré-requisitos](#prerequisites).
1. Verifique se sua pasta atual é **GitHub**.
1. Execute o comando a seguir:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Substitua o **[NomeDaSuaConta]** pelo nome da conta do GitHub e **[NomeDoSeuRepositórioGitHub]** pelo nome do repositório criado no procedimento anterior.

A pasta **CreateWebApp** é a pasta em que o modelo é armazenado. O comando **pwd** mostra o caminho da pasta. O caminho é local em que você salva o modelo no procedimento a seguir.

### <a name="download-a-quickstart-template"></a>Baixar um Modelo de início rápido

Em vez de criar os modelos, você pode baixá-los e salvá-los na pasta **CreateWebApp**.

* O modelo principal: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* O modelo vinculado: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

O nome da pasta e o nome do arquivo são usados como estão no pipeline.  Se você alterar esses nomes, deverá atualizar os nomes usados no pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Enviar por push o modelo para o repositório remoto

O azuredeploy.json foi adicionado ao repositório local. Em seguida, faça upload do modelo para o repositório remoto.

1. Abra o *Git Shell* ou o *Git Bash*, se ainda não estiver aberto.
1. Altere o diretório para a pasta CreateWebApp em seu repositório local.
1. Verifique se o arquivo **azuredeploy.json** está na pasta.
1. Execute o comando a seguir:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Você pode receber um aviso sobre LF. É possível ignorá-lo. **master** é o branch mestre.  Normalmente, você cria um branch para cada atualização. Para simplificar o tutorial, use o branch mestre diretamente.
1. Navegue até o repositório GitHub em um navegador.  A URL é **https://github.com/ [NomeDaSuaConta]/[RepositórioGitHub]** . Você deverá ver a pasta **CreateWebApp** e os três arquivos dentro dela.
1. Selecione **linkedStorageAccount.json** para abrir o modelo.
1. Selecione a botão **Bruto**. A URL é iniciado com **raw.githubusercontent.com**.
1. Faça uma cópia da URL.  Você precisará fornecer esse valor quando configurar o pipeline posteriormente no tutorial.

Até agora, você criou um repositório GitHub e carregou os modelos para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto de DevOps

Uma organização de DevOps é necessária antes de poder passar para o próximo procedimento.  Se você não tiver uma, confira [Pré-requisitos](#prerequisites).

1. Entre no [Azure DevOps](https://dev.azure.com).
1. Selecione uma organização de DevOps à esquerda.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar projeto do Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Novo projeto**. Se você não tiver nenhum projeto, a página criar projeto será aberta automaticamente.
1. Insira os valores a seguir:

    * **Nome do projeto**: insira um nome para o projeto. É possível usar o nome do projeto selecionado no começo do tutorial.
    * **Controle de versão**: Selecione **Git**. Talvez seja necessário expandir **Avançado** para ver **Controle de versão**.

    Use o valor padrão para outras propriedades.
1. Selecione **Criar**.

Crie uma conexão de serviço usada para implantar projetos no Azure.

1. Selecione as **Configurações do projeto** na parte inferior do menu esquerdo.
1. Selecione **Conexões de serviço** em **Pipelines**.
1. Selecione **Nova conexão de serviço**, **Azure Resource Manager** e, em seguida, selecione **Avançar**.
1. Selecione **Entidade de serviço** e, em seguida, **Avançar**.
1. Insira os valores a seguir:

    * **Nível do escopo**: selecione **Assinatura**.
    * **Assinatura**: selecione sua assinatura.
    * **Grupo de Recursos**: Deixe em branco.
    * **Nome da conexão**: insira um nome para a conexão. Por exemplo, **AzureRmPipeline-conn**. Anote esse nome; você precisará dele ao criar seu pipeline.
    * **Conceder permissão de acesso a todos os pipelines**. (selecionado)
1. Clique em **Salvar**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, você concluiu as seguintes tarefas.  Se você ignorar as seções anteriores porque conhece o GitHub e o DevOps, será necessário concluir as tarefas antes de continuar.

* Crie um repositório GitHub e salve os modelos na pasta **CreateWebApp** no repositório.
* Crie um projeto de DevOps e uma conexão de serviço do Azure Resource Manager.

Para criar um pipeline com uma etapa para implantar um modelo:

1. Selecione **Pipelines** no menu esquerdo.
1. Selecione **Novo pipeline**.
1. Na guia **Conectar**, selecione **GitHub**. Se solicitado, insira suas credenciais do GitHub e siga as instruções. Se você vir a tela a seguir, selecione **Selecionar apenas repositórios** e verifique se seu repositório está na lista antes de selecionar **Aprovar e instalar**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines selecionar somente repositórios](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na guia **Selecionar**, selecione seu repositório.  O nome padrão é **[NomeDaSuaConta]/[NomeDoSeuRepositórioGitHub]** .
1. Na guia **Configurar**, selecione **Pipeline inicial**. Ela mostra o arquivo de pipeline **azure-pipelines.yml** com duas etapas de script.
1. Exclua as duas etapas de script do arquivo yml.
1. Mova o cursor para a linha após **etapas:** .
1. Selecione **Mostrar assistente** à direita da tela para abrir o painel **Tarefas**.
1. Selecione **Implantação de modelo do ARM**.
1. Insira os valores a seguir:

    * **deploymentScope**: Selecione **Grupo de Recursos**. Para saber mais sobre os escopos, confira [Escopos de implantação](deploy-rest.md#deployment-scope).
    * **Conexão do Azure Resource Manager**: especifique o nome da conexão de serviço criada anteriormente.
    * **Assinatura**:  Especifique a ID da assinatura de destino.
    * **Ação**: selecionar a ação **Criar ou Atualizar Grupo de Recursos** executa 2 ações – 1. criar um grupo de recursos se um novo nome de grupo de recursos for fornecido; 2. implantar o modelo especificado.
    * **Grupo de recursos**: insira um nome para o novo grupo de recursos. Por exemplo, **AzureRmPipeline-rg**.
    * **Localização**: selecione um local para o grupo de recursos, por exemplo, **EUA Central**.
    * **Local do modelo**: selecione **Artefato vinculado**, que significa que a tarefa pesquisa o arquivo de modelo diretamente do repositório conectado.
    * **Modelo**: digite **CreateWebApp/azuredeploy.json**. Se você tiver alterado o nome da pasta e o nome do arquivo, precisará alterar esse valor.
    * **Parâmetros de modelo**: Deixe este campo em branco. Você especificará os valores de parâmetro em *Substituir parâmetros de modelo.
    * **overrideParameters**: digite **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]** . Substitua o nome do projeto e a URL do modelo vinculado. A URL do modelo vinculado é o que você anotou ao final de [Criar um repositório GitHub](#create-a-github-repository).
    * **Modo de implantação**: selecione **Incremental**.
    * **Nome da implantação**: digite **DeployPipelineTemplate**. Selecione **Avançado** antes de poder ver o **Nome da implantação**.

    ![Etapa do Azure Resource Manager Azure DevOps Azure Pipelines](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selecione **Adicionar**.

    Para obter mais informações sobre a tarefa, confira [Tarefa de implantação de grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment) e [Tarefa de implantação de modelo do Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    O arquivo yml deve ser semelhante a:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selecione **Salvar e executar**.
1. No painel **Salvar e executar**, selecione **Salvar e executar** novamente. Uma cópia do arquivo YAML é salva no repositório conectado. É possível ver o arquivo YAML navegando até seu repositório.
1. Verifique se o pipeline foi executado com êxito.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é o que você especificou no arquivo YAML do pipeline.  Você verá uma conta de armazenamento criada.  O nome da conta de armazenamento começa com **store**.
1. Selecione o nome da conta de armazenamento para abri-la.
1. Selecione **Propriedades**. Observe que a **Replicação** é **LRS (armazenamento com redundância local)** .

## <a name="update-and-redeploy"></a>Atualizar e reimplantar

Quando você atualiza o modelo e envia as alterações por push para o repositório remoto, o pipeline atualiza os recursos automaticamente, a conta de armazenamento neste caso.

1. Abra **linkedStorageAccount.json** em seu repositório local no Visual Studio Code ou em qualquer editor de texto.
1. Atualize o **defaultValue** de **storageAccountType** para **Standard_GRS**. Veja a seguinte captura de tela:

    ![Azure Resource Manager Azure DevOps Azure Pipelines atualizar yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Salve as alterações.
1. Efetue push das alterações para o repositório remoto executando os seguintes comandos do Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    O primeiro comando (pull) sincroniza o repositório local com o repositório remoto. O arquivo YAML do pipeline foi adicionado ao repositório remoto. A execução do comando pull baixa uma cópia do arquivo YAML para o branch local.

    O quarto comando (push) carrega o arquivo linkedStorageAccount.json revisado para o repositório remoto. Com o branch mestre do repositório remoto atualizado, o pipeline é disparado novamente.

Para confirmar as alterações, verifique a propriedade Replicação da conta de armazenamento.  Confira [Verificar a implantação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

Também convém excluir o repositório GitHub e o projeto do Azure DevOps.

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu este tutorial de implantação de modelo do Resource Manager. Informe-nos se você tiver comentários e sugestões na seção de comentários. Obrigado!
Você está pronto para acessar conceitos mais avançados sobre modelos. O próximo tutorial detalha mais o uso da documentação de referência do modelo para ajudar na definição de recursos a serem implantados.

> [!div class="nextstepaction"]
> [Utilizar a referência de modelo](./template-tutorial-use-template-reference.md)
