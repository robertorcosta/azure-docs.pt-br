---
title: Usar identidades gerenciadas nos serviços de comunicação
titleSuffix: An Azure Communication Services quickstart
description: Identidades gerenciadas permitem autorizar o acesso dos serviços de comunicação do Azure de aplicativos em execução em VMs do Azure, aplicativos de funções e outros recursos.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657597"
---
# <a name="use-managed-identities"></a>Usar identidades gerenciadas
Introdução aos serviços de comunicação do Azure usando identidades gerenciadas. A identidade dos serviços de comunicação e as bibliotecas de cliente SMS dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este guia de início rápido mostra como autorizar o acesso às bibliotecas de cliente de identidade e SMS de um ambiente do Azure que dá suporte a identidades gerenciadas. Ele também descreve como testar seu código em um ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

 - Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free)
 - Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Ao configurar o

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Habilitar identidades gerenciadas em uma máquina virtual ou serviço de aplicativo

Identidades gerenciadas devem ser habilitadas nos recursos do Azure que você está autorizando. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de aplicativos](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Atribuir funções do Azure com o portal do Azure

1. Navegue até o portal do Azure.
1. Navegue até o recurso serviço de comunicação do Azure.
1. Navegue até o controle de acesso (IAM) menu-> + Add-> adicionar atribuição de função.
1. Selecione a função "colaborador" (essa é a única função com suporte).
1. Selecione "identidade gerenciada atribuída pelo usuário" (ou uma "identidade gerenciada atribuída pelo sistema") e selecione a identidade desejada. Salve sua seleção.

![Função de identidade gerenciada](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Atribuir funções do Azure com o PowerShell

Para atribuir funções e permissões usando o PowerShell, consulte [Adicionar ou remover atribuições de função do Azure usando Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end