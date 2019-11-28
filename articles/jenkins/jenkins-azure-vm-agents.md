---
title: Dimensione implantações do Jenkins com agentes de VM do Azure.
description: Adicione capacidade adicional aos pipelines do Jenkins usando máquinas virtuais do Azure com o plug-in de Agente de VM do Azure do Jenkins.
keywords: jenkins, azure, devops, máquina virtual, agentes
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 2e811d628c017316a5bc50a8ddc22ee24d6f744e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158537"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Dimensionar implantações do Jenkins para atender à demanda com agentes de VM do Azure

Este tutorial mostra como usar o [plug-in de Agentes de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) do Jenkins para adicionar capacidade sob demanda com máquinas virtuais Linux em execução no Azure.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Instalar o plug-in de Agentes de VM do Azure
> * Configurar o plug-in para criar recursos em sua assinatura do Azure
> * Definir os recursos de computação disponíveis para cada agente
> * Definir o sistema operacional e as ferramentas instaladas em cada agente
> * Criar um novo trabalho freestyle do Jenkins
> * Executar o trabalho em um agente de VM do Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure
* Um servidor mestre do Jenkins. Se você não tiver um, exiba o [guia de início rápido](install-jenkins-solution-template.md) para configurar um no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instale o plug-in de agentes da VM do Azure

> [!TIP]
> Se você implantou o Jenkins no Azure usando o [modelo de solução](install-jenkins-solution-template.md), o plug-in de Agente de VM do Azure já está instalado.

1. No painel do Jenkins, selecione **Gerenciar o Jenkins** e, em seguida, selecione **Gerenciar Plug-ins**.
1. Selecione a guia **Disponível** e, em seguida, pesquise **Agentes de VM do Azure**. Selecione a caixa de seleção ao lado da entrada do plug-in e selecione **Instalar sem reinicialização** na parte inferior do painel.

## <a name="configure-the-azure-vm-agents-plugin"></a>Configurar o plug-in de Agentes de VM do Azure

1. No painel do Jenkins, selecione **Gerenciar o Jenkins** e, em seguida, **Configurar Sistema**.
1. Role até a parte inferior da página e encontre a seção **Nuvem** com a lista suspensa **Adicionar nova nuvem** e escolha **Agentes de VM do Microsoft Azure**.
1. Selecione uma entidade de serviço existente na lista suspensa **Adicionar**, na seção **Credenciais do Azure**. Se nenhuma estiver listada, execute as seguintes etapas para [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) para sua conta do Azure e adicioná-la à configuração do Jenkins:   

    a. Selecione **Adicionar** ao lado de **Credenciais do Azure** e escolha **Jenkins**.   
    b. Na caixa de diálogo **Adicionar Credenciais**, selecione **Entidade de Serviço do Microsoft Azure** na lista suspensa **Tipo**.   
    c. Crie uma Entidade de serviço do Active Directory por meio da CLI do Azure ou do [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Insira as credenciais da entidade de serviço na caixa de diálogo **Adicionar credenciais**. Se você não souber sua ID de assinatura do Azure, consulte-a na CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    A entidade de serviço concluída deve usar o campo `id` para **ID da Assinatura** , o `appId` valor para **ID do Cliente**, `password` para **Segredo do Cliente** e `tenant` para **ID do Locatário**. Selecione **Adicionar** para adicionar a entidade de serviço e, em seguida, configure o plug-in para usar a credencial recém-criada.

    ![Configurar uma entidade de serviço do Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. Na seção **Nome do Grupo de Recursos**, deixe a opção **Criar novo** marcada e insira `myJenkinsAgentGroup`.
1. Selecione **Verificar configuração** para se conectar ao Azure para testar as configurações de perfil.
1. Selecione **Aplicar** para atualizar a configuração de plug-in.

## <a name="configure-agent-resources"></a>Configurar recursos do agente

Configure um modelo para usar na definição de um agente de VM do Azure. Esse modelo define os recursos de computação que cada agente tem quando é criado.

1. Selecione **Adicionar** ao lado de **Adicionar Modelo de Máquina Virtual do Azure**.
1. Insira `defaulttemplate` no **Nome**
1. Insira `ubuntu` no **Rótulo**
1. Selecione a [região do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) desejada na caixa de combinação.
1. Selecione um [tamanho de VM](/azure/virtual-machines/linux/sizes) na lista suspensa em **Tamanho da Máquina Virtual**. Um tamanho `Standard_DS1_v2` de uso geral é suficiente para este tutorial.   
1. Deixe a opção **Tempo de retenção** como `60`. Essa configuração define o número de minutos que o Jenkins pode aguardar antes de desalocar agentes ociosos. Especifique 0 se não desejar que os agentes ociosos sejam removidos automaticamente.

   ![Configuração geral da VM](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Configurar o sistema operacional e as ferramentas do agente

Na seção **Configuração de Imagem** da configuração de plug-in, selecione **Ubuntu 16.04 LTS**. Marque as caixas de seleção ao lado de **Instalar o Git (Último)** , **Instalar o Maven (V3.5.0)** e **Instalar o Docker** para instalar essas ferramentas nos agentes recém-criados.

![Configurar o sistema operacional e as ferramentas da VM](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Selecione **Adicionar** ao lado de **Credenciais de Administrador** e, em seguida, selecione **Jenkins**. Insira um nome de usuário e senha usados para entrar nos agentes, verificando se eles atendem à [política de nome de usuário e senha](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) para as contas administrativas nas VMs do Azure.

Selecione **Confirmar Modelo** para confirmar a configuração e, em seguida, selecione **Salvar** para salvar as alterações e retornar ao painel do Jenkins.

## <a name="create-a-job-in-jenkins"></a>Crie um trabalho no Jenkins

1. No painel do Jenkins, clique em **Novo Item**. 
1. Insira `demoproject1` no nome e selecione **Projeto Freestyle** e, em seguida, selecione **OK**.
1. Na guia **Geral**, escolha **Restringir o local em que o projeto pode ser executado** e digite `ubuntu` na **Expressão de Rótulo**. Você verá uma mensagem confirmando que o rótulo é atendido pela configuração de nuvem criada na etapa anterior. 
   ![Configurar o trabalho](./media/jenkins-azure-vm-agents/job-config.png)
1. Na guia **Gerenciamento de Código-Fonte**, selecione **Git** e adicione a seguinte URL ao campo **URL do Repositório**: `https://github.com/spring-projects/spring-petclinic.git`
1. Na guia **Compilar**, selecione **Adicionar etapa de build** e, em seguida, **Invocar destinos de nível superior do Maven**. Insira `package` no campo **Metas**.
1. Selecione **Salvar** para salvar a definição de trabalho.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Compilar o novo trabalho em um agente de VM do Azure

1. Volte para o painel do Jenkins.
1. Selecione o trabalho criado na etapa anterior e, em seguida, clique em **Compilar agora**. Um novo build será colocado na fila, mas não será iniciado até que uma VM do agente seja criada em sua assinatura do Azure.
1. Quando a compilação estiver concluída, vá para **Saída do Console**. Você verá que o build foi executado remotamente em um agente do Azure.

![Saída do console](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>O plug-in do Jenkins de solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [CI/CD no Serviço de Aplicativo do Azure](java-deploy-webapp-tutorial.md)