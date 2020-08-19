---
title: Problemas conhecidos – gêmeos digital do Azure
description: Obtenha ajuda para reconhecer e atenuar problemas conhecidos com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 9130a3248e881c9d4e2c9bfe9017f43198d50f51
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590159"
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

## <a name="missing-role-assignment-after-scripted-setup"></a>Atribuição de função ausente após a instalação com script

Alguns usuários podem enfrentar problemas com a parte de atribuição de função de [*como: configurar uma instância e autenticação (com script)*](how-to-set-up-instance-scripted.md). O script não indica falha, mas a função de *proprietário do Azure digital gêmeos (versão prévia)* não foi atribuída com êxito ao usuário, e isso afetará a capacidade de criar outros recursos no caminho.

Para determinar se a atribuição de função foi configurada com êxito após a execução do script, siga as instruções na seção [*verificar atribuição de função de usuário*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) do artigo de instalação. Se o usuário não for exibido com essa função, esse problema afetará você.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Para resolver o, você pode configurar sua atribuição de função manualmente usando a CLI ou portal do Azure. 

Siga estas instruções:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Possíveis causas

Para usuários conectados com um [MSA (conta Microsoft pessoal)](https://account.microsoft.com/account), a ID principal do usuário que o identifica em comandos como esse pode ser diferente do email de logon do usuário, dificultando a descoberta e o uso do script para atribuir a função corretamente.

## <a name="issue-with-interactive-browser-authentication"></a>Problema com a autenticação interativa do navegador

Ao gravar o código de autenticação em seus aplicativos do Azure digital gêmeos usando a versão mais recente (versão **1.2.0**) da **biblioteca [do Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **, você pode ter problemas com o método [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

O método afetado é usado nos seguintes artigos: 
* [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
* [*Como: usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)

O problema inclui uma resposta de erro "Azure. Identity. AuthenticationFailedException" ao tentar autenticar em uma janela do navegador. A janela do navegador pode falhar na inicialização completa ou parecer autenticar o usuário com êxito, enquanto o aplicativo cliente ainda falha com o erro.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Para resolver, faça com que seus aplicativos usem explicitamente o Azure. Identity versão **1.1.1**. Com esta versão da biblioteca, o navegador deve carregar e autenticar conforme o esperado.

>[!NOTE]
> Não é suficiente adicionar a biblioteca sem especificar nenhuma versão, pois isso ainda usará como padrão a **1.2.0**mais recente. Você precisará especificar a versão **1.1.1** explicitamente.

### <a name="possible-causes"></a>Possíveis causas

Essa é uma incompatibilidade entre o Azure digital gêmeos e a versão mais recente da biblioteca do Azure. Identity, versão **1.2.0**. 

Você verá esse problema se usar a versão **1.2.0** em seu aplicativo ou se adicionar a biblioteca ao seu projeto sem especificar uma versão (pois isso também é o padrão para essa versão mais recente).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre segurança e permissões no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)