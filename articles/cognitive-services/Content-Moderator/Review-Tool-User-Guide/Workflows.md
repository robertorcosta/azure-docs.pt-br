---
title: Defina e use fluxos de trabalho de conteúdo através da ferramenta Revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Você pode usar o designer de fluxo de trabalho Azure Content Moderator para definir fluxos de trabalho e limiares personalizados com base em suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754248"
---
# <a name="define-and-use-moderation-workflows"></a>Defina e use fluxos de trabalho de moderação

Neste guia, você aprenderá como configurar e usar [fluxos de trabalho](../review-api.md#workflows) no site da [ferramenta Revisão.](https://contentmoderator.cognitive.microsoft.com) Os fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas apropriadas. Este guia mostra como usar o conector Moderador de Conteúdo (que está incluído por padrão) para filtrar conteúdo e configurar avaliações humanas em um cenário típico de moderação.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Acesse a [ferramenta Revisão de Moderador de Conteúdo](https://contentmoderator.cognitive.microsoft.com/) e faça login. Na guia **Configurações**, selecione **Fluxos de trabalho**.

![Configuração de fluxos de trabalho](images/2-workflows-0.png)

Na próxima tela, selecione **Adicionar fluxo de trabalho**.

![Adicionar um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir nome e descrição

Nomeie seu fluxo de trabalho, digite uma descrição e escolha se o fluxo de trabalho lidará com imagens ou texto.

![Nome e descrição do fluxo de trabalho](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

Na próxima tela, vá para a seção **If.** No menu suspenso superior, escolha **Condição**. Isso permitirá que você configure a condição na qual o fluxo de trabalho agirá. Se você quiser usar várias condições, escolha **Combinação** em vez disso. 

Em seguida, selecione um conector. Este exemplo usa **Content Moderator**. Dependendo do conector escolhido, você terá diferentes opções para saída de dados. Consulte a seção [Conectores](./configure.md#connectors) do guia De configurações da ferramenta Revisão para saber como configurar outros conectores.

![Selecione o conector do fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada para usar e defina as condições para verificar.

![Definir condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Defina a ação

Vá para a seção **Then,** onde você seleciona uma ação. O exemplo a seguir cria uma revisão de imagem e atribui uma tag. Opcionalmente, você pode adicionar um caminho alternativo (Else) e definir uma ação para isso também.

![Definir ação do fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salve o fluxo de trabalho

Observe o nome do fluxo de trabalho; você precisa do nome para iniciar um trabalho de moderação com a API workflow (veja abaixo). Por fim, salve o fluxo de trabalho usando o botão **Salvar** na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que você definiu um fluxo de trabalho personalizado, teste-o com conteúdo de amostra. Vá para **Fluxos de Trabalho** e selecione o botão **Executar fluxo de trabalho** correspondente.

![Testar fluxo de trabalho](images/image-workflow-execute.PNG)

Salve esta [imagem de amostra](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) em sua unidade local. Em seguida, **selecione Escolher arquivo(s)** e carregue a imagem para o fluxo de trabalho.

![Um corredor com uma citação sobreposta à imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Você pode ver o progresso do fluxo de trabalho na próxima janela pop-up.

![Monitorar a execução do fluxo de trabalho](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verificar a ação do fluxo de trabalho

Vá para a guia **Imagem** em **Revisão** e verifique se há uma revisão de imagem recém-criada.

![Analisar imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como configurar e usar fluxos de trabalho de moderação a partir da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com)de Moderador de Conteúdo . Em seguida, consulte o [guia da API REST](../try-review-api-workflow.md) para saber como criar fluxos de trabalho de forma programática.
