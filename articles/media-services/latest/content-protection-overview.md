---
title: Proteja seu conteúdo com criptografia dinâmica v3 do Media Services
titleSuffix: Azure Media Services
description: Saiba mais sobre proteção de conteúdo com criptografia dinâmica, protocolos de streaming e tipos de criptografia no Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461105"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Proteja seu conteúdo com criptografia dinâmica do Media Services

Use o Azure Media Services para ajudar a proteger sua mídia desde o momento em que ela deixa seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Se o conteúdo for criptografado com uma chave clara AES e for enviado por HTTPS, ele não será claro até que chegue ao cliente. 

No Media Services v3, uma chave de conteúdo está associada ao Streaming Locator (veja [este exemplo](protect-with-aes128.md)). Se usar o serviço de entrega chave do Media Services, você pode permitir que o Azure Media Services gere a chave de conteúdo para você. A chave de conteúdo deve ser gerada você mesmo se você estiver usando seu próprio serviço de entrega chave, ou se você precisa lidar com um cenário de alta disponibilidade onde você precisa ter a mesma chave de conteúdo em dois data centers.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de entrega de chave dos Serviços de Mídia ou do serviço de entrega de chaves especificado. Para decidir se o usuário está autorizado a obter a chave, o serviço avalia a política-chave de conteúdo especificada para a chave.

Você pode usar o API REST ou biblioteca de cliente de Serviços de Mídia para configurar políticas de autenticação e autorização para suas licenças e chaves.

A imagem a seguir ilustra o fluxo de trabalho para proteção de conteúdo do Media Services:

![Fluxo de trabalho para proteção de conteúdo do Media Services](./media/content-protection/content-protection.svg)
  
&#42; *a criptografia Dynamic suporta a tecla clara AES-128, CBCS e CENC. Para obter detalhes, consulte a [matriz de suporte](#streaming-protocols-and-encryption-types).*

Este artigo explica conceitos e terminologia que ajudam você a entender a proteção de conteúdo com os Serviços de Mídia.

## <a name="main-components-of-a-content-protection-system"></a>Principais componentes de um sistema de proteção de conteúdo

Para concluir com sucesso seu sistema de proteção de conteúdo, você precisa entender completamente o escopo do esforço. As seções a seguir dão uma visão geral de três partes que você precisa implementar.

> [!NOTE]
> Recomendamos que você se concentre e teste totalmente cada parte nas seguintes seções antes de passar para a próxima parte. Para testar seu sistema de proteção de conteúdo, use as ferramentas especificadas nas seções.

### <a name="media-services-code"></a>Código dos Serviços de Mídia
  
