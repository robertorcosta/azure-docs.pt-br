---
title: Administração do Portal do EA do Azure
description: Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 33a53fa46d7d07183b77f2608d44f8ea5d0d2804
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132254"
---
# <a name="azure-ea-portal-administration"></a>Administração do Portal do EA do Azure

Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure (https://ea.azure.com). O Portal do EA do Azure é um portal de gerenciamento online que ajuda os clientes a gerenciar o custo dos respectivos serviços de EA do Azure. Para obter informações introdutórias sobre o Portal do EA do Azure, confira o artigo [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os administradores corporativos podem associar contas existentes a Departamentos no registro.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione **Departamento**.
1. Posicione o cursor sobre a linha com a conta e selecione o ícone de lápis à direita.
1. Escolha o departamento no menu suspenso.
1. Clique em **Salvar**.

## <a name="department-spending-quotas"></a>Cotas de gastos do departamento

Os clientes do EA podem definir ou alterar as cotas de gastos para cada departamento em um registro. O valor da cota de gastos é definido para o termo do pagamento antecipado atual. No final do termo do pagamento antecipado atual, o sistema estenderá a cota de gastos existente para o próximo termo do pagamento antecipado, a menos que os valores sejam atualizados.

O administrador do departamento pode exibir a cota de gastos, mas somente o administrador corporativo pode atualizar o valor da cota. O administrador corporativo e o administrador do departamento receberão notificações assim que a cota atingir 50%, 75%, 90% e 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Para o administrador corporativo definir a cota:

 1. Abra o Portal do EA do Azure.
 1. Selecione **Gerenciar** na área de navegação à esquerda.
 1. Selecione a guia **Departamento**.
 1. Selecione o Departamento.
 1. Selecione o símbolo de lápis na seção Detalhes do Departamento ou o símbolo **+ Adicionar Departamento** para adicionar uma cota de gastos juntamente com um novo departamento.
 1. Em Detalhes do Departamento, insira um valor para a cota de gastos na moeda do registro na caixa $ da Cota de Gastos (o valor precisa ser maior que 0).
    - As propriedades Nome do Departamento e Centro de Custo também podem ser editadas nesse momento.
 1. Clique em **Salvar**.

A cota de gastos do departamento agora estará visível no modo de exibição Lista de Departamentos na guia Departamento. No final do pagamento antecipado atual, o Portal do Azure EA manterá as cotas de gastos para o próximo termo do pagamento antecipado.

O valor da cota de departamento é independente do pagamento antecipado do Azure atual, e o valor e os alertas da cota se aplicam somente ao uso interno. A cota de gastos do departamento serve meramente para fins informativos e não impõe limites de gastos.

### <a name="department-administrator-to-view-the-quota"></a>Para o administrador do departamento exibir a cota:

1. Abra o Portal do EA do Azure.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Departamento** e veja a exibição Lista de Departamentos com as cotas de gastos.

Se você for um cliente indireto, os recursos de custos deverão ser habilitados pelo seu parceiro de canal.

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

O Portal do EA do Azure ajuda você a administrar seus custos e uso do EA do Azure. Há três funções principais no Portal do EA do Azure:

- Administrador de EA
- Administrador de departamento
- Proprietário da conta

Cada função tem um nível diferente de acesso e de autoridade.

Para saber mais sobre as funções de usuário, confira [Funções de usuário corporativo](./ea-portal-get-started.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta de EA do Azure

A conta do EA do Azure é uma unidade organizacional no Portal do EA do Azure. Ela é usada para administrar assinaturas e também é usada para relatórios. Para acessar e usar os serviços do Azure, é necessário que você crie uma conta ou que uma seja criada para você.

Para obter mais informações sobre as contas do Azure, confira [Adicionar uma conta](./ea-portal-get-started.md#add-an-account).

## <a name="enterprise-devtest-offer"></a>Oferta de Desenvolvimento/Teste Enterprise

Como administrador corporativo do Azure, agora você pode permitir que os proprietários da conta na sua organização criem assinaturas com base na oferta Desenvolvimento/Teste do EA. Para fazer isso, marque a caixa Desenvolvimento/Teste do proprietário da conta no Portal do EA do Azure.

Depois de marcar a caixa Desenvolvimento/Teste, informe o proprietário da conta, de modo que ele possa configurar as assinaturas de Desenvolvimento/Teste do EA necessárias para as respectivas equipes de assinantes de Desenvolvimento/Teste.

Essa oferta permite que os assinantes do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure com taxas especiais de Desenvolvimento/Teste. Ela fornece acesso à galeria completa de imagens de Desenvolvimento/Teste, incluindo Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar a oferta de Desenvolvimento/Teste Enterprise:

1. Entre como administrador corporativo.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Conta**.
1. Selecione a linha da conta em que deseja habilitar o acesso ao Desenvolvimento/Teste.
1. Selecione o símbolo de lápis à direita da linha.
1. Marque a caixa de seleção Desenvolvimento/Teste.
1. Clique em **Salvar**.

Quando um usuário é adicionado como um proprietário de conta por meio do Portal do EA do Azure, todas as assinaturas do Azure associadas ao proprietário da conta que se baseiam na oferta de Desenvolvimento/Teste do PAYG ou as ofertas de crédito mensal para assinantes do Visual Studio serão convertidas na oferta de Desenvolvimento/Teste do EA. As assinaturas baseadas em outros tipos de oferta, como PAYG, associadas ao Proprietário da Conta serão convertidas em ofertas do Microsoft Azure Enterprise.

No momento, a oferta de Desenvolvimento/Teste não é aplicável aos clientes do Azure Gov.

## <a name="delete-subscription"></a>Excluir assinatura

Para excluir uma assinatura na qual você seja o proprietário da conta:

1. Entre no portal do Azure com as credenciais associadas a sua conta.
1. No menu Hub, selecione **Assinaturas**.
1. Na guia de assinaturas no canto superior esquerdo da página, escolha a assinatura que deseja cancelar e selecione **Cancelar Assinatura** para iniciar a guia de cancelamento.
1. Insira o nome da assinatura, escolha um motivo para o cancelamento e selecione o botão **Cancelar Assinatura**.

Só os administradores das contas podem cancelar assinaturas.

Para obter mais informações, confira [O que acontecerá depois que eu cancelar minha assinatura?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Excluir uma conta

A remoção da conta só poderá ser concluída para contas ativas sem assinaturas ativas.

1. No Enterprise Portal, escolha **Gerenciar** no painel de navegação à esquerda.
1. Selecione a guia **Conta**.
1. Na tabela Contas, escolha a conta que você deseja excluir.
1. Selecione o símbolo X à direita da linha Conta.
1. Quando não houver assinaturas ativas na conta, selecione **Sim** na linha Conta para confirmar a remoção da Conta.

## <a name="update-notification-settings"></a>Atualizar configurações de notificação

Os administradores corporativos são registrados automaticamente para receber notificações de uso associadas ao registro deles. Cada administrador corporativo pode alterar o intervalo das notificações individuais ou pode desativá-las por completo.

Os contatos de notificação são mostrados no Portal do EA do Azure na seção **Contato de Notificação**. O gerenciamento de seus contatos de notificação garante que as pessoas certas em sua organização obtenham as notificações do EA do Azure.

Para exibir as configurações de notificações atuais:

1. No Portal do EA do Azure, navegue até **Gerenciar** > **Contato de Notificação**.
2. Endereço de email – o endereço de email associado à conta Microsoft, corporativa ou de estudante do administrador corporativo que recebe as notificações.
3. Frequência de notificação de saldo não cobrado – a frequência que é definida para que as notificações sejam enviadas para cada administrador corporativo individual.

Para adicionar um contato:

1. Selecione **+Adicionar Contato**.
2. Insira o endereço de email e depois confirme-o.
3. Clique em **Salvar**.

O novo contato de notificação é exibido na seção **Contato de Notificação**. Para alterar a frequência de notificação, selecione o contato de notificação e selecione o símbolo de lápis à direita da linha selecionada. Defina a frequência como **diária**, **semanal**, **mensal** ou **nenhuma**.

Você pode suprimir a _data de término do período de cobertura_ e _desabilitar e desprovisionar as notificações de data de término do ciclo de vida se aproximando_. Desabilitar as notificações de ciclo de vida suprime as notificações sobre o período de cobertura e a data de término do contrato.

## <a name="azure-sponsorship-offer"></a>Oferta do Azure Sponsorship

A oferta do Azure Sponsorship é uma conta Microsoft Azure limitada patrocinada. Ela está disponível como um convite por email, somente para clientes limitados selecionados pela Microsoft. Se estiver qualificado para a oferta do Microsoft Azure Sponsorship, você receberá um convite por email para aceitar a oferta para sua ID de conta.

Para saber mais, crie uma [solicitação de suporte para a ativação do patrocínio](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversão para autenticação da conta corporativa ou de estudante

Os usuários do Azure Enterprise podem converter de um tipo de autenticação de Conta Microsoft (MSA ou Live ID) para uma Conta Corporativa ou de Estudante (que usa o Active Directory no Azure).

Para começar:

1. adicione a conta corporativa ou de estudante ao Portal do EA do Azure na função necessária.
1. Se receber erros, é possível que a conta não seja válida no Active Directory.  O Azure usa o nome UPN, que nem sempre é idêntico ao endereço de email.
1. Autentique-se no portal do EA do Azure usando a conta corporativa ou de estudante.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para converter subscrições de contas Microsoft em contas corporativas ou de estudante:

1. Entre no portal de gerenciamento usando a conta Microsoft que tem as assinaturas.
1. Use a transferência de propriedade da conta para mover para a nova conta.
1. A conta Microsoft já deve estar livre de todas as assinaturas ativas e pode ser excluída.
1. Todas as contas excluídas ficarão no modo de exibição do portal com status inativo para fins de cobrança histórica.  Para filtrá-las no modo de exibição, marque a caixa de seleção para mostrar apenas as contas ativas.

## <a name="account-subscription-ownership-faq"></a>Perguntas frequentes sobre a propriedade da assinatura da conta

Este documento responde às perguntas frequentes relacionadas à propriedade da assinatura da conta.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários da conta do Azure você pode ter por assinatura?

Somente um proprietário de conta é permitido por assinatura.  Outras funções podem ser adicionadas usando o Azure RBAC (controle de acesso baseado em função) na guia da assinatura no canto superior esquerdo da página do [portal do Azure](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode ser listado em mais de um departamento?

Não. Um proprietário da conta só pode estar associado a um departamento individual. A política ajuda a garantir o monitoramento preciso e a repartição dos custos e dos gastos associados ao departamento ao qual o proprietário está alinhado no registro do EA no Portal do EA do Azure.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não, um proprietário de assinatura deve ser uma autenticação exclusiva da conta Microsoft (MSA) ou do Azure Active Directory (AAD). Para explicar a sucessão em sua organização, considere criar contas genéricas e usar o AAD para gerenciar o acesso à assinatura.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um usuário individual pode ter várias assinaturas?

Um proprietário de conta do Azure pode criar e gerenciar um número ilimitado de assinaturas.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso acessar/ver todas as assinaturas da minha organização?

Atualmente, isso deve ser feito pela política, ou seja, você precisará exigir que, para cada assinatura criada, sua conta seja adicionada a uma função de assinatura por meio do Azure RBAC (controle de acesso baseado em função).

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde posso criar uma assinatura?

Antes de criar uma assinatura de oferta do Azure Enterprise (EA), sua conta precisará ser adicionada à função de proprietário da conta pelo administrador do registro do EA no Portal do EA do Azure. Em seguida, você precisará entrar no Portal do EA do Azure para obter o direito de criar assinaturas do tipo de oferta do EA. Recomendamos que a sua primeira assinatura do EA seja criada por meio do link '+ Adicionar Assinatura' na guia de assinaturas no Portal do EA.  No entanto, depois que a conta estiver autorizada, é provável que fique mais fácil criar assinaturas em portal.azure.com na guia de assinaturas no canto superior esquerdo da página. Lá, você poderá criar e renomear sua assinatura em uma única etapa.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma assinatura?

Para criar uma assinatura do tipo de oferta do Azure Enterprise, você deve estar qualificado na função de proprietário da conta no [portal do EA](https://ea.azure.com).

## <a name="next-steps"></a>Próximas etapas

- Leia sobre como as [reservas de máquina virtual](ea-portal-vm-reservations.md) podem ajudá-lo a economizar dinheiro.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](ea-portal-troubleshoot.md).