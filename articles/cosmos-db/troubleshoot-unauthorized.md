---
title: Solucionar problemas Azure Cosmos DB exceção não autorizada
description: Como diagnosticar e corrigir exceções não autorizadas
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293945"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceção não autorizada

HTTP 401: a assinatura MAC encontrada na solicitação HTTP não é a mesma que a assinatura computada.
Se você recebeu a seguinte mensagem de erro 401: "A assinatura MAC encontrada na solicitação HTTP não é igual à assinatura computada". pode ser causado pelos cenários a seguir.

SDKs mais antigos a exceção pode aparecer como uma exceção JSON inválida em vez da exceção correta de 401 não autorizada. Os SDKs mais recentes lidam corretamente com esse cenário e apresentam uma mensagem de erro válida.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceção não autorizada.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. a chave não foi girada corretamente é o cenário mais comum.
A assinatura MAC 401 é vista logo após um revezamento de chave e eventualmente é interrompida sem alterações. 

#### <a name="solution"></a>Solução:
A chave foi revezada e não segue as [melhores práticas](secure-access-to-data.md#key-rotation). A rotação de chave de conta Cosmos DB pode levar de alguns segundos a possivelmente dias, dependendo do tamanho da conta de Cosmos DB.

### <a name="2-the-key-is-misconfigured"></a>2. a chave está configurada incorretamente 
401 o problema de assinatura MAC será consistente e acontecerá para todas as chamadas que usam essa chave

#### <a name="solution"></a>Solução:
A chave está configurada incorretamente no aplicativo e está usando a chave incorreta para a conta ou a chave inteira não foi copiada.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. o aplicativo está usando as chaves somente leitura para operações de gravação
401 o problema de assinatura MAC está ocorrendo apenas para operações de gravação, como criar ou substituir, mas a solicitação de leitura foi realizada com sucesso.

#### <a name="solution"></a>Solução:
Alterne o aplicativo para usar uma chave de leitura/gravação para permitir que as operações sejam concluídas com êxito.

### <a name="4-race-condition-with-create-container"></a>4. condição de corrida com criar contêiner
401 o problema de assinatura MAC é visto logo após uma criação de contêiner. Isso só ocorre até que a criação do contêiner seja concluída.

#### <a name="solution"></a>Solução:
Há uma condição de corrida com a criação de contêiner. Uma instância do aplicativo está tentando acessar o contêiner antes que a criação do contêiner seja concluída. O cenário mais comum para essa condição de corrida é se o aplicativo estiver em execução e o contêiner for excluído e recriado com o mesmo nome. O SDK tentará usar o novo contêiner, mas, como a criação do contêiner ainda está em andamento, ele não terá as chaves.

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)