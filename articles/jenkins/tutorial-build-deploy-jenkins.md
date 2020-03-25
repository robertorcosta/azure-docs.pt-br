---
title: Tutorial – CI/CD do Jenkins para VMs do Azure com o Azure DevOps Services
description: Neste tutorial, você aprenderá a configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo Node.js usando o Jenkins para VMs do Azure por meio do Release Management no Visual Studio Team Services ou Microsoft Team Foundation Server
keywords: jenkins, azure, devops, virtual machine, cicd
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: cae28b293a6217996b44c839dc8836ec940c3155
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78192601"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Tutorial: Implantar seu aplicativo em máquinas virtuais do Linux no Azure usando o Jenkins e o Azure DevOps Services

A CI (integração contínua) e a CD (implantação contínua) forma um pipeline por meio do qual você pode compilar, liberar e implantar seu código. O Azure DevOps Services fornece um conjunto completo de ferramentas de automação de CI/CD para implantação no Azure. O Jenkins é uma ferramenta de terceiros popular baseada em servidor de CI/CD que também fornece a automação de CI/CD. Use o Azure DevOps Services e o Jenkins juntos para personalizar a maneira como você fornece seu aplicativo ou serviço de nuvem.

Neste tutorial, você usa o Jenkins para criar um aplicativo Web Node.js. Em seguida, você usa o Azure DevOps para implantá-lo

em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) que contém máquinas virtuais (VMs) Linux. Você aprenderá como:

