---
title: Comparação de recursos do Azure Stream Analytics
description: Este artigo compara os recursos suportados para os trabalhos em nuvem e IoT Edge do Azure Stream Analytics no portal Azure, Visual Studio e Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d3b27a50fa86916b71c84b30ecdbf45deb0ec45c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770766"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos do Azure Stream Analytics

Com o Azure Stream Analytics, você pode criar soluções de streaming na nuvem e na Borda IoT usando o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code.](quick-create-vs-code.md) As tabelas deste artigo mostram quais características são suportadas por cada plataforma para ambos os tipos de trabalho.

> [!NOTE]
> As ferramentas Visual Studio e Visual Studio Code não suportam empregos nas regiões nordeste da China, China, Alemanha Central e Alemanha.

## <a name="cloud-job-features"></a>Recursos de trabalho em nuvem


|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma cruzada     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Autor do roteiro     |Sim         |Sim         |Sim         |
|Script Intellisense     |Realce de sintaxe         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |
|Defina todos os tipos de entradas, saídas e configurações de trabalho     |Sim         |Sim         |Sim         |
|Controle do código-fonte     |Não         |Sim         |Sim         |
|Suporte a CI/CD     |Parcial         |Sim         |Sim         |
|Compartilhe entradas e saídas em várias consultas     |Não         |Sim         |Sim         |
|Teste de consulta com um arquivo de amostra     |Sim         |Sim        |Sim         |
|Testes locais de dados ao vivo     |Não         |Sim       |Sim      |
|Liste empregos e veja entidades de trabalho     |Sim         |Sim        |Sim         |
|Exportar um emprego para um projeto local     |Não         |Sim         |Sim         |
|Enviar, iniciar e parar os trabalhos     |Sim         |Sim         |Sim         |
|Ver métricas de trabalho e diagrama     |Sim         |Sim         |Abrir no portal         |
|Exibir erros de tempo de execução do trabalho     |Sim         |Sim         |Não         |
|Logs de diagnóstico     |Sim         |Não         |Não         |
|Propriedades de mensagens personalizadas     |Sim         |Sim         |Não       |
|C# função de código personalizado e Deserializador|Modo somente leitura|Sim|Não|
|JavaScript UDF e UDA     |Sim         |Sim         |Somente Windows         |
|Serviço do Machine Learning     |Sim        |Sim         |Não         |
|Machine Learning Studio     |Sim, mas a consulta não pode ser testada        |Sim |Não         |
|Nível de Compatibilidade     |1.0</br>1,1</br>1.2 (padrão)         |1.0</br>1,1</br>1.2 (padrão)           |1.0</br>1,1</br>1.2 (padrão)           |
|Funções incorporadas de detecção de anomalias baseadas em ML     |Sim         |Sim         |Sim         |
|Funções GeoEspaciais incorporadas     |Sim         |Sim         |Sim         |



## <a name="iot-edge-job-features"></a>Recursos de trabalho do IoT Edge

|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Criação de trabalho     |Sim         |Sim         |Não         |
|Controle do código-fonte     |Não         |Sim         |Não         |
|Exportar um emprego para um projeto local     |Não         |Sim         |Não         |
|Teste de consulta com um arquivo de amostra     |Sim         |Sim         |Não         |
|Compartilhe entradas e saídas em várias consultas     |Não         |Sim         |Não         |
|UDF do C#     |Não         |Sim         |Não         |
|Enviar trabalhos     |Sim         |Sim         |Não         |
|Liste empregos e veja entidades de trabalho     |Sim         |Sim         |Não         |
|Ver métricas de trabalho e diagrama     |Sim         |Parcial         |Não         |
|Exibir erros de tempo de execução do trabalho     |Sim         |Parcial         |Não         |
|Suporte a CI/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Próximas etapas

* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escreva uma função c# definida pelo usuário para o trabalho de IoT Edge do Azure Stream Analytics (Preview)](stream-analytics-edge-csharp-udf.md)
* [Desenvolva trabalhos de IoT Edge do Stream Analytics usando ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explore o Azure Stream Analytics com o Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


