---
title: Personalizar modelo de idioma com o site do indexador de vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de idioma com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128086"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de Linguagem com o site do Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas.

Para obter uma visão geral detalhada e práticas recomendadas para modelos de linguagem personalizados, consulte [Personalizar um modelo de linguagem com indexador de vídeo](customize-language-model-overview.md).

Você pode usar o site do Video Indexer para criar e editar modelos de linguagem personalizados em sua conta, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de Linguagem usando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

1. Acesse o site do [Video Indexer](https://www.videoindexer.ai/) e faça login.
2. Para personalizar um modelo em sua conta, selecione o botão **de personalização** do modelo de conteúdo no canto superior direito da página.

   ![Personalize o modelo de conteúdo no Indexador de Vídeo](./media/content-model-customization/content-model-customization.png)

3. Selecione a guia **Linguagem**.

    Obtenha uma lista de linguagens de programação compatíveis.

    ![Lista de modelos de idiomas no Indexador de Vídeo](./media/customize-language-model/customize-language-model.png)

4. No idioma que você deseja, selecione **Adicionar modelo**.
5. Digite o nome para o modelo de Linguagem e pressione Enter.

    Esta etapa cria o modelo e dá a opção de carregar arquivos de texto para o modelo.

6. Para adicionar um arquivo de texto, selecione **Adicionar arquivo**. Seu explorador de arquivos abrirá.

7. Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

    Você também pode adicionar um arquivo de texto selecionando o **botão ...** no lado direito do modelo de Idioma e selecionando **Adicionar arquivo**.

8. Depois de terminar de carregar os arquivos de texto, selecione a opção **Trem** verde.

    ![Modelo de linguagem de trem em Indexador de Vídeo](./media/customize-language-model/train-model.png)

O processo de treinamento pode levar alguns minutos. Depois que o treinamento for concluído, você verá o texto **Treinado** ao lado do modelo. Você pode visualizar, baixar e excluir o arquivo do modelo.

![Modelo de linguagem treinado em Indexador de Vídeo](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de Linguagem em um novo vídeo

Para usar seu modelo de idioma em um novo vídeo, faça uma das seguintes ações:

* Selecione o botão **Carregar** na parte superior da página.

    ![Indexador de vídeo do botão de upload](./media/customize-language-model/upload.png)

* Solte seu arquivo de áudio ou vídeo no círculo ou navegue pelo seu arquivo.

    ![Upload de indexador de vídeo de arquivo de mídia](./media/customize-language-model/upload2.png)

Você tem a opção de selecionar o **idioma de origem**do vídeo . Selecione a lista de paradas e selecione um modelo de idioma criado na lista. Ele deve indicar o idioma do seu modelo de Linguagem e o nome que você deu a ele entre parênteses.

Selecione a opção **Upload** na parte inferior da página e seu novo vídeo será indexado usando seu modelo de Idioma.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de Linguagem para reindexação

Para usar seu modelo de Linguagem para reindexar um vídeo em sua coleção, vá para seu **Vídeos da conta** na página inicial do [Video Indexer](https://www.videoindexer.ai/) e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar seu vídeo, excluir seu vídeo e reindexar seu vídeo. Selecione a opção para reindexar seu vídeo.

![Reindexar com indexador de vídeo](./media/customize-language-model/reindex1.png)

Você tem a opção de selecionar o **idioma de origem** de vídeo para reindexar seu vídeo. Selecione a lista de paradas e selecione um modelo de idioma criado na lista. Ele deve indicar o idioma do seu modelo de linguagem e o nome que você deu a ele entre parênteses.

![Escolha o idioma de origem de vídeo — reindexe um vídeo com o indexador de vídeo](./media/customize-language-model/reindex.png)

Selecione o botão **Reindexe** e seu vídeo será reindexado usando seu modelo de Idioma.

## <a name="edit-a-language-model"></a>Editar um modelo de Linguagem

Você pode editar um modelo de Linguagem alterando seu nome, adicionando arquivos a ele e excluindo arquivos dele.

Se você adicionar ou excluir arquivos do modelo Language, você terá que treinar o modelo novamente selecionando a opção **Trem** verde.

### <a name="rename-the-language-model"></a>Renomear o modelo de Linguagem

Você pode alterar o nome do modelo de idioma selecionando o botão ellipsis (**...**) no lado direito do modelo de idioma e selecionando **Renome**.

Digite o novo nome e clique em Enter.

### <a name="add-files"></a>Adicionar arquivos

Para adicionar um arquivo de texto, selecione **Adicionar arquivo**. Seu explorador de arquivos abrirá.

Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

Você também pode adicionar um arquivo de texto selecionando o botão ellipsis (**...**) no lado direito do modelo de Idioma e selecionando **Adicionar arquivo**.

### <a name="delete-files"></a>Excluir arquivos

Para excluir um arquivo do modelo Language, selecione o botão ellipsis (**...**) no lado direito do arquivo de texto e **selecione Excluir**. Uma nova janela aparece dizendo que a exclusão não pode ser desfeita. Selecione a opção **Excluir** na nova janela.

Esta ação remove o arquivo completamente do modelo de Linguagem.

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

Para excluir um modelo de idioma da sua conta, selecione o botão ellipsis (**...**) no lado direito do modelo de Idioma e selecione **Excluir**.

Uma nova janela aparece dizendo que a exclusão não pode ser desfeita. Selecione a opção **Excluir** na nova janela.

Esta ação remove o modelo de Linguagem completamente de sua conta. Qualquer vídeo que estivesse usando o modelo de idioma excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, você pode atribuir um novo modelo de idioma ao vídeo. Caso contrário, o Video Indexer usará seu modelo padrão para reindexar o vídeo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de idioma corrigindo transcrições

O Video Indexer suporta personalização automática de modelos de idioma com base nas correções reais que os usuários fazem às transcrições de seus vídeos.

1. Para fazer correções em uma transcrição, abra o vídeo que deseja editar a partir de seus vídeos de conta. Selecione a guia **Linha do Tempo.**

    ![Personalizar a guia de linha do tempo do modelo de idioma — indexador de vídeo](./media/customize-language-model/timeline.png)

1. Selecione o ícone do lápis para editar a transcrição de sua transcrição.

    ![Personalizar transcrição de edição de modelo de idioma — indexador de vídeo](./media/customize-language-model/edits.png)

    O Video Indexer captura todas as linhas que são corrigidas por você na transcrição do seu vídeo e as adiciona automaticamente a um arquivo de texto chamado "A partir de edições de transcrição". Essas edições são usadas para retreinar o modelo de linguagem específico que foi usado para indexar este vídeo.
    
    Se você não especificou um modelo de idioma ao indexar este vídeo, todas as edições deste vídeo serão armazenadas em um modelo de idioma padrão chamado "Adaptações de conta" dentro da linguagem detectada do vídeo.
    
    Caso várias edições tenham sido feitas para a mesma linha, apenas a última versão da linha corrigida será usada para atualizar o modelo Language.  
    
    > [!NOTE]
    > Apenas correções texuricas são usadas para a personalização. Correções que não envolvem palavras reais (por exemplo, marcas de pontuação ou espaços) não estão incluídas.
    
1. Você verá as correções de transcrição aparecerem na guia Idioma da página de personalização do modelo conteúdo.

    ![Personalizar o modelo de linguagem — indexador de vídeo](./media/customize-language-model/customize.png)

   Para olhar o arquivo "De edições de transcrição" para cada um dos seus modelos de idioma, selecione-o para abri-lo.

    ![A partir de edições de transcrição — Indexador de vídeo](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Próximas etapas

[Personalize o modelo de linguagem usando APIs](customize-language-model-with-api.md)
