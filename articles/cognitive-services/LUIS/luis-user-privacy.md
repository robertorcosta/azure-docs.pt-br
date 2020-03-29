---
title: Exportação & excluir dados - LUIS
titleSuffix: Azure Cognitive Services
description: Você tem controle total sobre a visualização, exportação e exclusão de seus dados. Exclua os dados do cliente para garantir privacidade e conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273356"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e excluir dados do cliente no LUIS (Serviço Inteligente de Reconhecimento Vocal) nos Serviços Cognitivos

Exclua os dados do cliente para garantir privacidade e conformidade.

## <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente
O LUIS (Serviço Inteligente de Reconhecimento Vocal) preserva o conteúdo do cliente para operar o serviço, mas o usuário do LUIS tem controle total sobre a exibição, a exportação e a exclusão dos próprios dados. Isso pode ser feito através do [portal](luis-reference-regions.md) LUIS ou das APIs de [Autoria do LUIS (também conhecida como Programática).](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente foi armazenado criptografado no armazenamento do Azure regional da Microsoft e inclui:

- Conteúdo da conta de usuário coletado no momento do registro
- Dados de treinamento necessários para construir os modelos
- Consultas de usuário registradas usadas pelo [aprendizado ativo](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os usuários podem desligar o registro em log de consulta anexando `&log=false` à solicitação; detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente
Os usuários do LUIS têm controle total para excluir qualquer conteúdo do usuário, seja através do portal luis ou das APIs de Autoria do LUIS (também conhecida como Programmatic). A tabela a seguir exibe links para ajudar a ajudar com ambos:

| | **Conta de Usuário** | **Aplicativo** | **Exemplo de Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Enunciados de aprendizagem ativos](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Enunciados registrados](luis-concept-data-storage.md#disable-logging-utterances) |
| **Apis** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportando os dados do cliente
Os usuários do LUIS têm controle total para visualizar os dados no portal, porém devem ser exportados através das APIs de Autoria do LUIS (também conhecida como Programática). A tabela a seguir exibe links que auxiliam nas exportações de dados através das APIs de Autoria luis (também conhecidas como Programáticas):

| | **Conta de Usuário** | **Aplicativo** | **Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Apis** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Localização do aprendizado ativo

Para permitir [o aprendizado ativo,](luis-how-to-review-endpoint-utterances.md#enable-active-learning)as declarações registradas dos usuários, recebidas nos pontos finais do LUIS publicados, são armazenadas nas seguintes geografias do Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizagem ativa (detalhados abaixo), o LUIS segue as [práticas de armazenamento de dados para serviços regionais.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Europa

O portal [eu.luis.ai](https://eu.luis.ai) e a Europe Authoring (também conhecida como PROGRAMmatic APIs) estão hospedados na geografia europeia do Azure. O portal eu.luis.ai e a Europe Authoring (também conhecida como PROGRAMmatic APIs) apoiam a implantação de pontos finais para as seguintes geografias do Azure:

* Europa
* França
* United Kingdom

Ao implantar nessas geografias do Azure, as declarações recebidas até o ponto final dos usuários finais do seu aplicativo serão armazenadas na geografia da Europa do Azure para aprendizado ativo. Você pode desativar o aprendizado ativo, ver [Desativar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar expressões armazenadas, consulte [Excluir enunciados](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Austrália

O portal [au.luis.ai](https://au.luis.ai) e a Australia Authoring (também conhecida como PROGRAMmatic APIs) estão hospedados na geografia australiana do Azure. O portal au.luis.ai e a Australia Authoring (também conhecida como PROGRAMmatic APIs) suportam a implantação de pontos finais para as seguintes geografias do Azure:

* Austrália

Ao implantar nessas geografias do Azure, as declarações recebidas até o ponto final dos usuários finais do seu aplicativo serão armazenadas na geografia australiana do Azure para aprendizado ativo. Você pode desativar o aprendizado ativo, ver [Desativar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar expressões armazenadas, consulte [Excluir enunciados](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Estados Unidos

O portal [luis.ai](https://www.luis.ai) e a Autoria dos Estados Unidos (também conhecidos como APIs Programáticas) estão hospedados na geografia dos Estados Unidos do Azure. O portal luis.ai e a Autoria dos Estados Unidos (também conhecidacomo PROGRAMmatic APIs) apoiam a implantação de pontos finais para as seguintes geografias do Azure:

* Geografias azure não apoiadas pela Europa ou Austrália regiões de autoria

Ao implantar nessas geografias do Azure, as declarações recebidas até o ponto final dos usuários finais do seu aplicativo serão armazenadas na geografia dos Estados Unidos do Azure para aprendizado ativo. Você pode desativar o aprendizado ativo, ver [Desativar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar expressões armazenadas, consulte [Excluir enunciados](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de regiões do LUIS](./luis-reference-regions.md)