> [!div class="checklist"]
> * Obter o aplicativo de exemplo.
> * Configure os plug-ins do Jenkins.
> * Configure um projeto Freestyle do Jenkins para Node.js.
> * Configurar o Jenkins para integração com o Azure DevOps Services.
> * Crie um ponto de extremidade de serviço do Jenkins.
> * Crie um grupo de implantação para as máquinas virtuais do Azure.
> * Criar um pipeline de lançamento no Azure Pipelines.
> * Execute implantações manuais e disparadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Você precisa ter acesso a um servidor Jenkins. Se você ainda não tiver criado um servidor Jenkins, consulte [Criar um mestre Jenkins em uma máquina virtual do Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Entrar em sua organização do Azure DevOps Services (**https://{suaorganização}.visualstudio.com**). 
  Você precisa obter uma [organização do Azure DevOps Services gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para saber mais, confira [Conectar-se ao Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  É necessária uma máquina virtual Linux para um destino de implantação.  Para obter mais informações, consulte [Criar e gerenciar VMs Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para sua máquina virtual. Para obter mais informações, consulte [Criar grupos de segurança de rede usando o Portal do Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Obter o aplicativo de exemplo

Você precisa de um aplicativo para implantação, armazenado em um repositório Git.
Para este tutorial, recomendamos o uso [deste aplicativo de exemplo disponível no GitHub](https://github.com/azure-devops/fabrikam-node). Este tutorial contém um script de exemplo usado para instalar o Node.js e um aplicativo. Se quiser trabalhar com seu próprio repositório, você deverá configurar um exemplo semelhante.

Crie uma bifurcação deste aplicativo e anote o local (URL) para usar em etapas posteriores deste tutorial. Para obter mais informações, consulte [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Criar fork para um repositório).    

> [!NOTE]
> O aplicativo foi criado por meio do [Yeoman](https://yeoman.io/learning/index.html). Ele usa o Express, Bower e o Grunt. Além disso, ele tem alguns pacotes npm como dependências.
> O exemplo também contém um script que configura o Nginx e implanta o aplicativo. Ele é executado nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, Nginx e PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia o aplicativo Node.

## <a name="configure-jenkins-plug-ins"></a>Configurar os plug-ins do Jenkins

Primeiro, você deve configurar dois plug-ins do Jenkins: **NodeJS** e **Implantação Contínua do VS Team Services**.

1. Abra sua conta do Jenkins e selecione **Gerenciar o Jenkins**.
2. Na página **Gerenciar o Jenkins**, selecione **Gerenciar Plug-ins**.
3. Filtre a lista para localizar o plug-in **NodeJS** e marque a opção **Instalar sem reinicialização**.
    ![Como adicionar o plug-in do NodeJS ao Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtre a lista para localizar o plug-in **Implantação Contínua do VS Team Services** e marque a opção **Instalar sem reinicialização**.
5. Volte para o painel do Jenkins e selecione **Gerenciar o Jenkins**.
6. Selecione **Configuração da Ferramenta Global**. Localize **NodeJS** e selecione **Instalações do NodeJS**.
7. Marque a opção **Instalar automaticamente** e, em seguida, insira um valor de **Nome**.
8. Clique em **Salvar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto Freestyle Jenkins para Node.js

1. Selecione **Novo Item**. Insira um nome de item.
2. Selecione **Projeto Freestyle**. Selecione **OK**.
3. Na guia **Gerenciamento de Código-Fonte**, selecione **Git** e insira os detalhes do repositório e do branch que contém o código do aplicativo.    
    ![Adicionar um repositório ao seu build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na guia **Criar Gatilhos**, selecione **Sondar SCM** e insira o agendamento `H/03 * * * *` para sondar o repositório Git em busca de alterações a cada três minutos. 
5. Na guia **Ambiente de Build**, selecione **Fornecer Nó &amp; CAMINHO do compartimento/pasta de npm** e selecione o valor da **Instalação do NodeJS**. Deixe o **arquivo npmrc** definido como **usar padrão do sistema**.
6. Na guia **Build**, selecione **Executar shell** e insira o comando `npm install` para garantir que todas as dependências são atualizadas.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Configurar o Jenkins para integração com o Azure DevOps Services

> [!NOTE]
> Certifique-se de que o token de acesso pessoal (PAT) usado nas etapas a seguir contém a permissão *Versão* (ler, gravar, executar e gerenciar) no Azure DevOps Services.
 
1.  Crie um PAT em sua organização do Azure DevOps Services se você ainda não tiver um. O Jenkins exige essas informações para acessar sua conta da organização do Azure DevOps Services. Armazene as informações de token para as próximas etapas desta seção.
  
    Para saber como gerar um token, leia [Como fazer para criar um token de acesso pessoal para o Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts).
2. Na guia **Ações de Pós-build**, selecione **Adicionar ação de pós-build**. Selecione **Arquivar os artefatos**.
3. Em **Arquivos para arquivar**, insira `**/*` para incluir todos os arquivos.
4. Para criar outra ação, selecione **Adicionar ação de pós-build**.
5. Selecione **Disparar versão no TFS/Team Services**. Insira o URI para sua organização do Azure DevOps Services, como **https://{your-organization-name}.visualstudio.com**.
6. Insira o nome do **Projeto**.
7. Escolha um nome para o pipeline de lançamento. (Você cria esse pipeline de lançamento posteriormente no Azure DevOps Services.)
8. Escolha as credenciais para se conectar ao ambiente do Azure DevOps Services ou do Azure DevOps Server:
   - Deixe **Nome de usuário** em branco se estiver usando o Azure DevOps Services. 
   - Insira um nome de usuário e uma senha caso esteja usando uma versão local do Azure DevOps Server.    
   ![Configurando as ações de pós-build do Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Salve o projeto do Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto de extremidade de serviço do Jenkins

Um ponto de extremidade de serviço permite que o Azure DevOps Services se conecte ao Jenkins.

1. Abra a página **Serviços** no Azure DevOps Services, abra a lista **Novo Ponto de Extremidade de Serviço** e selecione **Jenkins**.
   ![Adicionar um ponto de extremidade do Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Insira um nome para a conexão.
3. Insira a URL de seu servidor Jenkins e marque a opção **Aceitar certificados SSL não confiáveis**. Uma URL de exemplo é **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Insira o nome de usuário e a senha de sua conta do Jenkins.
5. Selecione **Verificar conexão** para verificar se as informações estão corretas.
6. Selecione **OK** para criar o ponto de extremidade de serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implantação para máquinas virtuais do Azure

É necessário ter um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registrar o agente do Azure DevOps Services, de modo que o pipeline de lançamento possa ser implantado na máquina virtual. Os grupos de implantação facilitam a definição de grupos lógicos de computadores de destino para implantação e a instalação do agente necessário em cada computador.

   > [!NOTE]
   > No procedimento a seguir, instale os pré-requisitos e *não execute o script com privilégios sudo.*

1. Abra a guia **Versões** do hub **Build &amp; Versão**, abra **Grupos de implantação** e selecione **+ Novo**.
2. Insira um nome para o grupo de implantação e uma descrição opcional. Em seguida, selecione **Criar**.
3. Escolha o sistema operacional para sua máquina virtual de destino de implantação. Por exemplo, selecione **Ubuntu 16.04+** .
4. Selecione **Usar um token de acesso pessoal no script para autenticação**.
5. Selecione o link **Pré-requisitos do sistema**. Instale os pré-requisitos para seu sistema operacional.
6. Selecione **Copiar script para área de transferência** para copiar o script.
7. Faça logon na máquina virtual de destino da implantação e execute o script. Não execute o script com privilégios sudo.
8. Após a instalação, serão solicitadas marcações de grupo de implantação. Aceite os padrões.
9. No Azure DevOps Services, verifique sua máquina virtual recém-registrada em **Destinos** em **Grupos de implantação**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Criar um pipeline de lançamento no Azure Pipelines

Um pipeline de lançamento especifica o processo que o Azure Pipelines usa para implantar o aplicativo. Neste exemplo, você deve executar um script de shell.

Para criar um pipeline de lançamento no Azure Pipelines:

1. Abra a guia **Versões** do hub **Build &amp; Versão** e selecione **Criar pipeline de lançamento**. 
2. Selecione o modelo **Vazio** escolhendo começar com um **Processo vazio**.
3. Na seção **Artefatos**, clique em **+ Adicionar Artefato** e escolha **Jenkins** em **Tipo de fonte**. Selecione sua conexão de ponto de extremidade de serviço do Jenkins. Depois, selecione o trabalho de origem do Jenkins e selecione **Adicionar**.
4. Selecione as reticências ao lado de **Ambiente 1**. Selecione **Adicionar fase do grupo de implantação**.
5. Escolha seu grupo de implantação.
5. Selecione **+** para adicionar uma tarefa à **Fase do grupo de implantação**.
6. Selecione a tarefa **Script do Shell** e **Adicionar**. A tarefa **Script do Shell** fornece a configuração para execução de um script em cada servidor, a fim de instalar o Node.js e iniciar o aplicativo.
8. Para **Caminho do Script**, insira **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selecione **Avançado** e, em seguida, habilite a opção **Especificar Diretório de Trabalho**.
10. Para **Diretório de Trabalho**, insira **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Edite o nome do pipeline de lançamento com base no nome especificado na guia **Ações de Pós-build** do build no Jenkins. O Jenkins exige esse nome para poder disparar uma nova versão quando os artefatos de origem forem atualizados.
12. Selecione **Salvar** e **OK** para salvar o pipeline de lançamento.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar implantações manuais e disparadas por CI

1. Selecione **+ Versão** e **Criar Versão**.
2. Selecione o build concluído na lista suspensa realçada e selecione **Fila**.
3. Escolha o link de versão na mensagem pop-up. Por exemplo: "A versão **Release-1** foi criada".
4. Abra a guia **Logs** para observar a saída do console de versão.
5. No navegador, abra a URL de um dos servidores adicionados ao grupo de implantação. Por exemplo, insira **http://{your-server-ip-address}** .
6. Acesse o repositório do Git de origem e modifique o conteúdo do título **h1** no arquivo app/views/index.jade com um texto alterado.
7. Confirme a alteração.
8. Depois de alguns minutos, você verá uma nova versão criada na página **Versões** do Azure DevOps. Abra a versão para ver a implantação acontecendo. Parabéns!

## <a name="troubleshooting-the-jenkins-plugin"></a>O plug-in do Jenkins de solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a implantação de um aplicativo no Azure usando o Jenkins para o build e o Azure DevOps Services para a versão. Você aprendeu a:

> [!div class="checklist"]
> * Crie seu aplicativo no Jenkins.
> * Configurar o Jenkins para integração com o Azure DevOps Services.
> * Crie um grupo de implantação para as máquinas virtuais do Azure.
> * Criar um pipeline do Azure que configura as VMs e implanta o aplicativo.

Para saber mais sobre como usar o Azure Pipelines para as etapas de build e versão, veja [isto](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-linuxvm-deploygroups).

Para saber mais sobre como criar um pipeline da CI/CD baseado em YAML para implantação em VMs, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/Jenkins/)
