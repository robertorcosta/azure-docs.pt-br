---
title: Usar um bloco markdown personalizado nos painéis do Azure
description: Saiba como adicionar um bloco markdown a um painel do Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: how-to
ms.custom: devx-track-js
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 5ca225f136b0c3a6fc8b47d00ff64064fe17d4ee
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281752"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usar um bloco markdown em painéis do Azure para mostrar conteúdo personalizado

É possível adicionar um bloco markdown a painéis do Azure para exibir conteúdo estático personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks em um bloco de redução.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco markdown ao seu painel

1. Selecione **Painel** na barra lateral do portal do Azure.

   ![Captura de tela mostrando a barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se você criou painéis personalizados, na exibição de painel, use a lista suspensa para selecionar o painel em que o bloco markdown personalizado deve aparecer. Selecione o ícone de edição para abrir a **Galeria de Blocos**.

   ![Captura de tela mostrando o modo de exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Galeria de blocos**, localize o bloco chamado **redução** e selecione **Adicionar**. O bloco é adicionado ao painel e a janela **Editar Markdown** é exibida.

1. Insira valores para **título** e **subtítulo**, que são exibidos no bloco depois de você passar para outro campo.

   ![Captura de tela mostrando os resultados da inserção do título e do subtítulo](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir o conteúdo de redução: **edição embutida** ou **Inserir conteúdo usando a URL**.

   - Selecione **edição embutida** se desejar inserir a redução diretamente.

      ![Captura de tela mostrando a inserção de conteúdo embutido](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecione **Inserir conteúdo usando a URL** se você quiser usar o conteúdo de redução existente que está hospedado online.

      ![Captura de tela mostrando a inserção da URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para maior segurança, você pode criar um arquivo de redução e armazená-lo em um [BLOB da conta de armazenamento do Azure em que a criptografia está habilitada](../storage/common/storage-service-encryption.md)e, em seguida, apontar para o arquivo usando a opção URL. O conteúdo de redução é criptografado por meio das opções de criptografia da conta de armazenamento. Somente os usuários com permissões para o arquivo podem ver o conteúdo de redução no painel. Defina uma regra de compartilhamento de recursos entre origens (CORS) na conta de armazenamento para que o portal do Azure ( _https://portal.azure.com/_ ) possa acessar o arquivo de redução no BLOB.

1. Selecione **Concluído** para ignorar o painel **Editar Markdown**. Seu conteúdo aparece no bloco de redução, que você pode redimensionar arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco markdown personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funcionalidades e limitações de conteúdo do markdown

É possível usar qualquer combinação de texto sem formatação, sintaxe de Markdown e conteúdo HTML no bloco markdown. O portal do Azure usa uma biblioteca de software livre chamada _marked_ para transformar seu conteúdo no HTML que é mostrado no bloco. O HTML produzido por _marked_ é pré-processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afete a segurança ou o layout do portal. Durante esse pré-processamento, qualquer parte do HTML que represente uma ameaça em potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript: marcas `<script>` e avaliações embutidas de JavaScript serão removidas.
* iframes: marcas `<iframe>` serão removidas.
* Estilo: marcas `<style>` serão removidas. Oficialmente, não há suporte para atributos de estilo embutido em elementos HTML. Você pode achar que alguns elementos de estilo embutido são ideais para você, porém, se eles interferirem no layout do portal, poderão parar de funcionar a qualquer momento. O bloco Markdown destina-se a conteúdo básico e estático que usa os estilos padrão do portal.

## <a name="next-steps"></a>Próximas etapas

* Para criar um painel personalizado, confira [Criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
