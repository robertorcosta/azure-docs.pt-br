---
title: Ciclo de vida do modelo e do ponto de extremidade do serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada fornece modelos básicos para adaptação e permite criar modelos personalizados com base em seus dados. Este artigo descreve as linhas do tempo para modelos e pontos de extremidade que usam esses modelos.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555189"
---
# <a name="model-and-endpoint-lifecycle"></a>Ciclo de vida de modelo e ponto de extremidade

Fala Personalizada usa *modelos de base* e *modelos personalizados*. Cada idioma tem um ou mais modelos de base. Geralmente, quando um novo modelo de fala é liberado para o serviço de fala regular, ele também é importado para o serviço de Fala Personalizada como um novo modelo de base. Eles são atualizados a cada 6 a 12 meses. Modelos mais antigos normalmente se tornam menos úteis ao longo do tempo porque o modelo mais recente geralmente tem maior precisão.

Por outro lado, modelos personalizados são criados adaptando um modelo de base escolhido com dados de seu cenário de cliente específico. Você pode continuar usando um modelo personalizado específico por um longo período depois de ter um que atenda às suas necessidades. Mas é recomendável que você atualize periodicamente para o modelo base mais recente e retreine-o ao longo do tempo com dados adicionais. 

Outros termos principais relacionados ao ciclo de vida do modelo incluem:

* **Adaptação**: pegar um modelo base e personalizá-lo para seu domínio/cenário usando dados de texto e/ou dados de áudio.
* **Decodificação**: usando um modelo e executando o reconhecimento de fala (decodificando áudio em texto).
* **Ponto de extremidade**: uma implantação específica do usuário de um modelo base ou de um modelo personalizado que é acessível *somente* para um determinado usuário.

### <a name="expiration-timeline"></a>Linha do tempo de expiração

Conforme novos modelos e novas funcionalidades se tornam disponíveis e modelos mais antigos e menos precisos são desativados, consulte as seguintes linhas do tempo para expiração de modelo e ponto de extremidade:

**Modelos de base** 

* Adaptação: disponível por um ano. Depois que o modelo for importado, ele estará disponível por um ano para criar modelos personalizados. Após um ano, novos modelos personalizados devem ser criados a partir de uma versão mais recente do modelo base.  
* Decodificação: disponível por dois anos após a importação. Portanto, você pode criar um ponto de extremidade e usar a transcrição do lote por dois anos com esse modelo. 
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação.

**Modelos personalizados**

* Decodificação: disponível por dois anos após a criação do modelo. Portanto, você pode usar o modelo personalizado por dois anos (lote/tempo real/teste) após sua criação. Depois de dois anos, *você deve treinar novamente seu modelo* porque o modelo base normalmente terá sido preterido para adaptação.  
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação.

Quando um modelo de base ou modelo personalizado expira, ele sempre retorna para a *versão mais recente do modelo base*. Portanto, sua implementação nunca será interrompida, mas poderá se tornar menos precisa para *seus dados específicos se os* modelos personalizados atingirem a expiração. Você pode ver a expiração de um modelo nos seguintes locais na área de Fala Personalizada do Speech Studio:

* Resumo do treinamento de modelo
* Detalhes de treinamento do modelo
* Resumo de implantação
* Detalhes da implantação

Aqui está um formulário de exemplo do Resumo de treinamento do modelo:

![Resumo de treinamento de modelo ](media/custom-speech/custom-speech-model-training-with-expiry.png) e também da página de detalhes de treinamento do modelo:

![Detalhes de treinamento do modelo](media/custom-speech/custom-speech-model-details-with-expiry.png)

Você também pode verificar as datas de expiração por meio das [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) APIs de fala personalizadas e sob a `deprecationDates` Propriedade na resposta JSON.

Aqui está um exemplo dos dados de expiração da chamada à API GetModel. O "DEPRECATIONDATES" mostra: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Observe que você pode atualizar o modelo em um ponto de extremidade de fala personalizado sem tempo de inatividade alterando o modelo usado pelo ponto de extremidade na seção de implantação do Speech Studio ou por meio da API de fala personalizada.

## <a name="next-steps"></a>Próximas etapas

* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)