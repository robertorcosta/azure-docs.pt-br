---
title: Tutorial – Configurar a extensão Visual Studio Code do Azure Terraform
description: Saiba como instalar e usar a extensão do Terraform do Azure no Visual Studio Code.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: fcb47024fd26f061ca4475b01d00f1ae13303b61
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472274"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Tutorial: Configurar a extensão do Visual Studio Code do Azure Terraform

A extensão Visual Studio Code do Azure Terraform permite que você trabalhe com o Terraform no editor. Com essa extensão, você pode criar, testar e executar configurações do Terraform. A extensão também dá suporte à visualização de grafo de recursos.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Automatizar o provisionamento de serviços do Azure usando o Terraform
> * Instalar e usar a extensão Visual Studio Code do Terraform para serviços do Azure.
> * Usar o Visual Studio Code para gravar, planejar e executar planos do Terraform.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Terraform**: [instale e configure o Terraform](terraform-install-configure.md).

- **Visual Studio Code**: instale a versão do [Visual Studio Code](https://code.visualstudio.com/download) apropriado para seu ambiente.

## <a name="prepare-your-dev-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="install-git"></a>Instalar o Git

Para concluir os exercícios neste artigo, você precisará [instalar o Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Instalar o HashiCorp Terraform

Siga as instruções na página da Web [Instalar o Terraform](https://www.terraform.io/intro/getting-started/install.html) da HashiCorp, que abrange:

- Baixando o Terraform
- Instalando o Terraform
- Verificar se o Terraform é instalado corretamente

>[!Tip]
>Certifique-se de seguir as instruções sobre como configurar sua variável do sistema PATH.

### <a name="install-nodejs"></a>Instalar o Node. js

Para usar o Terraform no Cloud Shell, você precisará [instalar o Node.js](https://nodejs.org/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e insira `node -v`.

### <a name="install-graphviz"></a>Instalar GraphViz

Para usar a função visualizar do Terraform, será necessário [instalar o GraphViz](https://graphviz.org/).

>[!NOTE]
>Para verificar se o GraphViz está instalado, abra uma janela do terminal e insira `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Instalar a extensão do Visual Studio Code do Azure Terraform

1. Inicie o Visual Studio Code.

1. Selecione **Extensões**.

    ![Botão Extensões](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Use a caixa de texto **Pesquisar extensões no Marketplace** para procurar a extensão do Terraform do Azure:

    ![Pesquisar extensões do Visual Studio Code no Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Selecione **Instalar**.

    >[!NOTE]
    >Quando você seleciona **Instalar** para a extensão do Terraform do Azure, o Visual Studio Code automaticamente instala a extensão de Conta do Azure. Conta do Azure é um arquivo de dependência para a extensão Terraform do Azure, usado para executar autenticações de assinatura do Azure e extensões de código relacionadas ao Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Verificar se que a extensão do Terraform está instalada no Visual Studio Code

1. Selecione **Extensões**.

1. Insira `@installed` na caixa de texto de pesquisa.

    ![Extensões instaladas](media/terraform-vscode-extension/tf-installed-extensions.png)

A extensão do Terraform do Azure será exibida na lista de extensões instaladas.

![Extensões do Terraform instaladas](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Agora é possível executar todos os comandos com suporte do Terraform em seu ambiente do Cloud Shell de dentro do Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Exercício 1: Passo a passo dos comandos básicos do Terraform

Neste exercício, você cria e executa um arquivo de configuração básico do Terraform que provisiona um novo grupo de recursos do Azure.

### <a name="prepare-a-test-plan-file"></a>Preparar um arquivo de plano de teste

1. No Visual Studio Code, selecione **Arquivo > Novo Arquivo** na barra de menus.

1. No seu navegador, navegue até a [página Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) e copie o código no bloco de código **Exemplo de uso**:

    ![Exemplo de uso](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Cole o código copiado no novo arquivo criado no Visual Studio Code.

    ![Colar o código Exemplo de Uso](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Você pode alterar o valor **nome** do grupo de recursos, mas ele deve ser exclusivo para sua assinatura do Azure.

1. Na barra de menus, selecione **Arquivo > Salvar Como**.

1. Na caixa de diálogo **Salvar como**, navegue até um local de sua escolha e, em seguida, selecione **Nova pasta**. (Altere o nome da nova pasta para algo mais descritivo do que *Nova pasta*.)

    >[!NOTE]
    >Neste exemplo, a pasta é nomeada TERRAFORM-TEST-PLAN.

1. Verifique se a nova pasta está realçada (selecionada) e, em seguida, selecione **Abrir**.

1. Na caixa de diálogo **Salvar Como**, altere o nome padrão do arquivo para *main.tf*.

    ![Salvar como main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Clique em **Salvar**.
1. Na barra de menus, selecione **Arquivo > Abrir Pasta**. Navegue e selecione a nova pasta que você criou.

### <a name="run-terraform-init-command"></a>Executar o comando *init* do Terraform

1. Inicie o Visual Studio Code.

1. Na barra de menus do Visual Studio Code, selecione **Arquivo > Abrir Pasta…** e localize e selecione o arquivo *main.tf*.

    ![arquivo main.TF](media/terraform-vscode-extension/tf-main-tf.png)

1. Na barra de menus, selecione **Exibir > Paleta de Comandos... > Azure Terraform: iniciar**.

1. Quando a confirmação for exibida, selecione **OK**.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Na primeira vez que iniciar o Cloud Shell em uma nova pasta, você deverá criar o aplicativo Web. Selecione **Abrir**.

    ![Primeira inicialização do Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. A página Bem-vindo ao Azure Cloud Shell será aberta. Selecione Bash ou PowerShell.

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Neste exemplo, Bash (Linux) foi selecionado.

1. Se você ainda não configurou uma conta de armazenamento do Azure, a tela a seguir será exibida. Selecione **Criar armazenamento**.

    ![Você não tem nenhum armazenamento montado](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que você selecionou anteriormente e exibe informações para a unidade de nuvem que acabou de ser criada.

    ![Sua unidade de nuvem foi criada](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Agora você pode sair do Cloud Shell

1. Na barra de menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: init**.

    ![O Terraform foi inicializado com êxito](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualizar o plano

Anteriormente neste tutorial, você instalou o GraphViz. O Terraform pode usar o GraphViz para gerar uma representação visual de uma configuração ou plano de execução. A extensão do Terraform do Visual Studio Code do Azure implementa esse recurso por meio do comando *visualize*.

- Na barra de menus, selecione **Exibir > Paleta de Comandos... > Azure Terraform: visualizar**.

    ![Visualizar o plano](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Executar o comando *plan* do Terraform

O comando *plan* do Terraform é usado para verificar se o plano de execução para um conjunto de alterações fará o que você pretendia.

>[!NOTE]
>O *plano* do Terraform não faz quaisquer alterações nos recursos reais do Azure. Para efetivamente fazer as alterações contidas em seu plano, nós usamos o comando *apply* do Terraform.

- Na barra de menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: plan**.

    ![Plano do Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Executar o comando *apply* do Terraform

Depois de estar satisfeito com os resultados do *plano* do Terraform, você pode executar o comando *apply*.

1. Na barra de menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: apply**.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Digite `yes`.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Verifique se o plano do Terraform foi executado

Para ver se o novo grupo de recursos do Azure foi criado com êxito:

1. Abra o portal do Azure.

1. Selecione **Grupos de recursos** no painel de navegação esquerdo.

    ![Verificar o novo recurso](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Seu novo grupo de recursos deve estar listado na coluna **NAME**.

>[!NOTE]
>Você pode deixar a janela do portal do Azure aberta por enquanto; nós vamos usá-lo na próxima etapa.

### <a name="run-terraform-destroy-command"></a>Executar o comando *destroy* do Terraform

1. Na barra de menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: destroy**.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Insira *sim*.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Verifique se o grupo de recursos foi destruído

Para confirmar se o Terraform destruiu com êxito o novo grupo de recursos:

1. Clique em **Atualizar** na página **Grupos de recursos** do portal do Azure.

1. O grupo de recursos não será mais listado.

    ![Verificar se o grupo de recursos foi destruído](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Exercício 2: módulo *compute* do Terraform

Neste exercício, você aprende a carregar o módulo *compute* do Terraform no ambiente do Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Clonar o módulo terraform-azurerm-compute

1. Use [este link](https://github.com/Azure/terraform-azurerm-compute) para acessar o módulo de Computação do Rm do Azure do Terraform no GitHub.

1. Selecione **Clonar ou baixar**.

    ![Clonar ou baixar](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Neste exemplo, nossa pasta foi nomeada *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Abra a pasta no Visual Studio Code

1. Inicie o Visual Studio Code.

1. Na barra de menus, selecione **Arquivo > Abrir Pasta**, navegue e selecione a pasta criada na etapa anterior.

    ![pasta terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicializar Terraform

Antes de começar a usar os comandos do Terraform de dentro do Visual Studio Code, baixe os plug-ins para dois provedores do Azure: random e azurerm.

1. No painel Terminal do IDE do Visual Studio Code, insira `terraform init`.

    ![comando terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Insira `az login`, pressione **<Enter** e siga as instruções na tela.

### <a name="module-test-lint"></a>Teste de módulo: *lint*

1. Na barra de menus, selecione **Exibir > Paleta de Comandos... > Azure Terraform: executar teste**.

1. Na lista de opções de tipo de teste, selecione **lint**.

    ![Selecionar o tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Quando a confirmação for exibida, selecione **OK**e siga as instruções na tela.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando você executa qualquer o teste **lint** ou **ponta a ponta**, o Azure usa um serviço de contêiner para provisionar uma máquina de teste para executar o teste real. Por esse motivo, os resultados do teste normalmente podem levar vários minutos para serem retornados.

Após alguns instantes, você deve ver uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados do teste Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>Testar o módulo

1. Na barra de menus, selecione **Exibir > Paleta de Comandos... > Azure Terraform: executar teste**.

1. Na lista de opções de tipo de teste, selecione **ponta a ponta**.

    ![Selecionar o tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Quando a confirmação for exibida, selecione **OK**e siga as instruções na tela.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando você executa qualquer o teste **lint** ou **ponta a ponta**, o Azure usa um serviço de contêiner para provisionar uma máquina de teste para executar o teste real. Por esse motivo, os resultados do teste normalmente podem levar vários minutos para serem retornados.

Após alguns instantes, você deve ver uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados do teste](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Lista os módulos do Terraform disponíveis para o Azure (e outros provedores com suporte)](https://registry.terraform.io/)