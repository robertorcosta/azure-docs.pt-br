---
title: Personalizar um modelo Person com o site Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo Person com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/06/2020
ms.author: kumud
ms.openlocfilehash: 9f5a18cc51aa92b53a5391404b1e9d3fc800bb47
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356614"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo de Pessoa com o site do Video Indexer

Video Indexer dá suporte ao reconhecimento de celebridade para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Para obter uma visão geral detalhada, consulte [Personalizar um modelo Person no video indexer](customize-person-model-overview.md).

Use o site do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar um modelo Person usando APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gerenciamento central de modelos de pessoa em sua conta

1. Para exibir, editar e excluir os modelos de pessoa em sua conta, navegue até o site Video Indexer e entre.

2. Selecione o botão personalização do modelo de conteúdo no canto superior direito da página.

    ![Captura de tela mostra a Video Indexer com o botão de personalização do modelo de conteúdo selecionado.](./media/customize-face-model/content-model-customization.png)

3. Selecione a guia pessoas.

    Você verá o modelo de pessoa padrão em sua conta. O modelo Person padrão mantém as faces que você pode ter editado ou alterado nas informações de seus vídeos para os quais você não especificou um modelo Person personalizado durante a indexação.

    Se você criou outros modelos de pessoa, eles também serão listados nesta página.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo.](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

1. Selecione o botão **+ Adicionar modelo** .

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo com a guia pessoas selecionada.](./media/customize-face-model/add-new-person.png)

2. Insira o nome do modelo e selecione o botão de seleção ao lado do nome.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo com um nome inserido para o modelo.](./media/customize-face-model/add-new-person2.png)

    Você criou um novo modelo de pessoa. Agora você pode adicionar faces ao novo modelo Person.

3. Selecione o botão de menu lista e escolha **+ Adicionar pessoa**.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo com um menu contextual para o novo modelo com a opção de adicionar Person.](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Adicionar uma nova pessoa a um modelo Person

> [!NOTE]
> Video Indexer permite adicionar várias pessoas com o mesmo nome em um modelo Person. No entanto, é recomendável que você forneça nomes exclusivos para cada pessoa em seu modelo para usabilidade e clareza.

1. Para adicionar uma nova face a um modelo Person, selecione o botão de menu lista ao lado do modelo Person ao qual você deseja adicionar a face.

1. Selecione **+ Adicionar pessoa** no menu.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo pronta para adicionar uma pessoa com o menu contextual.](./media/customize-face-model/add-new-face.png)

    Um pop-up solicitará que você preencha os detalhes da pessoa. Digite o nome da pessoa e selecione o botão de seleção.

    ![Captura de tela mostra os detalhes de uma pessoa.](./media/customize-face-model/add-new-face2.png)

    Você pode escolher no explorador de arquivos ou arrastar e soltar as imagens de face da face. Video Indexer usará todos os tipos de arquivo de imagem padrão (por exemplo, JPG, PNG e muito mais).

    Video Indexer pode detectar ocorrências dessa pessoa nos vídeos futuros que você indexa e os vídeos atuais já indexados, usando o modelo Person ao qual você adicionou essa nova face. O reconhecimento da pessoa em seus vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lotes.

## <a name="rename-a-person-model"></a>Renomear um modelo Person

Você pode renomear qualquer modelo Person em sua conta, incluindo o modelo Person padrão. Mesmo que você renomeie o modelo Person padrão, ele ainda servirá como o modelo Person padrão em sua conta.

1. Selecione o botão de menu lista ao lado do modelo Person que você deseja renomear.
2. Selecione **Renomear** no menu.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo com um menu contextual com a opção de renomear um modelo Person. ](./media/customize-face-model/rename-person.png)

3. Selecione o nome atual do modelo e digite seu novo nome.

    ![Captura de tela mostra o modelo de pessoa renomeada.](./media/customize-face-model/rename-person2.png)

4. Selecione o botão de seleção do seu modelo a ser renomeado.

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Você pode excluir qualquer modelo de pessoa que você criou em sua conta. No entanto, você não pode excluir o modelo de pessoa padrão.

