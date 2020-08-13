---
title: 'Início Rápido: Criar e usar o loop de aprendizado com o SDK – Personalizador'
description: Este guia de início rápido mostra como criar e gerenciar sua base de dados de conhecimento usando o SDK do cliente.
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 480e860952c2659d053f8a5659e312feacff2d2b
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874479"
---
# <a name="quickstart-personalizer-client-library"></a>Início Rápido: Biblioteca de clientes do Personalizador

Exiba o conteúdo personalizado neste início rápido com o serviço Personalizador.

Introdução à biblioteca de clientes do Personalizador. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

 * API de Classificação: seleciona o melhor item dentre as ações, com base em informações em tempo real fornecidas sobre o conteúdo e o contexto.
 * API de Recompensa: você determina a pontuação de recompensa de acordo com as suas necessidades empresariais e, em seguida, envia-a para o Personalizador com essa API. Essa pontuação pode ser um só valor, como 1 para bom e 0 para ruim, ou um algoritmo criado de acordo com as suas necessidades empresariais.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Como funciona o Personalizador](how-personalizer-works.md)

* [O que é Personalizador?](what-is-personalizer.md)
* [Onde você pode usar o Personalizador?](where-can-you-use-personalizer.md)
* [Solução de problemas](troubleshooting.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
