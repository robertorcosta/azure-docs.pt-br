---
title: Desenvolver e depurar empregos do Azure Stream Analytics localmente
description: Aprenda a desenvolver e testar os trabalhos do Azure Stream Analytics no seu computador local antes de executá-los no portal Azure.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879837"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Desenvolver e depurar empregos do Azure Stream Analytics localmente

Embora você seja capaz de criar e testar os trabalhos do Azure Stream Analytics no portal Azure, muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Stream Analytics facilita o uso do editor de código e das ferramentas de desenvolvimento favoritas para criar e testar trabalhos com transmissões de eventos ao vivo do Azure Event Hub, IoT Hub e Blob Storage usando um único nó em funcionamento em tempo de execução local. Você também pode enviar empregos para o Azure diretamente do seu ambiente de desenvolvimento local.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento locais

A maneira como você desenvolve trabalhos de Stream Analytics em seu computador local depende de suas preferências de ferramentas e disponibilidade de recursos. Consulte [a comparação dos recursos do Azure Stream Analytics](feature-comparison.md) para ver quais recursos são suportados para cada ambiente de desenvolvimento.

Os ambientes na tabela a seguir dão suporte a desenvolvimento local:

|Ambiente                              |Descrição    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| A [extensão Azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) para Visual Studio Code permite que você autorize, gerencie e teste seu trabalho de análise de fluxo local mente e na nuvem com o rico IntelliSense e controle de origem nativo. Suporta desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar um trabalho do Azure Stream Analytics no Visual Studio Code](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |O Stream Analytics Tools faz parte das cargas de trabalho de desenvolvimento e processamento de dados do Azure no Visual Studio. Você pode usar o Visual Studio para escrever funções e desserializadores personalizados c# definidos pelo usuário. Para saber mais, consulte [Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).|
|[Prompt de comando ou terminal](stream-analytics-tools-for-visual-studio-cicd.md)|O pacote Azure Stream Analytics CI/CD NuGet fornece ferramentas para a construção de projetos do estúdio Visual, testes locais em uma máquina arbitrária. O pacote Azure Stream Analytics CI/CD npm fornece ferramentas para compilações de projetos do Visual Studio Code (que gera um modelo de Gerenciador de Recursos do Azure) em uma máquina arbitrária.|

## <a name="next-steps"></a>Próximas etapas

* [Test Stream Analytics consulta localmente com dados de amostra usando visual studio code](visual-studio-code-local-run.md)
* [Test Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testar as consultas do Stream Analytics localmente com o Microsoft Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md)
