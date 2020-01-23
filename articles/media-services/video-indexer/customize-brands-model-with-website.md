---
title: Use o site Video Indexer para personalizar modelos de marcas – Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de marcas com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513891"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizar um modelo de marcas com o site do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionada em um conteúdo de áudio ou de vídeo ou se ela aparece no texto visual em um vídeo, o Video Indexer detecta-a como uma marca presente no conteúdo. Um modelo de marcas personalizado permite que você selecione se deseja ou não que o Video Indexer detecte marcas do banco de dados de marcas do Bing, exclua determinadas marcas da lista daquelas que serão detectadas (essencialmente criando uma lista de bloqueio de marcas) e inclua marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de permissões de marcas).

Para obter uma visão geral detalhada, confira [Visão geral](customize-brands-model-overview.md).

Você pode usar o site do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Editar as configurações do modelo de marcas  

Você tem a opção de definir se deseja que as marcas do banco de dados de marcas do Bing sejam detectadas ou não. Para isso, você precisará editar as configurações do seu modelo de marcas.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Para personalizar um modelo em sua conta, clique no botão **Personalização de modelo de conteúdo** no canto superior direito da página.
 
   ![Personalizar o modelo de conteúdo](./media/content-model-customization/content-model-customization.png) 
3. Para editar marcas, selecione a guia **Marcas**.

    ![Personalizar o modelo de marcas](./media/customize-brand-model/customize-brand-model.png)
4. Marque a opção **Mostrar marcas sugeridas pelo Bing** se você desejar que o Video Indexer inclua as marcas sugeridas pelo Bing. Deixe a opção desmarcada se você não quiser que o Video Indexer detecte as marcas sugeridas pelo Bing no seu conteúdo. 

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A seção **Incluir marcas** representa marcas personalizadas que você deseja que o Video Indexer detecte, mesmo se elas não são sugeridas pelo Bing.  

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ Adicionar marca".

    ![Personalizar o modelo de marcas](./media/customize-brand-model/add-brand.png)

    Forneça um nome (obrigatório), uma categoria (opcional), uma descrição (opcional) e uma URL de referência (opcional).
    A função do campo de categoria é ajudá-lo na marcação das marcas comerciais. Esse campo aparece como as *marcas* da marca comercial ao usar as APIs do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

    O campo de URL de referência pode ser qualquer site de referência para a marca, assim como um link para sua página da Wikipédia.
2. Clique em "Adicionar marca" e você verá que a marca foi adicionada à lista **Incluir marcas**.

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis ao lado da marca que você deseja editar.

    Você pode atualizar a categoria, a descrição ou a URL de referência de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.
2. Clique no botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Excluir uma marca

1. Clique no ícone de lixeira ao lado da marca que você deseja excluir.
2. Clique em "Excluir" e a marca não aparecerá mais na sua lista *Incluir marcas*.

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A seção **Excluir marcas** representa as marcas que você deseja que o Video Indexer não detecte.

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ Adicionar marca".

    Forneça um nome (obrigatório) e uma categoria (opcional).
2. Clique em "Adicionar marca" e você verá que a marca foi adicionada à lista *Excluir marcas*.

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis ao lado da marca que você deseja editar.

    Só é possível atualizar a categoria de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.
2. Clique no botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Excluir uma marca

1. Clique no ícone de lixeira ao lado da marca que você deseja excluir.
2. Clique em "Excluir" e a marca não aparecerá mais na sua lista *Excluir marcas*.

## <a name="next-steps"></a>Próximos passos

[Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
