---
title: A API de gerenciamento de sessão REST
description: Descreve como gerenciar sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681240"
---
# <a name="use-the-session-management-rest-api"></a>Use a API REST de gerenciamento de sessão

Para usar a funcionalidade de renderização remota do Azure, você precisa criar uma *sessão*. Cada sessão corresponde a uma máquina virtual (VM) sendo alocada no Azure e esperando que um dispositivo cliente se conecte. Quando um dispositivo se conecta, a VM renderiza os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação da sessão, você escolhe em que tipo de servidor deseja executar, que determina os preços. Uma vez que a sessão não é mais necessária, ela deve ser interrompida. Se não for parado manualmente, ele será desligado automaticamente quando o tempo de *locação* da sessão expirar.

Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamado *RenderingSession.ps1*, que demonstra o uso do nosso serviço. O script e sua configuração são descritos aqui: [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Os comandos PowerShell listados nesta página são feitos para complementar um ao outro. Se você executar todos os scripts em seqüência dentro do mesmo prompt de comando PowerShell, eles serão construídos em cima um do outro.

## <a name="regions"></a>Regiões

Consulte a [lista de regiões disponíveis](../reference/regions.md) para as URLs base para enviar as solicitações.

Para os roteiros de amostra abaixo escolhemos a região *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Exemplo de script: Escolha um ponto final

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Contas

Se você não tiver uma conta de renderização remota, [crie uma](create-an-account.md). Cada recurso é identificado por um *accountId*, que é usado durante toda a sessão APIs.

### <a name="example-script-set-accountid-and-accountkey"></a>Script de exemplo: Definir accountId e accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Cabeçalhos de solicitação comuns

* O cabeçalho *de* autorização`Bearer TOKEN`deve`TOKEN`ter o valor de " ", onde " " é o token de autenticação [devolvido pelo Secure Token Service](tokens.md).

### <a name="example-script-request-a-token"></a>Exemplo de script: Solicite um token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

* O cabeçalho *MS-CV* pode ser usado pela equipe do produto para rastrear a chamada dentro do serviço.

## <a name="create-a-session"></a>Criar uma sessão

Este comando cria uma sessão. Ele retorna o ID da nova sessão. Você precisa do ID da sessão para todos os outros comandos.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**Solicitar corpo:**

* maxLeaseTime (timepan): um valor de tempo quando a VM será desativada automaticamente
* modelos (matriz): URLs de contêiner de ativos para pré-carregar
* tamanho (string): o tamanho da VM **("padrão"** ou **"premium").** Consulte [limitações](../reference/limits.md#overall-number-of-polygons)específicas do tamanho da VM .

**Respostas:**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Sucesso |

### <a name="example-script-create-a-session"></a>Exemplo de script: Crie uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Exemplo de script: Store sessionId

A resposta da solicitação acima inclui uma **sessionId**, que você precisa para todas as solicitações de acompanhamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Atualize uma sessão

Este comando atualiza os parâmetros de uma sessão. Atualmente, você só pode estender o tempo de locação de uma sessão.

> [!IMPORTANT]
> O tempo de locação é sempre dado como um tempo total desde o início da sessão. Isso significa que se você criou uma sessão com um tempo de locação de uma hora, e você quiser estender seu tempo de locação por mais uma hora, você tem que atualizar seu maxLeaseTime para duas horas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*sessionId* | PATCH |

**Solicitar corpo:**

* maxLeaseTime (timepan): um valor de tempo quando a VM será desativada automaticamente

**Respostas:**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | | Sucesso |

### <a name="example-script-update-a-session"></a>Exemplo de script: Atualize uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Obter sessões ativas

Este comando retorna uma lista de sessões ativas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**Respostas:**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - sessões: matriz de propriedades de sessão | consulte a seção "Obter propriedades de sessão" para obter uma descrição das propriedades da sessão |

### <a name="example-script-query-active-sessions"></a>Exemplo de script: Consultas sessões ativas

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Obtenha propriedades de sessões

Este comando retorna informações sobre uma sessão, como seu nome de host VM.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**Respostas:**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | - mensagem: string<br/>- sessionElapsedTime: timepan<br/>- sessionNome de host: string<br/>- sessionId: string<br/>- sessãoMaxLeaseTime: timespan<br/>- sessionTamanho: enum<br/>- sessãoStatus: enum | enum sessionStatus { partida, pronto, parando, parado, expirado, erro}<br/>Se o status for 'erro' ou 'expirado', a mensagem conterá mais informações |

### <a name="example-script-get-session-properties"></a>Script de exemplo: Obtenha propriedades de sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Pare uma sessão

Este comando interrompe uma sessão. A VM alocada será recuperada logo depois.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId* | Delete (excluir) |

**Respostas:**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 204 | | Sucesso |

### <a name="example-script-stop-a-session"></a>Exemplo de script: Pare uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Próximas etapas

* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