1. Selecione **Excluir** no menu.

    ![Captura de tela mostra a caixa de diálogo personalização do modelo de conteúdo com um menu contextual com a opção de excluir um modelo Person.](./media/customize-face-model/delete-person.png)

    Um pop-up será exibido e você será notificado de que essa ação excluirá o modelo Person e todas as pessoas e os arquivos que ele contém. Esta ação não pode ser desfeita.

    ![Captura de tela mostra uma caixa de diálogo de confirmação para excluir o modelo Person.](./media/customize-face-model/delete-person2.png)

1. Se tiver certeza, selecione Excluir novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando esse modelo de pessoa (agora excluído) não suportarão a capacidade de atualizar os nomes das faces que aparecem no vídeo. Você poderá editar os nomes de rostos nestes vídeos somente depois de reindexá-los usando outro modelo de pessoa. Se você reindexar sem especificar um modelo Person, o modelo padrão será usado.

## <a name="manage-existing-people-in-a-person-model"></a>Gerenciar pessoas existentes em um modelo de pessoa

Para examinar o conteúdo de qualquer um dos seus modelos de pessoa, selecione a seta ao lado do nome do modelo Person. A lista suspensa mostra todas as pessoas desse modelo Person específico. Se você selecionar o botão de menu lista ao lado de cada pessoa, verá as opções gerenciar, renomear e excluir.  

