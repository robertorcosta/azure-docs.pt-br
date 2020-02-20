---
title: Solucionar problemas de credenciais da assinatura do Azure
description: Ajuda a resolver os problemas em que você não consegue entrar no portal do Azure ou no Centro de Contas do Azure.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199476"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Solucionar problemas de credenciais da assinatura do Azure

Este guia ajuda a resolver os problemas em que você não consegue entrar no portal do Azure ou no Centro de Contas do Azure.

## <a name="issues"></a>Problemas

### <a name="page-hangs-in-the-loading-status"></a>A página para de responder no status de carregamento

Se a página do navegador da Internet parar, experimente cada uma das etapas a seguir até chegar ao portal do Azure.

- Atualize a página.
- Use um navegador de Internet diferente.
- Use o modo de navegação privada do seu navegador. Para Internet Explorer: Clique em **Ferramentas** > **Segurança** > **Navegação InPrivate** e, em seguida, navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [Centro de Contas do Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Você está automaticamente conectado como um usuário diferente

Esse problema poderá ocorrer se você estiver usando mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente usar um dos seguintes métodos:

- Limpe o cache e exclua cookies da Internet. No Internet Explorer, clique em **Ferramentas** > **Opções da Internet** > **Excluir**. Verifique se as caixas de seleção dos arquivos temporários, dos cookies, das senha e do histórico de navegação estão marcadas e clique em Excluir.
- Redefina as configurações do Internet Explorer para reverter todas as configurações pessoais que você tenha feito. Clique em **Ferramentas** > **Opções da Internet** > **Avançado** > selecione a caixa **Excluir configurações pessoais** > **Redefinir**.
- Use o modo de navegação privada do seu navegador. Para Internet Explorer:  Clique em **Ferramentas** > **Segurança** > **Navegação InPrivate** e, em seguida, navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [Centro de Contas do Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Consigo entrar, mas não vejo *Nenhuma assinatura encontrada*

Esse problema ocorrerá se tiver selecionado no diretório errado ou se sua conta não tiver permissões suficientes.

**Cenário 1:** A mensagem de erro é recebida no [portal do Azure](https://portal.azure.com/)

Para corrigir esse problema:

- Verifique se o diretório correto do Azure está selecionado clicando em sua conta no canto superior direito.
- Se o diretório correto do Azure estiver selecionado, mas você continuar recebendo a mensagem de erro, solicite que a sua conta seja [adicionada como Proprietário](add-change-subscription-administrator.md).

**Cenário 2:** A mensagem de erro é recebida no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta usada é o Administrador da Conta. Para verificar quem é o Administrador da Conta, siga estas etapas:

1. Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.

3. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para a Cobrança e as Assinaturas do Azure

- [Cartão recusado](troubleshoot-declined-card.md)
- [Problemas no cadastro da assinatura](troubleshoot-azure-sign-up.md)
- [Nenhuma assinatura encontrada](no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- [Documentação de cobrança do Azure](../../billing/index.md)
