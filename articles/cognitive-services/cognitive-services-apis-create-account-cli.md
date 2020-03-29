---
title: Crie um recurso de Serviços Cognitivos usando o Azure CLI
titleSuffix: Azure Cognitive Services
description: Comece com o Azure Cognitive Services criando e assinando um recurso usando a interface de linha de comando do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219605"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Crie um recurso de Serviços Cognitivos usando a Interface de Linha de Comando do Azure (CLI)

Use este quickstart para começar com os Serviços Cognitivos Do Azure usando a [Interface de Linha de Comando do Azure (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Os Serviços Cognitivos são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure que você cria em sua assinatura do Azure. Depois de criar o recurso, use as chaves e o ponto final geradopara você autenticar seus aplicativos. 


Neste quickstart, você aprenderá como se inscrever no Azure Cognitive Services e criar uma conta que tenha uma assinatura de serviço único ou multi-serviço, usando a [Interface de Linha de Comando do Azure (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Esses serviços são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)do Azure, que permitem que você se conecte a uma ou mais das APIs de Serviços Cognitivos do Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure - [Crie uma](https://azure.microsoft.com/free/) gratuitamente.
* A [Interface de Linha de Comando do Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Instale o Azure CLI e faça login 

Instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Para entrar na instalação local da CLI, execute o comando [az login:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Você também pode usar o botão verde **Try It** para executar esses comandos no seu navegador.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Crie um novo grupo de recursos do Azure Cognitive Services

Antes de criar um recurso de Serviços Cognitivos, você deve ter um grupo de recursos do Azure para conter o recurso. Quando você cria um novo recurso, você tem a opção de criar um novo grupo de recursos ou usar um já existente. Este artigo mostra como criar um novo grupo de recursos.

### <a name="choose-your-resource-group-location"></a>Escolha a localização do seu grupo de recursos

Para criar um recurso, você precisará de um dos locais do Azure disponíveis para sua assinatura. Você pode recuperar uma lista de locais disponíveis com o comando [az account list-locations.](/cli/azure/account#az-account-list-locations) A maioria dos Serviços Cognitivos pode ser acessada de vários locais. Escolha o mais próximo de você ou veja quais locais estão disponíveis para o serviço.

> [!IMPORTANT]
> * Lembre-se da localização do Azure, pois você precisará dele ao ligar para os Serviços Cognitivos do Azure.
> * A disponibilidade de alguns Serviços Cognitivos pode variar de acordo com a região. Para obter mais informações, consulte [produtos Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Depois de ter sua localização azul, crie um novo grupo de recursos no Azure CLI usando o comando [az group create.](/cli/azure/group#az-group-create)

No exemplo abaixo, substitua a `westus2` localização do azure por uma das localizações do Azure disponíveis para sua assinatura.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolha um nível de serviço cognitivo e preços

Ao criar um novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou sku) que você deseja. Você usará essa e outras informações como parâmetros ao criar o recurso.

### <a name="multi-service"></a>Vários serviços

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Múltiplos serviços. Consulte a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter mais detalhes.            | `CognitiveServices`     |


> [!NOTE]
> Muitos dos Serviços Cognitivos abaixo têm um nível gratuito que você pode usar para experimentar o serviço. Para usar o nível `F0` livre, use como sku para o seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Visual Computacional            | `ComputerVision`          |
| Visão Personalizada - Previsão | `CustomVision.Prediction` |
| Visão Personalizada - Treinamento   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Reconhecimento de Formulários            | `FormRecognizer`          |
| Reconhecimento de Tinta Digital             | `InkRecognizer`           |

### <a name="search"></a>Search

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidade do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Fala

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Fala    | `SpeechServices`     |
| Reconhecimento de fala | `SpeakerRecognition` |

### <a name="language"></a>Idioma

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de formulários | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de texto     | `TextAnalytics`     |
| Tradução de texto   | `TextTranslation`   |

### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detector de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |

Você pode encontrar uma lista de "tipos" de serviço cognitivo disponíveis com o comando [az cognitiveservices account list-type:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicione um novo recurso ao seu grupo de recursos

Para criar e assinar um novo recurso de Serviços Cognitivos, use o [comando az cognitiveservices account create.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) Este comando adiciona um novo recurso de cobrança ao grupo de recursos criado anteriormente. Ao criar seu novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com seu nível de preços (ou sku) e uma localização do Azure:

Você pode criar um recurso F0 (gratuito) `anomaly-detector-resource` para o Detector de Anomalias, nomeado com o comando abaixo.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Obtenha as chaves para o seu recurso

Para fazer login na instalação local da Interface de Linha de Comando (CLI), use o comando [az login.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Use o comando [az cognitiveservices account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) para obter as chaves do seu recurso de Serviço Cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Níveis de preços e faturamento

Os níveis de preços (e o valor cobrado) são baseados no número de transações que você envia usando suas informações de autenticação. Cada camada de preços especifica:
* número máximo de transações permitidas por segundo (TPS).
* recursos de serviço habilitados dentro do nível de preços.
* O custo para uma quantidade predefinida de transações. Ir acima desse valor causará uma taxa extra conforme especificado nos [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

## <a name="get-current-quota-usage-for-your-resource"></a>Obtenha o uso atual de cotas para o seu recurso

Use o comando [az cognitiveservices account list-use para](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) obter o uso do seu recurso de Serviço Cognitivo.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover um recurso de Serviços Cognitivos, você pode excluí-lo ou o grupo de recursos. A exclusão do grupo de recursos também exclui quaisquer outros recursos contidos no grupo.

Para remover o grupo de recursos e seus recursos associados, use o comando az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Confira também

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que é o Azure Cognitive Services?](Welcome.md)
* [Suporte à linguagem natural](language-support.md)
* [Suporte ao contêiner Docker](cognitive-services-container-support.md)
