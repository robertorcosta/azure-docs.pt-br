---
title: Início rápido – Criar e gerenciar tokens de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerenciar identidades e tokens de acesso usando a biblioteca de clientes da Administração dos Serviços de Comunicação do Azure.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943234"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Início Rápido: Criar e gerenciar tokens de acesso

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure com a biblioteca de clientes da Administração dos Serviços de Comunicação para provisionar e gerenciar seus tokens de acesso. Os tokens de acesso permitem que suas bibliotecas de clientes de Chat e Chamada se autentiquem diretamente nos Serviços de Comunicação do Azure. Esses tokens são gerados em um serviço de provisionamento de token no servidor que você implementa. Em seguida, eles são usados para inicializar as bibliotecas de clientes dos Serviços de Comunicação em dispositivos cliente.

Observe que todos os preços vistos nas imagens ao longo deste tutorial são apenas para fins de exemplo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

A saída do aplicativo descreve cada ação concluída:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Gerenciar identidades
> * Emitir tokens de acesso
> * Usar a biblioteca do cliente da Administração dos Serviços de Comunicação


> [!div class="nextstepaction"]
> [Adicionar chamada de voz ao aplicativo](./voice-video-calling/getting-started-with-calling.md)

Você também pode querer:

 - [Aprender sobre autenticação](../concepts/authentication.md)
 - [Adicionar o chat ao seu aplicativo](./chat/get-started.md)
 - [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
