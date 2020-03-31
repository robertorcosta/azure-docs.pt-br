---
title: Use o editor do Video Indexer para criar projetos
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor do Video Indexer para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839160"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Use o editor do Video Indexer para criar projetos

O site do Video Indexer permite que você use os insights profundos de seus vídeos para: encontrar o conteúdo de mídia certo, localizar as partes em que você está interessado e usar os resultados para criar um projeto totalmente novo. Uma vez criado, o projeto pode ser renderizado e baixado do Video Indexer e ser usado em seus próprios aplicativos de edição ou fluxos de trabalho a jusante.

Alguns cenários onde você pode achar esse recurso útil são: 

* Criando destaques de filmes para trailers.
* Usando clipes antigos de vídeos em noticiários.
* Criando conteúdo mais curto para mídias sociais.

Este artigo mostra como criar um projeto do zero e também como criar um projeto a partir de um vídeo em sua conta.

## <a name="create-new-project-and-manage-videos"></a>Crie um novo projeto e gerencie vídeos

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
1. Selecione a guia **Projetos.** Se você já criou projetos antes, você verá todos os seus outros projetos aqui.
1. Clique **em Criar novo projeto**.  

    ![Novo Projeto](./media/video-indexer-view-edit/new-project.png)
1. Dê um nome ao seu projeto clicando no ícone do lápis. Substitua o texto que diz "Projeto sem título" pelo nome do seu projeto e clique na verificação.

    ![Novo Projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicione vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos só podem conter vídeos indexados no mesmo idioma. Depois de selecionar um vídeo em um idioma, você não pode adicionar os vídeos em sua conta que estão em um idioma diferente.

1. Adicione vídeos com os que você deseja trabalhar neste projeto selecionando **Adicionar vídeos**.

    Você verá todos os vídeos em sua conta e uma caixa de pesquisa que diz "Procure texto, palavras-chave ou conteúdo visual". Pesquisar vídeos que tenham uma pessoa especificada, rótulo, marca, palavra-chave ou ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estamos procurando vídeos que mencionem "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Você pode filtrar ainda mais seus resultados selecionando **resultados do Filtro**. Você pode filtrar para mostrar vídeos que têm uma determinada pessoa neles ou especificar que você só quer ver resultados de vídeo que são um em um determinado idioma ou têm um proprietário específico. <br/> Você também pode especificar o escopo de sua consulta. Por exemplo, se você quiser pesquisar "GitHub" no OCR, selecione **Texto Visual**.

    ![Filtrar](./media/video-indexer-view-edit/visual-text.png)

    Você pode colocar vários filtros em camadas na sua consulta. Use **+** / **-** os botões para adicionar/remover filtros. Use **filtros Limpar** para remover todos os filtros.
1. Para adicionar vídeos, selecione-os e selecione **Adicionar**.
1. Agora, você verá todos os vídeos que você escolheu. Estes são os vídeos dos quais você vai selecionar clipes para o seu projeto.

    Você pode reorganizar a ordem dos vídeos arrastando e soltando ou selecionando o botão de menu da lista e selecionando **Mover para baixo** ou Mover para **cima**. No menu da lista, você também poderá remover o vídeo deste projeto. 

    ![Reorganizar](./media/video-indexer-view-edit/rearrange.png)
    
    Você tem a opção de adicionar mais vídeos a este projeto a qualquer momento, selecionando **Adicionar vídeos**. Você também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. Você pode querer fazer isso se você quiser mostrar um clipe de um vídeo e, em seguida, um clipe de outro e, em seguida, outro clipe do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clipes para usar em seu projeto

Se você clicar na seta para baixo no lado direito de cada vídeo, você abrirá os insights no vídeo com base em carimbos de tempo (clipes do vídeo). 

1. Selecione **Exibir insights** para personalizar quais insights você deseja ver e quais você não quer ver. 

    ![Exibir insights](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clipes específicos, use a caixa de pesquisa que diz "Pesquisar na transcrição, texto visual, pessoas e rótulos".
1. Adicione filtros para especificar ainda mais detalhes sobre quais cenas você está procurando selecionando **opções de filtro**.

    ![Opções de filtro](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, você pode querer ver clipes onde o GitHub é mencionado enquanto Donovan Brown está na tela. Para isso, você precisa adicionar um filtro "incluir" que tenha "Pessoas" como tipo de insight. Então você precisa digitar "Donovan Brown" na caixa de pesquisa para o filtro.
    
    ![Incluir](./media/video-indexer-view-edit/include.png)
    
    Se você quiser clipes onde o GitHub é mencionado enquanto Donovan Brown _não_ estiver na tela, você simplesmente mudaria o filtro "incluir" em um filtro "excluir" usando o dropdown. 

1. Adicione um clipe ao seu projeto selecionando o segmento que deseja adicionar. Você pode desmarcar este clipe clicando no segmento novamente.
    
    Adicione todos os segmentos de um vídeo clicando na opção menu de lista ao lado do vídeo e **selecionando Selecionar todos os segmentos**. 

    ![Adicionar todos](./media/video-indexer-view-edit/add-all.png)

    Você pode limpar toda a sua seleção selecionando A seleção Clear.

> [!TIP]
> Como você está selecionando e encomendando seus clipes, você pode visualizar o vídeo no player no lado direito da página. 

![Visualização](./media/video-indexer-view-edit/preview.png)

Lembre-se de salvar seu projeto quando fizer alterações selecionando **o projeto Salvar**. 

### <a name="render-and-download-the-project"></a>Render e baixar o projeto

> [!NOTE]
> Para contas pagas do Video Indexer, renderizar seu projeto tem custos de codificação. As contas de avaliação do Indexador de Vídeo são limitadas a 5 horas de renderização.

1. Uma vez feito, certifique-se de que seu projeto foi salvo. Agora você pode renderizar este projeto. Selecione **Render e Download**. 

    ![Salvar](./media/video-indexer-view-edit/save.png)

    Haverá um popup que informa que o indexador de vídeo renderizará um arquivo e, em seguida, o link de download será enviado para o seu e-mail. Selecione Continuar. 
    
    Você também verá uma notificação de que o projeto está sendo renderizado no topo da página. Uma vez feito o feito sendo renderizado, você verá uma nova notificação de que o projeto foi renderizado com sucesso. Clique na notificação para baixar o projeto. Ele baixará o projeto em formato mp4.

    ![Renderização feita](./media/video-indexer-view-edit/rendering-done.png)

1. Você pode acessar projetos salvos na guia **Projetos.** 

    Se você selecionar este projeto, verá todos os insights e a linha do tempo deste projeto. Se você selecionar **o editor de**vídeo, você pode continuar fazendo edições para este projeto. As edições incluem a adição ou remoção de vídeos e clipes ou a renomeação do projeto.

    ![Editor de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Crie um projeto a partir do seu vídeo

Você pode criar um novo projeto diretamente a partir de um vídeo em sua conta. 

1. Vá para a guia **Biblioteca** do site do Indexador de Vídeo.
1. Abra o vídeo que você deseja usar para criar seu projeto. Na página insights e linha do tempo, selecione o botão **Editor de vídeo.**

    Isso leva você à mesma página que você usou para criar um novo projeto. Ao contrário do novo projeto, você vê os segmentos de insights carimbados do vídeo, que você havia começado a editar anteriormente.

## <a name="see-also"></a>Confira também

[Visão geral do indexador vídeo](video-indexer-overview.md)

