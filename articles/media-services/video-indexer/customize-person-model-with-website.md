---
title: Personalize um modelo de pessoa com o site do Indexador de Vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Pessoa com o site do Indexador de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499959"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo de Pessoa com o site do Video Indexer

O Video Indexer suporta o reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Para obter uma visão geral detalhada, consulte [Personalizar um modelo person no Indexador de vídeo](customize-person-model-overview.md).

Use o site do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito no [modelo Personalizar uma Pessoa usando APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gerenciamento central de modelos de pessoa em sua conta

1. Para visualizar, editar e excluir os modelos person em sua conta, navegue até o site do Indexador de Vídeo e faça login.

2. Selecione o botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização de modelo de conteúdo](./media/customize-face-model/content-model-customization.png)

3. Selecione a guia Pessoas.

    Você verá o modelo Pessoa Padrão em sua conta. O modelo Pessoa Padrão contém quaisquer faces que você possa ter editado ou alterado nos insights de seus vídeos para os quais você não especificou um modelo de Pessoa personalizada durante a indexação.

    Se você criou outros modelos de Pessoa, eles também serão listados nesta página.

    ![Personalização de modelo de conteúdo](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

1. Selecione o botão **+ Adicionar modelo.**

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person.png)

2. Digite o nome do modelo e selecione o botão de seleção ao lado do nome.

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person2.png)

    Você criou um novo modelo de Pessoa. Agora você pode adicionar rostos ao novo modelo Person.

3. Selecione o botão de menu da lista e escolha **+ Adicionar pessoa**.

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Adicione uma nova pessoa a um modelo de Pessoa

> [!NOTE]
> O Video Indexer permite adicionar várias pessoas com o mesmo nome em um modelo person. No entanto, é recomendável que você dê nomes únicos para cada pessoa em seu modelo para usabilidade e clareza.

1. Para adicionar um novo rosto a um modelo Person, selecione o botão de menu de lista ao lado do modelo Person ao que você deseja adicionar o rosto.

1. Selecione **+ Adicione pessoa** no menu.

    ![Adicione um novo rosto à pessoa](./media/customize-face-model/add-new-face.png)

    Um pop-up solicitará que você preencha os detalhes da Pessoa. Digite o nome da pessoa e selecione o botão de verificação.

    ![Adicione um novo rosto à pessoa](./media/customize-face-model/add-new-face2.png)

    Em seguida, você pode escolher entre o explorador de arquivos ou arrastar e soltar as imagens faciais do rosto. O Video Indexer levará todos os tipos de arquivos de imagem padrão (ex: JPG, PNG e muito mais).

    O Video Indexer pode detectar ocorrências dessa pessoa nos vídeos futuros que você indexa e os vídeos atuais que você já havia indexado, usando o modelo Person ao qual você adicionou esse novo rosto. O reconhecimento da pessoa em seus vídeos atuais pode levar algum tempo para fazer efeito, já que este é um processo de lote.

## <a name="rename-a-person-model"></a>Renomear um modelo pessoa

Você pode renomear qualquer modelo de Pessoa em sua conta, incluindo o modelo Pessoa Padrão. Mesmo que você renomeie seu modelo de Pessoa padrão, ele ainda servirá como modelo de pessoa padrão em sua conta.

1. Selecione o botão de menu da lista ao lado do modelo Pessoa que você deseja renomear.
2. Selecione **Renomear** no menu.

    ![Renomear um modelo de pessoa](./media/customize-face-model/rename-person.png)

3. Selecione o nome atual do modelo e digite seu novo nome.

    ![Renomear um modelo de pessoa](./media/customize-face-model/rename-person2.png)

4. Selecione o botão de verificação para que seu modelo seja renomeado.

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Você pode excluir qualquer modelo de Pessoa que você criou em sua conta. No entanto, você não pode excluir seu modelo de pessoa padrão.

1. Selecione **Excluir** no menu.

    ![Exclua um modelo de pessoa](./media/customize-face-model/delete-person.png)

    Um pop-up aparecerá e notificará você de que esta ação excluirá o modelo Person e todas as pessoas e arquivos que ele contém. Esta ação não pode ser desfeita.

    ![Exclua um modelo de pessoa](./media/customize-face-model/delete-person2.png)

1. Se você tiver certeza, selecione excluir novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando este modelo de pessoa (agora excluído) não suportarão a capacidade de atualizar os nomes dos rostos que aparecem no vídeo. Você será capaz de editar os nomes dos rostos nesses vídeos somente depois de reindexá-los usando outro modelo Person. Se você reindexar sem especificar um modelo Pessoa, o modelo padrão será usado.

## <a name="manage-existing-people-in-a-person-model"></a>Gerenciar pessoas existentes em um modelo de Pessoa

Para olhar o conteúdo de qualquer um dos modelos de sua Pessoa, selecione a seta ao lado do nome do modelo Pessoa. O drop-down mostra todas as pessoas naquele modelo de Pessoa em particular. Se você selecionar o botão de menu da lista ao lado de cada uma das pessoas, você verá gerenciar, renomear e excluir opções.  

