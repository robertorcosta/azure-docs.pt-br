---
title: API de Tradução de Texto v2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API v2.0 de texto tradutor.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242493"
---
# <a name="translator-text-api-v20"></a>API de Tradução de Texto v2.0

> [!IMPORTANT]
> Esta versão da API de Tradução de Texto foi preterida. [Exibir documentação para a versão 3 da API de texto do tradutor](v3-0-reference.md).

A versão 2 da API de texto tradutor pode ser perfeitamente integrada em seus aplicativos, sites, ferramentas ou outras soluções para fornecer experiências de usuário multilinguagem. Você pode usá-lo em qualquer plataforma de hardware e com qualquer sistema operacional para executar a tradução de idiomas e outras tarefas relacionadas ao idioma, como detecção de linguagem de texto e texto para falar, de acordo com os padrões do setor. Para obter mais informações, consulte [API de texto tradutor](../translator-info-overview.md).

## <a name="getting-started"></a>Introdução
Para acessar a API de texto do tradutor, você precisa [se inscrever no Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas para a API de texto do tradutor exigem uma chave de assinatura para autenticação. A API suporta três métodos de autenticação:

- Um token de acesso. Use a chave de assinatura para criar um token de acesso fazendo uma solicitação POST ao serviço de autenticação. Veja a documentação do serviço de token para obter detalhes. Passe o token de acesso ao `Authorization` serviço `access_token` Tradutor usando o cabeçalho ou o parâmetro de consulta. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue usando o mesmo token de acesso para solicitações repetidas durante os 10 minutos.
- Uma chave de assinatura usada diretamente. Passe sua chave de assinatura `Ocp-Apim-Subscription-Key` como um valor no cabeçalho incluído com sua solicitação à API de texto tradutor. Quando você usa a chave de assinatura diretamente, você não precisa chamar o serviço de autenticação de token para criar um token de acesso.
- Uma [assinatura multi-serviço do Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Este método permite que você use uma única chave secreta para autenticar solicitações de vários serviços.
Quando você usa uma chave secreta de vários serviços, você precisa incluir dois cabeçalhos de autenticação com sua solicitação. O primeiro cabeçalho passa a chave secreta. O segundo cabeçalho especifica a região associada à sua assinatura:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura da API de texto de texto de vários serviços. A região selecionada é a única região que você pode usar para tradução de texto quando você usa a chave de assinatura de vários serviços. Ele precisa ser a mesma região que você selecionou quando se inscreveu para sua assinatura multi-serviço no portal Azure.

As regiões `australiaeast`disponíveis `brazilsouth` `canadacentral`são, `eastasia`, `eastus` `eastus2`, `japaneast` `northeurope`, `southcentralus` `southeastasia` `centralindia` `centraluseuap`, `westcentralus`, `westeurope` `westus`, , , , , , , `westus2` `uksouth`, , , e .

Sua chave de assinatura e token de acesso são segredos que devem ser escondidos da vista.

## <a name="profanity-handling"></a>Tratamento de vulgaridade
Normalmente, o serviço tradutor manterá palavrões presentes na fonte. O grau de profanação e o contexto que torna as palavras profanas diferem de acordo com a cultura. Assim, o grau de profanação na linguagem-alvo poderia ser aumentado ou reduzido.

Se você quiser evitar palavrões na tradução mesmo quando está no texto de origem, você pode usar a opção de filtragem de palavrões para os métodos que a suportam. A opção permite que você escolha se deseja ver palavrões excluídos ou marcados com tags apropriadas, ou se você deseja permitir a profanação no destino. Os valores `ProfanityAction` `NoAction` aceitos `Marked`são `Deleted`(padrão), e .


|ProfanityAction    |Ação |Fonte de exemplo (japonês)  |Tradução por exemplo (inglês)  |
|:--|:--|:--|:--|
|NoAction   |Padrão. O mesmo que não configurar a opção. O conteúdo ofensivo passará da origem para o destino.        |彼はジャッカスです。     |Ele é um babaca.   |
|Marked     |Palavras profanas serão cercadas por \<tags XML \<> e /profano>.       |彼はジャッカスです。 |Ele é \<um profano>\<idiota/profanação>.  |
|Deleted    |Palavras ofensivas serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo conteúdo da tradução
Quando você traduz conteúdo com`contentType=text/html`tags, como HTML (), às vezes é útil excluir conteúdo específico da tradução. É possível usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer no idioma original. No exemplo a seguir, o `div` conteúdo do primeiro elemento não será traduzido, mas o conteúdo no segundo `div` elemento será traduzido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de caracteres de texto de um idioma para outro.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor de retorno:** Uma seqüência que representa o texto traduzido.

Se você usou `AddTranslation` `AddTranslationArray` anteriormente ou para inserir uma tradução com uma `Translate` classificação de 5 ou mais para a mesma frase de origem, retorna apenas a melhor escolha disponível para o seu sistema. "Mesma frase de origem" significa exatamente o mesmo (100% de correspondência), exceto para capitalização, espaço em branco, valores de tag e pontuação no final de uma frase. Se nenhuma classificação for armazenada com uma classificação de 5 ou mais, o resultado retornado será a tradução automática pelo Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição    |Tipo de parâmetro|tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|text|(vazio)   |Obrigatórios. Uma seqüência que representa o texto para traduzir. O texto não pode conter mais de 10.000 caracteres.|Consulta|string|
|de|(vazio)   |Opcional. Uma seqüência que representa o código de idioma do texto que está sendo traduzido. Por exemplo, en para inglês.|Consulta|string|
|para|(vazio) |Obrigatórios. Uma seqüência que representa o código do idioma para traduzir o texto para.|Consulta|string|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Os formatos `text/plain` suportados são `text/html`(padrão) e . Todos os elementos HTML precisam ser bem formados, elementos completos.|Consulta|string|
|category|(vazio)   |Opcional. Uma seqüência que contém a categoria (domínio) da tradução. O padrão é `general`.|Consulta|string|
|Autorização|(vazio)  |Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera traduções para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Aqui está o formato do corpo de solicitação:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Esses elementos `TranslateArrayRequest`estão em:


* `AppId`: obrigatório. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `AppId` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .
* `From`: opcional. Uma seqüência que representa o código de idioma do texto que está sendo traduzido. Se este campo for deixado vazio, a resposta incluirá o resultado da detecção automática de linguagem.
* `Options`: opcional. Um `Options` objeto que contém os seguintes valores. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - `Category`: Uma seqüência que contém a categoria (domínio) da tradução. O padrão é `general`.
    - `ContentType`: o formato do texto que está sendo traduzido. Os formatos suportados `text/plain` são `text/xml`(padrão) e `text/html`. Todos os elementos HTML precisam ser bem formados, elementos completos.
    - `ProfanityAction`: Especifica como os palavrões são tratados, conforme explicado anteriormente. Os valores aceitos são `NoAction` (padrão) `Marked`e `Deleted`.
    - `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
    - `Uri`: filtre os resultados por esse URI. Padrão: `all`.
    - `User`: filtre os resultados por esse usuário. Padrão: `all`.
* `Texts`: obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 2.000.
* `To`: obrigatório. Uma seqüência que representa o código do idioma para traduzir o texto para.

Você pode omiti-lo elementos opcionais. Os elementos que `TranslateArrayRequest` são filhos diretos devem ser listados em ordem alfabética.

O `TranslateArray` método `application/xml` aceita `text/xml` `Content-Type`ou para .

**Valor retornado:** uma matriz `TranslateArrayResponse`. Cada `TranslateArrayResponse` um tem esses elementos:

* `Error`: Indica um erro se ocorrer. Caso contrário, definido como nulo.
* `OriginalSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: o texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase no texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Retorna o mesmo conteúdo da solicitação.

Aqui está o formato do corpo de resposta:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma resposta bem-sucedida `TranslateArrayResponse` inclui uma matriz de matrizes no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)  |Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP   |Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada. Os erros comuns incluem: <ul><li>O elemento matriz não pode estar vazio.</li><li>Categoria inválida.</li><li>Da linguagem é inválido.</li><li>A linguagem é inválida.</li><li>A solicitação contém muitos elementos.</li><li>A linguagem From não é suportada.</li><li>A linguagem To não é suportada.</li><li>Traduzir solicitação tem muitos dados.</li><li>HTML não está em um formato correto.</li><li>Muitas cordas foram passadas no Pedido de Tradução.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera nomes amigáveis para as línguas `languageCodes`passadas como parâmetro, localizado na língua passada. `locale`

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo de solicitação inclui uma matriz de strings que representa os códigos de idioma ISO 639-1 para os quais recuperar os nomes amigáveis. Aqui está um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de retorno:** Uma matriz de strings que contém nomes de idiomas suportados pelo serviço Tradutor, localizado no idioma solicitado.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de strings que contém nomes de idiomas suportados pelo serviço Tradutor, localizado no idioma solicitado.

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|localidade|(vazio) |Obrigatórios. Uma string que representa um dos seguintes, usado para localizar os nomes do idioma: <ul><li>A combinação de um código de cultura em minúsculas ISO 639 de duas letras associado a uma língua e um código de subcultura de duas letras ISO 3166. <li>Um código de cultura de minúsculas ISO 639 por si só.|Consulta|string|
|Autorização|(vazio)  |Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista de códigos de idiomas que representam idiomas suportados pelo serviço de tradução.  `Translate` e `TranslateArray` podem traduzir entre qualquer um desses idiomas.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor de retorno:** Uma matriz de strings que contém os códigos de idioma suportados pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de strings que contém os códigos de idioma suportados pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|Autorização|(vazio)  |Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera os idiomas disponíveis para a sintetização de voz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor de retorno:** Uma matriz de strings que contém os códigos de idioma suportados para a síntese de fala pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de strings que contém os códigos de idioma suportados para a síntese de fala pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Notas de implementação
Retorna um fluxo WAV ou MP3 do texto aprovado, falado no idioma desejado.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor de retorno:** Um fluxo WAV ou MP3 do texto aprovado, falado na língua desejada.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

binary

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|text|(vazio)   |Obrigatórios. Uma seqüência que contém uma ou mais frases a serem faladas para o fluxo, no idioma especificado. O texto não deve exceder 2.000 caracteres.|Consulta|string|
|Linguagem|(vazio)   |Obrigatórios. Uma seqüência que representa o código de idioma suportado do idioma em que falar o texto. O código deve ser um dos códigos `GetLanguagesForSpeak`devolvidos pelo método.|Consulta|string|
|format|(vazio)|Opcional. Uma seqüência que especifica o ID do tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor padrão é `audio/wav`.|Consulta|string|
|opções|(vazio)    |Opcional. Uma string que especifica propriedades da fala sintetizada:<ul><li>`MaxQuality`e `MinSize` especificar a qualidade do sinal de áudio. `MaxQuality`fornece a mais alta qualidade. `MinSize`fornece o menor tamanho do arquivo. O padrão `MinSize`é .</li><li>`female`e `male` especificar o gênero desejado da voz. O padrão é `female`. Use a barra<code>\|</code>vertical () para incluir várias opções. Por exemplo, `MaxQuality|Male`.</li></li></ul>  |Consulta|string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Identifica a linguagem de uma seção de texto.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor de retorno:** Uma seqüência que contém um código de linguagem de dois caracteres para o texto.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|text|(vazio)|Obrigatórios. Uma seqüência que contém texto cujo idioma deve ser identificado. O texto não deve exceder 10.000 caracteres.|Consulta|  string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key  |(vazio)    |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica os idiomas em uma matriz de strings. Detecta independentemente a linguagem de cada elemento de matriz individual e retorna um resultado para cada linha da matriz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Aqui está o formato do corpo de solicitação:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode exceder 10.000 caracteres.

**Valor de retorno:** Uma matriz de strings que contém um código de linguagem de dois caracteres para cada linha na matriz de entrada.

Aqui está o formato do corpo de resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`DetectArray`foi bem sucedido. Retorna uma matriz de strings que contém um código de linguagem de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Após 31 de janeiro de 2018, este método não aceitará novas alegações de sentença. Você receberá uma mensagem de erro. Consulte o edital sobre alterações no Quadro de Tradução Colaborativa (CTF).

Adiciona uma tradução à memória de tradução.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicativo: xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|originalText|(vazio)|Obrigatórios. Uma seqüência que contém o texto para traduzir. O comprimento máximo da seqüência é de 1.000 caracteres.|Consulta|string|
|translatedText|(vazio) |Obrigatórios. Uma seqüência que contém texto traduzido para o idioma de destino. O comprimento máximo da seqüência é de 2.000 caracteres.|Consulta|string|
|de|(vazio)   |Obrigatórios. Uma seqüência que representa o código de idioma do idioma original do texto. Por exemplo, en para inglês e de para alemão.|Consulta|string|
|para|(vazio)|Obrigatórios. Uma seqüência que representa o código de idioma do idioma para traduzir o texto para.|Consulta|string|
|classificação|(vazio) |Opcional. Um inteiro que representa a classificação de qualidade para a seqüência. O valor é entre -10 e 10. O padrão é 1.|Consulta|inteiro|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Os formatos suportados são `text/plain` e `text/html`. Todos os elementos HTML precisam ser bem formados, elementos completos.    |Consulta|string|
|category|(vazio)|Opcional. Uma seqüência que contém a categoria (domínio) da tradução. O padrão é `general`.|Consulta|string|
|usuário|(vazio)|Obrigatórios. Uma seqüência que é usada para rastrear o criador da submissão.|Consulta|string|
|uri|(vazio)|Opcional. Uma seqüência que contém o local de conteúdo da tradução.|Consulta|string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.  |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410|`AddTranslation` não é mais suportada.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Após 31 de janeiro de 2018, este método não aceitará novas alegações de sentença. Você receberá uma mensagem de erro. Consulte o edital sobre alterações no Quadro de Tradução Colaborativa (CTF).

Adiciona uma série de traduções à memória de tradução. Este método é uma `AddTranslation`versão de matriz de .

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Aqui está o formato do corpo de solicitação:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Esses elementos `AddtranslationsRequest`estão em:

* `AppId`: obrigatório. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `AppId` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .
* `From`: obrigatório. Uma seqüência que contém o código de idioma da língua de origem. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `To`: obrigatório. Uma seqüência que contém o código de idioma da linguagem de destino. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `Translations`: obrigatório. Uma matriz de traduções para adicionar à memória de tradução. Cada tradução `OriginalText`deve `TranslatedText`conter `Rating`, e . O tamanho máximo `OriginalText` `TranslatedText` de cada um é de 1.000 caracteres. O total `OriginalText` de `TranslatedText` todos e elementos não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é 100.
* `Options`: obrigatório. Um conjunto de `Category`opções, incluindo, `ContentType`e `Uri` `User`. `User` é obrigatório. `Category`, `ContentType`e `Uri` são opcionais. Os elementos especificado devem estar listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`AddTranslationArray`método conseguiu. 

Após 31 de janeiro de 2018, as alegações de sentença não serão aceitas. O serviço responderá com o código de erro 410.

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410    |`AddTranslation` não é mais suportada.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Quebra uma seção de texto em frases e retorna uma matriz que contém os comprimentos de cada frase.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor de retorno:** Uma matriz de inteiros que representa os comprimentos das sentenças. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de inteiros que representa os comprimentos das sentenças. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

inteiro

Tipo de conteúdo de resposta: aplicativo/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta| string|
|text|(vazio)   |Obrigatórios. Uma seqüência que representa o texto para dividir em frases. O tamanho máximo do texto é de 10.000 caracteres.|Consulta|string|
|Linguagem   |(vazio)    |Obrigatórios. Uma seqüência que representa o código de idioma do texto de entrada.|Consulta|string|
|Autorização|(vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.   |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500|Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma matriz de traduções para um dado par de idiomas do armazenamento e do mecanismo de MT. `GetTranslations`difere de `Translate` que ele retorna todas as traduções disponíveis.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo da solicitação inclui `TranslationOptions` o objeto opcional, que tem esse formato:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

O `TranslateOptions` objeto contém os valores na lista a seguir. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.

* `Category`: Uma seqüência que contém a categoria (domínio) da tradução. O padrão é `general`.
* `ContentType`: A única opção suportada, `text/plain`e o padrão, é .
* `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais de uma alternativa deve ser devolvida do motor MT. Os valores válidos são `true` e `false` (sensíveis a maiúsculas e minúsculas). O padrão `false`é , que retorna apenas uma alternativa. A configuração `true` da bandeira permite a criação de alternativas artificiais, totalmente integradas ao Framework de Tradução Colaborativa (CTF). O recurso permite o retorno de alternativas para frases que não têm traduções em CTF adicionando alternativas artificiais da *lista n-melhor*do decodificador.
    - Avaliações. As classificações são aplicadas assim: 
         - A melhor tradução automática tem uma classificação de 5.
       - As alternativas da CTF refletem a autoridade do revisor. Eles variam de -10 a +10.
       - As alternativas de tradução geradas automaticamente *(n*-melhor) têm uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas retornadas pode ser tão `maxTranslations`alto quanto o valor especificado em , mas pode ser menor.
    - Pares de idiomas. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Translator.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: filtre os resultados por esse URI. Se nenhum valor for definido, o padrão será `all`.
* `User`: filtre os resultados por esse usuário. Se nenhum valor for definido, o padrão será `all`.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Aqui está o formato da resposta:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Esta resposta inclui `GetTranslationsResponse` um elemento que contém os seguintes valores:

* `Translations`: Uma matriz das correspondências `TranslationMatch` encontradas, armazenadas em objetos (descritas na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, partidas difusas em seguida.
* `From`: Se o método não `From` especificar um idioma, esse valor virá da detecção automática de idiomas. Caso contrário, será a `From` linguagem especificada.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Contém o valor `TranslateOptions` fornecido no parâmetro.

O `TranslationMatch` objeto consiste nesses valores:

* `Error`: O código de erro, se ocorrer um erro para uma seqüência de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica o quão próximo o texto de entrada corresponde ao texto original encontrado na loja. O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas. O valor devolvido varia de 0 a 100, onde 0 não é similar idade e 100 é uma correspondência exata, sensível a maiúsculas.
* `MatchedOriginalText`: texto original correspondente para esse resultado. Esse valor só é devolvido se o texto original compatível for diferente do texto de entrada. É usado para retornar o texto fonte de uma correspondência difusa. Esse valor não é devolvido para os resultados do Microsoft Translator.
* `Rating`: indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Os resultados da Tradução automática têm uma classificação de 5. Traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas com autoridade geralmente terão uma classificação de 6 a 10.
* `Count`: o número de vezes que essa tradução com essa classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: o texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Um `GetTranslationsResponse` objeto no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatórios. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho for `appid` usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .|Consulta|string|
|text|(vazio)|Obrigatórios. Uma seqüência que representa o texto para traduzir. O tamanho máximo do texto é de 10.000 caracteres.|Consulta|string|
|de|(vazio)|Obrigatórios. Uma seqüência que representa o código de idioma do texto que está sendo traduzido.|Consulta|string|
|para |(vazio)    |Obrigatórios. Uma seqüência que representa o código de idioma do idioma para traduzir o texto para.|Consulta|string|
|maxTranslations|(vazio)|Obrigatórios. Um inteiro que representa o número máximo de traduções para retornar.|Consulta|inteiro|
|Autorização| (vazio)|Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|string|  cabeçalho|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Por favor, forneça-nos a data aproximada & hora da solicitação e `X-MS-Trans-Info`com o ID de solicitação incluído no cabeçalho de resposta .|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera vários candidatos de tradução para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Aqui está o formato do corpo de solicitação:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest`inclui esses elementos:

* `AppId`: obrigatório. Se `Authorization` o cabeçalho `AppId` for usado, deixe o campo vazio. Caso contrário, inclua uma `"Bearer" + " " + "access_token"`seqüência que contenha .
* `From`: obrigatório. Uma seqüência que representa o código de idioma do texto que está sendo traduzido.
* `MaxTranslations`: obrigatório. Um inteiro que representa o número máximo de traduções para retornar.
* `Options`: opcional. Um `Options` objeto que contém os seguintes valores. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - `Category`: Uma seqüência que contém a categoria (domínio) da tradução. O padrão é `general`.
    - `ContentType`: A única opção suportada, `text/plain`e o padrão, é .
    - `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais de uma alternativa deve ser devolvida do motor MT. Os valores válidos são `true` e `false` (sensíveis a maiúsculas e minúsculas). O padrão `false`é , que retorna apenas uma alternativa. Definir a `true` bandeira para permitir a geração de alternativas artificiais na tradução, totalmente integrada ao Framework de Traduções Colaborativas (CTF). O recurso permite o retorno de alternativas para frases que não têm alternativas no CTF adicionando alternativas artificiais da *lista n-melhor*do decodificador.
        - Classificações As classificações são aplicadas assim:
          - A melhor tradução automática tem uma classificação de 5.
          - As alternativas da CTF refletem a autoridade do revisor. Eles variam de -10 a +10.
          - As alternativas de tradução geradas automaticamente *(n*-melhor) têm uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas retornadas pode ser tão `maxTranslations`alto quanto o valor especificado em , mas pode ser menor.
        - Pares de idiomas. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Translator.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: filtre os resultados por esse URI. Se nenhum valor for definido, o padrão será `all`.
* `User`: filtre os resultados por esse usuário. Se nenhum valor for definido, o padrão será `all`.
* `Texts`: obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é dez.
* `To`: obrigatório. Uma seqüência que representa o código de idioma do idioma para traduzir o texto para.

Você pode omiti-lo elementos opcionais. Os elementos que `GetTranslationsArrayRequest` são filhos diretos devem ser listados em ordem alfabética.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Aqui está o formato da resposta:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Cada `GetTranslationsResponse` elemento contém esses valores:

* `Translations`: Uma matriz das correspondências `TranslationMatch` encontradas, armazenadas em objetos (descritas na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, partidas difusas em seguida.
* `From`: Se o método não `From` especificar um idioma, esse valor virá da detecção automática de idiomas. Caso contrário, será a `From` linguagem especificada.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Contém o valor `TranslateOptions` fornecido no parâmetro.

O `TranslationMatch` objeto contém os seguintes valores:
* `Error`: O código de erro, se ocorrer um erro para uma seqüência de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica o quão próximo o texto de entrada corresponde ao texto original encontrado na loja. O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas. O valor devolvido varia de 0 a 100, onde 0 não é similar idade e 100 é uma correspondência exata, sensível a maiúsculas.
* `MatchedOriginalText`: texto original correspondente para esse resultado. Esse valor só é devolvido se o texto original compatível for diferente do texto de entrada. É usado para retornar o texto fonte de uma correspondência difusa. Esse valor não é devolvido para os resultados do Microsoft Translator.
* `Rating`: indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Os resultados da Tradução automática têm uma classificação de 5. Traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas com autoridade geralmente têm uma classificação de 6 a 10.
* `Count`: o número de vezes que essa tradução com essa classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: o texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicativo/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Necessário se `appid` o campo `Ocp-Apim-Subscription-Key` e o cabeçalho estiverem vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se `appid` o campo `Authorization` e o cabeçalho estiverem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar para API de texto tradutor v3](../migrate-to-v3.md)


