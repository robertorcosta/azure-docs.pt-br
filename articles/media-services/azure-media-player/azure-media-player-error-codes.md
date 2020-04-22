---
title: Códigos de erro do Azure Media Player
description: Uma referência de código de erro para O Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727249"
---
# <a name="error-codes"></a>Códigos do Erro #

Quando a reprodução não pode ser iniciada ou interrompida, `error()` um evento de erro será acionado e a função retornará um código e uma mensagem opcional para ajudar o desenvolvedor do aplicativo a obter mais detalhes. `error().message`não é a mensagem exibida para o usuário.  A mensagem exibida ao usuário `error().code` é baseada nos bits 27-20, veja tabela abaixo.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Códigos de erro, bits [31-28] (4 bits) ##

Descreva a área do erro.

- 0 – desconhecido
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - FlashsS
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Códigos de erro, bits [27-0] (28 bits) ##

Descreva detalhes do erro, bits 27-20 fornecem um alto nível, bits 19-0 fornecem mais detalhes se disponíveis.


| amp.errorCode. [nome] | Códigos, Bits [27-0] (28 bits) | Descrição |
|---|---:|---|
| **MEDIA_ERR_ABORTED faixa de erros (0x0100000 - 0x01FFFFF)** | | |
| abortadoErrUnknown | 0x0100000 | Erro de abortamento genérico |
| abortedErrNãoimplementado | 0x0100001 | Erro abortar, não implementado |
| abortedErrHttpMixedContentBloqueado | 0x0100002 | Erro abortar, conteúdo misto bloqueado - geralmente `http://` ocorre `https://` ao carregar um fluxo de uma página |
| **MEDIA_ERR_NETWORK valor de início de erros (0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Erro de rede genérico |
| networkerrhttpBadUrlformat | 0x0200190 | Resposta de erro http 400 |
| networkerrhttpuserauthrequired | 0x0200191 | Resposta de erro http 401 |
| networkerrhttpuserProibido | 0x0200193 | Resposta de erro http 403 |
| networkerrhttpUrlNotFound | 0x0200194 | Resposta de erro http 404 |
| networkerrhttpNão permitido | 0x0200195 | Resposta de erro http 405 |
| networkerrhttpGone | 0x020019A | Resposta de erro http 410 |
| networkErrhttpPreconditionFailed | 0x020019C | Resposta de erro http 412 |
| networkErrhttpInternalServerFailure | 0x02001F4 | Resposta de erro http 500
| networkerrhttpBadGateway | 0x02001F6 | Resposta de erro http 502 |
| networkErrHttpServiceInam | 0x02001F7 | Resposta de erro http 503 |
| networkerrhttpGatewayTimeout | 0x02001F8 | Resposta de erro http 504 |
| networkErrTimeout | 0x0200258 | Erro de tempo de tempo de rede
| networkErrErr | 0x0200259 | Resposta de erro de conexão de rede |
| **MEDIA_ERR_DECODE erros (0x0300000 - 0x03FFFFF)** | | |
| decodificadordesconhecido | 0x0300000 | Erro genérico de decodificação |
| **MEDIA_ERR_SRC_NOT_SUPPORTED erros (0x0400000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x04000000 | Fonte genérica não suportada erro |
| srcErrParseApresentação | 0x0400001 | Erro de análise de apresentação |
| srcErrParseSegment | 0x0400002 | Erro de análise de segmento |
| srcErrUnsupportedPresentation | 0x0400003 | Apresentação não suportada |
| srcErrInvalidSegment | 0x0400004 | Segmento inválido |
| **MEDIA_ERR_ENCRYPTED de erros de valor inicial (0x0500000 - 0x05FFFFF)** | | |
| criptografadordesconhecido | 0x0500000 | Erro criptografado genérico | 
| encryptErrDecrypterNãoencontrado | 0x0500001 | Decodificador não encontrado |
| encryptErrDecrypterInit | 0x0500002 | Erro de inicialização do descriptografador |
| encryptErrDecrypterNãosuportado | 0x0500003 | Decodificador não suportado |
| encryptErrKeyAcquire | 0x0500004 | Falha na aquisição de chave |
| criptografadorDecryption | 0x0500005 | Falha na descriptografia do segmento |
| encryptErrLicenseAcquire | 0x0500006 | Licença de aquisição falha |
| **SRC_PLAYER_MISMATCH os erros de valor inicial (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Genérico sem jogador de tecnologia correspondente para jogar a fonte |
| srcPlayerMismatchFlashNotInstalado | 0x0600001 |O plugin flash não está instalado, se instalado a fonte pode ser retonada. *OU* O Flash 30 está instalado e reproduzindo conteúdo AES.  Se este for o caso, por favor tente um navegador diferente. Flash 30 não é suportado hoje a partir de 7 de junho. Veja [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes. Nota: Se 0x00600003, tanto o Flash quanto o Silverlight não estiverem instalados, se especificados na techOrder.|
| srcPlayerMismatchSilverlightNotInstalado | 0x0600002 | O plugin Silverlight não está instalado, se instalado a fonte pode ser retonada. Nota: Se 0x00600003, tanto o Flash quanto o Silverlight não estiverem instalados, se especificados na techOrder. |
| | 0x00600003 | Tanto o Flash quanto o Silverlight não estão instalados, se especificados na techOrder. |
| **Erros desconhecidos (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Erros desconhecidos |

## <a name="user-error-messages"></a>Mensagens de erro do usuário ##

A mensagem do usuário exibida é baseada nos bits do código de erro 27-20.

- MEDIA_ERR_ABORTED (1) -"Você abortou a reprodução de vídeo"
- MEDIA_ERR_NETWORK (2) - "Um erro de rede fez com que o download de vídeo falhasse parcialmente."
- MEDIA_ERR_DECODE (3) - "A reprodução de vídeo foi abortada devido a um problema de corrupção ou porque o vídeo usado apresenta recursos que seu navegador não suportava."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"O vídeo não pôde ser carregado, seja porque o servidor ou a rede falharam ou porque o formato não é suportado."
- MEDIA_ERR_ENCRYPTED (5) - "O vídeo é criptografado e não temos as chaves para descriptografá-lo."
- SRC_PLAYER_MISMATCH (6) - "Nenhuma fonte compatível foi encontrada para este vídeo."
- MEDIA_ERR_UNKNOWN (0xFF) - "Ocorreu um erro desconhecido."

## <a name="examples"></a>Exemplos ##

### <a name="0x10600001"></a>0x10600001 ##

"Nenhuma fonte compatível foi encontrada para este vídeo." é exibido para o usuário final.

Não há nenhum jogador de tecnologia que possa reproduzir as fontes solicitadas, mas se o plugin Flash estiver instalado, é provável que uma fonte possa ser reproduzida.  

### <a name="0x20200194"></a>0x20200194 ###

"Um erro de rede fez com que o download de vídeo falhasse parcialmente." é exibido para o usuário final.

O AzureHtml5JS não conseguiu reproduzir uma resposta http 404.

### <a name="categorizing-errors"></a>Categorizando erros ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Pegando um erro específico ###

O código a seguir captura apenas 404 erros:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)