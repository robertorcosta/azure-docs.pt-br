---
title: Associações do Serviço SignalR do Azure Functions
description: Entenda como usar associações do Serviço SignalR com o Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523029"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associações do Serviço SignalR para o Azure Functions

Este conjunto de artigos explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) usando vinculações do SignalR Service em Funções Azure. O Azure Functions dá suporte a associações de entrada e saída para o Serviço SignalR.

| Ação | Type |
|---------|---------|
| Retorne o URL do ponto final do serviço e o token de acesso | [Vinculação de entrada](./functions-bindings-signalr-service-input.md) |
| Enviar mensagens de serviço signalR |[Vinculação de saída](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione ao seu aplicativo Functions

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as amarras requer que você faça referência ao pacote apropriado. O pacote NuGet é usado para bibliotecas de classe .NET, enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicione por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet,]versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | [Recomenda-se que a extensão Azure Tools] use com o Visual Studio Code. |
| C# Script (somente on-line no portal Azure)         | Adicionando uma vinculação                            | Para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função, consulte [Atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão do Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Para obter detalhes sobre como configurar e usar as funções SignalR Service e Azure em conjunto, consulte o [desenvolvimento e a configuração de funções do Azure com o Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteca de anotações (somente Java)

Para usar as anotações do SignalR Service nas funções Java, você precisa adicionar uma dependência ao artefato *azure-functions-java-library-signalr* (versão 1.0 ou superior) ao seu arquivo *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Próximas etapas

- [Retorne o URL do ponto final de serviço e o token de acesso (vinculação de entrada)](./functions-bindings-signalr-service-input.md)
- [Enviar mensagens de serviço signalR (vinculação de saída)](./functions-bindings-signalr-service-output.md) 
