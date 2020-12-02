---
title: Introdução ao Azure Functions
description: Execute as primeiras etapas para trabalhar com o Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975000"
---
# <a name="getting-started-with-azure-functions"></a>Introdução ao Azure Functions

## <a name="introduction"></a>Introdução

O [Azure Functions](./functions-overview.md) permite que você implemente a lógica do seu sistema em blocos de código prontamente disponíveis. Esses blocos de código são denominados "funções".

Use os recursos a seguir para começar.

::: zone pivot="programming-language-csharp"

| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Linha de comando](./create-first-function-cli-csharp.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Explorar um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Executar uma função do Azure com gatilhos](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Função Java/Maven com terminal/prompt de comando](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Explorar um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Desenvolver um aplicativo usando o plug-in do Maven para Azure Functions](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminal/prompt de comando do Node.js](./create-first-function-cli-java.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refatore as APIs do Node.js e Express para APIs sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a referência da linguagem [JavaScript](./functions-reference-node.md) ou [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | <li>Usar o [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Executar uma função do Azure com gatilhos](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem PowerShell](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminal/prompt de comando](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre a anatomia de um aplicativo Azure Functions](./functions-reference.md)
