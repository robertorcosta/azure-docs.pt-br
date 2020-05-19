---
title: Como gerenciar configurações? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Como gerenciar configurações, criar workspace, compartilhar workspace e gerenciar a chave de assinatura no Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: fe2c0eed2d476d01dffd22a61dd431140f26c475
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587215"
---
# <a name="how-to-manage-settings"></a>Como gerenciar configurações

Na página de configurações do Custom Translator, você pode criar um novo workspace, compartilhar seu workspace e adicionar ou modificar sua chave de assinatura do Microsoft Translation.

Para acessar a página de configurações:

1. Entrar para o [conversor personalizado](https://portal.customtranslator.azure.ai/) portal.
2. No portal do Custom Translator, clique no ícone de engrenagem na barra lateral.

    ![Configurando Link](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associando a assinatura do Microsoft Translator

Você precisa ter uma chave de assinatura do tradutor associada ao seu espaço de trabalho para treinar ou implantar modelos.

Se você não tem uma assinatura, siga as etapas abaixo:

1. Assine o tradutor. Este artigo mostra como assinar o tradutor.
2. Anote a chave da sua assinatura de tradutor. Qualquer uma das Key1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal do Custom Translator.

### <a name="add-existing-key"></a>Adicionar chave existente

1.  Navegue até a página &quot;Configurações&quot; do seu workspace.
2.  Clique em Adicionar chave

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key.png)

3. Na caixa de diálogo, insira a chave da assinatura do seu tradutor e clique no botão "Adicionar".

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Depois de adicionar uma chave, você pode modificar ou excluir a chave a qualquer momento.

    ![Chave de assinatura depois de adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerenciar seu workspace

Um workspace é uma área de trabalho para compor e construir o sistema de tradução personalizado. Um workspace pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisar ser compartilhada com pessoas diferentes, a criação de vários workspaces poderá ser útil.

## <a name="create-a-new-workspace"></a>Criar um novo workspace

1.  Navegue até a página &quot;Configurações&quot; do workspace.
2.  Clique no botão “Novo Workspace” na seção “Criar Novo Workspace”.

    ![Criar novo workspace](media/how-to/create-new-workspace.png)

4.  Na caixa de diálogo, insira o nome do novo workspace.
5.  Clique em "Criar".

    ![Criar caixa de diálogo do novo workspace](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Compartilhe seu workspace

No tradutor Personalizado você pode compartilhar seu workspace com outras pessoas, se diferentes partes do seu trabalho precisarem ser compartilhadas com pessoas diferentes.

1.  Navegue até a página &quot;Configurações&quot; do workspace.
2.  Clique no botão "Compartilhar" na seção "Configurações de compartilhamento".

    ![Compartilhar o workspace](media/how-to/share-workspace.png)

3.  Na caixa de diálogo, insira uma lista separada por vírgulas de endereços de e-mail com os quais você deseja compartilhar esse workspace. Não se esqueça de compartilhar com o endereço de e-mail que a pessoa usa para fazer login no Custom Translator com. Em seguida, selecione o nível apropriado de permissão de compartilhamento.

4.  Se o seu workspace ainda tiver o nome padrão &quot;Meu workspace&quot;, você deverá alterá-lo antes de compartilhar seu workspace.
5.  Clique em "Salvar".

## <a name="sharing-permissions"></a>Permissões de compartilhamento

1.  **Leitor:** Um leitor no workspace poderá visualizar todas as informações no workspace.

2.  **Editor:** Um editor no workspace poderá adicionar documentos, treinar modelos e excluir documentos e projetos. Eles podem adicionar uma chave de assinatura, mas não podem modificar com quem o workspace é compartilhado, excluir o workspace ou alterar o nome do workspace.

3.  **Proprietário:** um proprietário tem permissões totais para o workspace.

## <a name="change-sharing-permission"></a>Altere a permissão de compartilhamento

Quando um workspace é compartilhado, a seção “Configurações de compartilhamento” mostra todos os endereços de e-mail com os quais esse workspace é compartilhado. Você pode alterar a permissão de compartilhamento existente para cada endereço de e-mail se tiver acesso de proprietário ao workspace.

1.  Na seção "Configurações de compartilhamento" de cada e-mail, um menu suspenso mostra o nível de permissão atual.

2.  Clique no menu suspenso e selecione o novo nível de permissão que você deseja atribuir a esse endereço de e-mail.

    ![Configurações de permissão de compartilhamento](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Próximas etapas

- Aprenda [como migrar seu workspace e projeto](how-to-migrate.md) do [Microsoft Translator Hub](https://hub.microsofttranslator.com)
