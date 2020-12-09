---
title: Detecção de caracteres animados com Video Indexer como
titleSuffix: Azure Media Services
description: Este "como" demonstra como usar a detecção de caracteres animados com Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 9effac182acdea6fcb41ed26faf6c2f6535a5cbf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906158"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Usar a detecção de caracteres animados (versão prévia) com o portal e a API 

Os serviços de mídia do Azure Video Indexer dão suporte à detecção, agrupamento e reconhecimento de caracteres em conteúdo animado, e essa funcionalidade está disponível por meio do portal do Azure e da API. Examine [Este tópico de visão geral](animated-characters-recognition.md) .

Este artigo demonstra como usar a detecção de caracteres animados com o portal do Azure e a API de Video Indexer.

## <a name="use-the-animated-character-detection-with-portal"></a>Usar a detecção de caracteres animados com o portal 

Esta seção descreve as etapas que você precisa seguir para começar a usar o modelo de detecção de caracteres animados. 

Uma vez que, nas contas de avaliação, a integração de Visão Personalizada é gerenciada pelo Video Indexer, você pode começar a criar e usar o modelo de caracteres animados e ignorar a seção a seguir ("conectar sua conta de Visão Personalizada").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Conectar sua conta do Visão Personalizada (somente contas pagas)

Se você possui uma conta paga Video Indexer, você precisa conectar uma conta de Visão Personalizada primeiro. Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [visão personalizada](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Ambas as contas precisam estar na mesma região. Atualmente, não há suporte para a integração do Visão Personalizada na região do Japão.

#### <a name="connect-a-custom-vision-account-with-api"></a>Conectar uma conta de Visão Personalizada com a API 

Siga estas etapas para se conectar Visão Personalizada conta para Video Indexer ou para alterar a conta de Visão Personalizada que está atualmente conectada ao Video Indexer:

1. Navegue até [www.customvision.ai](https://www.customvision.ai) e faça logon.
1. Copie as seguintes chaves: 

    * Chave de treinamento (para o recurso de treinamento)
    * Chave de previsão (para o recurso de previsão)
    * Ponto de extremidade 
    * ID do recurso de previsão
    
    > [!NOTE]
    > Para fornecer todas as chaves, você precisa ter dois recursos separados no Visão Personalizada, um para treinamento e outro para previsão.
1. Navegue e entre no [Video indexer](https://vi.microsoft.com/).
1. Clique no ponto de interrogação no canto superior direito da página e escolha referência de **API**.
1. Certifique-se de que você está inscrito no gerenciamento de API clicando na guia **produtos** . Se você tiver uma API conectada, poderá continuar na próxima etapa, caso contrário, assinar. 
1. No portal do desenvolvedor, clique na **referência de API completa** e navegue até **operações**.  
1. Selecione **conectar visão personalizada conta (versão prévia)** e clique em **experimentar**.
1. Preencha os campos obrigatórios, bem como o token de acesso e clique em **Enviar**. 

    Para obter mais informações sobre como obter o token de acesso de Video Indexer, acesse o [portal do desenvolvedor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)e veja a [documentação relevante](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Depois que a chamada retornar 200 OK, sua conta estará conectada.
1. Para verificar sua conexão, navegue até o portal do [Video indexer](https://vi.microsoft.com/)):
1. Clique no botão **personalização do modelo de conteúdo** no canto superior direito.
1. Vá para a guia **caracteres animados** .
1. Depois de clicar em gerenciar modelos no Visão Personalizada "* *, você será transferido para a conta de Visão Personalizada que você acabou de conectar.

> [!NOTE]
> Atualmente, somente os modelos criados por meio de Video Indexer têm suporte. Os modelos criados por meio do Visão Personalizada não estarão disponíveis. Além disso, a prática recomendada é editar modelos criados por meio de Video Indexer somente por meio da plataforma Video Indexer, já que as alterações feitas por meio de Visão Personalizada podem causar resultados não intencionais.

### <a name="create-an-animated-characters-model"></a>Criar um modelo de caracteres animados

1. Navegue até o site do [Video Indexer](https://vi.microsoft.com/) e entre.
1. Clique no botão personalização do modelo de conteúdo no canto superior direito da página.

    ![Captura de tela que mostra a página "Video Indexer" com o botão "personalização do modelo de conteúdo" selecionado no canto superior direito.](./media/animated-characters-recognition/content-model-customization.png)
1. Vá para a guia **caracteres animados** na seção personalização do modelo.
1. Clique em **Adicionar modelo**.
1. Nomeie-o como modelo e clique em Enter para salvar o nome.

> [!NOTE]
> A prática recomendada é ter um modelo de visão personalizada para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexar um vídeo com um modelo animado

1. Clique no botão **carregar** no menu superior.
1. Escolha um vídeo para carregar (de um arquivo ou de uma URL).
1. Clique em **Opções avançadas**.
1. Em **pessoas/caracteres animados** , escolha **modelos de animação**.
1. Se você tiver um modelo, ele será escolhido automaticamente e, se você tiver vários modelos, poderá escolher o que é relevante no menu suspenso.
1. Clique em carregar.
1. Depois que o vídeo for indexado, você verá os caracteres detectados na seção **caracteres animados** no painel **insights** .

> [!NOTE] 
> Antes de marcar e treinar o modelo, todos os caracteres animados serão nomeados "desconhecido #X". Depois de treinar o modelo, eles também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalizar os modelos de caracteres animados

1. Marque e treine o modelo.

    1. Marcar o caractere detectado editando seu nome. Depois que um caractere é treinado no modelo, ele será reconhecido no próximo vídeo indexado com esse modelo. 
    1. Para marcar um caractere animado em seu vídeo, vá para a guia **insights** e clique no botão **Editar** no canto superior direito da janela.
    1. No painel **insights** , clique em qualquer um dos caracteres animados detectados e altere seus nomes de "desconhecido #X" (ou o nome que foi atribuído anteriormente ao caractere).
    1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salva o novo nome no modelo em Video Indexer.
    1. Depois de concluir a edição de todos os nomes desejados, você precisará treinar o modelo.

        Abra a página personalização e clique na guia **caracteres animados** e, em seguida, clique no botão **treinar** para treinar seu modelo.
         
        Se você tiver uma conta paga, poderá clicar no link **gerenciar modelos no Customer Vision** (como mostrado abaixo). Em seguida, você será encaminhado para a página do modelo em **visão personalizada**.
 
        ![Personalização do modelo de conteúdo](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Uma vez treinado, qualquer vídeo que será indexado ou reindexado com esse modelo reconhecerá os caracteres treinados. 
    Contas pagas que têm acesso à sua conta de Visão Personalizada podem ver os modelos e imagens marcadas ali. Saiba mais sobre como [melhorar seu classificador em visão personalizada](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

1. Excluir um caractere animado.

    1. Para excluir um caractere animado em suas ideias de vídeo, vá para a guia **insights** e clique no botão **Editar** no canto superior direito da janela.
    1. Escolha o caractere animado e, em seguida, clique no botão **excluir** em seu nome.

    > [!NOTE]
    > Isso excluirá o insight deste vídeo, mas não afetará o modelo.

1. Excluir um modelo.

    1. Clique no botão **personalização do modelo de conteúdo** no menu superior e vá para a guia **caracteres animados** .
    1. Clique no ícone de reticências à direita do modelo que você deseja excluir e, em seguida, no botão excluir.
    
    * Conta paga: o modelo será desconectado da Video Indexer e você não poderá reconectá-lo.
    * Conta de avaliação: o modelo também será excluído da visão alfandegária. 
    
        > [!NOTE]
        > Em uma conta de avaliação, você tem apenas um modelo que pode usar. Depois de excluí-lo, você não pode treinar outros modelos.

## <a name="use-the-animated-character-detection-with-api"></a>Usar a detecção de caracteres animados com a API 

1. Conecte uma conta de Visão Personalizada.

    Se você possui uma conta paga Video Indexer, você precisa conectar uma conta de Visão Personalizada primeiro. <br/>
    Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [visão personalizada](../../cognitive-services/custom-vision-service/overview.md).

    [Conecte sua conta do visão personalizada usando a API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Crie um modelo de caracteres animados.

    Use a API [criar modelo de animação](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indexar ou reindexar um vídeo.

    Use a API de [reindexação](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Personalize os modelos de caracteres animados.

    Use a API de [modelo de animação Train](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Exibir a saída

Consulte os caracteres animados no arquivo JSON gerado.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Limitações

* Atualmente, não há suporte para a funcionalidade de "identificação de animação" na região East-Asia.
* Os caracteres que parecem ser pequenos ou distantes no vídeo podem não ser identificados corretamente se a qualidade do vídeo for ruim.
* A recomendação é usar um modelo por conjunto de caracteres animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
