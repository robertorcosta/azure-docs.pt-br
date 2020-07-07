---
title: Personalizar o modelo de linguagem com o site Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de linguagem com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128086"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de Linguagem com o site do Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas.

Para obter uma visão geral detalhada e práticas recomendadas para modelos de linguagem personalizados, consulte [Personalizar um modelo de linguagem com Video indexer](customize-language-model-overview.md).

Você pode usar o site do Video Indexer para criar e editar modelos de linguagem personalizados em sua conta, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de Linguagem usando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

1. Acesse o site [Video indexer](https://www.videoindexer.ai/) e entre.
2. Para personalizar um modelo em sua conta, selecione o botão **personalização do modelo de conteúdo** no canto superior direito da página.

   ![Personalizar o modelo de conteúdo no Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Selecione a guia **Linguagem**.

    Obtenha uma lista de linguagens de programação compatíveis.

    ![Lista de modelos de idiomas no Video Indexer](./media/customize-language-model/customize-language-model.png)

4. No idioma desejado, selecione **Adicionar modelo**.
5. Digite o nome para o modelo de Linguagem e pressione Enter.

    Esta etapa cria o modelo e dá a opção de carregar arquivos de texto para o modelo.

6. Para adicionar um arquivo de texto, selecione **Adicionar arquivo**. O explorador de arquivos será aberto.

7. Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

    Você também pode adicionar um arquivo de texto selecionando o botão **...** no lado direito do modelo de idioma e selecionando **Adicionar arquivo**.

8. Quando terminar de carregar os arquivos de texto, selecione a opção de **trem** verde.

    ![Treinar o modelo de idioma no Video Indexer](./media/customize-language-model/train-model.png)

O processo de treinamento pode levar alguns minutos. Depois que o treinamento for concluído, você verá o texto **Treinado** ao lado do modelo. Você pode visualizar, baixar e excluir o arquivo do modelo.

![Modelo de idioma treinado no Video Indexer](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de Linguagem em um novo vídeo

Para usar seu modelo de idioma em um novo vídeo, execute uma das seguintes ações:

* Selecione o botão **carregar** na parte superior da página.

    ![Botão carregar Video Indexer](./media/customize-language-model/upload.png)

* Solte o arquivo de áudio ou vídeo no círculo ou procure pelo arquivo.

    ![Carregar arquivo de mídia Video Indexer](./media/customize-language-model/upload2.png)

Você tem a opção de selecionar o **idioma da fonte de vídeo**. Selecione o menu suspenso e selecione um modelo de idioma que você criou na lista. Ele deve indicar o idioma do seu modelo de Linguagem e o nome que você deu a ele entre parênteses.

Selecione a opção **carregar** na parte inferior da página e o novo vídeo será indexado usando seu modelo de linguagem.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de Linguagem para reindexação

Para usar seu modelo de Linguagem para reindexar um vídeo em sua coleção, vá para seu **Vídeos da conta** na página inicial do [Video Indexer](https://www.videoindexer.ai/) e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar seu vídeo, excluir seu vídeo e reindexar seu vídeo. Selecione a opção para reindexar o vídeo.

![Reindexar com Video Indexer](./media/customize-language-model/reindex1.png)

Você tem a opção de selecionar o **idioma de origem do vídeo** com o qual reindexar o vídeo. Selecione o menu suspenso e selecione um modelo de idioma que você criou na lista. Ele deve indicar o idioma do seu modelo de linguagem e o nome que você deu a ele entre parênteses.

![Escolher idioma da fonte de vídeo — reindexe um vídeo com Video Indexer](./media/customize-language-model/reindex.png)

Selecione o botão **reindexar** e o vídeo será reindexado usando seu modelo de linguagem.

## <a name="edit-a-language-model"></a>Editar um modelo de Linguagem

Você pode editar um modelo de Linguagem alterando seu nome, adicionando arquivos a ele e excluindo arquivos dele.

Se você adicionar ou excluir arquivos do modelo de idioma, precisará treinar o modelo novamente selecionando a opção de **trem** verde.

### <a name="rename-the-language-model"></a>Renomear o modelo de Linguagem

Você pode alterar o nome do modelo de idioma selecionando o botão de reticências (**...**) no lado direito do modelo de idioma e selecionando **renomear**.

Digite o novo nome e clique em Enter.

### <a name="add-files"></a>Adicionar arquivos

Para adicionar um arquivo de texto, selecione **Adicionar arquivo**. O explorador de arquivos será aberto.

Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

Você também pode adicionar um arquivo de texto selecionando o botão de reticências (**...**) no lado direito do modelo de idioma e selecionando **Adicionar arquivo**.

### <a name="delete-files"></a>Excluir arquivos

Para excluir um arquivo do modelo de idioma, selecione o botão de reticências (**...**) no lado direito do arquivo de texto e selecione **excluir**. Uma nova janela é exibida informando que a exclusão não pode ser desfeita. Selecione a opção **excluir** na janela novo.

Esta ação remove o arquivo completamente do modelo de Linguagem.

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

Para excluir um modelo de idioma da sua conta, selecione o botão de reticências (**...**) no lado direito do modelo de idioma e selecione **excluir**.

Uma nova janela é exibida informando que a exclusão não pode ser desfeita. Selecione a opção **excluir** na janela novo.

Esta ação remove o modelo de Linguagem completamente de sua conta. Qualquer vídeo que estava usando o modelo de linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de linguagem ao vídeo. Caso contrário, Video Indexer usará seu modelo padrão para reindexar o vídeo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem corrigindo transcrições

O Video Indexer dá suporte à personalização automática de modelos de linguagem com base nas correções reais que os usuários fazem nas transcrições de seus vídeos.

1. Para fazer correções em uma transcrição, abra o vídeo que você deseja editar de seus vídeos de conta. Selecione a guia **linha do tempo** .

    ![Personalizar a guia da linha do tempo do modelo de idioma — Video Indexer](./media/customize-language-model/timeline.png)

1. Selecione o ícone de lápis para editar a transcrição de sua transcrição.

    ![Personalizar a transcrição de edição do modelo de linguagem — Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer captura todas as linhas que são corrigidas por você na transcrição do vídeo e as adiciona automaticamente a um arquivo de texto chamado "das edições de transcrição". Essas edições são usadas para treinar novamente o modelo de linguagem específico que foi usado para indexar este vídeo.
    
    Se você não especificou um modelo de linguagem ao indexar este vídeo, todas as edições desse vídeo serão armazenadas em um modelo de idioma padrão chamado "adaptações de conta" dentro da linguagem detectada do vídeo.
    
    Caso várias edições tenham sido feitas na mesma linha, somente a última versão da linha corrigida será usada para atualizar o modelo de idioma.  
    
    > [!NOTE]
    > Somente correções textuais são usadas para a personalização. As correções que não envolvem palavras reais (por exemplo, marcas ou espaços de pontuação) não são incluídas.
    
1. Você verá as correções de transcrição aparecerem na guia idioma da página personalização do modelo de conteúdo.

    ![Personalizar o modelo de linguagem — Video Indexer](./media/customize-language-model/customize.png)

   Para examinar o arquivo "de edições de transcrição" para cada um dos seus modelos de idioma, selecione-o para abri-lo.

    ![De edições de transcrição — Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)
