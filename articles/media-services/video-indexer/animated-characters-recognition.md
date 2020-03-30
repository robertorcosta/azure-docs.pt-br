---
title: Detecção de caracteres animados com indexador de vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a detecção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989902"
---
# <a name="animated-character-detection-preview"></a>Detecção de caracteres animados (visualização)

O Azure Media Services Video Indexer suporta detecção, agrupamento e reconhecimento de personagens em conteúdo animado por meio da integração com [a visão personalizada dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Essa funcionalidade está disponível tanto através do portal quanto através da API.

Depois de carregar um vídeo animado com um modelo de animação específico, o Video Indexer extrai quadros-chave, detecta caracteres animados nesses quadros, agrupa caracteres semelhantes e escolhe a melhor amostra. Em seguida, ele envia os caracteres agrupados para a Visão Personalizada que identifica personagens com base nos modelos em que foi treinado. 

Antes de começar a treinar seu modelo, os caracteres são detectados sem nome. À medida que você adiciona nomes e treina o modelo, o Indexador de Vídeo reconhecerá os caracteres e os nomeará de acordo.

## <a name="flow-diagram"></a>Fluxograma

O diagrama a seguir demonstra o fluxo do processo de detecção de caracteres animados.

![Fluxograma](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo de sua conta do Video Indexer, diferentes conjuntos de recursos estão disponíveis. Para obter informações sobre como conectar sua conta ao Azure, consulte [Criar uma conta de indexador de vídeo conectada ao Azure](connect-to-azure.md).

* Conta de teste: O Video Indexer usa uma conta interna da Visão Personalizada para criar modelo e conectá-lo à sua conta do Indexador de vídeo. 
* Conta paga: você conecta sua conta do Personal Vision à sua conta do Video Indexer (se você ainda não tiver uma, você precisa criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Julgamento vs. pago

|Funcionalidade|Avaliação|Pago|
|---|---|---|
|Conta visão personalizada|Gerenciado nos bastidores pelo Video Indexer. |Sua conta visão personalizada está conectada ao Video Indexer.|
|Número de modelos de animação|Um|Até 100 modelos por conta (limitação da Visão Personalizada).|
|Treinamento do modelo|O Video Indexer treina o modelo para novos personagens, exemplos adicionais de caracteres existentes.|O dono da conta treina o modelo quando está pronto para fazer mudanças.|
|Opções avançadas em Visão Personalizada|Sem acesso ao portal Visão Personalizada.|Você pode ajustar os modelos você mesmo no portal Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal"></a>Use a detecção de caracteres animados com portal 

Esta seção descreve os passos que você precisa tomar para começar a usar o modelo de detecção de caracteres animados. 

Como nas contas de teste a integração Visão Personalizada é gerenciada pelo Video Indexer, você pode começar a criar e usar o modelo de caracteres animados e pular a seção a seguir ("Conecte sua conta de Visão Personalizada").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Conecte sua conta de Visão Personalizada (somente contas pagas)

Se você possui uma conta paga do Video Indexer, você precisa conectar uma conta do Custom Vision primeiro. Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [Visão Personalizada](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Ambas as contas precisam estar na mesma região. A integração visão personalizada não é suportada atualmente na região do Japão.

#### <a name="connect-a-custom-vision-account-with-api"></a>Conecte uma conta de Visão Personalizada com API 

Siga estas etapas para conectar sua conta visão personalizada ao Indexador de vídeo, ou para alterar a conta visão personalizada que está atualmente conectada ao Indexador de vídeo:

1. Navegue até [www.customvision.ai](https://www.customvision.ai) e faça login.
1. Copie as seguintes teclas: 

    * Chave de treinamento (para o recurso de treinamento)
    * Chave de previsão (para o recurso de previsão)
    * Ponto de extremidade 
    * ID de recurso de predição
    
    > [!NOTE]
    > Para fornecer todas as chaves você precisa ter dois recursos separados na Visão Personalizada, um para treinamento e outro para previsão.
1. Navegue e faça login no [Indexador de Vídeo](https://vi.microsoft.com/).
1. Clique no ponto de interrogação no canto superior direito da página e escolha **API Reference**.
1. Certifique-se de que está inscrito no Gerenciamento de API clicando na guia **Produtos.** Se você tiver uma API conectada, você pode continuar para o próximo passo, caso contrário, inscreva-se. 
1. No portal do desenvolvedor, clique na Referência completa da **API** e navegue até **Operações**.  
1. Selecione **Conectar a Conta de Visão Personalizada (PREVIEW)** e clique em **Experimentá-la**.
1. Preencha os campos necessários, bem como o token de acesso e clique em **Enviar**. 

    Para obter mais informações sobre como obter o token de acesso do Indexador de vídeo, acesse o portal do [desenvolvedor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)e consulte a [documentação relevante.](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)  
1. Uma vez que a resposta de retorno de chamada 200 OK, sua conta está conectada.
1. Para verificar sua conexão, navegue pelo portal [Doindexador de](https://vi.microsoft.com/)Vídeo:
1. Clique no botão **de personalização** do modelo Conteúdo no canto superior direito.
1. Vá para a guia **Caracteres Animados.**
1. Uma vez que você clique em Gerenciar modelos em Visão Personalizada"**, você será transferido para a conta Visão Personalizada que você acabou de conectar.

> [!NOTE]
> Atualmente, apenas modelos que foram criados via Video Indexer são suportados. Os modelos que são criados através do Custom Vision não estarão disponíveis. Além disso, a melhor prática é editar modelos que foram criados através do Video Indexer apenas através da plataforma Video Indexer, uma vez que as alterações feitas através da Visão Personalizada podem causar resultados não intencionais.

### <a name="create-an-animated-characters-model"></a>Crie um modelo de personagens animados

1. Navegue até o site do [Video Indexer](https://vi.microsoft.com/) e entre.
1. Clique no botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização de modelo de conteúdo](./media/animated-characters-recognition/content-model-customization.png)
1. Vá para a guia **Caracteres animados** na seção de personalização do modelo.
1. Clique em **Adicionar modelo**.
1. Nomeie seu modelo e clique em inserir para salvar o nome.

> [!NOTE]
> A melhor prática é ter um modelo de visão personalizado para cada série animada. 

### <a name="index-a-video-with-an-animated-model"></a>Indexe um vídeo com um modelo animado

1. Clique no botão **Carregar** no menu superior.
1. Escolha um vídeo para carregar (de um arquivo ou de uma URL).
1. Clique em **opções avançadas**.
1. Em **Pessoas / Personagens animados** escolhem **modelos de animação**.
1. Se você tiver um modelo, ele será escolhido automaticamente, e se você tiver vários modelos, você pode escolher o relevante fora do menu suspenso.
1. Clique em upload.
1. Uma vez que o vídeo é indexado, você verá os caracteres detectados na seção **Caracteres Animados** no painel **Insights.**

> [!NOTE] 
> Antes de marcar e treinar o modelo, todos os personagens animados serão chamados de "Desconhecido #X". Depois de treinar o modelo, eles também serão reconhecidos.

### <a name="customize-the-animated-characters-models"></a>Personalize os modelos de personagens animados

1. Marque e treine o modelo.

    1. Marque o caractere detectado editando seu nome. Uma vez que um personagem é treinado para o modelo, ele será reconhecido o próximo vídeo indexado com esse modelo. 
    1. Para marcar um caractere animado no vídeo, vá para a guia **'''Editar',** no canto superior direito da janela. **Edit**
    1. No painel **Insights,** clique em qualquer um dos personagens animados detectados e altere seus nomes de "#X Desconhecido" (ou o nome que foi anteriormente atribuído ao personagem).
    1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salva o novo nome no modelo em Video Indexer.
    1. Depois de terminar de editar todos os nomes que quiser, você precisa treinar o modelo.

        Abra a página de personalização e clique na guia **Caracteres Animados** e clique no botão **Trem** para treinar seu modelo.
         
        Se você tiver uma conta paga, você pode clicar nos **modelos Gerenciar no** link Visão do Cliente (conforme mostrado abaixo). Em seguida, você será encaminhado para a página do modelo em **Visão Personalizada**.
 
        ![Personalização de modelo de conteúdo](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Uma vez treinado, qualquer vídeo que será indexado ou reindexado com esse modelo reconhecerá os caracteres treinados. 
    Contas pagas que têm acesso à sua conta do Custom Vision podem ver os modelos e imagens marcadas lá. Saiba mais sobre [como melhorar seu classificador em Visão Personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Exclua um personagem animado.

    1. Para excluir um caractere animado em seus insights de vídeo, vá para a guia **Insights** e clique no botão **Editar** no canto superior direito da janela.
    1. Escolha o caractere animado e clique no botão **Excluir** em seu nome.

    > [!NOTE]
    > Isso excluirá o insight deste vídeo, mas não afetará o modelo.

1. Exclua um modelo.

    1. Clique no botão **de personalização** do modelo de conteúdo no menu superior e vá para a guia **Caracteres Animados.**
    1. Clique no ícone ellipsis à direita do modelo que deseja excluir e, em seguida, no botão excluir.
    
    * Conta paga: o modelo será desconectado do Video Indexer e você não poderá reconectá-lo.
    * Conta de avaliação: o modelo também será excluído da visão aduaneira. 
    
        > [!NOTE]
        > Em uma conta de teste, você só tem um modelo que você pode usar. Depois de excluí-lo, você não pode treinar outros modelos.

## <a name="use-the-animated-character-detection-with-api"></a>Use a detecção de caracteres animados com API 

1. Conecte uma conta de Visão Personalizada.

    Se você possui uma conta paga do Video Indexer, você precisa conectar uma conta do Custom Vision primeiro. <br/>
    Se você ainda não tem uma conta de Visão Personalizada, crie uma. Para obter mais informações, consulte [Visão Personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Conecte sua conta de Visão Personalizada usando API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Crie um modelo de personagens animados.

    Use a API [do modelo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) de animação criar.
1. Indexe ou reindexe um vídeo.

    Use a API [de reindexação.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Personalize os modelos de personagens animados.

    Use a API modelo de animação do [trem.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

### <a name="view-the-output"></a>Exibir a saída

Veja os personagens animados no arquivo JSON gerado.

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

* Atualmente, o recurso de "identificação de animação" não é suportado na região leste-asiática.
* Personagens que parecem ser pequenos ou distantes no vídeo podem não ser identificados adequadamente se a qualidade do vídeo for ruim.
* A recomendação é usar um modelo por conjunto de personagens animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
