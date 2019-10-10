---
title: Migrar um notebook local do Jupyter para o Azure Notebooks
description: Saiba como transferir rapidamente um notebook do Jupyter para o Azure Notebooks do seu computador local ou de uma URL da web e, em seguida, compartilhá-lo para colaboração.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2151d5b04b0fe5d47e8e2ddfb750ac279b6f74fa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970195"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Início Rápido: Migrar um notebook local do Jupyter

Os notebooks Jupyter que você criou localmente no seu próprio computador são acessíveis apenas para você. Você pode compartilhar seus arquivos por meio de uma variedade de meios, mas, em seguida, os destinatários têm sua própria cópia local do bloco de anotações e é difícil incorporar as alterações que podem fazer. Você também pode armazenar os blocos de anotações em um repositório compartilhado online, como o GitHub, mas isso ainda exige que cada colaborador tenha sua própria instalação do Jupyter local com a mesma configuração que o seu.

Ao migrar seus blocos de anotações locais ou baseada em repositório para o Azure Notebooks, armazena-as na nuvem da qual você pode instantaneamente compartilhar com seus colaboradores. Esses colaboradores precisam apenas de um navegador para exibir e executar o bloco de anotações e se eles [entrarem](quickstart-sign-in-azure-notebooks.md) para o Azure Notebooks eles também podem fazer alterações.

Este Início Rápido demonstra o processo de migração de um bloco de anotações do seu computador local ou outra URL de arquivo acessível. Para migrar os notebooks de um repositório GitHub, consulte o [Guia de Início Rápido: Clonar um notebook](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto no Azure Notebooks

1. Vá até [Azure Notebooks](https://notebooks.azure.com) e entre. (Para obter mais detalhes, consulte [Início Rápido – Entrar no Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Em sua página de perfil público, selecione **Meus Projetos** na parte superior da página:

    ![Link Meus Projetos na parte superior da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **Meus Projetos**, selecione **+ Novo Projeto**(atalho de teclado: n); o botão poderá aparecer apenas como **+** , se a janela do navegador for estreita:

    ![Comando Novo Projeto na página Meus Projetos](media/quickstarts/new-project-command.png)

1. Na janela pop-up **Criar novo projeto** que aparece, insira os valores apropriados para o bloco de anotações que você está migrando nos campos **Nome do projeto** e **ID do projeto**, desmarque as opções para **Projeto público** e **Criar um README.md**, em seguida, selecione **Criar**.

## <a name="upload-the-local-notebook"></a>Carregar o notebook local

1. Na página do projeto, selecione **carregar** (que pode ser exibido com uma seta para cima apenas se a janela do navegador for pequena), em seguida, selecione 1. No popup que aparece, selecione **Do computador** se o seu notebook estiver localizado no sistema de arquivos local, ou **Da URL** se o bloco de anotações estiver localizado online:

    ![Comando para carregar um bloco de anotações de uma URL ou o computador local](media/quickstarts/upload-from-computer-url-command.png)

   (novamente, se o bloco de anotações estiver em um repositório do GitHub, siga as etapas no [Guia de Início Rápido: Clonar um notebook](quickstart-clone-jupyter-notebook.md) em vez disso).

   - Se estiver usando **Do computador**, arraste e solte seus arquivos *ipynb* para o pop-up ou selecione **Escolher arquivos**, em seguida, navegue e selecione os arquivos que você deseja importar. Depois selecionar **Carregar**. Os arquivos carregados recebem o mesmo nome que os arquivos locais. (você não precisa carregar o conteúdo de quaisquer pastas *.ipynb_checkpoints*.)

     ![Carregar do popup do computador](media/quickstarts/upload-from-computer-popup.png)

   - Se usar **Da URL**, insira o endereço de origem no campo **URL do Arquivo** e o nome do arquivo para atribuir ao bloco de anotações do projeto no campo **Nome do Arquivo**. Depois selecionar **Carregar**. Se você tiver vários arquivos com URLs separadas, use o comando **+ Adicionar arquivo** para verificar a primeira URL que você inseriu, após o qual o pop-up fornece novos campos para outro arquivo.

     ![Carregar popup da URL](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute o notebook recentemente carregado para verificar seu conteúdo e a operação. Quando terminar, selecione **Arquivo** > **Halt e fechar** para fechar o notebook.

1. Para compartilhar um link para o notebook, clique no arquivo no projeto e selecione **Copiar Link** (atalho de teclado: y), em seguida, cole o link na mensagem apropriada. Como alternativa, você pode compartilhar o projeto como um todo usando o controle **Compartilhar** na página do projeto.

1. Para editar arquivos que não sejam notebooks, clique com o botão direito do mouse no projeto e selecione **Editar arquivo** (atalho de teclado: i). A ação padrão, **Executar** (atalho de teclado: r), apenas mostra o conteúdo do arquivo e não permite a edição.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um Jupyter Notebook para fazer uma regressão linear](tutorial-create-run-jupyter-notebook.md)
