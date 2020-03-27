---
title: Erro Nenhuma assinatura encontrada – Entrada no portal do Azure
description: Fornece a solução para um problema no qual o erro Nenhuma assinatura encontrada ocorre ao entrar no Portal do Azure ou no centro de contas do Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 709d30d3d8563b07c73658a0a9d96748615eb899
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202872"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Erro de entrada Nenhuma assinatura encontrada no portal do Azure ou no centro de contas do Azure

Você poderá receber uma mensagem de erro "Nenhuma assinatura encontrada" ao tentar entrar no [Portal do Azure](https://portal.azure.com/) ou no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions). Este artigo fornece uma solução para esse problema.

## <a name="symptom"></a>Sintoma

Ao tentar entrar no [portal do Azure](https://portal.azure.com/) ou no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions), a seguinte mensagem de erro é exibida: "Nenhuma assinatura encontrada".

## <a name="cause"></a>Causa

Esse problema ocorrerá se tiver selecionado no diretório errado ou se sua conta não tiver permissões suficientes.

## <a name="solution"></a>Solução

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Cenário 1: A mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)

Para corrigir esse problema:

* Verifique se o diretório correto do Azure está selecionado clicando em sua conta no canto superior direito.

  ![Selecione o diretório na parte superior direita do portal do Azure](./media/no-subscriptions-found/directory-switch.png)
* Se o diretório correto do Azure estiver selecionado, mas você continuar recebendo a mensagem de erro, [atribua a função Proprietário à sua conta](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Cenário 2: A mensagem de erro é recebida no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta usada é o Administrador da Conta. Para verificar quem é o Administrador da Conta, siga estas etapas:

1. Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
1. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
