---
title: Copiar ou fazer backup dos trabalhos do Azure Stream Analytics
description: Este artigo descreve como copiar ou fazer backup de um trabalho do Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771488"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copiar ou fazer backup dos trabalhos do Azure Stream Analytics

Você pode copiar ou fazer backup de seus trabalhos implantados do Azure Stream Analytics usando visual studio code ou visual studio. 

## <a name="before-you-begin"></a>Antes de começar
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Faça login no [portal Azure](https://portal.azure.com/).

* Instale [a extensão Azure Stream Analytics para ferramentas Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) ou [Azure Stream Analytics para Visual Studio](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Clique no ícone **Azure** na barra de atividadede código visual do estúdio e, em seguida, expanda o nó **Stream Analytics.** Seus trabalhos devem aparecer suas assinaturas.

   ![Explorador de análise de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Para exportar um trabalho para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto.

    ![Exportar trabalho ASA em Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    O projeto é exportado para a pasta selecionada e adicionada ao seu espaço de trabalho atual.

    ![Exportar trabalho ASA em Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Para publicar o trabalho em outra região ou fazer backup usando outro nome, **selecione Selecionar entre suas assinaturas para publicar** no editor de consulta (\*.asaql) e siga as instruções.

    ![Publique para o Azure no Visual Studio Code](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Siga a [exportação de um trabalho azure Stream Analytics implantado para uma instrução de projeto](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Abra \*o arquivo .asaql no Editor de consulta, selecione Enviar para o **Azure** no editor de script e siga as instruções para publicar o trabalho em outra região ou fazer backup usando um novo nome.

## <a name="next-steps"></a>Próximas etapas

* [Quickstart: Crie um trabalho de Stream Analytics usando o Visual Studio Code](quick-create-vs-code.md)
* [Quickstart: Crie um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Implantar um trabalho do Azure Stream Analytics com CI/CD usando o Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
