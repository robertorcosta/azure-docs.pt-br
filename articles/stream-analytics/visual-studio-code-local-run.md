---
title: Teste um trabalho do Azure Stream Analytics localmente com dados de amostra usando o Visual Studio Code
description: Este artigo descreve como testar consultas localmente com dados de amostra usando a Azure Stream Analytics Tools para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486463"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Test Stream Analytics consulta localmente com dados de amostra usando visual studio code

Você pode usar as ferramentas do Azure Stream Analytics para o Visual Studio Code para testar seus trabalhos de Stream Analytics localmente com dados de amostra. Você pode encontrar os resultados da consulta em arquivos JSON na pasta **LocalRunOutputs** do seu projeto.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [o SDK do núcleo .NET](https://dotnet.microsoft.com/download) e reinicie o Visual Studio Code.

* Use este [quickstart](quick-create-vs-code.md) para aprender como criar um trabalho de Stream Analytics usando o Visual Studio Code.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

Você precisa preparar arquivos de dados de entrada de amostra primeiro. Se você já tem alguns arquivos de dados de amostra em sua máquina, você pode pular este passo e passar para o próximo.

1. Clique **em Visualizar dados** em seu arquivo de configuração de entrada a partir da linha superior. Alguns dados de entrada serão obtidos no IoT Hub e mostrados na janela de visualização. Note que isso pode demorar um pouco.

2. Uma vez que os dados são mostrados, clique **em Salvar como** para salvar os dados em um arquivo local.

 ![Visualizar a entrada dinâmica](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Defina uma entrada local

1. Clique **em input.json** na pasta Inputs em seu projeto Stream Analytics. Em seguida, **selecione Adicionar entrada local** da linha superior.

    ![Adicionar entrada local do projeto](./media/quick-create-vs-code/add-input-from-project.png)

    Você também pode usar **Ctrl+Shift+P** para abrir a paleta de comando e inserir **ASA: Adicionar entrada**.

   ![Adicionar entrada do Stream Analytics no VS Code](./media/quick-create-vs-code/add-input.png)

2. Selecione **Entrada local**.

    ![Adicionar entrada local ASA no código Visual Studio](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ Nova entrada local**.

    ![Adicione uma nova entrada local ASA no código Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Digite o mesmo alias de entrada que você usou em sua consulta.

    ![Adicionar um novo alias de entrada local ASA](./media/vscode-local-run/new-local-input-alias.png)

5. No arquivo **LocalInput_Input.json** recém-gerado, digite o caminho do arquivo onde seu arquivo de dados local está localizado.

    ![Insira o caminho do arquivo local no Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Selecione **'''Visualizar dados'** para visualizar os dados de entrada. O tipo de serialização de seus dados é detectado automaticamente se seu JSON ou CSV. Use o seletor para visualizar seus dados no formato **Table** ou **Raw.** A tabela a seguir é um exemplo de dados no **formato Tabela**:

     ![Visualizar dados locais em formato de tabela](./media/vscode-local-run/local-file-preview-table.png)

    A tabela a seguir é um exemplo de dados no **formato Raw**:

    ![Visualizar dados locais em formato bruto](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Retorne ao seu editor de consulta e **selecione Executar localmente**. Em seguida, **selecione Usar entrada local** na lista de desída.

![Selecione executar localmente no editor de consulta](./media/vscode-local-run/run-locally.png)

![Use entrada local](./media/vscode-local-run/run-locally-use-local-input.png)

O resultado é mostrado na janela direita. Você pode clicar em **Executar** para testar novamente. Você também pode selecionar **Abrir na pasta** para ver os arquivos de resultado no explorador de arquivos e abri-los ainda mais com outras ferramentas. Observe que os arquivos de resultado estão disponíveis apenas no formato JSON.

![Exibir o resultado da execução local](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Próximas etapas

* [Test Azure Stream Analytics trabalhos localmente com entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com o Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
