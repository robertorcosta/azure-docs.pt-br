---
title: Personalizar modelos de idioma com o site Video Indexer-Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Linguagem com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513908"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de Linguagem com o site do Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas. 

Para obter uma visão geral detalhada e as melhores práticas para modelos de linguagem personalizados, confira [Personalizar um modelo de Linguagem com o Video Indexer](customize-language-model-overview.md).

Você pode usar o site do Video Indexer para criar e editar modelos de linguagem personalizados em sua conta, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de Linguagem usando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Para personalizar um modelo em sua conta, clique no botão **Personalização de modelo de conteúdo** no canto superior direito da página.

   ![Personalizar o modelo de conteúdo](./media/content-model-customization/content-model-customization.png)

3. Selecione a guia **Linguagem**.

    Obtenha uma lista de linguagens de programação compatíveis. 

    ![Personalizar um modelo de linguagem](./media/customize-language-model/customize-language-model.png)

4. Na linguagem de programação que você deseja, clique em **Adicionar modelo**.
5. Digite o nome para o modelo de Linguagem e pressione Enter.

    Isso cria o modelo e oferece a opção de carregar arquivos de texto no modelo.
6. Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o Explorador de Arquivos.

7. Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

    Você também pode adicionar um arquivo de texto, clicando no botão **...** no lado direito do modelo de Linguagem e selecionando **Adicionar arquivo**.
8. Depois que você terminar de carregar os arquivos de texto, clique na opção verde **Treinar**.

    ![Treinar modelo de linguagem](./media/customize-language-model/train-model.png)

O processo de treinamento pode levar alguns minutos. Depois que o treinamento for concluído, você verá o texto **Treinado** ao lado do modelo. Você pode visualizar, baixar e excluir o arquivo do modelo.

![Modelo de linguagem treinado](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de Linguagem em um novo vídeo

Para usar seu modelo de Linguagem em um novo vídeo, siga um destes procedimentos:

* Clique no botão **Carregar** na parte superior da página 

    ![Carregar](./media/customize-language-model/upload.png)
* Solte o arquivo de áudio ou vídeo no círculo ou procure o arquivo

    ![Carregar](./media/customize-language-model/upload2.png)

Isso lhe dará a opção de selecionar o **idioma de origem de vídeo**. Clique na lista suspensa e selecione um modelo de Linguagem que você criou na lista. Ele deve indicar o idioma do seu modelo de Linguagem e o nome que você deu a ele entre parênteses.

Clique na opção **Carregar** na parte inferior da página e seu novo vídeo será indexado usando seu modelo de Linguagem.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de Linguagem para reindexação

Para usar seu modelo de Linguagem para reindexar um vídeo em sua coleção, vá para seu **Vídeos da conta** na página inicial do [Video Indexer](https://www.videoindexer.ai/) e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar seu vídeo, excluí-lo e reindexá-lo. Clique na opção de reindexar o vídeo.

![Reindexar](./media/customize-language-model/reindex1.png)

Isso lhe dá a opção de selecionar o **Idioma de origem de vídeo** com o qual reindexar o vídeo em questão. Clique na lista suspensa e selecione um modelo de Linguagem que você criou na lista. Ele deve indicar o idioma do seu modelo de linguagem e o nome que você deu a ele entre parênteses.

![Reindexar](./media/customize-language-model/reindex.png)

Clique no botão **Reindexar** e o vídeo será reindexado usando o modelo de Linguagem.

## <a name="edit-a-language-model"></a>Editar um modelo de Linguagem

Você pode editar um modelo de Linguagem alterando seu nome, adicionando arquivos a ele e excluindo arquivos dele.

Se adicionar ou excluir arquivos de modelo de idioma, você terá que treinar o modelo novamente, clicando na opção verde **Treinar**.

### <a name="rename-the-language-model"></a>Renomear o modelo de Linguagem

Você pode alterar o nome do modelo de Linguagem clicando em **...**  no lado direito do modelo de Linguagem e selecionando **Renomear**. 

Digite o novo nome e clique em Enter.

### <a name="add-files"></a>Adicionar arquivos

Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o Explorador de Arquivos.

Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

Você também pode adicionar um arquivo de texto, clicando no botão **...** no lado direito do modelo de Linguagem e selecionando **Adicionar arquivo**.

### <a name="delete-files"></a>Excluir arquivos

Para excluir um arquivo do modelo de Linguagem, clique no botão **...** no lado direito do arquivo de texto e selecione **Excluir**. Isso abre uma nova janela, informando que a exclusão não pode ser desfeita. Clique na opção **Excluir** na nova janela.

Esta ação remove o arquivo completamente do modelo de Linguagem.

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

Para excluir um modelo de Linguagem de sua conta, clique no botão **...** no lado direito do modelo de Linguagem e selecione **Excluir**.

Isso abre uma nova janela, informando que a exclusão não pode ser desfeita. Clique na opção **Excluir** na nova janela.

Esta ação remove o modelo de Linguagem completamente de sua conta. Qualquer vídeo que estava usando o modelo de Linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de Linguagem a ele. Caso contrário, o Video Indexer usará seu modelo padrão para reindexar o vídeo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem corrigindo transcrições

O Video Indexer dá suporte à personalização automática de modelos de linguagem com base nas correções reais que os usuários fazem nas transcrições de seus vídeos.

1. Para fazer correções em uma transcrição, abra o vídeo que você deseja editar de seus vídeos de conta. Selecione a guia **linha do tempo** .

    ![Personalizar um modelo de linguagem](./media/customize-language-model/timeline.png)
1. Clique no ícone de lápis para editar a transcrição de sua transcrição. 

    ![Personalizar um modelo de linguagem](./media/customize-language-model/edits.png)

    Video Indexer captura todas as linhas que são corrigidas por você na transcrição do vídeo e as adiciona automaticamente a um arquivo de texto chamado "das edições de transcrição". Essas edições são usadas para treinar novamente o modelo de linguagem específico que foi usado para indexar este vídeo. 
    
    Se você não especificou um modelo de linguagem ao indexar este vídeo, todas as edições desse vídeo serão armazenadas em um modelo de idioma padrão chamado adaptações de conta no idioma detectado do vídeo. 
    
    Caso várias edições tenham sido feitas na mesma linha, somente a última versão da linha corrigida será usada para atualizar o modelo de idioma.  
    
    > [!NOTE]
    > Somente correções textuais são usadas para a personalização. Isso significa que as correções que não envolvem palavras reais (por exemplo, marcas ou espaços de pontuação) não são incluídas. 
    
1. Você verá as correções de transcrição aparecerem na guia idioma da página personalização do modelo de conteúdo.

    ![Personalizar um modelo de linguagem](./media/customize-language-model/customize.png)

   Para ver o arquivo "de edições de transcrição" para cada um dos seus modelos de idioma, clique nele para abri-lo. 

    ![De edições de transcrição](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Próximos passos

[Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)
