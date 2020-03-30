---
title: Criar e compartilhar painéis no Portal do Azure
description: Este artigo descreve como criar, personalizar, publicar e compartilhar dashboards no portal Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4c01321662b302103cdedfb5b78dadf89860fb8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132055"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e compartilhar painéis no Portal do Azure

Os dashboards são uma visão focada e organizada de seus recursos em nuvem no portal Azure. Use os painéis como um workspace no qual você possa iniciar rapidamente tarefas para operações diárias e monitorar recursos. Crie painéis personalizados com base em projetos, tarefas ou funções de usuário, por exemplo.

O portal Azure fornece um painel padrão como ponto de partida. Você pode editar o painel padrão. Crie e personalize painéis adicionais e publique e compartilhe dashboards para torná-los disponíveis para outros usuários. Este artigo descreve como criar um novo painel de controle, personalizar a interface e publicar e compartilhar dashboards.

## <a name="create-a-new-dashboard"></a>Crie um novo painel

Neste exemplo, criamos um novo painel de instrumentos privado e atribuímos um nome. Siga estas etapas para começar:

1. Faça login no [portal Azure](https://portal.azure.com).

1. No menu portal Azure, selecione **Painel**. Sua exibição padrão já pode estar definida como painel de instrumentos.

    ![Abra o painel](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecione Novo painel de **instrumentos**.

    ![Captura de tela do novo painel](./media/azure-portal-dashboards/create-new-dashboard.png)

    Esta ação abre a **Galeria de Azulejos**, a partir da qual você selecionará telhas, e uma grade vazia onde você organizará as telhas.

    ![Captura de tela da galeria de azulejos e grade vazia](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecione o texto **Meu Painel** na etiqueta do painel e digite um nome que o ajudará a identificar facilmente o painel personalizado.

1. Selecione **Feito personalizando** no cabeçalho da página para sair do modo de edição.

A visualização do painel agora mostra seu novo painel. Selecione a seta ao lado do nome do painel para ver os painéis disponíveis para você. A lista pode incluir dashboards que outros usuários criaram e compartilharam.

## <a name="edit-a-dashboard"></a>Editar um painel

Agora, vamos editar o painel de instrumentos para adicionar, redimensionar e organizar ladrilhos que representam seus recursos do Azure.

### <a name="add-tiles-from-the-dashboard"></a>Adicionar ladrilhos do painel

Para adicionar ladrilhos a um painel, siga estas etapas:

1. Selecione ![](./media/azure-portal-dashboards/dashboard-edit-icon.png) editar ícone **Editar** a partir do cabeçalho da página.

    ![Captura de tela da edição de destaque do painel](./media/azure-portal-dashboards/dashboard-edit.png)

1. Navegue pela **Galeria de Azulejos** ou use o campo de pesquisa para encontrar o azulejo que você deseja.

1. Selecione **Adicionar** para adicionar o azulejo ao painel de instrumentos com um tamanho e localização padrão. Ou, arraste o azulejo para a grade e coloque-o onde quiser.

> [!TIP]
> Se você trabalhar com mais de uma organização, adicione o bloco **de identidade** da Organização ao seu painel para mostrar claramente a qual organização os recursos pertencem.

### <a name="add-tiles-from-a-resource-page"></a>Adicionar ladrilhos de uma página de recursos

Há uma maneira alternativa de adicionar azulejos ao seu painel. Muitas páginas de recursos incluem um ícone pushpin na barra de comando. Se você selecionar o ícone, um bloco representando a página de origem será fixado no painel de controle que está ativo no momento. 

![Captura de tela da barra de comando da página com ícone de pino](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar as telhas

Para alterar o tamanho de um azulejo ou reorganizar as telhas em um painel, siga estas etapas:

1. Selecione ![](./media/azure-portal-dashboards/dashboard-edit-icon.png) editar ícone **Editar** a partir do cabeçalho da página.

1. Selecione o menu de contexto no canto superior direito de um azulejo. Em seguida, escolha um tamanho de azulejo. As telhas que suportam qualquer tamanho também incluem uma "alça" no canto inferior direito que permite arrastar o azulejo para o tamanho que você deseja.

    ![Captura de tela do painel com menu de tamanho de azulejo aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecione um azulejo e arraste-o para um novo local na grade para organizar seu painel.

### <a name="additional-tile-configuration"></a>Configuração adicional de ladrilhos

Algumas telhas podem exigir mais configuração para mostrar as informações desejadas. Por exemplo, o bloco **gráfico Metrics** deve ser configurado para exibir uma métrica do **Monitor Azure**. Você também pode personalizar dados de ladrilhos para substituir as configurações de tempo padrão do painel.

Qualquer ladrilho que precise ser configurado exibe um banner **configure tile** até personalizar o azulejo. Para personalizar o azulejo:

1. Selecione **Feito personalizando** no cabeçalho da página para sair do modo de edição.

1. Selecione o banner e faça a configuração necessária.

    ![Captura de tela de azulejo que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um azulejo de marcação permite que você exiba conteúdo estático personalizado no painel. Isso pode ser instruções básicas, uma imagem, um conjunto de hiperlinks, ou até mesmo informações de contato. Para obter mais informações sobre o uso de um bloco de marcação, consulte [Use um bloco de marcação nos painéis do Azure para mostrar conteúdo personalizado](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Personalizar dados de ladrilhos

Os dados no painel mostram automaticamente a atividade nas últimas 24 horas. Para mostrar um período de tempo diferente para apenas este azulejo, siga estas etapas:

1. Selecione Personalizar dados de **azulejo** saem do menu de contexto ou do ![filtro de ícone](./media/azure-portal-dashboards/dashboard-filter.png) do filtro no canto superior esquerdo do azulejo.

    ![Captura de tela do menu de contexto de azulejo](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selecione a caixa de seleção para **substituir as configurações de tempo do painel no nível do azulejo**.

    ![Captura de tela de diálogo para configurar configurações de tempo de ladrilho](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Escolha o intervalo de tempo para mostrar para este azulejo. Você pode escolher entre os últimos 30 minutos até os últimos 30 dias ou definir um intervalo personalizado.

1. Escolha a granularidade de tempo a ser exibida. Você pode mostrar em qualquer lugar desde incrementos de um minuto até um mês.

1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Excluir um bloco

Para remover um azulejo de um painel, siga estas etapas:

* Selecione o menu de contexto no canto superior direito do azulejo e, em seguida, **selecione Remover no painel**. Ou,

* Selecione ![](./media/azure-portal-dashboards/dashboard-edit-icon.png) editar ícone **Editar** para entrar no modo de personalização. Passar o tempo no canto superior direito ![do](./media/azure-portal-dashboards/dashboard-delete-icon.png) azulejo e, em seguida, selecione o ícone excluir ícone de exclusão para remover o azulejo do painel.

   ![Captura de tela mostrando como remover azulejos do painel](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um painel

Para usar um painel existente como modelo para um novo painel, siga estas etapas:

1. Certifique-se de que a visualização do painel está mostrando o painel que você deseja copiar.

1. No cabeçalho ![da](./media/azure-portal-dashboards/dashboard-clone.png) página, selecione clone ícone **Clone**.

1. Uma cópia do painel, chamada **Clone do** nome do *painel,* é aberta no modo de edição. Use as etapas anteriores neste artigo para renomear e personalizar o painel de instrumentos.

## <a name="publish-and-share-a-dashboard"></a>Publique e compartilhe um painel

Quando você cria um painel, ele é privado por padrão, o que significa que você é o único que pode vê-lo. Para disponibilizar dashboards para outros, você pode publicá-los e compartilhá-los. Para obter mais informações, consulte [os painéis do Share Azure usando o Controle de Acesso Baseado em Função](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abra um painel compartilhado

Para encontrar e abrir um painel compartilhado, siga estas etapas:

1. Selecione a seta ao lado do nome do painel.

1. Selecione na lista exibida de painéis. Se o painel que você deseja abrir não estiver listado:

    1. selecione **Procurar todos os painéis**.

        ![Captura de tela do menu de seleção do painel](./media/azure-portal-dashboards/dashboard-browse.png)

    1. No **campo Tipo,** selecione **painéis compartilhados**.

        ![Captura de tela de todos os menus de seleção de painéis](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selecione uma ou mais assinaturas. Você também pode inserir texto para filtrar painéis pelo nome.

    1. Selecione um painel na lista de painéis compartilhados.

## <a name="delete-a-dashboard"></a>Excluir um painel

Para excluir permanentemente um painel privado ou compartilhado, siga estas etapas:

1. Selecione o painel que deseja excluir da lista ao lado do nome do painel.

1. Selecione ![](./media/azure-portal-dashboards/dashboard-delete-icon.png) excluir ícone **Excluir** do cabeçalho da página.

1. Para um painel privado, selecione **OK** na caixa de confirmação para remover o painel. Para um painel compartilhado, na caixa de diálogo de confirmação, selecione a caixa de seleção para confirmar que o painel publicado não será mais visível por outros. Em seguida, selecione **OK**.

    ![Captura de tela da confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Próximas etapas

* [Compartilhar painéis do Azure usando o Controle de Acesso Baseado em Função](azure-portal-dashboard-share-access.md)
* [Criar programáticamente painéis Azure](azure-portal-dashboards-create-programmatically.md)
