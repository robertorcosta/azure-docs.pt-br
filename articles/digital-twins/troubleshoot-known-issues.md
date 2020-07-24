---
title: Problemas conhecidos – gêmeos digital do Azure
description: Obtenha ajuda para reconhecer e atenuar problemas conhecidos com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044141"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemas conhecidos no Azure digital gêmeos

Este artigo fornece informações sobre problemas conhecidos associados ao Azure digital gêmeos.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"erro de cliente 400: solicitação inadequada" em Cloud Shell

Os comandos no Cloud Shell podem falhar intermitentemente com o erro "erro de cliente 400: solicitação inadequada para URL: http://localhost:50342/oauth2/token " seguido por rastreamento de pilha completo.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Isso pode ser resolvido executando novamente o `az login` comando e concluindo as etapas de logon subsequentes.

Depois disso, você deve ser capaz de executar novamente o comando.

### <a name="possible-causes"></a>Possíveis causas

Esse é o resultado de um problema conhecido no Cloud Shell: [*obter o token do Cloud Shell falha intermitentemente com 400 erro de cliente: solicitação inválida*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre segurança e permissões no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)