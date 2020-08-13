---
title: Gerenciar grupos de aplicativos para o portal da Área de Trabalho Virtual do Windows – Azure
description: Como gerenciar grupos de aplicativos da Área de Trabalho Virtual do Windows com o portal do Azure.
author: Heidilohr
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6baada4c3b376b936f693d45e39e5887e77ef54b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010082"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Tutorial: Gerenciar grupos de aplicativos com o portal do Azure

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

O grupo de aplicativos padrão criado para um novo pool de hosts da Área de Trabalho Virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos individuais do menu Iniciar.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo do RemoteApp.
> * Permitir acesso a programas do RemoteApps.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

Se você já tiver criado um pool de host e VMs de host de sessão usando o portal do Azure ou o PowerShell, poderá adicionar grupos de aplicativos do portal do Azure com o seguinte processo:

1.  Entre no [portal do Azure](https://portal.azure.com/).

2.  Pesquise e selecione **Área de Trabalho Virtual do Windows**.

3.  Selecione **Grupos de aplicativos** no menu no lado esquerdo da página e, em seguida, selecione **+ Adicionar**.

4. Na guia **Noções básicas**, selecione o grupo de assinaturas e o grupo de recursos para o qual você deseja criar o grupo de aplicativos. Você também pode optar por criar um grupo de recursos em vez de selecionar um existente.

5. Selecione o pool de host que será associado ao grupo de aplicativos no menu suspenso ao lado de **Pool de host**.

    >[!NOTE]
    >Você deve selecionar o pool de host associado ao grupo de aplicativos. Os grupos de aplicativos têm aplicativos ou áreas de trabalho que são atendidos de um host de sessão e os hosts de sessão fazem parte dos pools de hosts. O grupo de aplicativos precisa ser associado a um pool de host durante a criação.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da guia Noções Básicas no portal do Azure.](media/basics-tab.png)

6. Se você quiser adicionar grupos de aplicativos ao seu pool de host, selecione **Pools de host** no menu no lado esquerdo da tela.

    Em seguida, selecione o nome do pool de host ao qual você deseja adicionar grupos de aplicativos.

    Depois disso, selecione **Grupos de aplicativos** no menu no lado esquerdo da tela e, em seguida, selecione **+ Adicionar**.

    Por fim, selecione o grupo de assinaturas e o grupo de recursos para o qual você deseja criar o grupo de aplicativos. Você pode selecionar o nome de um grupo de recursos existente no menu suspenso ou selecionar **Criar** para criar um.

      >[!NOTE]
      >Quando você adiciona grupos de aplicativos ao seu pool de host, o pool de host que está relacionado ao grupo de aplicativos já está selecionado porque você navegou dele.
      >
      > [!div class="mx-imgBorder"]
      >![Uma captura de tela da guia Noções Básicas com o pool de host pré-selecionado.](media/host-pool-selected.png)

7. Selecione **RemoteApp** em tipo de grupo de aplicativos e insira um nome para o seu RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela dos campos de tipo de grupo de aplicativos. "RemoteApp" está realçado.](media/remoteapp-button.png)

8.  Selecione a guia **Atribuições**.

9.  Para publicar usuários individuais ou grupos de usuários no grupo de aplicativos, selecione **+Adicionar usuários ou grupos de usuários do Azure AD**.

10.  Selecione o número de usuários aos quais você deseja adicionar os aplicativos. Você pode selecionar um ou vários usuários e grupos de usuários.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela do menu de seleção de usuário.](media/select-users.png)

11.  Selecione **Selecionar**.

12.  Selecione a guia **Aplicativos** e, em seguida, selecione **+Adicionar aplicativos**.

13.  Para adicionar um aplicativo do menu iniciar:

      - Acesse **Origem do aplicativo** e selecione **menu Iniciar** no menu suspenso. Em seguida, vá para **Aplicativo** e escolha o aplicativo no menu suspenso.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da tela Adicionar Aplicativo com o menu Iniciar selecionado.](media/add-app-start.png)

      - Em **Nome de exibição**, insira o nome do aplicativo que será mostrado ao usuário em seu cliente.

      - Deixe as outras opções no estado em que se encontram e selecione **Salvar**.

14. Para adicionar um aplicativo de um caminho de arquivo específico:

      - Acesse **Origem do aplicativo** e selecione **Caminho do arquivo** no menu suspenso.

      - Insira o caminho para o aplicativo no host da sessão, registrado com o pool de host associado.

      - Insira os detalhes do aplicativo nos campos **Nome do aplicativo**, **Nome de exibição**, **Caminho do ícone** e **Índice do ícone**.

      - Clique em **Salvar**.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da página Adicionar Aplicativo com o caminho de arquivo selecionado.](media/add-app-file.png)

     Repita esse processo para cada aplicativo que você deseja adicionar ao grupo de aplicativos.

15.  Em seguida, selecione a guia **Workspace**.

16.  Se você quiser registrar o grupo de aplicativos em um workspace, vá para **Registrar grupo de aplicativos** e selecione **Sim**. Se você preferir registrar o grupo de aplicativos depois, selecione **Não**.

17.  Se você selecionar **Sim**, poderá selecionar um workspace existente para registrar seu grupo de aplicativos.

       >[!NOTE]
       >Você só pode registrar o grupo de aplicativos em workspaces criados na mesma localização que o pool de host. Além disso, Se você tiver registrado anteriormente outro grupo de aplicativos do mesmo pool de host que o novo grupo de aplicativos em um workspace, ele será selecionado e você não poderá editá-lo. Todos os grupos de aplicativos de um pool de host devem ser registrados no mesmo workspace.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da página Registrar grupo de aplicativos para um workspace existente. O pool de host é pré-selecionado.](media/register-existing.png)

18. Opcionalmente, se você quiser criar marcas para facilitar a organização do seu workspace, selecione a guia **Marcas** e insira os nomes das marcas.

19. Ao terminar, selecione a guia **Examinar + criar**.

20. Aguarde um pouco para que o processo de validação seja concluído. Ao terminar, selecione **Criar** para implantar seu grupo de aplicativos.

O processo de implantação fará o seguinte para você:

- Criar o grupo de aplicativos do RemoteApp.
- Adicionar os aplicativos selecionados ao grupo de aplicativos.
- Publicar o grupo de aplicativos publicado para usuários e grupos de usuários selecionados.
- Registrar o grupo de aplicativos, se você optou por fazer isso.
- Criar um link para um modelo de Azure Resource Manager com base em sua configuração que você pode baixar e salvar para mais tarde.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um grupo de aplicativos, preenchê-lo com os programas RemoteApp e atribuir usuários ao grupo de aplicativos. Para saber como criar um pool de host de validação, consulte o tutorial a seguir. Você pode usar um pool de host de validação para monitorar a atualizações de serviço antes de implantá-las em seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um pool de hosts para validar as atualizações de serviço](./create-validation-host-pool.md)
