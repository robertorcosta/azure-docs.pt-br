---
title: 'Tutorial: Teste do lote para encontrar problemas — LUIS'
description: Este tutorial demonstra como usar o teste em lotes para validar a qualidade do aplicativo LUIS (Reconhecimento vocal).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298300"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: testar conjuntos de dados em lote

Este tutorial demonstra como usar o teste em lotes para validar a qualidade do aplicativo LUIS (Reconhecimento vocal).

O teste em lotes permite que você valide o estado do modelo ativo e treinado com um conjunto conhecido de entidades e enunciados rotulados. No arquivo em lotes em formato JSON, adicione os enunciados e defina os rótulos de entidade que você precisa prever dentro do enunciado.

Requisitos para o teste em lotes:

* Máximo de 1000 enunciados por teste.
* Sem duplicatas.
* Tipos de entidade permitidos: somente entidades de aprendizado de máquina.

Ao usar um aplicativo diferente deste tutorial, *não* use os enunciados de exemplo já adicionados ao aplicativo.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Criar um arquivo de teste de lote
> * Executar um teste de lote
> * Examinar resultados de teste

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

Importe um aplicativo que recebe um pedido de pizza, como `1 pepperoni pizza on thin crust`.

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Importe o JSON para um novo aplicativo e nomeie o aplicativo `Pizza app`.


1. Selecione **Treinar** no canto superior direito da navegação para treinar o aplicativo.

## <a name="what-should-the-batch-file-utterances-include"></a>O que está incluído nos enunciados do arquivo em lotes

O arquivo em lotes deve incluir enunciados com as entidades de aprendizado de máquina de nível superior rotuladas, incluindo as posições inicial e final. Os enunciados não devem fazer parte dos exemplos já existentes no aplicativo. Eles devem ser enunciados dos quais você deseja prever positivamente a intenção e as entidades.

Você pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1.000 enunciados) no mesmo arquivo.

## <a name="batch-file"></a>Arquivo em lotes

O JSON de exemplo inclui um enunciado com uma entidade rotulada para ilustrar a aparência de um arquivo de teste. Em seus testes, você deverá ter muitos enunciados com a intenção correta e a entidade de aprendizado de máquina rotulada.

1. Crie `pizza-with-machine-learned-entity-test.json` em um editor de texto ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. No arquivo em lotes formatado em JSON, adicione um enunciado com a **Intenção** que deseja prever no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Testar** na barra de navegação superior.

2. Selecione **Painel de teste do lote** no painel direito.

3. Selecione **Importar conjunto de dados**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do aplicativo de LUIS com o conjunto de dados de importação realçado](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Escolha o local do arquivo do `pizza-with-machine-learned-entity-test.json`.

5. Nomeie o conjunto de dados `pizza test` e selecione **Concluído**.

    > [!div class="mx-imgBorder"]
    > ![Selecionar arquivo](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecione o botão **Executar**.

7. Selecione **Ver resultados**.

8. Examine os resultados no grafo e na legenda.

## <a name="review-batch-results-for-intents"></a>Examinar as intenções dos resultados do lote

Os resultados de teste mostram graficamente como os enunciados de teste foram previstos em relação à versão ativa.

O gráfico do lote exibe quatro quadrantes dos resultados. À direita do gráfico, há um filtro. O filtro contém intenções e entidades. Ao selecionar uma [seção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro do gráfico, os enunciados associados são exibidos abaixo do gráfico.

Ao passar o mouse sobre o gráfico, um botão de rolagem do mouse pode ampliar ou reduzir a exibição no gráfico. Isso é útil quando há muitos pontos no gráfico agrupados em conjunto.

O gráfico está em quatro quadrantes, com duas das seções exibidas em vermelho.

1. Selecione a intenção **ModifyOrder** na lista de filtros.

    > [!div class="mx-imgBorder"]
    > ![Selecionar a intenção ModifyOrder na lista de filtros](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    O enunciado é previsto como um **verdadeiro positivo**, o que significa que o enunciado correspondeu com êxito à previsão positiva listada no arquivo em lotes.

    > [!div class="mx-imgBorder"]
    > ![O enunciado correspondeu com êxito à previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas de seleção verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1, porque o enunciado foi testado em relação a cada intenção, como um teste negativo para as intenções não listadas no teste em lotes.

1. Selecione a intenção **Confirmação**. Essa intenção não está listada no teste em lotes e, portanto, esse é um teste negativo do enunciado listado no teste em lotes.

    > [!div class="mx-imgBorder"]
    > ![Enunciado previsto negativo com êxito para a intenção não listada no arquivo em lotes](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem-sucedido, conforme observado com o texto verde no filtro e na grade.

## <a name="review-batch-test-results-for-entities"></a>Examinar as entidades nos resultados de teste em lotes

A entidade ModifyOrder, como uma entidade de computador com subentidades, é exibida se a entidade de nível superior é correspondida e exibe como as subentidades são previstas.

1. Selecione a entidade **ModifyOrder** na lista de filtros e, em seguida, selecione o círculo na grade.

1. A previsão de entidade é exibida abaixo do gráfico. A exibição inclui linhas sólidas para previsões que correspondem à expectativa e linhas pontilhadas para previsões que não correspondem à expectativa.

    > [!div class="mx-imgBorder"]
    > ![Entidade pai prevista com êxito no arquivo em lotes](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Como encontrar erros com um teste em lotes

Este tutorial mostrou como executar um teste e interpretar os resultados. Ele não abrangeu a filosofia do teste nem como responder a testes com falha.

* Não se esqueça de abranger enunciados positivos e negativos no teste, incluindo os enunciados que possam ser previstos para uma intenção diferente, mas relacionada.
* Para os enunciados com falha, execute as seguintes tarefas e, em seguida, execute os testes novamente:
    * Examine os exemplos atuais para ver se há intenções e entidades e confirme se os exemplos de enunciados da versão ativa estão corretos em relação à rotulagem de intenção e de entidade.
    * Adicionar recursos que ajudam seu aplicativo a prever intenções e entidades
    * Adicionar mais exemplos de enunciados positivos
    * Examinar o equilíbrio de exemplos de enunciados entre as intenções

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Próxima etapa

O tutorial usou um teste em lotes para validar o modelo atual.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões](luis-tutorial-pattern.md)

