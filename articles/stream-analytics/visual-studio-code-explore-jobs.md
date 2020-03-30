---
title: Explore os trabalhos do Azure Stream Analytics no Visual Studio Code
description: Este artigo mostra como exportar um trabalho do Azure Stream Analytics para um projeto local, listar empregos e visualizar entidades de trabalho.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479235"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explore o Azure Stream Analytics com o Visual Studio Code (Preview)

A extensão Azure Stream Analytics for Visual Studio Code oferece aos desenvolvedores uma experiência leve para gerenciar seus trabalhos no Stream Analytics. Pode ser usado no Windows, Mac e Linux. Com a extensão Azure Stream Analytics, você pode:

- [Criar,](quick-create-vs-code.md)iniciar e parar empregos
- Exportar empregos existentes para um projeto local
- Liste empregos e veja entidades de trabalho

## <a name="export-a-job-to-a-local-project"></a>Exportar um emprego para um projeto local

Para exportar um trabalho para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta selecionada, e você pode continuar a gerenciar o trabalho a partir do Visual Studio Code. Para obter mais informações sobre como usar o Visual Studio Code para gerenciar trabalhos do Stream Analytics, consulte o Visual Studio Code [quickstart](quick-create-vs-code.md).

![Exportar trabalho ASA em Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Listar o trabalho e ver entidades de trabalho

Você pode usar o modo de exibição de trabalho para interagir com trabalhos do Azure Stream Analytics do Microsoft Visual Studio.


1. Clique no ícone **Azure** na Barra de Atividade de Código do Estúdio Visual e, em seguida, expanda **o nó Stream Analytics**. Seus trabalhos devem aparecer suas assinaturas.

   ![Explorador de análise de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda seu nó de trabalho, você pode abrir e visualizar a consulta de trabalho, configuração, entradas, saídas e funções. 

3. Clique com o botão direito do mouse no nó de trabalho e escolha o **nó Open Job View no portal** para abrir a visualização de trabalho no portal Azure.

   ![Visão de emprego aberta no portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho em nuvem do Azure Stream Analytics no Visual Studio Code (versão prévia)](quick-create-vs-code.md)
