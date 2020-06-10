---
title: Criar um projeto da versão prévia do Azure Notebooks com um ambiente personalizado
description: Crie um projeto na versão prévia do Azure Notebooks configurado com um conjunto específico de pacotes instalados e scripts de inicialização.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: tracking-python
ms.openlocfilehash: 03935b9672c25db11b91733fac1c63f1f5978b87
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559743"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Início Rápido: Criar um projeto com um ambiente personalizado na versão prévia do Azure Notebooks

Um projeto no Azure Notebooks é uma coleção de arquivos, como notebooks, arquivos de dados, documentação, imagens, e assim por diante, em conjunto com um ambiente que pode ser configurado com comandos de configuração específicos. Ao definir o ambiente com o projeto, qualquer pessoa que clonar o projeto em sua própria conta do Azure Notebooks terá todas as informações necessárias para recriar o ambiente necessário.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Criar um projeto

1. Vá até [Azure Notebooks](https://notebooks.azure.com) e entre. (Para obter mais detalhes, consulte [Início Rápido – Entrar no Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Em sua página de perfil público, selecione **Meus Projetos** na parte superior da página:

    ![Link Meus Projetos na parte superior da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **Meus Projetos**, selecione **+ Novo Projeto**(atalho de teclado: n); o botão poderá aparecer apenas como **+** , se a janela do navegador for estreita:

    ![Comando Novo Projeto na página Meus Projetos](media/quickstarts/new-project-command.png)

1. No pop-up **Criar Novo Projeto** exibido, insira ou defina os seguintes detalhes e selecione **Criar**:

    - **Nome do projeto:** : Projeto com um ambiente personalizado
    - **ID do projeto**: ambiente de projeto personalizado
    - **Projeto público**: (desmarcado)
    - **Criar um README.md**: (desmarcado)

1. Após alguns segundos, o Azure Notebooks o direcionará para o novo projeto. Adicione um notebook ao projeto, selecionando o menu suspenso **+ Novo** (que pode aparecer somente como **+** ) e, em seguida, selecionando **Notebook**.

1. Forneça um nome ao notebook como *environment.ipynb personalizado*, selecione **Python 3.6** para a linguagem e selecione **Novo**.

## <a name="add-a-custom-setup-step"></a>Adicionar uma etapa de instalação personalizada

1. Na página do projeto, selecione **Configurações do Projeto**.

    ![Comando de configurações do projeto](media/quickstarts/project-settings-command.png)

1. No pop-up **Configurações do Projeto**, selecione a guia **Ambiente** e, em seguida, em **Etapas de Configuração de Ambiente**, selecione **+ Adicionar**:

    ![Comando para adicionar uma nova etapa de configuração de ambiente](media/quickstarts/environment-add-command.png)

1. O comando **+ Adicionar** cria uma etapa definida por uma operação e um arquivo de destino selecionado dos arquivos no projeto. Há suporte para as operações a seguir:

   | Operação | Descrição |
   | --- | --- |
   | Requirements.txt | Os projetos do Python definem as dependências em um arquivo requirements.txt. Com essa opção, selecione o arquivo apropriado na lista de arquivos do projeto e também selecione a versão do Python na lista suspensa adicional que é exibida. Se necessário, selecione **Cancelar** para retornar ao projeto, carregar ou criar o arquivo e, em seguida, retorne à guia **Configurações do Projeto** > **Ambiente** e crie uma nova etapa. Com essa etapa, a execução de um notebook no projeto é executada automaticamente `pip install -r <file>` |
   | Script de Shell | Use para indicar um script de shell de bash (normalmente um arquivo com a extensão *.sh*) contendo todos os comandos que você quer executar para inicializar o ambiente. |
   | Environment.yml | Um projeto do Python que usa conda para gerenciar um ambiente usa um arquivo *environments.yml* para descrever dependências. Com essa opção, selecione o arquivo apropriado na lista de arquivos do projeto. |

   > [!WARNING]
   > Como este é um serviço em versão prévia que está em desenvolvimento, há um problema conhecido de que a configuração `Environment.yml` não é aplicada ao projeto conforme o esperado. O projeto e os notebooks do Jupyter internos não carregam o arquivo de ambiente especificado no momento.

1. Para remover qualquer etapa de configuração, selecione **X** à direita da etapa.

1. Quando todas as etapas de configuração estiverem em vigor, selecione **Salvar**. (Selecione **Cancelar** para descartar as alterações).

1. Para testar o ambiente, crie e execute um novo notebook e, em seguida, crie uma célula de código com instruções que dependam de um pacote no ambiente como o uso de uma instrução Python`import`. Se a instrução tiver êxito, significa que o pacote necessário foi instalado com êxito no ambiente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar e configurar projetos no Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um Jupyter Notebook para fazer uma regressão linear](tutorial-create-run-jupyter-notebook.md)
