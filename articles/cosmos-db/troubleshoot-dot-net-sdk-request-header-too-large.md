---
title: Solucionar problemas de cabeçalho de solicitação muito grande ou 400 solicitação inadequada no Azure Cosmos DB
description: Como diagnosticar e corrigir a exceção de cabeçalho de solicitação muito grande
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293985"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticar e solucionar problemas Azure Cosmos DB mensagem de cabeçalho de solicitação muito grande
A mensagem cabeçalho de solicitação muito grande é lançada com um código de erro HTTP 400. Esse erro ocorre se o tamanho do cabeçalho da solicitação cresceu muito grande e excede o tamanho máximo permitido. Recomendamos que você use a versão mais recente do SDK. Certifique-se de usar pelo menos a versão 3. x ou 2. x, pois essas versões adicionam o rastreamento de tamanho de cabeçalho à mensagem de exceção.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A mensagem cabeçalho de solicitação muito grande ocorrerá se a sessão ou o token de continuação for muito grande. As seções a seguir descrevem a causa e a solução para o problema em cada categoria.

### <a name="1-session-token-too-large"></a>1. token de sessão muito grande

#### <a name="cause"></a>Causa:
400 uma solicitação inadequada é provavelmente causada pelo token de sessão como grande. Se as instruções a seguir forem verdadeiras, ele confirmará que o token de sessão é muito grande.

* O erro ocorre na operação de ponto como criar, ler, atualizar e etc. onde não há um token de continuação.
* A exceção foi iniciada sem fazer nenhuma alteração no aplicativo. O token de sessão aumenta conforme o número de partições aumenta no contêiner. Os números de partição aumentam conforme a quantidade de dados aumenta ou se a taxa de transferência for aumentada.

#### <a name="temporary-mitigation"></a>Mitigação temporária: 
Reinicie o aplicativo cliente para redefinir todos os tokens de sessão. O token de sessão, eventualmente, será aumentado de volta para o tamanho anterior que causou o problema. Portanto, use a solução na próxima seção para evitar esse problema completamente.

#### <a name="solution"></a>Solução:
1. Siga as orientações no artigo Dicas de desempenho do .NET [v3](performance-tips-dotnet-sdk-v3-sql.md) ou do [.net v2](performance-tips.md) e converta o aplicativo para usar o modo de conexão direta com o protocolo TCP. O modo direto com o protocolo TCP não tem a restrição de tamanho de cabeçalho como o protocolo HTTP, portanto, ele evita esse problema. Certifique-se de usar a versão mais recente do SDK, que tem uma correção para operações de consulta quando a interoperabilidade do serviço não está disponível.
2. Se o modo de conexão direta com o protocolo TCP não for uma opção para sua carga de trabalho, atenue-o alterando o [nível de consistência do cliente](how-to-manage-consistency.md). O token de sessão é usado somente para consistência de sessão, que é o nível de consistência padrão para Azure Cosmos DB. Outros níveis de consistência não usam o token de sessão.

### <a name="2-continuation-token-too-large"></a>2. token de continuação muito grande

#### <a name="cause"></a>Causa:
A solicitação inadequada 400 está ocorrendo em operações de consulta em que o token de continuação é usado. Se o token de continuação ficou muito grande ou se consultas diferentes têm tamanhos de token de continuação diferentes.
    
#### <a name="solution"></a>Solução:
1. Siga as orientações no artigo Dicas de desempenho do .NET [v3](performance-tips-dotnet-sdk-v3-sql.md) ou do [.net v2](performance-tips.md) e converta o aplicativo para usar o modo de conexão direta com o protocolo TCP. O modo direto com o protocolo TCP não tem a restrição de tamanho de cabeçalho como o protocolo HTTP, portanto, ele evita esse problema. 
3. Se o modo de conexão direta com o protocolo TCP não for uma opção para sua carga de trabalho, tente definir a `ResponseContinuationTokenLimitInKb` opção. Você pode encontrar essa opção no `FeedOptions` para v2 ou `QueryRequestOptions` no v3.

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)