A [amostra DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra como implementar um sistema multi-DRM com o Media Services v3 usando o .NET. Ele também mostra como usar o serviço de entrega de licença/chave dos Serviços de Mídia.
  
Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Para ver o que faz sentido combinar, consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

O exemplo mostra como:

1. Crie e configure uma [política de chave de conteúdo](content-key-policy-concept.md).

   Você cria uma política de chave de conteúdo para configurar como a chave de conteúdo (que fornece acesso seguro aos seus ativos) é entregue aos clientes finais:  

   * Defina a autorização de entrega de licenças. Especifique a lógica da verificação de autorização com base em sinistros no JSON Web Token (JWT).
   * Configure as licenças [PlayReady,](playready-license-template-overview.md) [Widevine](widevine-license-template-overview.md)e/ou [FairPlay.](fairplay-license-overview.md) Os modelos permitem configurar direitos e permissões para cada um dos DRMs.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Crie um [localizador de streaming](streaming-locators-concept.md) configurado para transmitir o ativo criptografado.
  
   O localizador de streaming deve estar associado a uma [política de streaming.](streaming-policy-concept.md) No exemplo, estabelecemos `StreamingLocator.StreamingPolicyName` a política de "Predefined_MultiDrmCencStreaming".

   As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use a política "Predefined_MultiDrmStreaming".

   O localizador de streaming também está associado à política de chave de conteúdo definida.

3. Crie um token de teste.

   O `GetTokenAsync` método mostra como criar um token de teste.
4. Crie a URL de streaming.

   O `GetDASHStreamingUrlAsync` método mostra como construir a URL de streaming. Nesse caso, os fluxos de URL o conteúdo DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Jogador com um cliente AES ou DRM

Um aplicativo de player de vídeo baseado em um player SDK (nativo ou baseado em navegador) precisa atender aos seguintes requisitos:

* O Player SDK suporta os clientes DRM necessários.
* O SDK do jogador suporta os protocolos de streaming necessários: Smooth, DASH e/ou HTTP Live Streaming (HLS).
* O jogador SDK pode lidar com a passagem de um token JWT em uma solicitação de aquisição de licença.

Você consegue criar um player usando a [API do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Use a [API ProtectionInfo do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas de DRM.

Para testar conteúdo criptografado AES ou CENC (Widevine e/ou PlayReady), é possível usar o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer). Certifique-se de selecionar **opções avançadas** e verificar suas opções de criptografia.

Se você quiser testar o conteúdo do FairPlay criptografado, use [esse teste player](https://aka.ms/amtest). O jogador suporta RDMs Widevine, PlayReady e FairPlay, juntamente com criptografia de chave clara AES-128.

Escolha o navegador certo para testar diferentes DRMs:

* Chrome, Opera ou Firefox para Widevine.
* Microsoft Edge ou Internet Explorer 11 para PlayReady.
* Safari no macOS para FairPlay.

### <a name="security-token-service"></a>Serviço de token de segurança

Um serviço de token de segurança (STS) emite o JWT como o token de acesso ao back-end para acesso a recursos back-end. Você pode usar o serviço de entrega de chaves do Azure Media Services como recurso back-end. Um STS tem que definir as seguintes coisas:

* Emissor e audiência (ou escopo).
* Reivindicações, que dependem dos requisitos do negócio na proteção de conteúdo.
* Verificação simétrica ou assimétrica para verificação de assinatura.
* Suporte de capotamento de chave (se necessário).

Você pode usar [esta ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS. Ele suporta todos os três tipos de chaves de verificação: simétrica, assimétrica ou Azure Active Directory (Azure AD) com rolagem de chaves.

## <a name="streaming-protocols-and-encryption-types"></a>Tipos de criptografia e protocolos de streaming

É possível usar os Serviços de Mídia para entregar conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM usando o PlayReady, o Widevine ou o FairPlay. Atualmente, você pode criptografar os formatos HLS, MPEG DASH e Smooth Streaming. Cada protocolo suporta os seguintes métodos de criptografia.

### <a name="hls"></a>HLS

O protocolo HLS suporta os seguintes formatos de contêiner e esquemas de criptografia:

|Formato de contêiner|Esquema de criptografia|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

O HLS/CMAF + FairPlay (incluindo HEVC/H.265) é suportado nos seguintes dispositivos:

* iOS 11 ou posterior.
* iPhone 8 ou posterior.
* MacOS High Sierra com CPU Intel de 7ª Geração.

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH suporta os seguintes formatos de contêiner e esquemas de criptografia:

|Formato de contêiner|Esquema de criptografia|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

O protocolo Smooth Streaming suporta os seguintes formatos de contêiner e esquemas de criptografia.

|Protocolo|Formato de contêiner|Esquema de criptografia|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Navegadores

Os navegadores comuns suportam os seguintes clientes DEM:

|Navegador|Criptografia|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controlando o acesso ao conteúdo

É possível controlar quem tem acesso ao seu conteúdo, configurando a política de chave de conteúdo. Os serviços de mídia oferecem suporte a várias maneiras de autorizar os usuários que fazem solicitações de chave. O cliente (player) deve estar em conformidade com a política antes de a chave poder ser entregue ao cliente. A política de chave de conteúdo pode ter *abra* ou *token* restrição.

Uma política de chave de conteúdo com restrição aberta pode ser usada quando você deseja emitir licença para qualquer pessoa sem autorização. Por exemplo, se sua receita for baseada em anúncios e não baseada em assinatura.  

Com uma política de chave de conteúdo restrita a tokens, a chave de conteúdo é enviada apenas para um cliente que apresenta um token JWT válido ou um simples token web (SWT) na solicitação de licença/chave. Este token deve ser emitido por um STS.

Você pode usar o Azure AD como um STS ou implantar um [STS personalizado](#using-a-custom-sts). O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de entrega de chaves do Media Services devolve a licença ou a chave solicitadas ao cliente se ambas as condições existirem:

* O token é válido.
* As reivindicações no token correspondem às configuradas para a licença ou chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou o recurso ao que o token autoriza o acesso. O serviço de licença/entrega chave do Media Services valida que esses valores no token correspondem aos valores do modelo.

### <a name="token-replay-prevention"></a>Prevenção de replay de token

O recurso *Token Replay Prevention* permite que os clientes do Media Services definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar `urn:microsoft:azure:mediaservices:maxuses` uma reivindicação de tipo no token, onde o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todas as solicitações subseqüentes com o mesmo token para entrega de chaves retornarão uma resposta não autorizada. Veja como adicionar a reivindicação na [amostra DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controle sobre a geração de tokens. A reivindicação precisa ser colocada no próprio token.
* Ao usar esse recurso, solicitações com tokens cujo tempo de validade é de mais de uma hora do momento em que a solicitação é recebida são rejeitadas com uma resposta não autorizada.
* Os tokens são identificados exclusivamente por sua assinatura. Qualquer alteração na carga útil (por exemplo, atualizar para o tempo de validade ou a reclamação) altera a assinatura do token e contará como um novo token que o Key Delivery não encontrou antes.
* A reprodução falha se o `maxuses` token tiver excedido o valor definido pelo cliente.
* Esse recurso pode ser usado para todos os conteúdos protegidos existentes (apenas o token emitido precisa ser alterado).
* Este recurso funciona tanto com JWT quanto com SWT.

## <a name="using-a-custom-sts"></a>Usando um STS personalizado

Um cliente pode optar por usar um STS personalizado para fornecer tokens. Os motivos incluem:

* O provedor de identidade (IDP) usado pelo cliente não suporta STS. Nesse caso, um STS personalizado poderá ser uma opção.
* O cliente poderá precisar de controle mais flexível ou mais restrito na integração do STS com o sistema de cobrança de assinante do cliente.

   Por exemplo, uma operadora de serviço [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) pode oferecer vários pacotes para assinantes, como premium, básico e esportivo. O operador poderá corresponder as declarações de um token com o pacote do assinante para que somente o conteúdo de um pacote específico seja disponibilizado. Nesse caso, um STS personalizado oferece a flexibilidade e o controle necessários.

* Incluir reivindicações personalizadas no token para selecionar entre diferentes Opções de ConteúdoKeyPolicyOptions com diferentes parâmetros de licença DRM (uma licença de assinatura versus uma licença de aluguel).
* Para incluir uma reivindicação representando o identificador de chave de conteúdo da chave a que o token concede acesso.

Ao usar um STS personalizado, duas alterações devem ser feitas:

* Ao configurar o serviço de entrega de licença para um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado em vez da chave atual do Azure AD.
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual no Azure AD.

Há dois tipos de chaves de segurança:

* Chave simétrica: a mesma chave é usada para gerar e verificar um JWT.
* Chave assimétrica: um par de chaves pública/privada em um certificado X509 é usado com uma chave privada para criptografar/gerar um JWT e, com a chave pública, verificar o token.

Se você usa o .NET Framework /C# como sua plataforma de desenvolvimento, o certificado X509 usado para uma chave assimétrica de segurança deve ter um comprimento de chave de pelo menos 2048. Este comprimento-chave é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção é lançada: IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para assinatura não pode ser menor do que os bits '2048'.

## <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de chaves e licenças personalizadas

Use os seguintes modelos se quiser especificar um serviço de entrega de licença/chave diferente (não serviços de mídia). Os dois campos substituíveis nos modelos estão lá para que você possa compartilhar sua política de streaming em muitos ativos, em vez de criar uma política de streaming por ativo. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Modelo para a URL do serviço personalizado que fornece chaves para jogadores de usuário final. Não é necessário quando você está usando o Azure Media Services para emitir chaves. 

   O modelo suporta tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação.  Os valores de token suportados atualmente são:
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Modelo para a URL do serviço personalizado que fornece licenças para jogadores de usuário final. Não é necessário quando você está usando o Azure Media Services para emitir licenças.

   O modelo suporta tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação. Os valores de token suportados atualmente são:  
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo do modelo anterior, apenas para Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Mesmo modelo anterior, somente para FairPlay.  

Por exemplo: 

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`tem um valor da chave solicitada. Você pode `AlternativeMediaId` usar se quiser mapear a solicitação para uma entidade ao seu lado. Por exemplo, `AlternativeMediaId` pode ser usado para ajudá-lo a procurar permissões.

Para exemplos REST que usam URLs de licença/aquisição de chaves personalizadas, consulte [Políticas de streaming - Criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="troubleshoot"></a>Solução de problemas

Se você `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` tiver o erro, certifique-se de especificar a política de streaming apropriada.

Se você tiver erros `_NOT_SPECIFIED_IN_URL`que terminam com , certifique-se de especificar o formato de criptografia na URL. Um exemplo é `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Proteger com criptografia AES](protect-with-aes128.md)
* [Proteger com DRM](protect-with-drm.md)
* [Projete sistema de proteção de conteúdo multi-DRM com controle de acesso](design-multi-drm-system-with-access-control.md)
* [Criptografia do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas frequentes](frequently-asked-questions.md)
* [Manipulador de token Web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
