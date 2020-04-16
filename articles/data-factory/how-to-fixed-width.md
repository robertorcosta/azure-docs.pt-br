---
title: Processe arquivos de texto de comprimento fixo com mapeamento de fluxos de dados na Fábrica de Dados do Azure
description: Saiba como processar arquivos de texto de comprimento fixo na Fábrica de Dados Do Azure usando fluxos de dados de mapeamento.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414382"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Processe arquivos de texto de comprimento fixo usando fluxos de dados de mapeamento da fábrica de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usando fluxos de dados de mapeamento no Microsoft Azure Data Factory, você pode transformar dados de arquivos de texto de largura fixa. Na tarefa seguinte, definiremos um conjunto de dados para um arquivo de texto sem um delimitador e, em seguida, configuraremos divisões de substring com base na posição ordinal.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+Novo pipeline** para criar um novo pipeline.

2. Adicionar uma atividade de fluxo de dados, que será usada para processar arquivos de largura fixa:

    ![Tubulação de largura fixa](media/data-flow/fwpipe.png)

3. Na atividade de fluxo de dados, selecione **Novo fluxo de dados de mapeamento**.

4. Adicionar uma transformação De Origem, Coluna Derivada, Selecionar e Afundar:

    ![Fluxo de dados de largura fixa](media/data-flow/fw2.png)

5. Configure a transformação Origem para usar um novo conjunto de dados, que será do tipo Texto Delimitado.

6. Não defina nenhum delimitador de coluna ou cabeçalhos.

   Agora vamos definir pontos de partida de campo e comprimentos para o conteúdo deste arquivo:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Na guia **Projeção** da sua transformação Origem, você deve ver uma coluna de strings nomeada *Column_1*.

8. Na coluna Derivada, crie uma nova coluna.

9. Vamos dar às colunas nomes simples como *col1.*

10. No construtor de expressões, digite o seguinte:

    ```substring(Column_1,1,4)```

    ![coluna derivada](media/data-flow/fwderivedcol1.png)

11. Repita o passo 10 para todas as colunas que você precisa analisar.

12. Selecione a guia **Inspecionar** para ver as novas colunas que serão geradas:

    ![Inspecionar](media/data-flow/fwinspect.png)

13. Use a transformação Select para remover qualquer uma das colunas que você não precisa para a transformação:

    ![selecionar transformação](media/data-flow/fwselect.png)

14. Use sink para produzir os dados em uma pasta:

    ![pia de largura fixa](media/data-flow/fwsink.png)

    Eis como é a saída:

    ![saída de largura fixa](media/data-flow/fxdoutput.png)

  Os dados de largura fixa estão agora divididos, com quatro caracteres cada e atribuídos a Col1, Col2, Col3, Col4, e assim por diante. Com base no exemplo anterior, os dados são divididos em quatro colunas.

## <a name="next-steps"></a>Próximas etapas

* Construa o resto da lógica de fluxo de dados usando transformações de [fluxos](concepts-data-flow-overview.md)de dados de mapeamento .
