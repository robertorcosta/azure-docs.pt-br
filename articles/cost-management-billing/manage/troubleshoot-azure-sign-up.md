---
title: Solucionar problemas de inscrição no Azure
description: Solução de problemas ao tentar se inscrever em uma nova conta no Centro de Contas do portal do Microsoft Azure.
author: v-miegge
ms.reviewer: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: e6f6866cdcfb53782c7dbb18bb76143eb76755f9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200496"
---
# <a name="troubleshoot-azure-sign-up"></a>Solucionar problemas de inscrição no Azure

Você pode ter problemas ao tentar se inscrever em uma nova conta no portal do Microsoft Azure ou no Centro de Contas do Azure. Antes de solucionar o problema, primeiro verifique o seguinte:

- Se as informações que você forneceu em seu perfil de conta do Azure (incluindo o endereço de email de contato, o endereço residencial e o número de telefone) estão corretas.
- Se as informações do cartão de crédito estão corretas.
- Se você já não tem uma conta Microsoft com as mesmas informações.

## <a name="resolutions"></a>Resoluções

Para resolver eventuais erros, selecione o problema enfrentado ao tentar se inscrever no Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Erro: *Não é possível prosseguir com a inscrição devido a um problema na sua conta. Entre em contato com o suporte de cobrança.*

Para resolver o problema, siga estas etapas:

1. Entre no [Centro de Contas do Azure](https://account.azure.com/Profile) usando a credencial do administrador da conta.

2. Selecione em **Editar detalhes**.

3. Certifique-se de que todos os campos de endereço estejam preenchidos e válidos.

4. Ao se inscrever para a assinatura do Azure, certifique-se de que o endereço para cobrança inserido durante o registro do cartão de crédito corresponde os dados do registro bancário.

Se o erro persistir, tente se inscrever usando um navegador diferente.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso trava na seção *Verificação de identidade por cartão*.

Para concluir a verificação de identidade por cartão, cookies de terceiros devem ser permitidos no navegador.

![Verificação de identidade por cartão](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Use as etapas a seguir para atualizar as configurações de cookies do navegador.

1. Se estiver usando o Chrome, selecione **Configurações** > **Mostrar configurações avançadas** > **Privacidade** > **Configurações de conteúdo**. Desmarque a opção **Bloquear cookies e dados de site de terceiros**.

2. Se você estiver usando o Microsoft Edge, selecione **Configurações** > **Exibir Configurações Avançadas** > **Cookies** > **Não bloquear cookies**.

3. Atualize a página de inscrição do Azure e, em seguida, verifique se o problema foi resolvido.

4. Se a atualização não resolver o problema, saia, reinicie o navegador e tente novamente.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário de cartão de crédito não dá suporte ao meu endereço para cobrança

Seu endereço para cobrança precisa estar localizado no país selecionado na seção **Sobre você**. Verifique se selecionou o país correto.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nenhuma mensagem de texto ou chamada durante a verificação da conta de inscrição

Embora o processo seja normalmente rápido, pode levar até quatro minutos para que o código de verificação seja entregue. O número de telefone inserido para verificação não é armazenado como um número de contato da conta.

Estas são algumas outras dicas:

- Um número de telefone VoIP (Voz sobre IP) não pode ser usado para o processo de verificação por telefone.
- Confira o número de telefone inserido, incluindo o código do país selecionado no menu suspenso.
- Se o telefone não receber mensagens de texto (SMS), tente a opção **Ligar para mim**.
- Verifique se o telefone pode receber chamadas ou mensagens SMS de um número de telefone localizado nos Estados Unidos.

Ao receber a mensagem de texto ou a chamada telefônica, insira o código recebido na caixa de texto.

### <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito recusado ou não aceito

Cartões de crédito ou débito pré-pagos ou virtuais não são aceitos como forma de pagamento para as assinaturas do Azure. Para ver o que mais pode fazer com que seu cartão seja recusado, confira [Seu cartão de débito ou crédito foi recusado durante a inscrição no Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>A Avaliação Gratuita não está disponível

Você já usou uma assinatura do Azure anteriormente? O Contrato de Termos de Uso do Azure limita a ativação gratuita da avaliação para usuários que sejam novos no Azure. Se você já teve qualquer outro tipo de assinatura do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever-se em uma [assinatura Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Observei um encargo em minha conta de Avaliação Gratuita

Você poderá ter uma pequena demora na verificação da sua conta de cartão de crédito após a inscrição. Isso é resolvido dentro de três a cinco dias. Se estiver preocupado com o gerenciamento de custos, leia mais sobre como [evitar custos inesperados](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Não consigo ativar um plano de benefícios do Azure como o MSDN, o BizSpark, o BizSparkPlus ou o MPN

Verifique se você está usando as credenciais de conexão corretas. Em seguida, verifique o programa de benefícios para confirmar que está qualificado.

- MSDN
  - Verifique o status de qualificação na [página da conta do MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se não conseguir verificar seu status, entre em contato com os [Centros de Atendimento ao Cliente das Assinaturas do MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft para Startups
  - Entre no [portal do Microsoft para Startups](https://startups.microsoft.com/#start-two) para verificar seu status de qualificação para o Microsoft para Startups.
  - Se não conseguir verificar seu status, você pode obter ajuda nos [fóruns do Microsoft para Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Entre no [portal do MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) para verificar seu status de qualificação. Se tiver as [Competências de Plataforma de Nuvem](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)adequadas, você poderá estar qualificado para receber outros benefícios.
  - Se não conseguir verificar seu status, entre em contato com o [suporte do MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Não consigo ativar uma nova assinatura do Azure via Open

Para criar uma assinatura do Azure via Open, você precisa ter uma chave OSA (Ativação de Serviço Online) válida com pelo menos um token do Azure via Open associado a ela. Se você não tiver uma chave OSA, entre em contato com um dos Parceiros da Microsoft listados no [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Erro: *Você não está qualificado para uma assinatura do Azure*

Para resolver esse problema, verifique se os seguintes itens:

- Se as informações que você forneceu em seu perfil de conta do Azure (incluindo o endereço de email de contato, o endereço residencial e o número de telefone) estão corretas.
- Se as informações do cartão de crédito estão corretas.
- Se você já não tem uma conta Microsoft que usa as mesmas informações.

### <a name="error-your-current-account-type-is-not-supported"></a>Erro: *Seu tipo de conta atual não é compatível*

Esse problema poderá ocorrer se a conta estiver registrada em um [diretório não gerenciado do Azure AD](../../active-directory/users-groups-roles/directory-self-service-signup.md) e se não estiver no diretório do Azure AD da sua organização.

Para resolver esse problema, inscreva-se na conta do Azure usando outra conta ou assuma o controle do diretório do AD não gerenciado. Para obter mais informações, confira [Controlar um diretório não gerenciado como administrador no Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md).

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para a Cobrança e as Assinaturas do Azure

- [Cartão recusado](troubleshoot-declined-card.md)
- [Problemas na entrada da assinatura](troubleshoot-sign-in-issue.md)
- [Nenhuma assinatura encontrada](no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- [Documentação de cobrança do Azure](../../billing/index.md)
