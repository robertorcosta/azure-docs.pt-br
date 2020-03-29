---
title: Exibir detalhes do modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: A guia Modelos, em qualquer projeto, mostra detalhes de cada modelo como nome do modelo, status do modelo, pontuação BLEU, treinamento, ajuste, contagem de frase de teste.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595570"
---
# <a name="view-model-details"></a>Exibir detalhes do modelo

A guia Modelos no projeto mostra todos os modelos nesse projeto. Todos os modelos treinados para esse projeto estão listados nesta guia.

Para cada modelo do projeto, esses detalhes são exibidos.

1.  Nome do modelo: mostra o nome do modelo de um determinado modelo.

2.  Status: mostra o status de um determinado modelo. O novo treinamento terá um status de Enviado até que seja aceito. O status será alterado para processamento de dados, enquanto o serviço avalia o conteúdo dos documentos. Quando a avaliação dos documentos estiver concluída, o status será alterado para Em Execução e você poderá ver o número de frases que fazem parte do treinamento, incluindo os conjuntos de ajustes e testes criados automaticamente para você. Abaixo está uma lista de status do modelo que descreve o estado dos modelos.

    -  Enviado: especifica que o back-end está processando os documentos para esse modelo.

    -  TrainingQueued: especifica que o treinamento está sendo colocado na fila do sistema MT para esse modelo.

    -  Em Execução: especifica que o treinamento está sendo executado no sistema MT para esse modelo.

    -  Êxito: especifica que o treinamento teve êxito no sistema MT e um modelo está disponível. Nesse status, uma pontuação BLEU é exibida para esse modelo.

    -  Implantado: especifica que o modelo treinado com êxito seja enviado ao sistema MT para implantação.

    -  Desimplantar: especifica que o modelo implantado está sendo desimplantado.

    -  Desimplantado: especifica que o processo de não implantação de um modelo foi concluído com êxito.

    -  Falha no treinamento: especifica que o treinamento falhou. Se ocorrer uma falha de treinamento, tente novamente o trabalho de treinamento. Se o erro persistir, contate-nos. Não exclua o modelo com falha.

    - DataProcessingFailed: especifica que o processamento de dados falhou para um ou mais documentos pertencentes ao modelo.

    - DeploymentFailed: especifica que a implantação de modelo falhou.

    - MigratedDraft: especifica que o modelo está no estado de rascunho após a migração do Hub para o Tradutor Personalizado.

4.  Pontuação BLEU: mostra a pontuação BLEU (Bilingual Evaluation Understudy) do modelo, indicando a qualidade do sistema de tradução. Essa pontuação informa o quanto as traduções feitas pelo sistema de tradução resultantes desse treinamento correspondem às frases de referência no conjunto de dados de teste. A pontuação BLEU será exibida se o treinamento for concluído com êxito. Se o treinamento não estiver concluído/com falha, nenhuma pontuação BLEU será exibida.

5.  Contagem de frases de treinamento: mostra o número total de frases usadas como conjunto de treinamento.

6.  Contagem de frases de ajuste: mostra o número total de frases usadas como conjunto de ajuste.

7.  Contagem de frases de treinamento: mostra o número total de frases usadas como conjunto de testes.

8.  Contagem de frases mono: mostra o número total de frases usadas como conjunto mono.

9.  Botão de ação Implantar: para um modelo treinado com êxito, mostra o botão "Implantar", se não estiver implantado. Se houver um modelo implantado, um botão "Desimplantar" será exibido.

10. Excluir: você pode usará esse botão se quiser excluir o modelo. A exclusão de um modelo não excluirá nenhum documento usado para criar esse modelo.

    ![Exibir detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar treinamentos consecutivos para os mesmos sistemas, é importante manter o conjunto de ajustes e o conjunto de testes constantes.

## <a name="view-model-training-details"></a>Exibir detalhes do treinamento do modelo

Quando o treinamento estiver concluído, você poderá revisar os detalhes do treinamento na página de detalhes. Selecione um projeto, localize e selecione a guia de modelos e escolha um modelo.

A página do modelo tem duas guias: Detalhes do treinamento e Teste.

1.  **Detalhes do treinamento:** essa aba mostra a lista de documentos usados no treinamento:

    -  Nome de documentos: esse campo mostra o nome do documento

    -  Tipo de documento: esse campo mostra se o documento é paralelo/mono.

    -  Contagem de frases no idioma de origem: esse campo mostra o número de frases como parte do idioma de origem.

    -  Contagem de frases no idioma de destino: esse campo mostra o número de frases como parte do idioma de destino.

    -  Sentenças alinhadas: esse campo mostra que o número de frases foi alinhado pelo Tradutor Personalizado durante o processo de alinhamento.

    -  Frases usadas: esse campo mostra o número de frases que foram usadas pelo Tradutor Personalizado durante esse treinamento.

    ![Detalhes do treinamento de modelo](media/how-to/how-to-model-training-details.png)

2.  **Teste:** essa guia mostra os detalhes do teste para um treinamento com êxito.

## <a name="next-steps"></a>Próximas etapas

- Revise os [resultados do teste](how-to-view-system-test-results.md) e analise os resultados do treinamento.