![Adicione um novo rosto à pessoa](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renomeie uma pessoa

1. Para renomear uma pessoa no modelo Person, selecione o botão de menu da lista e escolha **Renomear** no menu da lista.
1. Selecione o nome atual da pessoa e digite seu novo nome.
1. Selecione o botão de verificação e a pessoa será renomeada.

### <a name="delete-a-person"></a>Exclua uma pessoa

1. Para excluir uma pessoa do modelo Pessoa, selecione o botão de menu da lista e escolha **Excluir** no menu da lista.
1. Um pop-up diz que essa ação excluirá a pessoa e que essa ação não pode ser desfeita.
1. Selecione **Excluir** novamente e isso removerá a pessoa do modelo Pessoa.

### <a name="manage-a-person"></a>Gerencie uma pessoa

Se você selecionar **Gerenciar,** verá todos os rostos dos quais este modelo person está sendo treinado. Esses rostos vêm de ocorrências dessa pessoa em vídeos que usam este modelo Person ou de imagens que você carregou manualmente.

Você pode adicionar mais rostos à pessoa selecionando **Adicionar imagens**.

Você pode usar o painel de gerenciamento para renomear a pessoa e excluir a pessoa do modelo Pessoa.

## <a name="use-a-person-model-to-index-a-video"></a>Use um modelo Person para indexar um vídeo

Você pode usar um modelo Person para indexar seu novo vídeo atribuindo o modelo Pessoa durante o upload do vídeo.

Para usar o modelo Person em um novo vídeo, faça as seguintes etapas:

1. Selecione o botão **Carregar** na parte superior da página.

    ![Carregar um modelo de pessoa](./media/customize-face-model/upload.png)

1. Solte seu arquivo de vídeo no círculo ou navegue pelo seu arquivo.
1. Selecione a **seta de opções Avançadas.**

    ![Carregar um modelo de pessoa](./media/customize-face-model/upload2.png)

1. Selecione o drop-down e selecione o modelo Pessoa que você criou.

    ![Carregar um modelo de pessoa](./media/customize-face-model/upload3.png)

1. Selecione a opção **Upload** na parte inferior da página e seu novo vídeo será indexado usando o modelo Pessoa.

Se você não especificar um modelo Pessoa durante o upload, o Indexador de vídeo indexará o vídeo usando o modelo Pessoa Padrão em sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Use um modelo Person para reindexar um vídeo

Para usar um modelo Person para reindexar um vídeo em sua coleção, vá para os vídeos da sua conta na página inicial do Indexador de vídeo e dismar sobre o nome do vídeo que você deseja reindexar.

Você vê opções para editar, excluir e reindexar seu vídeo.

1. Selecione a opção para reindexar seu vídeo.

    ![Use o modelo Person para reindexar um vídeo](./media/customize-face-model/reindex.png)

    Agora você pode selecionar o modelo Pessoa para reindexar seu vídeo.
1. Selecione o drop-down e selecione o modelo Pessoa que deseja usar.

    ![Use o modelo Person para reindexar um vídeo](./media/customize-face-model/reindex2.png)

1. Selecione o botão **Reindexar** e seu vídeo será reindexado usando o modelo Pessoa.

Quaisquer novas edições que você fizer para os rostos detectados e reconhecidos no vídeo que você acabou de reindexar serão salvos no modelo Person que você usou para reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerenciando pessoas em seus vídeos

Você pode gerenciar os rostos detectados e as pessoas que são reconhecidas nos vídeos que você indexa editando e excluindo rostos.

Excluir um rosto remove um rosto específico das percepções do vídeo.

Editar um rosto renomeia um rosto detectado e possivelmente reconhecido em seu vídeo. Quando você edita um rosto em seu vídeo, esse nome é salvo como uma entrada de pessoa no modelo Pessoa que foi atribuído ao vídeo durante o upload e indexação.

Se você não atribuir um modelo Person ao vídeo durante o upload, sua edição será salva no modelo de pessoa padrão da sua conta.

### <a name="edit-a-face"></a>Editar um rosto

> [!NOTE]
> Se um modelo person tem duas ou mais pessoas diferentes com o mesmo nome, você não será capaz de marcar esse nome dentro dos vídeos que usam esse modelo Person. Você só poderá fazer alterações em pessoas que compartilham esse nome na guia Pessoas da página de personalização do modelo de conteúdo no Indexador de vídeo. Por essa razão, é recomendável que você dê nomes únicos para cada pessoa em seu modelo person.

1. Navegue até o site do Video Indexer e entre.
1. Pesquise um vídeo que você deseja exibir e editar em sua conta.
1. Para editar um rosto em seu vídeo, vá para a guia Insights e selecione o ícone do lápis no canto superior direito da janela.

    ![Edite um rosto em seu vídeo](./media/customize-face-model/edit-face.png)

1. Selecione qualquer um dos rostos detectados e altere seus nomes de "#X desconhecida" (ou o nome que foi anteriormente atribuído ao rosto).
1. Depois de digitar o novo nome, selecione o ícone de verificação ao lado do novo nome. Esta ação salva o novo nome e reconhece e nomeia todas as ocorrências deste rosto em seus outros vídeos atuais e nos vídeos futuros que você carrega. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Se você nomear um rosto com o nome de uma pessoa existente no modelo Pessoa que o vídeo está usando, as imagens faciais detectadas a partir deste vídeo dessa pessoa se fundirão com o que já existe no modelo. Se você nomear um rosto com um novo nome, uma nova entrada Person será criada no modelo Person que o vídeo está usando.

![Edite um rosto em seu vídeo](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Excluir um rosto

Para excluir um rosto detectado em seu vídeo, vá para o painel Insights e selecione o ícone do lápis no canto superior direito do painel. Selecione a opção **Excluir** abaixo do nome do rosto. Esta ação remove o rosto detectado do vídeo. O rosto da pessoa ainda será detectado nos outros vídeos em que aparece, mas você pode excluir o rosto desses vídeos também depois que eles foram indexados.

A pessoa, se tiver sido nomeada, também continuará a existir no modelo Person que foi usado para indexar o vídeo do qual você excluiu o rosto, a menos que você exclua especificamente a pessoa do modelo Pessoa.

![Exclua um rosto em seu vídeo](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
