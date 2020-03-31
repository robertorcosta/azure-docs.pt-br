---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483004"
---
Comece a usar o serviço Detector de Anomalias criando um dos recursos do Azure abaixo.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Criar um recurso de avaliação (abre em uma nova guia)</a>
    * Nenhuma assinatura do Azure necessária: 
    * Válido por sete dias, gratuitamente. Após a inscrição, um ponto de extremidade e uma chave de avaliação estarão disponíveis no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Essa é uma ótima opção se você deseja experimentar o Detector de Anomalias, mas não tem uma assinatura do Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Criar um recurso do Detector de Anomalias (abre em uma nova guia)</a>:
    * Disponível por meio do portal do Azure até que você exclua o recurso.
    * Use o tipo de preço gratuito para experimentar o serviço e atualize mais tarde para uma camada paga para produção.



### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos que não são de avaliação criados após 1º de julho de 2019 usam o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando a chave e o ponto de extremidade do recurso criado, crie duas variáveis de ambiente para autenticação:

* `ANOMALY_DETECTOR_KEY` – a chave de recurso para autenticar as solicitações.
* `ANOMALY_DETECTOR_ENDPOINT` – o ponto de extremidade do recurso para enviar solicitações de API. Ele terá esta aparência: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Use as instruções para seu sistema operacional.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.

***