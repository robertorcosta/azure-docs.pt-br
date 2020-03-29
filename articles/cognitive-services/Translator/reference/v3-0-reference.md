---
title: Referência da API de texto do tradutor V3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do tradutor V3.0. A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 3/13/2020
ms.author: swmachan
ms.openlocfilehash: 4180dc6127fb2d31465400b1b25fb7e2d68f4754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369158"
---
# <a name="translator-text-api-v30"></a>API de Tradução de Texto v3.0

## <a name="whats-new"></a>Novidades

A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON. Ela melhora o uso e o desempenho por meio da consolidação dos recursos existentes em menos operações, além de fornecer novos recursos.

 * Transliteração para converter texto em um idioma de um script para outro script.
 * Tradução para vários idiomas em uma solicitação.
 * Detecção de idioma, tradução e transliteração em uma solicitação.
 * Dicionário para procurar traduções alternativas de um termo, para encontrar traduções e exemplos mostrando termos usados no contexto.
 * Resultados de detecção de idioma mais informativos.

## <a name="base-urls"></a>URLs base

O Microsoft Translator é distribuído a partir de vários locais de datacenter. Atualmente eles estão localizados em 10 [geografias azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Américas:** Leste dos EUA, Centro-Sul dos EUA, Centro-Oeste dos EUA e Oeste dos EUA 2 
* **Ásia-Pacífico:** Coréia do Sul, Japão Leste, Sudeste Asiático e Austrália Leste
* **Europa:** Europa Do Norte e Europa Ocidental

As solicitações para a API de Tradução de Texto da Microsoft são na maioria dos casos, tratadas pelo datacenter mais próximo que originou a solicitação. Em caso de falha no data center, a solicitação pode ser encaminhada para fora da geografia do Azure.

Para forçar a solicitação a ser tratada por uma geografia específica do Azure, altere o ponto final global na solicitação de API para o ponto final regional desejado:

|Descrição|Geografia azure|URL base|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Pacífico Asiático|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autenticação

Assine a API de texto tradutor ou [serviços cognitivos multi-serviços](https://azure.microsoft.com/pricing/details/cognitive-services/) no Azure Cognitive Services e use sua chave de assinatura (disponível no portal Azure) para autenticar. 

Há três cabeçalhos que você pode usar para autenticar sua assinatura. Esta tabela descreve como cada um é usado:

|headers|Descrição|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Use com a assinatura dos Serviços Cognitivos se você estiver passando a chave secreta*.<br/>O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|
|Autorização|*Use com a assinatura dos Serviços Cognitivos se você estiver passando um token de autenticação.*<br/>O valor é o token de portador: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Use com recursos multi-serviços e tradutores regionais.*<br/>O valor é a região do recurso de tradutor multi-serviço ou regional. Esse valor é opcional ao usar um recurso tradutor global.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o cabeçalho `Ocp-Apim-Subscription-Key`. Adicione `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` o cabeçalho à sua solicitação.

#### <a name="authenticating-with-a-global-resource"></a>Autenticação com um recurso global

Quando você usa um [recurso de tradutor global,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)você precisa incluir um cabeçalho para chamar a API do tradutor.

|headers|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|

Aqui está um exemplo de solicitação para chamar a API do Tradutor usando o recurso tradutor global

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autenticação com um recurso regional

Quando você usa um [recurso tradutor regional.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)
Há 2 cabeçalhos que você precisa chamar de API tradutor.

|headers|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|
|Ocp-Apim-Subscription-Region| O valor é a região do recurso do tradutor. |

Aqui está um exemplo de solicitação para chamar a API tradutora usando o recurso tradutor regional

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autenticação com um recurso multi-serviço

Quando você usa o recurso multi-serviço de um Serviço Cognitivo. Isso permite que você use uma única chave secreta para autenticar solicitações para vários serviços. 

Quando você usa uma chave secreta de vários serviços, você deve incluir dois cabeçalhos de autenticação com sua solicitação. Há 2 cabeçalhos que você precisa chamar de API tradutor.

|headers|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta do Azure para o seu recurso multi-serviço.|
|Ocp-Apim-Subscription-Region| O valor é a região do recurso multi-serviço. |

A região é necessária para a assinatura da API de texto de texto de vários serviços. A região selecionada é a única região que você pode usar para tradução de texto ao usar a chave de assinatura de vários serviços, e deve ser a mesma região que você selecionou quando se inscreveu para sua assinatura multi-serviço através do portal Azure.

As regiões `australiaeast` `brazilsouth`disponíveis `canadacentral` `centralindia`são: , `eastus` `eastus2`, `francecentral` `japaneast`, `japanwest` `koreacentral`, `northcentralus` `centralus` `centraluseuap`, `southcentralus` `southeastasia`, `uksouth` `westcentralus`, `westeurope` `eastasia` `westus`, `westus2`, `southafricanorth`, , , , , , , `northeurope`, e .

Se você passar a chave secreta na cadeia de caracteres de consulta com o parâmetro `Subscription-Key`, então você deverá especificar a região com o parâmetro de consulta `Subscription-Region`.

### <a name="authenticating-with-an-access-token"></a>Autenticação com um token de acesso
Como alternativa, você pode trocar sua chave secreta por um token de acesso. Esse token é incluído em cada solicitação como o cabeçalho `Authorization`. Para obter um token de autorização, faça uma solicitação `POST` à URL a seguir:

| Tipo de recurso     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regional ou Multi-Serviço | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Estas são as solicitações de exemplo para obter um token de acordo com uma chave secreta:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Uma solicitação bem-sucedida retorna o token de acesso codificado como texto sem formatação no corpo da resposta. O token válido é passado para o serviço de Tradução como um token de portador na Autorização.

```http
Authorization: Bearer <Base64-access_token>
```

Um token de autenticação é válido por 10 minutos. O token deve ser reutilizado ao fazer várias chamadas para as APIs do Tradutor. No entanto, se o seu programa fizer solicitações à API do Tradutor por um longo período de tempo, então seu programa deve solicitar um novo token de acesso em intervalos regulares (por exemplo, a cada 8 minutos).

## <a name="virtual-network-support"></a>Suporte para Rede Virtual

O serviço tradutor está agora disponível com`WestUS2`recursos `EastUS` `SouthCentralUS`de `WestUS`Rede Virtual em regiões limitadas ( , , , `Central US EUAP`, ). `global` Para ativar a Rede Virtual, consulte [Configurando redes virtuais de serviços cognitivos do Azure.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal) 

Uma vez que você ative esse recurso, você deve usar o ponto final personalizado para chamar a API do Tradutor. Você não pode usar o ponto final do tradutor global ("api.cognitive.microsofttranslator.com") e não pode autenticar com um token de acesso.

Você pode encontrar o ponto final personalizado uma vez que você criar o [recurso tradutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).

|headers|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|
|Ocp-Apim-Subscription-Region| O valor é a região do recurso do tradutor. Este valor é opcional se o recurso for`global`|

Aqui está um pedido de exemplo para chamar a API do Tradutor usando o ponto final personalizado

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Errors

Uma resposta de erro padrão é um objeto JSON com o par de nome/valor chamado `error`. O valor também é um objeto JSON com propriedades:

  * `code`: um código de erro definido pelo servidor.
  * `message`: uma cadeia de caracteres fornecendo uma representação legível do erro.

Por exemplo, um cliente com uma assinatura de avaliação gratuita receberia o seguinte erro após a cota livre esgotar:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns são:

| Código | Descrição |
|:----|:-----|
| 400000| Uma das entradas de solicitação não é válida.|
| 400001| O parâmetro "scope" é inválido.|
| 400002| O parâmetro "category" é inválido.|
| 400003| Um especificador de linguagem está ausente ou inválido.|
| 400004| Um especificador de script de destino ("To script") está ausente ou é inválido.|
| 400005| Um texto de entrada está faltando ou é inválido.|
| 400006| A combinação de idioma e script não é válida.|
| 400018| Um especificador de script de origem ("From script") está ausente ou é inválido.|
| 400019| Uma das línguas especificadas não é suportada.|
| 400020| Um dos elementos na matriz de texto de entrada não é válido.|
| 400021| O parâmetro da versão da API está ausente ou é inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| O idioma de origem (campo "De") não é válido.|
| 400036| O idioma de destino (campo "Para") está ausente ou é inválido.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O ID de rastreio do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está ausente ou é inválido.|
| 400050| O texto de entrada é muito longo. Exibir [limites de solicitação](../request-limits.md).|
| 400064| O parâmetro "translation" está ausente ou é inválido.|
| 400070| O número de scripts de destino (parâmetro ToScript) não corresponde ao número de idiomas de destino (para o parâmetro To).|
| 400071| O valor não é válido para o TextType.|
| 400072| A matriz de texto de entrada possui muitos elementos.|
| 400073| O parâmetro de script não é válido.|
| 400074| O corpo da solicitação não é válido como JSON.|
| 400075| A combinação de pares de idiomas e categorias não é válida.|
| 400077| O tamanho máximo da solicitação foi excedido. Exibir [limites de solicitação](../request-limits.md).|
| 400079| O sistema personalizado solicitado para tradução entre e para o idioma não existe.|
| 400080| A transliteração não é suportada para o idioma ou script.|
| 401000| A solicitação não está autorizada porque as credenciais estão ausentes ou são inválidas.|
| 401015| "As credenciais fornecidas são para a Speech API. Esta solicitação requer credenciais para a API de texto. Use uma assinatura da API de texto do tradutor."|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a assinatura excedeu sua cota gratuita.|
| 405000| O método de solicitação não é suportado para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está sendo preparado. Tente novamente em alguns minutos.|
| 408002| Solicite tempo esgotado esperando no fluxo de entrada. O cliente não apresentou um pedido no tempo que o servidor estava preparado para esperar. O cliente pode repetir a solicitação sem modificações posteriormente.|
| 415000| O cabeçalho Content-Type está ausente ou é inválido.|
| 429000, 429001, 429002| O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.|
| 500000| Erro inesperado. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|

## <a name="metrics"></a>Métricas 
As métricas permitem visualizar as informações de uso e disponibilidade do tradutor no portal Azure, na seção métricas, conforme mostrado na captura de tela abaixo. Para obter mais informações, consulte [Dados e métricas da plataforma](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Métricas do Tradutor](../media/translatormetrics.png)

Esta tabela lista métricas disponíveis com a descrição de como elas são usadas para monitorar chamadas de API de tradução.

| Métricas | Descrição |
|:----|:-----|
| TotalCalls| Número total de chamadas de API.|
| TotalTokenCalls| Número total de chamadas de API via serviço de token usando token de autenticação.|
| SuccessfulCalls| Número de chamadas com êxito.|
| TotalErrors| Número de chamadas com resposta a erros.|
| BlockedCalls| Número de chamadas que excederam a taxa ou o limite de cota.|
| ServerErrors| Número de chamadas com erro interno do servidor(5XX).|
| ClientErrors| Número de chamadas com erro do lado do cliente(4XX).|
| Latency| Duração para completar a solicitação em milissegundos.|
| CharactersTranslated| Número total de caracteres na solicitação de texto de entrada.|
