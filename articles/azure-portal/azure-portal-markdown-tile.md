---
title: Usar um bloco markdown personalizado nos painéis do Azure
description: Saiba como adicionar um bloco markdown a um painel do Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310706"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usar um bloco markdown em painéis do Azure para mostrar conteúdo personalizado

É possível adicionar um bloco markdown a painéis do Azure para exibir conteúdo estático personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks em um azulejo de marcação.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco markdown ao seu painel

1. Selecione **Painel** na barra lateral do portal do Azure.

   ![Captura de tela mostrando barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se você criou painéis personalizados, na exibição de painel, use a lista suspensa para selecionar o painel em que o bloco markdown personalizado deve aparecer. Selecione o ícone de edição para abrir a **Galeria de Blocos**.

   ![Captura de tela mostrando o modo de exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Galeria de Azulejos,** localize o azulejo chamado **Markdown** e selecione **Adicionar**. O bloco é adicionado ao painel e a janela **Editar Markdown** é exibida.

1. Digite valores para **Título** e **Legenda**, que são exibidos no bloco depois de passar para outro campo.

   ![Captura de tela mostrando resultados de inserir título e legenda](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir conteúdo de marcação: **Edição inline** ou **inserir conteúdo usando URL**.

   - Selecione **edição inline** se quiser ativar a marcação diretamente.

      ![Captura de tela mostrando conteúdo inline](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecione **Inserir conteúdo usando URL** se quiser usar o conteúdo de marcação existente hospedado on-line.

      ![Captura de tela mostrando url de entrada](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para maior segurança, você pode criar um arquivo de marcação e armazená-lo em uma [bolha de conta de armazenamento do Azure onde](../storage/common/storage-service-encryption.md)a criptografia está ativada e, em seguida, apontar para o arquivo usando a opção URL. O conteúdo de marcação é criptografado através das opções de criptografia da conta de armazenamento. Apenas usuários com permissões para o arquivo podem ver o conteúdo de marcação no painel.

1. Selecione **Concluído** para ignorar o painel **Editar Markdown**. Seu conteúdo aparece no bloco Markdown, que você pode redimensionar arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco markdown personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funcionalidades e limitações de conteúdo do markdown

É possível usar qualquer combinação de texto sem formatação, sintaxe de Markdown e conteúdo HTML no bloco markdown. O portal do Azure usa uma biblioteca de software livre chamada _marked_ para transformar seu conteúdo no HTML que é mostrado no bloco. O HTML produzido por _marked_ é pré-processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afete a segurança ou o layout do portal. Durante esse pré-processamento, qualquer parte do HTML que represente uma ameaça em potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript: marcas `<script>` e avaliações embutidas de JavaScript serão removidas.
* iframes: marcas `<iframe>` serão removidas.
* Estilo: marcas `<style>` serão removidas. Oficialmente, não há suporte para atributos de estilo embutido em elementos HTML. Você pode achar que alguns elementos de estilo embutido são ideais para você, porém, se eles interferirem no layout do portal, poderão parar de funcionar a qualquer momento. O bloco Markdown destina-se a conteúdo básico e estático que usa os estilos padrão do portal.

## <a name="next-steps"></a>Próximas etapas

* Para criar um painel personalizado, confira [Criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
