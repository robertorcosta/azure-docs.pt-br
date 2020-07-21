---
title: Problemas conhecidos – gêmeos digital do Azure
description: Obtenha ajuda para reconhecer e atenuar problemas conhecidos com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530972"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemas conhecidos no Azure digital gêmeos

Este artigo fornece informações sobre problemas conhecidos associados ao Azure digital gêmeos.

## <a name="managing-event-routes-in-the-azure-portal"></a>Gerenciando rotas de eventos no portal do Azure

Se você tiver entrado no portal com um [**MSA (conta Microsoft pessoal)**](https://account.microsoft.com/account/Account), como uma *@outlook.com* conta, verá uma tela informando *que precisa de permissão para exibir as rotas de eventos* ao tentar gerenciar as rotas de eventos no portal, independentemente do seu nível de permissão.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Captura de tela da portal do Azure do erro de permissão ao tentar criar rotas de eventos em uma instância do gêmeos digital do Azure":::

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Os usuários que atualmente não conseguem gerenciar rotas de eventos no portal ainda podem gerenciar rotas de eventos usando a CLI ou APIs do gêmeos digital do Azure. Alternar para uma dessas ferramentas para o gerenciamento de rota de eventos é a estratégia recomendada para atenuar esse problema.

As instruções para isso podem ser encontradas em [*como: gerenciar pontos de extremidade e rotas*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Possíveis causas

Você entrou no portal com um [MSA (conta Microsoft pessoal)](https://account.microsoft.com/account/Account), como uma *@outlook.com* conta. O gerenciamento de rotas de eventos no portal do Azure está atualmente disponível apenas para usuários do Azure em contas de domínio corporativo.

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