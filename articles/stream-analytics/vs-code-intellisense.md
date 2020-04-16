---
title: IntelliSense em ferramentas azure Stream Analytics para Visual Studio Code
description: Este artigo descreve como usar os recursos do IntelliSense nas ferramentas do Azure Stream Analytics para o Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394391"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense em ferramentas azure Stream Analytics para Visual Studio Code

O IntelliSense está disponível para [o Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) nas ferramentas do [Azure Stream Analytics para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). O IntelliSense é uma ajuda de preenchimento de código que inclui inúmeras funcionalidades: Listar Membros, Informações do Parâmetro, Informações Rápidas e Completar Palavra. Os recursos do IntelliSense às vezes são chamados por outros nomes, como "conclusão de código", "assistência de conteúdo" e "sugestão de código".

![Demonstração do IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Recursos do IntelliSense

Os recursos do IntelliSense nas ferramentas stream analytics para VS Code são alimentados por um serviço de idioma. Um serviço de idioma analisa seu código-fonte e fornece conclusões inteligentes de código com base na semântica do idioma. Se um serviço de idiomas souber possíveis conclusões, as sugestões do IntelliSense aparecem conforme você digita. Se você continuar digitando, uma lista de membros, como variáveis e métodos, será filtrada para incluir apenas membros que contenham os caracteres digitados. Quando você `Tab` pressiona as teclas ou, `Enter` o IntelliSense insere o membro selecionado.

Você pode acionar o IntelliSense em qualquer janela do editor `.`digitando um caractere de gatilho, como o caractere dot .

![autoconclusão intellisense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> O widget de sugestões suporta a filtragem CamelCase. Você pode digitar as letras que estão em maiúsculas em um nome de método para limitar as sugestões. Por exemplo, "cra" trará rapidamente "createApplication".

### <a name="types-of-completions"></a>Tipos de finalizações

Ferramentas de Stream Analytics para CÓDIGO VS O IntelliSense oferece diferentes tipos de conclusão, incluindo sugestões de servidor de idiomas, trechos e simples conclusões texais baseadas em palavras.

|       |         |       |
| ----- | ------- | ----- |
| Palavras-chave | `keyword`
| Funções | `build-in function`, `user defined function`  |
| Nome do conjunto de dados| `input`, `output`, `intermediate result set`|
| Nome da coluna do conjunto de dados|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Conclusão de nome

Além da auto-conclusão da palavra-chave, as ferramentas stream analytics para o CÓDIGO VS lêem a lista de nomes de entrada e saída de trabalho, bem como os nomes das colunas em suas fontes de dados quando elas são configuradas. A extensão lembra essas informações para fornecer recursos de preenchimento de nomes que são úteis para inserir instruções com poucas teclas:

Durante a codificação, você não precisa deixar o editor para realizar pesquisas sobre nomes de entrada de trabalho, nome de saída e nomes de coluna. Você pode manter seu contexto, encontrar as informações necessárias, inserir elementos diretamente em seu código e fazer com que o IntelliSense complete sua digitação para você.

Observe que você precisa configurar entrada local ou entrada ao vivo e salvar o arquivo de configuração para poder usar o preenchimento de nome.

![conclusão do nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informações de Parâmetro

A opção IntelliSense **Parameter Info** abre uma lista de parâmetros que fornece informações sobre o número, nomes e tipos dos parâmetros que são exigidos por uma função. O parâmetro em negrito indica o próximo parâmetro necessário à medida que você digita uma função.

A lista de parâmetros também é exibida para funções aninhadas. Se você digitar uma função como um parâmetro para outra função, a lista de parâmetros exibirá os parâmetros da função interna. Em seguida, quando a lista de parâmetros da função interna estiver completa, a lista de parâmetros é revertida para exibir os parâmetros da função externa.

![informações de parâmetro](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informação Rápida

Conforme fornecido pelo serviço de idiomas, você pode ver **informações rápidas** para cada identificador em seu código. Alguns exemplos de identificadores são entrada, saída, um conjunto de resultados intermediário ou função. Quando você move o ponteiro do mouse sobre um identificador, sua declaração é exibida em uma janela pop-up. As propriedades e esquemas de dados para entradas, se configuradas, e conjunto de dados intermediários são mostrados.

![informações rápidas](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Solução de problemas do IntelliSense

Esse problema é causado pela falta de configuração de entrada que fornece dados. Você pode verificar se uma [entrada local](visual-studio-code-local-run.md#define-a-local-input) ou entrada [ao vivo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) foi configurada corretamente.

## <a name="next-steps"></a>Próximas etapas

* [Quickstart: Crie um trabalho de Análise de Fluxo azure no Visual Studio Code](quick-create-vs-code.md)
* [Test Stream Analytics consulta localmente com dados de amostra usando visual studio code](visual-studio-code-local-run.md)
* [Test Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando visual Studio Code](visual-studio-code-local-run-live-input.md)
