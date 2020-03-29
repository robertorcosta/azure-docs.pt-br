---
title: Referência sdk do leitor imersivo
titleSuffix: Azure Cognitive Services
description: O Immersive Reader SDK contém uma biblioteca JavaScript que permite integrar o Leitor Imersivo ao seu aplicativo.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156396"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guia de referência do Leitor Imersivo SDK

O Immersive Reader SDK contém uma biblioteca JavaScript que permite integrar o Leitor Imersivo ao seu aplicativo.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Lança o Leitor Imersivo dentro de um `iframe` aplicativo web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parâmetros

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `token` | string | O token de autenticação Azure AD. |
| `subdomain` | string | O subdomínio personalizado do recurso Immersive Reader no Azure. |
| `content` | [Conteúdo](#content) | Um objeto contendo o conteúdo a ser mostrado no Leitor Imersivo. |
| `options` | [Opções](#options) | Opções para configurar certos comportamentos do Leitor Imersivo. Opcional. |

### <a name="returns"></a>Retornos

Retorna `Promise<LaunchResponse>`a , que resolve quando o Leitor Imersivo é carregado. A `Promise` solução para [`LaunchResponse`](#launchresponse) um objeto.

### <a name="exceptions"></a>Exceções

O retornado `Promise` será rejeitado [`Error`](#error) com um objeto se o Leitor Imersivo não for carregado. Para obter mais informações, consulte os [códigos de erro](#error-codes).

## <a name="close"></a>close

Fecha o Leitor Imersivo.

Um exemplo de caso de uso para esta ```hideExitButton: true``` função é se o botão de saída estiver oculto definindo as [opções](#options). Em seguida, um botão diferente (por exemplo, a ```close``` seta traseira de um cabeçalho móvel) pode chamar essa função quando ela é clicada.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderBotões

Esta função estilo e atualiza os elementos do botão Leitor Imersivo do documento. Se ```options.elements``` for fornecido, esta função renderizará ```options.elements```botões dentro . Caso contrário, os botões serão renderizados dentro dos elementos ```immersive-reader-button```do documento que têm a classe .

Esta função é automaticamente chamada pelo SDK quando a janela é carregada.

Consulte [Atributos Opcionais](#optional-attributes) para obter mais opções de renderização.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parâmetros

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `options` | [Opções de renderButtons](#renderbuttonsoptions) | Opções para configurar certos comportamentos da função renderButtons. Opcional. |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no Leitor Imersivo.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Parte

Um único pedaço de dados, que será passado para o conteúdo do Leitor Imersivo.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>Resposta de lançamento

Contém a resposta da `ImmersiveReader.launchAsync`chamada para .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy enum

Um enum usado para definir a política para o uso de cookies do Immersive Reader. Veja [opções](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Tipos de MIME suportados

| Tipo MIME | Descrição |
| --------- | ----------- |
| texto/sem formatação | Texto simples. |
| texto/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicação/mathml+xml | Linguagem de Marcação Matemática (MathML). [Saiba mais](./how-to/display-math.md).
| aplicativo/vnd.openxmlformats-officedocument.wordprocessingml.document | Documento de formato Microsoft Word .docx.

### <a name="html-support"></a>Suporte HTML

| HTML | Conteúdo suportado |
| --------- | ----------- |
| Estilos de fonte | Negrito, Itálico, Sublinhado, Código, Strikethrough, Superscript, Subscrito |
| Listas não ordenadas | Disco, Círculo, Quadrado |
| Listas encomendadas | Decimal, Alfa Superior, Baixo-Alfa, Romano-Superior, Baixo-Romano |
| Hiperlinks | Em breve |

As tags sem suporte serão renderizadas comparativamente. Imagens e tabelas não são suportadas no momento.

### <a name="options"></a>Opções

Contém propriedades que configuram certos comportamentos do Leitor Imersivo.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>Opções de renderButtons

Opções para renderizar os botões do Leitor Imersivo.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Erro

Contém informações sobre o erro.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Códigos do Erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é `message` inválido, veja os detalhes. |
| Tempo limite | O Leitor Imersivo não conseguiu carregar dentro do intervalo especificado. |
| TokenExpired | O token fornecido está expirado. |
| Acelerado | O limite da taxa de chamada foi excedido. |

## <a name="launching-the-immersive-reader"></a>Lançando o Leitor Imersivo

O SDK fornece estilo padrão para o botão de inicialização do Leitor Imersivo. Use `immersive-reader-button` o atributo de classe para habilitar esse estilo. Veja [este artigo](./how-to-customize-launch-button.md) para mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Use os seguintes atributos para configurar a aparência do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text` ou `iconAndText`. Usa `icon` como padrão. |
| `data-locale` | Define o local. Por exemplo, `en-US` ou `fr-FR`. Padrão para `en`inglês . |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. Padrão para 20px. |

## <a name="browser-support"></a>Suporte ao navegador

Use as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o Immersive Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Próximas etapas

* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Quickstart: Crie um aplicativo web que lance o Immersive Reader (C#)](./quickstart.md)
