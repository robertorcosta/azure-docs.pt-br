---
title: Treinar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: O treinamento é o processo de ensinar sua versão do aplicativo LUIS (Reconhecimento vocal) para melhorar o reconhecimento vocal natural. Treine seu aplicativo de LUIS após atualizações ao modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou enunciados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219858"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treinar sua versão ativa do aplicativo LUIS 

O treinamento é o processo de ensinar seu aplicativo de LUIS (Serviço Inteligente de Reconhecimento Vocal) para melhorar seu reconhecimento vocal de idioma natural. Treine seu aplicativo de LUIS após atualizações ao modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou enunciados. 

Treinar e [testar](luis-concept-test.md) um aplicativo é um processo iterativo. Após treinar seu aplicativo de LUIS, teste-o com enunciados de amostra para ver se as intenções e as entidades são corretamente reconhecidas. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente. 

O treinamento é aplicado para a versão ativa no portal do LUIS. 

## <a name="how-to-train-interactively"></a>Como treinar interativamente

Para iniciar o processo iterativo no [portal LUIS](https://www.luis.ai), primeiro você precisa treinar seu aplicativo LUIS pelo menos uma vez. Verifique se que cada intenção tem pelo menos um enunciado antes do treinamento.

1. Acesse seu aplicativo selecionando seu nome na página **Meus Aplicativos**. 

1. Em seu aplicativo, selecione **Treinar** no painel superior. 

1. Quando o treinamento for concluído, uma notificação aparecerá na parte superior do navegador.

## <a name="training-date-and-time"></a>Data e hora de treinamento

A data e a hora de treinamento são GMT + 2. 

## <a name="train-with-all-data"></a>Treinar com todos os dados

O treinamento usa um pequeno percentual de amostragem negativa. Se você quiser usar todos os dados em vez da pequena amostragem negativa, use a [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Configurações de versão uso da API de UseAllTrainingData

Use a [API de configurações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) de versão `UseAllTrainingData` com o definido como true para desativar esse recurso. 

## <a name="unnecessary-training"></a>Treinamento desnecessário

Não é preciso treinar após cada alteração. O treinamento deve ser feito depois que um grupo de alterações são aplicadas ao modelo e a próxima etapa em que você deseja fazer é testar ou publicar. Se você não precisar de teste ou publicar, o treinamento não é necessário. 

## <a name="training-with-the-rest-apis"></a>Trenamento com as APIs REST

O treinamento no portal do LUIS é uma etapa única para pressionar o botão**Treinar**. Treinamento com as APIs REST é um processo de duas etapas. A primeira é [solicitar treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicite o [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get. 

Para saber quando o treinamento está concluído, você precisa sondar o status até que todos os modelos sejam treinados com êxito. 

## <a name="next-steps"></a>Próximas etapas

* [Teste interativo](luis-interactive-test.md)
* [Teste em lote](luis-how-to-batch-test.md)
