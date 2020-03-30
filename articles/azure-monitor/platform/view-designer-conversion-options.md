---
title: Azure Monitor visualiza designer para opções de conversão de livros de trabalho
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658703"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor visualiza designer para opções de conversão de livros de trabalho
[O View designer](view-designer.md) é um recurso do Azure Monitor que permite criar visualizações personalizadas para ajudá-lo a visualizar dados em seu espaço de trabalho do Log Analytics, com gráficos, listas e cronogramas. Eles estão sendo eliminados e substituídos por livros de trabalho que fornecem funcionalidade adicional. Este artigo compara conceitos fundamentais entre os dois e opções para converter pontos de vista em livros de trabalho.

## <a name="basic-workbook-designs"></a>Designs básicos de livros de trabalho

O designer de visualização tem um estilo fixo de representação estática, enquanto as agencias permitem a liberdade de incluir e modificar a forma como os dados são representados. As imagens abaixo mostram dois exemplos de como você pode organizar livros de trabalho ao converter visualizações.

[Carteira de trabalho](view-designer-conversion-examples.md#vertical)
![vertical](media/view-designer-conversion-options/view-designer-vertical.png)

[Guia de distribuição](view-designer-conversion-examples.md#tabbed)
![do](media/view-designer-conversion-options/distribution-tab.png)
![tipo de trabalho Guia do tipo de trabalho Tipos de dados sobre a guia de tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversão de ladrilhos
O designer de visualização usa o recurso de telha de visão geral para representar e resumir o estado geral. Estes são representados em sete telhas, variando de números a gráficos. Nos livros de trabalho, os usuários podem criar visualizações semelhantes e fixá-las para se assemelhar ao estilo original de telhas de visão geral. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Ver conversão do painel
Os ladrilhos de designer normalmente consistem em duas seções, uma visualização e uma lista que correspondem aos dados da visualização, por exemplo, o bloco **Donut & List.**

![Donut](media/view-designer-conversion-options/donut-example.png)

Com as cadernetas, permitimos que o usuário opte por consultar uma ou ambas as seções da exibição. Formular consultas em pastas de trabalho é um processo simples de duas etapas. Primeiro, os dados são gerados a partir da consulta e, em segundo lugar, os dados são renderizados como visualização.  Um exemplo de como essa exibição seria recriada em livros de trabalho é a seguinte:

![Converter](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Próximas etapas
- [Acessando as permissões & de trabalho](view-designer-conversion-access.md)