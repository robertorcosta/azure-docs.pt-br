---
title: Personalizar um modelo de marcas com o site do Video Indexer
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Marcas com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128055"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizar um modelo de marcas com o site do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em conteúdo de vídeo ou áudio ou se aparecer em texto visual em um vídeo, o Video Indexer detecta-o como uma marca no conteúdo.

Um modelo de marcas personalizadas permite:

- selecionar se você quiser indexador de vídeo para detectar marcas a partir do banco de dados de marcas Bing.
- selecionar se você quiser que o Indexador de vídeo exclua certas marcas de serem detectadas (essencialmente criando uma lista de negação de marcas).
- selecione se você quer que o Video Indexer inclua marcas que devem fazer parte do seu modelo que podem não estar no banco de dados de marcas de Bing (essencialmente criando uma lista de aceitação de marcas).

Para obter uma visão geral detalhada, consulte esta [visão geral](customize-brands-model-overview.md).

Você pode usar o site do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Editar configurações do modelo Marcas

Você tem a opção de definir se deseja que as marcas do banco de dados de marcas do Bing sejam detectadas ou não. Para definir essa opção, você precisa editar as configurações do modelo Brands. Siga estas etapas:

1. Acesse o site do [Video Indexer](https://www.videoindexer.ai/) e faça login.
2. Para personalizar um modelo em sua conta, selecione o botão **de personalização** do modelo de conteúdo no canto superior direito da página.

   ![Personalize o modelo de conteúdo no Indexador de Vídeo](./media/content-model-customization/content-model-customization.png)

3. Para editar marcas, selecione a guia **Marcas**.

    ![Personalizar modelo de marcas no Indexador de Vídeo](./media/customize-brand-model/customize-brand-model.png)

4. Verifique as **marcas de Show sugeridas pela** opção Bing se você quiser que o Indexador de vídeo detecte marcas sugeridas por Bing — deixe a opção sem controle se você não fizer isso.

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A seção **Incluir marcas** representa marcas personalizadas que você deseja que o Indexador de Vídeo detecte, mesmo que elas não sejam sugeridas por Bing.  

### <a name="add-a-brand-to-include-list"></a>Adicionar uma marca para incluir lista

1. Selecione **+ Adicionar marca**.

    ![Personalizar modelo de marcas no Indexador de Vídeo](./media/customize-brand-model/add-brand.png)

    Forneça um nome (obrigatório), uma categoria (opcional), uma descrição (opcional) e uma URL de referência (opcional).
    A função do campo de categoria é ajudá-lo na marcação das marcas comerciais. Esse campo aparece como as *marcas* da marca comercial ao usar as APIs do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

    O campo url de referência pode ser qualquer site de referência para a marca (como um link para sua página da Wikipédia).

2. Selecione **Adicionar marca** e verá que a marca foi adicionada à lista **Incluir marcas.**

### <a name="edit-a-brand-on-the-include-list"></a>Editar uma marca na lista de inclusão

1. Selecione o ícone do lápis ao lado da marca que deseja editar.

    Você pode atualizar a categoria, a descrição ou a URL de referência de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.

2. Selecione o botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-include-list"></a>Exclua uma marca na lista de inclusão

1. Selecione o ícone de lixo ao lado da marca que deseja excluir.
2. Selecione **Excluir** e a marca não aparecerá mais na lista *Incluir marcas.*

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A seção **Exclua marcas** representa as marcas que você não quer que o Indexador de Vídeo detecte.

### <a name="add-a-brand-to-exclude-list"></a>Adicionar uma marca para excluir lista

1. Selecione **+ Adicionar marca.**

    Forneça um nome (obrigatório) e uma categoria (opcional).

2. Selecione **Adicionar marca** e verá que a marca foi adicionada à lista De *excluir marcas.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Editar uma marca na lista de exclusão

1. Selecione o ícone do lápis ao lado da marca que deseja editar.

    Só é possível atualizar a categoria de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.

2. Selecione o botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-exclude-list"></a>Exclua uma marca na lista de exclusão

1. Selecione o ícone de lixo ao lado da marca que deseja excluir.
2. Selecione **Excluir** e a marca não aparecerá mais na lista *Despagar marcas.*

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
