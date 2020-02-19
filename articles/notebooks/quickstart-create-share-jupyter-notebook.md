---
title: Criar e compartilhar um Jupyter notebook na versão prévia do Azure Notebooks
description: Crie e execute rapidamente um Jupyter notebook na versão prévia do Azure Notebooks e, em seguida, compartilhe esse notebook com outras pessoas.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064437"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Início Rápido: Criar e compartilhar um notebook na versão prévia do Azure Notebooks

Neste início rápido, você criará e executará um Jupyter notebook no Azure Notebooks e, em seguida, compartilhará esse notebook com outras pessoas. O Jupyter permite combinar com facilidade o texto Markdown, o código executável, os dados persistentes, os gráficos e as visualizações em uma só tela compartilhável, o notebook. O Azure Notebooks é um serviço hospedado gratuito para desenvolver e executar os notebooks do Jupyter na nuvem sem instalação.

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="create-a-new-project-and-notebook"></a>Criar um projeto e um notebook

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Acesse o site do [Azure Notebooks (https://notebooks.azure.com)](https://notebooks.azure.com) e conecte-se. Para obter detalhes, confira [Início Rápido: entrar no Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Em sua página de perfil público, selecione **Meus Projetos** na parte superior da página:

    ![Link Meus Projetos na parte superior da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **Meus Projetos**, selecione **+ Novo Projeto** (atalho de teclado: n). O botão poderá ser exibido somente como **+** se a janela do navegador for estreita:

    ![Comando Novo Projeto na página Meus Projetos](media/quickstarts/new-project-command.png)

1. No pop-up **Criar Novo Projeto** exibido, insira ou defina os seguintes detalhes e selecione **Criar**:

   - **Nome do projeto:** : Olá, Mundo em Python
   - **ID do Projeto**: hello-world-python
   - **Projeto público**: (desmarcado)
   - **Criar um README.md**: (desmarcado)

     ![Pop-up Novo Projeto com detalhes preenchidos](media/quickstarts/new-project-popup.png)

1. Após alguns segundos, o Azure Notebooks o direcionará para o novo projeto. Adicione um notebook ao projeto, selecionando o menu suspenso **+ Novo** (que pode aparecer somente como **+** ) e, em seguida, selecionando **Notebook**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. No pop-up **Criar Novo Notebook** que aparece, insira um nome de arquivo para o notebook, como *HelloWorldInPython.ipynb* ( *.ipynb* que significa IronPython (Jupyter) Notebook), e selecione **Python 3.6** para a linguagem (também conhecido como *kernel*):

    ![Pop-up Criar Novo Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **Novo** para concluir a criação do notebook que aparece na lista de arquivos do projeto:

    ![Novo notebook aparecendo na lista de arquivos do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o notebook

1. Selecione o novo notebook para executá-lo no editor, o kernel que você selecionou (Python 3.6 neste exemplo) é ativado automaticamente para esse notebook:

    ![Exibição de um novo notebook no Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Por padrão, o notebook tem uma célula de código vazia. Para alterar o tipo de célula para **Markdown**, use o menu suspenso do tipo de célula para selecionar **Markdown**:

    ![Alterar o tipo de célula em um novo notebook](media/quickstarts/create-notebook-cell-type.png)

1. Insira ou cole o seguinte texto do cabeçalho na célula:

    ```markdown
    # Hello World in Python
    ```

1. Como você está editando o Markdown, o texto aparecerá como um cabeçalho com o "#". Para renderizar o Markdown em HTML, selecione o botão **Executar**. Em seguida, o Azure Notebooks criará automaticamente uma nova célula de código:

    ![O botão de execução de uma célula e o Markdown renderizado](media/quickstarts/run-cell-markdown-render.png)

1. Na célula de código, insira o seguinte código Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecione **Executar** (atalho de teclado: Shift+Enter) para executar o código. Abaixo da célula, você verá uma saída com êxito semelhante ao texto a seguir:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecione o ícone Salvar para salvar o trabalho:

    ![Ícone Salvar na barra de ferramentas do Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Selecione o comando de menu **Arquivo** > **Fechar e Parar** para interromper o servidor e fechar a janela do navegador.

## <a name="share-the-notebook"></a>Compartilhar o notebook

Para compartilhar o notebook, retorne à página do projeto, se necessário, clique com o botão direito do mouse no arquivo do notebook, selecione **Copiar Link** (atalho de teclado: y) e cole esse link em uma mensagem apropriada (email, mensagem instantânea, etc.).

Na página do projeto, também é possível usar o menu **Compartilhar** para obter um link, criar uma mensagem de email com o link ou obter o código de inserção HTML e Markdown:

![Comando de compartilhamento de projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um Jupyter notebook para fazer uma regressão linear](tutorial-create-run-jupyter-notebook.md)
