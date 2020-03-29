---
title: Use avaliações de conteúdo através da ferramenta Revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Saiba como a ferramenta de revisão permite que moderadores humanos revisem imagens em um portal da Web.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044125"
---
# <a name="create-human-reviews"></a>Criar avaliações humanas

Neste guia, você aprenderá como configurar [avaliações](../review-api.md#reviews) no site da ferramenta Revisão. Avaliações armazenam e exibem conteúdo para moderadores humanos avaliarem. Os moderadores podem alterar as tags aplicadas e aplicar suas próprias tags personalizadas conforme apropriado. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto final de retorno de chamada especificado e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Faça login ou crie uma conta no site da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderador de Conteúdo.

## <a name="image-reviews"></a>Análises de imagem

1. Vá para a [ferramenta Revisar,](https://contentmoderator.cognitive.microsoft.com/)selecione a guia **Tentar** e faça upload de algumas imagens para revisar.
1. Depois que as imagens enviadas tiverem concluído o processamento, vá para a guia **'Revisar'** e selecione **Imagem**.

    ![Navegador Chrome que mostra a ferramenta de análise com a opção de examinar imagem realçada](images/review-images-1.png)

    As imagens são exibidas com quaisquer etiquetas que tenham sido atribuídas pelo processo automático de moderação. As imagens enviadas pela ferramenta Revisão não são visíveis para outros revisores.

1. Opcionalmente, mova as **Revisões para exibir** o controle deslizante (1) para ajustar o número de imagens exibidas na tela. Clique nos botões **marcados** ou **não marcados** (2) para classificar as imagens de acordo. Clique em um painel de marcação (3) para alternar ou desatilar.

    ![Navegador Chrome mostrando a ferramenta Revisar com imagens marcadas para revisão](images/review-images-2.png)

1. Para ver mais informações sobre uma imagem, clique na elipse na miniatura e selecione **Exibir detalhes**. Você pode atribuir uma imagem a uma subequipe com a opção **Mover para** (consulte a seção [equipes](./configure.md#manage-team-and-subteams) para saber mais sobre subtimes).

    ![Uma imagem com a opção de exibição de detalhes realçada](images/review-images-3.png)

1. Procure as informações de moderação de imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados em um painel separado](images/review-images-4.png)

1. Depois de ter revisado e atualizado as atribuições de marca, conforme necessário, clique em **Próximo** para enviar suas revisões. Depois de enviar, você tem cerca de cinco segundos para clicar no botão **Prev** para retornar à tela anterior e examinar imagens novamente. Depois disso, as imagens não estarão mais na fila de envio e o botão **Prev** não estará mais disponível.

## <a name="text-reviews"></a>Análises de texto

As revisões de texto funcionam de forma semelhante às revisões de imagem. Em vez de carregar conteúdo, basta escrever ou colar em texto (até 1.024 caracteres). Em seguida, o Moderador de Conteúdo analisa o texto e aplica tags (além de outras informações de moderação, como palavrões e dados pessoais). Nas revisões de texto, você pode alternar as tags aplicadas e/ou aplicar tags personalizadas antes de enviar a revisão.

![Captura de tela da ferramenta de revisão mostrando o texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como configurar e usar avaliações da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com)de Moderador de Conteúdo . Em seguida, consulte o [guia rest API](../try-review-api-review.md) ou o [.NET SDK quickstart](../dotnet-sdk-quickstart.md) para aprender como criar avaliações programáticamente.