![Captura de tela mostra um menu contextual com opções para gerenciar, renomear e excluir.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renomear uma pessoa

1. Para renomear uma pessoa em seu modelo de pessoa, selecione o botão de menu lista e escolha **renomear** no menu lista.
1. Selecione o nome atual da pessoa e digite seu novo nome.
1. Selecione o botão de seleção e a pessoa será renomeada.

### <a name="delete-a-person"></a>Excluir uma pessoa

1. Para excluir uma pessoa do seu modelo de pessoa, selecione o botão de menu lista e escolha **excluir** no menu lista.
1. Um pop-up informa que essa ação excluirá a pessoa e que essa ação não pode ser desfeita.
1. Selecione **excluir** novamente e isso removerá a pessoa do modelo Person.

### <a name="manage-a-person"></a>Gerenciar uma pessoa

Se você selecionar **gerenciar**, verá a janela **detalhes da pessoa** com todas as faces das quais esse modelo de pessoa está sendo treinado. Esses rostos são provenientes de ocorrências dessa pessoa em vídeos que usam esse modelo de pessoa ou de imagens que você carregou manualmente.

> [!TIP]
> Você pode acessar a janela de **detalhes da pessoa** clicando no nome da pessoa ou clicando em **gerenciar**, conforme mostrado acima.

#### <a name="add-a-face"></a>Adicionar uma face

Você pode adicionar mais faces à pessoa selecionando **Adicionar imagens**.

#### <a name="delete-a-face"></a>Excluir um rosto

Selecione a imagem que você deseja excluir e clique em **excluir**.

#### <a name="rename-and-delete-the-person"></a>Renomear e excluir a pessoa 

Você pode usar o painel gerenciar para renomear a pessoa e excluir a pessoa do modelo Person.

## <a name="use-a-person-model-to-index-a-video"></a>Usar um modelo Person para indexar um vídeo

Você pode usar um modelo Person para indexar seu novo vídeo atribuindo o modelo Person durante o upload do vídeo.

Para usar o modelo Person em um novo vídeo, execute as seguintes etapas:

1. Selecione o botão **carregar** na parte superior da página.

    ![Captura de tela mostra o botão carregar.](./media/customize-face-model/upload.png)

1. Solte o arquivo de vídeo no círculo ou procure o arquivo.
1. Selecione a seta **Opções avançadas** .

    ![Captura de tela mostra uma caixa de diálogo carregar o arquivo de mídia.](./media/customize-face-model/upload2.png)

1. Selecione a lista suspensa e selecione o modelo Person que você criou.

    ![Captura de tela mostra uma caixa de diálogo carregar seu arquivo de mídia com o padrão selecionado para o modelo pessoas nas opções avançadas.](./media/customize-face-model/upload3.png)

1. Selecione a opção **carregar** na parte inferior da página e o novo vídeo será indexado usando seu modelo Person.

Se você não especificar um modelo de pessoa durante o carregamento, Video Indexer indexará o vídeo usando o modelo de pessoa padrão em sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usar um modelo Person para reindexar um vídeo

Para usar um modelo Person para reindexar um vídeo em sua coleção, vá para seus vídeos de conta no Video Indexer home page e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você vê opções para editar, excluir e reindexar seu vídeo.

1. Selecione a opção para reindexar o vídeo.

    ![Captura de tela mostra vídeos de conta e a opção de reindexar seu vídeo.](./media/customize-face-model/reindex.png)

    Agora você pode selecionar o modelo Person com o qual reindexar seu vídeo.
1. Selecione a lista suspensa e selecione o modelo Person que você deseja usar.

    ![Captura de tela mostra opções para um vídeo, incluindo a seleção de um modelo de pessoas.](./media/customize-face-model/reindex2.png)

1. Selecione o botão **reindexar** e seu vídeo será reindexado usando o modelo Person.

Todas as novas edições feitas nas faces detectadas e reconhecidas no vídeo que você acabou de reindexar serão salvas no modelo Person que você usou para reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerenciando pessoas em seus vídeos

Você pode gerenciar as faces detectadas e as pessoas que são reconhecidas nos vídeos que você indexa editando e excluindo rostos.

A exclusão de uma face remove uma face específica das informações do vídeo.

Editar uma face renomeia uma face que é detectada e possivelmente reconhecida em seu vídeo. Quando você edita uma face em seu vídeo, esse nome é salvo como uma entrada Person no modelo Person que foi atribuído ao vídeo durante o upload e a indexação.

Se você não atribuir um modelo Person ao vídeo durante o carregamento, a edição será salva no modelo de pessoa padrão da sua conta.

### <a name="edit-a-face"></a>Editar um rosto

> [!NOTE]
> Se um modelo de pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, você não poderá marcar esse nome nos vídeos que usam esse modelo de pessoa. Você só poderá fazer alterações em pessoas que compartilham esse nome na guia pessoas da página personalização do modelo de conteúdo no Video Indexer. Por esse motivo, é recomendável que você forneça nomes exclusivos para cada pessoa em seu modelo de pessoa.

1. Navegue até o site do Video Indexer e entre.
1. Pesquise um vídeo que você deseja exibir e editar em sua conta.
1. Para editar uma face em seu vídeo, vá para a guia insights e selecione o ícone de lápis no canto superior direito da janela.

    ![Captura de tela mostra um vídeo com uma face desconhecida para selecionar.](./media/customize-face-model/edit-face.png)

1. Selecione qualquer uma das faces detectadas e altere seus nomes de "desconhecido #X" (ou o nome que foi atribuído anteriormente à face).
1. Depois de digitar o novo nome, selecione o ícone de verificação ao lado do novo nome. Essa ação salva o novo nome e reconhece e nomeia todas as ocorrências dessa face em seus outros vídeos atuais e nos vídeos futuros que você carrega. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Se você nomear uma face com o nome de uma pessoa existente no modelo Person que o vídeo está usando, as imagens de face detectadas desse vídeo dessa pessoa serão mescladas com o que já existe no modelo. Se você nomear uma face com um novo nome, uma nova entrada de pessoa será criada no modelo Person que o vídeo está usando.

![Captura de tela mostra uma face com um nome adicionado.](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Excluir um rosto

Para excluir uma face detectada em seu vídeo, vá para o painel insights e selecione o ícone de lápis no canto superior direito do painel. Selecione a opção **excluir** abaixo do nome da face. Essa ação remove a face detectada do vídeo. A face da pessoa ainda será detectada nos outros vídeos em que aparece, mas você poderá excluir a face desses vídeos também depois que eles tiverem sido indexados.

A pessoa, se tiver sido nomeada, também continuará a existir no modelo Person que foi usado para indexar o vídeo do qual você excluiu a face, a menos que você exclua especificamente a pessoa do modelo Person.

![Excluir uma face em seu vídeo](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
