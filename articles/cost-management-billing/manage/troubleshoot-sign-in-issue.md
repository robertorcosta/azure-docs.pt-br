---
title: Solucionar problemas de credenciais da assinatura do Azure
description: Ajuda a resolver os problemas em que você não consegue entrar no portal do Azure ou no Centro de Contas do Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 2e9b14fa264f3286134913e3c279c4400ce5bcc3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132322"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Solucionar problemas de credenciais da assinatura do Azure

Este guia ajuda a resolver os problemas em que você não consegue entrar no portal do Azure ou no Centro de Contas do Azure.

> [!NOTE]
> Se você estiver com problemas para se inscrever em uma nova conta do Azure, confira [Solucionar problemas de inscrição de assinatura do Azure](./troubleshoot-azure-sign-up.md).

## <a name="page-hangs-in-the-loading-status"></a>A página para de responder no status de carregamento

Se a página do navegador da Internet parar, experimente cada uma das etapas a seguir até chegar ao portal do Azure.

- Atualize a página.
- Use um navegador de Internet diferente.
- Use o modo de navegação privada do seu navegador:

   - **Edge:** abra **Configurações** (os três pontos por sua imagem de perfil), selecione **Nova janela InPrivate** e navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [Centro de contas do Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** escolha o modo **Incognito**.
   - **Safari:** escolha **Arquivo** e então **Nova Janela Particular**.

- Limpe o cache e exclua cookies da Internet:

   - **Edge:** abra **Configurações** e selecione **Privacidade e Serviços**. Siga as etapas em **Limpar Dados de Navegação**. Verifique se as caixas de seleção **Histórico de navegação**, **Histórico de download** e **Imagens e arquivos armazenados em cache** estão marcadas e selecione **Excluir**.
   - **Chrome:** escolha **Configurações** e selecione **Limpar dados de navegação** em **Privacidade e Segurança**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Você está automaticamente conectado como um usuário diferente

Esse problema poderá ocorrer se você estiver usando mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente usar um dos seguintes métodos:

- Limpe o cache e exclua cookies da Internet.

   - **Edge:** abra **Configurações** e selecione **Privacidade e Serviços**. Siga as etapas em **Limpar Dados de Navegação**. Verifique se as caixas de seleção **Histórico de navegação**, **Histórico de download**, **Cookies** e **Imagens e arquivos armazenados em cache** estão marcadas e selecione **Excluir**.
   - **Chrome:** escolha **Configurações** e selecione **Limpar dados de navegação** em **Privacidade e Segurança**.
- Redefina as configurações do navegador para os padrões.
- Use o modo de navegação privada do seu navegador. 
   - **Edge:** abra **Configurações** (os três pontos por sua imagem de perfil), selecione **Nova janela InPrivate** e navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [Centro de contas do Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** escolha o modo **Incognito**.
   - **Safari:** escolha **Arquivo** e então **Nova Janela Particular**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Consigo entrar, mas vejo o erro "Nenhuma assinatura encontrada"

Esse problema ocorrerá se tiver selecionado no diretório errado ou se sua conta não tiver permissões suficientes.

**Cenário 1:** Você recebe o erro ao entrar no [portal do Azure](https://portal.azure.com/)

Para corrigir esse problema:

- Verifique se o diretório correto do Azure está selecionado selecionando em sua conta no canto superior direito.
- Se o diretório correto do Azure estiver selecionado, mas você continuar recebendo a mensagem de erro, solicite que a sua conta seja [adicionada como Proprietário](./add-change-subscription-administrator.md).

**Cenário 2:** Você recebe o erro ao entrar no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta usada é o Administrador da Conta. Para verificar quem é o Administrador da Conta, siga estas etapas:

1.  Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Selecione a assinatura que você deseja verificar e, em seguida, selecione **Configurações**.
1.  Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para a Cobrança e as Assinaturas do Azure

- [Cartão recusado](./troubleshoot-declined-card.md)
- [Problemas no cadastro da assinatura](./troubleshoot-azure-sign-up.md)
- [Nenhuma assinatura encontrada](./no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Documentação de cobrança do Azure](../index.yml)

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).