---
title: Apresentar um Jupyter Notebook como uma apresentação de slides no Azure
description: Como configurar as células para o modo de apresentação de slides em um notebook Jupyter e, em seguida, apresentar a apresentação de slides usando a extensão RISE.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 04612dc8892a544397251580b3494badc84e2ad5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973107"
---
# <a name="run-a-notebook-slideshow"></a>Executar uma apresentação de slides de notebook

O Azure Notebooks é pré-configurado com a Extensão de Slides de Jupyter/IPython (RISE) que permite que você apresente um notebook diretamente como apresentação de slides. Em uma apresentação de slides, as células são normalmente exibidas uma por vez usando um tamanho de fonte que é apropriado para apresentar em telas grandes e você ainda pode executar o código em vez de alternar para um computador separado de demonstração.

A imagem a seguir mostra a exibição de notebook padrão, no qual você pode ver tudo isso Markdown e células de código:

![Um notebook no modo de exibição padrão](media/slideshow/slideshow-notebook-view.png)

Quando você inicia uma apresentação de slides, a primeira célula é ampliada para preencher o navegador, onde o **X** na parte superior esquerda encerra a apresentação de slides, **?** na parte inferior esquerda exibe atalhos do teclado e as setas no canto inferior direito navegam entre os slides:

![Um notebook no modo de apresentação de slides](media/slideshow/slideshow-slide-view.png)

Preparar um notebook para uma apresentação de slides envolve duas atividades principais:

1. Como as células de Markdown são processadas com fontes grandes, algum conteúdo pode não estar visível na apresentação de slides. Você, portanto, normalmente limita a quantidade de texto em uma determinada célula; um cabeçalho com quatro a seis linhas geralmente funciona melhor. Se você tiver mais texto, divida essas informações em várias células.

2. Configure o comportamento de cada célula na apresentação de slides usando a barra de ferramentas da célula de apresentação de slides. Tipos de célula determinam o comportamento dos botões de navegação.

## <a name="the-anatomy-of-a-slideshow"></a>A anatomia de uma apresentação de slides

Se você pegar um notebook aleatório e usá-lo para uma apresentação de slides, normalmente descobrirá que todas as células estão confusas e que grande parte do conteúdo está oculta na parte inferior da janela do navegador. Para fazer uma apresentação eficiente, você precisará atribuir um tipo de apresentação de slides para cada célula usando a barra de ferramentas da célula de apresentação de slides:

1. No menu **Exibição**, selecione **Barra de ferramentas de célula** > **Apresentação de slides**:

    ![Ativar a barra de ferramentas de apresentação de slides da célula](media/slideshow/slideshow-view-cell-toolbar.png)

1. Um **tipo de Slide** suspenso aparece na parte superior direita de cada célula no Notebook:

    ![Barra de ferramentas de apresentação de slides de célula](media/slideshow/slideshow-cell-toolbar.png)

1. Para cada célula, selecione um dos cinco tipos:

    ![Tipos apresentação de slides de célula](media/slideshow/slideshow-cell-slide-types.png)

    | Tipo de slide | Comportamento |
    | --- | --- |
    | - (não definido) | A célula é exibida com a célula anterior, que geralmente não é um efeito desejado em uma apresentação de slides. |
    | Slide | A célula é um slide principal, navegada usando as setas à esquerda e direita do controle de navegação. |
    | Subslide | A célula esta “abaixo” do slide principal, navegado usando a seta para baixo do controle de navegação. A seta para cima retorna para o slide principal. Subslides são usados para o material secundário que você poderia ignorar no caminho principal de uma apresentação, mas está prontamente disponível, se necessário. |
    | Fragmento | Conteúdo da célula aparece no contexto do slide anterior ou subslide ao usar a seta para baixo de navegação (um fragmento é removido ao usar a seta para cima). Você pode usar um fragmento com uma célula de código para fazer com que esse código apareça dentro de um slide, ou você pode usar vários fragmentos para fazer os marcadores de texto aparecerem um por um (veja o exemplo na próxima seção). Uma vez que os fragmentos são construídos no slide atual, os fragmentos em excesso não ficarão visíveis na parte inferior da janela do navegador. |
    | Pular | A célula não é mostrada na apresentação de slides. |
    | Observações | A célula contém anotações do falante, que não são mostradas na apresentação de slides. |

1. Inicialmente, é útil escolher **Slide** para cada célula. Você pode executar a apresentação de slides e fazer os ajustes apropriados.

### <a name="example-fragment-cells-for-bullet-items"></a>Exemplo: células de fragmento para itens com marcadores

Para exibir marcadores em um slide um por um, coloque o cabeçalho de slides em uma célula de Markdown com o tipo de **Slide**, em seguida, coloque cada marcador em uma célula separada de Markdown com o tipo de **fragmento**:

![Exemplo de criação de várias células de Markdown para itens com marcadores](media/slideshow/slideshow-fragments.png)

Uma vez que a apresentação de slides processa fragmentos com espaçamento mais vertical que quando todos os marcadores estão na mesma célula, você não poderá usar tantos itens com marcadores.

## <a name="run-the-slideshow"></a>Executar a apresentação de slides

1. Se você tiver editado todas as células de Markdown, certifique-se de executá-las para renderizar HTML, caso contrário, eles aparecem *como* Markdown na apresentação de slides.

1. Depois de configurar o **Tipo de Slide** para cada célula, selecione a célula com o qual iniciar a apresentação de slides e, em seguida, selecione a **Apresentação de Slides RISE Entrar/Sair** na barra de ferramentas principal:

    ![Botão de apresentação de slides RISE Entrar/Sair na barra de ferramentas principal](media/slideshow/slideshow-start.png)

1. Para navegar entre os slides, bem como fragmentos, use as setas à esquerda e à direita no controle de navegação. O texto no controle mostra um número que representa *slide.sub slide*.

    ![Controle de navegação de apresentação de slides](media/slideshow/slideshow-navigation-control.png)

1. Para navegar entre slides e subslides, bem como fragmentos, use as setas e para baixo, se habilitado:

    ![Controles de navegação para apresentação de slides para subslides](media/slideshow/slideshow-navigation-control-subslide.png)

1. Em uma célula de código, use o botão Reproduzir para executar o código; a saída é exibida no slide:

    ![Botão Reproduzir para executar uma célula de código](media/slideshow/slideshow-run-code-cell.png)

    ![A saída de célula de código aparece na apresentação de slides](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > A saída de célula é considerada parte da célula em uma apresentação de slides. Se você executar uma célula no bloco de anotações ou modo de exibição de apresentação de slides, a saída é exibida em outra exibição. Para limpar a saída, use a **Célula** > **Saídas Atuais** > **comando** Limpar (para a célula atual) ou **Célula**  >  **Todas as saídas** > **Limpar** (para todas as células).

1. Quando tiver terminado a apresentação de slides, use o **X** para retornar à exibição de bloco de anotações.

## <a name="next-steps"></a>Próximas etapas

- [Como: Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes em um notebook](install-packages-jupyter-notebook.md)
- [Como: Trabalhar com arquivo de dados](work-with-project-data-files.md)
- [Como: Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
