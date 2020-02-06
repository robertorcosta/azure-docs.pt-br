---
title: Administração do Portal do EA do Azure
description: Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 88daa5913838511be59ce7a30b7e9430d8865a62
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023371"
---
# <a name="azure-ea-portal-administration"></a>Administração do Portal do EA do Azure

Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure (https://ea.azure.com). O Portal do EA do Azure é um portal de gerenciamento online que ajuda os clientes a gerenciar o custo dos respectivos serviços de EA do Azure. Para obter informações introdutórias sobre o Portal do EA do Azure, confira o artigo [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm mais privilégios ao gerenciar um registro de EA do Azure. O administrador inicial do EA do Azure foi criado quando o contrato de EA foi configurado. No entanto, você pode adicionar ou remover novos administradores a qualquer momento. Novos administradores são adicionados somente por administradores existentes. Para obter mais informações sobre como adicionar outros admins corporativos, confira [Criar outro admin corporativo](ea-portal-get-started.md#create-another-enterprise-administrator). Para saber mais sobre funções e tarefas do perfil de cobrança, confira [Funções e tarefas do perfil de cobrança](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Atualizar estado do usuário de pendente para ativo

Quando novos AOs (proprietários de conta) são adicionados a um registro de EA do Azure pela primeira vez, o status deles aparece como _pendente_. Quando um novo proprietário da conta receber o email de ativação de boas-vindas, ele poderá entrar para ativar a própria conta. Assim que ele ativa a própria conta, o status da conta é atualizado de _pendente_ para _ativo_. O proprietário da conta precisa ler a mensagem de 'Aviso' e clicar em **Continuar**. Pode ser solicitado de novos usuários que insiram o nome e sobrenome deles para criar uma conta de comércio. Nesse caso, eles devem adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um administrador de departamento

Depois que um administrador de EA cria um departamento, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. Novas contas são necessárias para que as assinaturas de EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, confira [Criar um administrador de departamento do Azure EA](ea-portal-get-started.md#add-a-department-administrator).

## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os administradores corporativos podem associar contas existentes a Departamentos no registro.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique em **Departamento**.
1. Passe o cursor do mouse sobre a linha com a Conta desejada e clique no ícone de lápis à direita.
1. Escolha o Departamento desejado na lista suspensa.
1. Clique em **Save** (Salvar).

## <a name="department-spending-quotas"></a>Cotas de gastos do departamento

Os clientes do EA podem definir ou alterar cotas de gastos para cada departamento em um registro. O valor da cota de gastos é definido para o termo do compromisso atual. No final do atual termo de compromisso, o sistema estenderá a cota de gastos existente para o próximo termo de compromisso, a não ser que os valores sejam atualizados.

O administrador do departamento pode exibir a cota de gastos, mas somente o administrador corporativo pode atualizar o valor da cota. O administrador corporativo e o administrador do departamento receberão notificações assim que a cota atingir 50%, 75%, 90% e 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Para o administrador corporativo definir a cota:

 1. Abra o Portal do EA do Azure.
 1. Clique em **Gerenciar** na área de navegação à esquerda.
 1. Clique na guia **Departamento**.
 1. Clique no Departamento desejado.
 1. Clique no ícone de lápis na seção Detalhes do Departamento ou clique no botão **+ Adicionar Departamento** para adicionar uma cota de gastos juntamente com um novo departamento.
 1. Em Detalhes do Departamento, insira um valor para a cota de gastos na moeda do registro na caixa $ da Cota de Gastos (o valor deve ser maior do que 0).
    - As propriedades Nome do Departamento e Centro de Custo também podem ser editadas nesse momento.
 1. Pressione **Salvar**.

A cota de gastos do departamento agora estará visível no modo de exibição Lista de Departamentos na guia Departamento. No final do compromisso atual, o Portal do EA do Azure manterá as cotas de gastos para o próximo termo de compromisso.

O valor da cota de departamento é independente do compromisso monetário atual e o valor e os alertas da cota se aplicam somente ao uso da primeira parte. A cota de gastos do departamento serve meramente para fins informativos e não impõe limites de gastos.

### <a name="department-administrator-to-view-the-quota"></a>Para o administrador do departamento exibir a cota:

1. Abra o Portal do EA do Azure.
1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Departamento** e mostre o modo de exibição Lista de departamentos com cotas de gastos.

Se você for um cliente indireto, os recursos de custos deverão ser habilitados pelo seu parceiro de canal.

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

O Portal do EA do Azure ajuda você a administrar seus custos e uso do EA do Azure. Há três funções principais no Portal do EA do Azure:

- Administrador de EA
- Administrador de departamento
- Proprietário da conta

Cada função tem um nível diferente de acesso e de autoridade.

Para saber mais sobre as funções de usuário, confira [Funções de usuário corporativo](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta de EA do Azure

A conta de EA do Azure é uma unidade organizacional no Portal do EA do Azure que é usada para administrar assinaturas e também é usada para fazer relatórios. Para acessar e usar os serviços do Azure, é necessário que você crie uma conta ou que uma seja criada para você.

Para obter mais informações sobre as contas do Azure, confira Adicionar uma conta.

## <a name="enterprise-devtest-offer"></a>Oferta de Desenvolvimento/Teste Enterprise

Como administrador corporativo do Azure, agora você pode permitir que os proprietários da conta na organização criem assinaturas com base na oferta Desenvolvimento/Teste do EA. Para fazer isso, marque a caixa Desenvolvimento/Teste desse proprietário no Portal do EA do Azure.

Depois de marcar a caixa Desenvolvimento/Teste, informe o proprietário da conta para que ele possa configurar as assinaturas de Desenvolvimento/Teste do EA necessárias para suas equipes de assinantes de Desenvolvimento/Teste.

Essa oferta permite que assinantes ativos do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure em taxas especiais de Desenvolvimento/Teste, com acesso à galeria completa de imagens de Desenvolvimento/Teste, incluindo Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar a oferta de Desenvolvimento/Teste Enterprise:

1. Entre como administrador corporativo.
1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Conta**.
1. Clique na linha da conta em que você gostaria de habilitar o acesso ao Desenvolvimento/Teste.
1. Clique no ícone de lápis à direita da linha.
1. Marque a caixa de seleção Desenvolvimento/Teste.
1. Pressione **Salvar**.

Quando um usuário é adicionado como um proprietário de conta por meio do Portal do EA do Azure, todas as assinaturas do Azure associadas ao proprietário da conta que se baseiam na oferta de Desenvolvimento/Teste do PAYG ou as ofertas de crédito mensal para assinantes do Visual Studio serão convertidas na oferta de Desenvolvimento/Teste do EA. As assinaturas baseadas em outros tipos de oferta, como PAYG, associadas ao Proprietário da Conta serão convertidas em ofertas do Microsoft Azure Enterprise.

No momento, a oferta de Desenvolvimento/Teste não se aplica aos clientes do Azure Gov.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta empresarial para um novo registro

Uma transferência de conta move um proprietário da conta de um registro para outro. Todas as assinaturas relacionadas no proprietário da conta serão movidas para o registro de destino. Isso é feito quando você tem vários registros ativos e só deseja mover os proprietários da conta selecionados.

Tenha os pontos a seguir em mente ao transferir uma conta empresarial para um novo registro:

- Somente as contas especificadas na solicitação são transferidas. Se todas as contas forem escolhidas, elas serão todas transferidas.
- O registro de origem retém o próprio status como ativo ou estendido. Você poderá continuar usando o registro até que ele expire.

### <a name="prerequisites"></a>Prerequisites

Ao solicitar uma transferência de conta, forneça as seguintes informações:

- O número do registro de destino, o nome da conta e o email do proprietário da conta a ser transferido
- Para o registro de origem, o número de registro e a conta a serem transferidos

Outros pontos para ter em mente antes de uma transferência de conta:

- A aprovação de um administrador de EA é necessária para o registro de origem e o de destino
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de registro.
- A transferência de conta transfere todos os serviços e assinaturas relacionados às contas específicas.
- Depois que a transferência for concluída, a conta transferida aparecerá inativa no registro de origem e aparecerá ativa no registro de destino.
- A conta mostra a data de término correspondente à data de transferência efetiva no registro de origem e como uma data de início no registro de destino.
- Qualquer uso ocorrido para a conta antes da data de transferência efetiva permanece sob o registro de origem.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir Registro Enterprise para um novo registro

Uma transferência de registro é considerada quando:

- O termo de compromisso de um registro atual chegou ao fim.
- Um registro está no status expirado/estendido e um novo contrato é negociado.
- Se você tiver vários registros e desejar consolidar todas as contas e a cobrança em um só registro.

Quando você solicita a transferência de um Registro Enterprise inteiro para um registro, as seguintes ações ocorrem:

- Todos os serviços, as assinaturas, as contas, os departamentos e toda a estrutura de registro do Azure, incluindo todos os administradores de departamento do EA, são transferidos para um novo registro de destino.
- O status do registro é definido como _Transferido_. O registro transferido está disponível apenas para fins de relatório de uso histórico.
- Você não pode adicionar funções nem assinaturas a um registro transferido. O status de transferido impede o uso adicional em relação ao registro.
- Qualquer saldo de compromisso monetário restante no contrato é perdido, incluindo termos futuros.
-   Se o registro do qual você está transferindo tiver compras de RI, o valor de compra de RI permanecerá no registro de origem, no entanto, todos os benefícios de RI serão transferidos para serem utilizados no novo registro.
-   O valor avulso de compra do Marketplace e todos os valores fixos mensais já incorridos no registro antigo não serão transferidos para o novo registro. As cobranças do Marketplace baseadas no consumo serão transferidas.

### <a name="effective-transfer-date"></a>Data de início de vigência da transferência

A data de efetivação da transferência pode ser a data de início do registro de destino ou uma data posterior a ela.

O uso do registro de origem é cobrado em relação ao compromisso monetário ou como excedente. O uso que ocorre após a data de início de vigência da transferência é transferido para o novo registro e cobrado de acordo.

### <a name="prerequisites"></a>Prerequisites

Ao solicitar uma transferência de registro, forneça as seguintes informações:

- Para o registro de origem, o número de registro.
- Para o registro de destino, o número de registro no qual transferir.
- A data de início de vigência da transferência do registro pode ser uma data igual ou posterior à data de início do registro de destino. A data escolhida não pode afetar o uso de nenhuma fatura excedente já emitida.

Outros pontos para ter em mente antes de uma transferência de registro:

- É necessária a aprovação dos administradores do EA do registro de origem e de destino.
- Se uma transferência de registro não atender aos seus requisitos, considere uma transferência de conta.
- O status do registro de origem será atualizado para transferido e só estará disponível para fins de relatório de uso histórico.

### <a name="monetary-commitment"></a>Compromisso monetário

O compromisso monetário não é transferível entre registros. Os saldos de compromisso monetário são associados contratualmente ao registro em que ele foi pedido. O compromisso monetário não é transferido como parte do processo de transferência do registro ou da conta.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Nenhum serviço afetado para transferências de conta e registro

Não há tempo de inatividade durante uma transferência de conta ou de registro. Ela poderá ser concluída no mesmo dia da solicitação se todas as informações necessárias forem fornecidas.

## <a name="change-account-owner"></a>Alterar proprietário da conta

O Portal do EA do Azure pode transferir assinaturas de um proprietário de conta para outro. Para obter mais informações, confira [Alterar proprietário da conta](ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Efeitos da transferência de assinatura

Quando uma assinatura do Azure é transferida para uma conta no mesmo locatário do Azure Active Directory, todos os usuários, grupos e entidades de serviço que tinham [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) para gerenciar recursos na assinatura retêm os respectivos acessos.

Para exibir os usuários com acesso RBAC à assinatura:

1. No portal do Azure, abra **Assinaturas**.
2. Selecione a assinatura que deseja exibir e, em seguida, selecione **Controle de Acesso (IAM)** .
3. Selecione **Atribuições de função**. A página de atribuições de função lista todos os usuários que têm acesso RBAC à assinatura.

Se a assinatura for transferida para uma conta em um locatário diferente do Azure AD, todos os usuários, grupos e entidades de serviço que tiverem [RBAC](../../role-based-access-control/overview.md) para gerenciar recursos _perderão_ o acesso. Embora o acesso RBAC não esteja presente, o acesso à assinatura pode estar disponível por meio de mecanismos de segurança, incluindo:

- Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../../cloud-services/cloud-services-certs-create.md).
- Chaves de acesso para serviços como Armazenamento. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../../storage/common/storage-account-overview.md).
- Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se o destinatário precisar restringir o acesso a seus recursos do Azure, ele deverá considerar a atualização dos segredos associados ao serviço. A maioria dos recursos pode ser atualizada usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **Todos os recursos**.
3. Selecione o recurso.
4. Na página de recursos, clique em **Configurações** para exibir e atualizar os segredos existentes.

## <a name="delete-subscription"></a>Excluir assinatura

Para excluir uma assinatura em que você é o proprietário da conta:

1. Entre no portal do Azure com as credenciais associadas a sua conta.
1. No menu Hub, selecione **Assinaturas**.
1. Na guia de assinaturas no canto superior esquerdo da página, escolha a assinatura que você deseja cancelar e clique em **Cancelar Assinatura** para iniciar a guia de cancelamento.
1. Insira o nome da assinatura, escolha um motivo para o cancelamento e clique no botão **Cancelar Assinatura**.

Observe que somente administradores das contas podem cancelar assinaturas.

## <a name="delete-an-account"></a>Excluir uma conta

A remoção da conta só poderá ser concluída para contas ativas sem assinaturas ativas.

1. No Enterprise Portal, escolha **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Conta**.
1. Na tabela Contas, escolha a conta que você deseja excluir.
1. Clique no ícone X à direita da linha Conta.
1. Quando não houver assinaturas ativas na conta, clique no botão **Sim** na linha Conta para confirmar a remoção da Conta.

## <a name="update-notification-settings"></a>Atualizar configurações de notificação

Os administradores corporativos são registrados automaticamente para receber notificações de uso associadas ao registro deles. Cada administrador corporativo pode alterar o intervalo das notificações individuais ou pode desativá-las por completo.

Os contatos de notificação são mostrados no Portal do EA do Azure na seção **Contato de Notificação**. O gerenciamento de seus contatos de notificação garante que as pessoas certas em sua organização obtenham as notificações do EA do Azure.

Para exibir as configurações de notificações atuais:

1. No Portal do EA do Azure, navegue até **Gerenciar** > **Contato de Notificação**.
2. Endereço de email – o endereço de email associado à conta Microsoft, corporativa ou de estudante do administrador corporativo que recebe as notificações.
3. Frequência de notificação de saldo não cobrado – a frequência que é definida para que as notificações sejam enviadas para cada administrador corporativo individual.

Para adicionar um contato:

1. Clique em **+ Adicionar Contato**.
2. Insira o endereço de email e depois confirme-o.
3. Clique em **Save** (Salvar).

O novo contato de notificação é exibido na seção **Contato de Notificação**. Para alterar a frequência de notificação, selecione o contato de notificação e clique no símbolo de lápis à direita da linha selecionada. Defina a frequência como **diária**, **semanal**, **mensal** ou **nenhuma**.

Você pode suprimir a _data de término do período de cobertura_ e _desabilitar e desprovisionar as notificações de data de término do ciclo de vida se aproximando_. Desabilitar as notificações de ciclo de vida suprime as notificações sobre o período de cobertura e a data de término do contrato.

## <a name="manage-partner-administrators"></a>Gerenciar administradores parceiros

Cada administrador parceiro no Portal do EA do Azure tem a capacidade de adicionar ou remover outros administradores parceiros. Os administradores parceiros estão associados às organizações parceiras de registros indiretos e não estão associados diretamente aos registros.

### <a name="add-a-partner-administrator"></a>Adicionar um administrador parceiro

Para exibir uma lista de todos os registros associados à mesma organização parceira como o usuário atual, clique na guia **Registro** e marque a caixa de registro desejada.

1. Entre como um administrador parceiro.
1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Parceiro**.
1. Clique em **+ Adicionar Administrador** e preencha o endereço de email, o contato da notificação e os detalhes da notificação.
1. Pressione **Adicionar**.

### <a name="remove-a-partner-administrator"></a>Remover um administrador parceiro

Para exibir uma lista de todos os registros associados à mesma organização parceira como o usuário atual, clique na guia **Registro** e marque a caixa de registro desejada.

1. Entre como um administrador parceiro.
1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Parceiro**.
1. Na seção Administrador, escolha a linha apropriada para o administrador que você deseja remover.
1. Pressione o ícone X à direita.
1. Confirme se você deseja excluir.

## <a name="manage-partner-notifications"></a>Gerenciar notificações de parceiro

Os administradores parceiros podem gerenciar a frequência com que recebem notificações de uso para os respectivos registros. Eles recebem automaticamente notificações semanais do próprio saldo não cobrado. Eles podem alterar a frequência de notificações individuais para mensal, semanal ou diária, podendo ainda desligá-las completamente.

Se uma notificação não tiver sido recebida por um usuário, verifique se as configurações de notificação do usuário estão corretas com as etapas a seguir.

1. Entrar no Portal do EA do Azure como um administrador parceiro.
2. Clique em **Gerenciar** e, em seguida, clique na guia **Parceiro**.
3. Exiba a lista de administradores na seção Administrador.
4. Para editar as preferências de notificação, passe o mouse sobre o administrador apropriado e clique no símbolo de lápis.
5. Aumente a frequência de notificação e as notificações de ciclo de vida conforme necessário.
6. Adicione um contato se necessário e clique em **Adicionar**.
7. Clique em **Save** (Salvar).

![Exemplo mostrando a caixa Adicionar Contato ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Exibir registros para administradores parceiros

Os administradores parceiros podem ver um modo de exibição de lista de todos os registros diretos e indiretos no Portal do EA do Azure. As caixas que contêm uma visão geral de cada registro serão exibidas com o número do registro, o nome do registro, o saldo e os valores excedentes.

### <a name="view-a-list-of-enrollments"></a>Exibir uma Lista de registros

1. Entre como um administrador parceiro.
1. Clique em **Gerenciar** no painel de navegação no lado esquerdo da página.
1. Clique na guia **Registro**.
1. Marque a caixa com o registro desejado.

Uma exibição de todos os registros é mantida na parte superior da página e apresenta caixas para cada registro. Além disso, você pode navegar pelos registros clicando no número de registro atual na navegação do lado esquerdo da página. Um pop-out será exibido permitindo que você pesquise registros ou escolha um registro diferente clicando na caixa apropriada.

## <a name="azure-sponsorship-offer"></a>Oferta do Azure Sponsorship

A oferta do Azure Sponsorship é uma conta Microsoft Azure limitada patrocinada. Ela está disponível via convite por email, somente para clientes limitados selecionados pela Microsoft. Se estiver qualificado para a oferta do Microsoft Azure Sponsorship, você receberá um convite por email para aceitar a oferta para sua ID de conta.

Para saber mais, crie uma [solicitação de suporte para a ativação do patrocínio](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversão para autenticação da conta corporativa ou de estudante

Os usuários do Azure Enterprise podem converter de um tipo de autenticação de Conta Microsoft (MSA ou Live ID) para uma Conta Corporativa ou de Estudante (que usa o Active Directory no Azure).

Para começar:

1. adicione a conta corporativa ou de estudante ao Portal do EA do Azure na função necessária.
1. Se receber erros, é possível que a conta não seja válida no Active Directory.  O Azure usa o UPN (nome principal do usuário), que nem sempre é idêntico ao endereço de email.
1. Autentique-se no portal do EA do Azure usando a conta corporativa ou de estudante.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para converter subscrições de contas Microsoft em contas corporativas ou de estudante:

1. Entre no portal de gerenciamento usando a conta Microsoft que tem as assinaturas.
1. Use a transferência de propriedade da conta para mover para a nova conta.
1. A conta Microsoft já deve estar livre de todas as assinaturas ativas e pode ser excluída.
1. Todas as contas excluídas ficarão no modo de exibição do portal com status inativo para fins de cobrança histórica.  Para filtrá-las no modo de exibição, marque a caixa de seleção para mostrar apenas as contas ativas.

## <a name="account-subscription-ownership-faq"></a>Perguntas frequentes sobre a propriedade da assinatura da conta

Este documento responde às perguntas frequentes relacionadas à propriedade da assinatura da conta.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários da conta do Azure você pode ter por assinatura?

Somente um proprietário de conta é permitido por assinatura.  Mais funções podem ser adicionadas por meio do Acesso baseado em função ou (Controle de acesso, IAM) na guia assinatura no canto superior esquerdo da página em [portal.azure.com]](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode ser listado em mais de um departamento?

Um proprietário de conta só pode estar associado a um único departamento.  A intenção é garantir o monitoramento preciso e a repartição dos custos/gastos associados ao departamento ao qual o proprietário está alinhado no registro do EA no Portal do EA do Azure

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não, um proprietário de assinatura deve ser uma autenticação exclusiva da conta Microsoft (MSA) ou do Azure Active Directory (AAD). Para explicar a sucessão em sua organização, considere criar contas genéricas e usar o AAD para gerenciar o acesso à assinatura.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um usuário individual pode ter várias assinaturas?

Um proprietário de conta do Azure pode criar e gerenciar um número ilimitado de assinaturas.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso acessar/exibir todas as assinaturas da minha organização?

Atualmente, isso deve ser feito pela política, ou seja, você precisará exigir que, para cada assinatura criada, sua conta seja adicionada a uma função de assinatura por meio do acesso baseado em função.

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde posso criar uma assinatura?

Antes de criar uma assinatura de oferta do Azure Enterprise (EA), sua conta deve ser adicionada à função de proprietário da conta pelo administrador do registro do EA no Portal do EA do Azure. Em seguida, você precisará entrar no Portal do EA do Azure para obter o direito de criar assinaturas do tipo de oferta do EA. Recomendamos que sua primeira assinatura do EA seja criada a partir do link '+ Adicionar Assinatura' na guia de assinaturas no Portal do EA.  No entanto, depois que a conta estiver autorizada, é provável que fique mais fácil criar assinaturas em portal.azure.com na guia de assinaturas no canto superior esquerdo da página. Lá, você poderá criar e renomear sua assinatura em uma única etapa.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma assinatura?

Para criar uma assinatura do tipo de oferta do Azure Enterprise, você deve estar qualificado na função de proprietário da conta no [portal do EA](https://ea.azure.com).

## <a name="next-steps"></a>Próximas etapas

- Leia sobre como as [reservas de máquina virtual](ea-portal-vm-reservations.md) podem ajudá-lo a economizar dinheiro.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](ea-portal-troubleshoot.md).
