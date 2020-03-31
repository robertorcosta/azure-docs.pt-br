---
title: Use recursos de acessibilidade no designer (visualização)
titleSuffix: Azure Machine Learning
description: Conheça os atalhos de teclado e os recursos de acessibilidade do leitor de tela disponíveis no designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366204"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Use um teclado para usar o azure Machine Learning designer (visualização)

Aprenda a usar um leitor de teclado e tela para usar o designer azure Machine Learning. Para obter uma lista de atalhos de teclado que funcionam em todos os lugares no portal Azure, consulte [atalhos de teclado no portal Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este fluxo de trabalho foi testado com [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [JAWS,](https://www.freedomscientific.com/products/software/jaws/)mas deve funcionar com outros leitores de tela padrão.

## <a name="navigate-the-pipeline-graph"></a>Navegue pelo gráfico do pipeline

O gráfico do pipeline é organizado como uma lista aninhada. A lista externa é uma lista de módulos, que descreve todos os módulos do gráfico de pipeline. A lista interna é uma lista de conexões, que descreve todas as conexões de um módulo específico.  

1. Na lista de módulos, use a tecla de seta para alternar módulos.
1. Use a guia para abrir a lista de conexões para o módulo de destino.
1. Use a tecla de seta para alternar entre as portas de conexão para o módulo.
1. Use "G" para ir ao módulo de destino.

## <a name="edit-the-pipeline-graph"></a>Editar o gráfico do pipeline

### <a name="add-a-module-to-the-graph"></a>Adicione um módulo ao gráfico

1. Use Ctrl+F6 para mudar o foco da tela para a árvore do módulo.
1. Encontre o módulo desejado na árvore do módulo usando o controle padrão de visão de árvore.

### <a name="edit-a-module"></a>Editar um módulo

Para conectar um módulo a outro módulo:

1. Use Ctrl + Shift + H ao direcionar um módulo na lista de módulos para abrir o ajudante de conexão.
1. Edite as portas de conexão para o módulo.

Para ajustar as propriedades do módulo:

1. Use Ctrl + Shift + E ao direcionar um módulo para abrir as propriedades do módulo.
1. Edite as propriedades do módulo.

## <a name="navigation-shortcuts"></a>Atalhos de navegação

| Pressionamento de tecla | Descrição |
|-|-|
| Ctrl + F6 | Alternar o foco entre tela e árvore de módulo |
| Ctrl + F1   | Abra o cartão de informações ao focar em um nó na árvore do módulo |
| Ctrl + Shift + H | Abra o ajudante de conexão quando o foco estiver em um nó |
| Ctrl + Shift + E | Abra as propriedades do módulo quando o foco está em um nó |
| Ctrl + G | Mova o foco para o primeiro nó com falha se a execução do gasoduto falhar |

## <a name="action-shortcuts"></a>Atalhos de ação

Use os seguintes atalhos com a chave de acesso. Para obter mais informações https://en.wikipedia.org/wiki/Access_keysobre as chaves de acesso, consulte .

| Pressionamento de tecla | Ação |
|-|-|
| Chave de acesso + R | Executar |
| Chave de acesso + P | Publicar |
| Chave de acesso + C | Clone |
| Chave de acesso + D | Implantar |
| Tecla de acesso + I | Criar/atualizar o pipeline de inferência |
| Tecla de acesso + B | Criar/atualizar o pipeline de inferência em lote |
| Chave de acesso + K | Abertura da queda do "Criar pipeline de inferência" |
| Chave de acesso + U | Abertura do pipeline de inferência "Atualizar inferência" |
| Chave de acesso + M | Abra mais(...) dropdown |

## <a name="next-steps"></a>Próximas etapas

- [Ativar alto contraste ou alterar tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Ferramentas relacionadas à acessibilidade na Microsoft](https://www.microsoft.com/accessibility)
