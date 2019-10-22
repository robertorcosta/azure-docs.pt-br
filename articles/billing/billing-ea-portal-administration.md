---
title: Administração do Portal do EA do Azure
description: Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: f344436b1e1f1a97b981c1ead469d965b06a12ce
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329348"
---
# <a name="azure-ea-portal-administration"></a>Administração do Portal do EA do Azure

Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure (https://ea.azure.com). O Portal do EA do Azure é um portal de gerenciamento online que ajuda os clientes a gerenciar o custo dos respectivos serviços de EA do Azure. Para obter informações introdutórias sobre o Portal do EA do Azure, confira o artigo [Introdução ao Portal do EA do Azure](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm mais privilégios ao gerenciar um registro de EA do Azure. O administrador inicial do EA do Azure foi criado quando o contrato de EA foi configurado. No entanto, você pode adicionar ou remover novos administradores a qualquer momento. Novos administradores são adicionados somente por administradores existentes. Para obter mais informações sobre como adicionar outros admins corporativos, confira [Criar outro admin corporativo](billing-ea-portal-get-started.md#create-another-enterprise-admin). Para saber mais sobre funções e tarefas do perfil de cobrança, confira [Funções e tarefas do perfil de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Atualizar estado do usuário de pendente para ativo

Quando novos AOs (proprietários de conta) são adicionados a um registro de EA do Azure pela primeira vez, o status deles aparece como _pendente_. Quando um novo proprietário da conta receber o email de ativação de boas-vindas, ele poderá entrar para ativar a própria conta. Quando ele ativa a própria conta, o status da conta é atualizado de _pendente_ para _ativo_. Pode ser solicitado de novos usuários que insiram o nome e sobrenome deles para criar uma conta de comércio. Nesse caso, eles devem adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um administrador de departamento

Depois que um administrador de EA cria um departamento, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. Novas contas são necessárias para que as assinaturas de EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, confira [Criar um administrador de departamento do Azure EA](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

O Portal do EA do Azure ajuda você a administrar seus custos e uso do EA do Azure. Há três funções principais no Portal do EA do Azure:

- Administrador de EA
- Administrador de departamento
- Proprietário da conta

Cada função tem um nível diferente de acesso e de autoridade.

Para obter mais informações sobre as funções de usuário, confira Funções de usuário corporativo.

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta de EA do Azure

A conta de EA do Azure é uma unidade organizacional no Portal do EA do Azure que é usada para administrar assinaturas e também é usada para fazer relatórios. Para acessar e usar os serviços do Azure, é necessário que você crie uma conta ou que uma seja criada para você.

Para obter mais informações sobre as contas do Azure, confira Adicionar uma conta.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta empresarial para um novo registro

Tenha os pontos a seguir em mente ao transferir uma conta empresarial para um novo registro:

- Somente as contas especificadas na solicitação são transferidas. Se todas as contas forem escolhidas, elas serão todas transferidas.
- O registro de origem retém o próprio status como ativo ou estendido. Você poderá continuar usando o registro até que ele expire.

### <a name="effective-transfer-date"></a>Data de início de vigência da transferência

A data de início de vigência da transferência pode ser uma data ou posterior à data de início do registro para o qual você deseja transferir. O registro para o qual você está transferindo é o _registro de destino_. Após a transferência da conta, todas as informações de uso na conta antes da data de início de vigência da transferência permanecem no registro do qual você está transferindo. O registro do qual você está transferindo é o _registro de origem_.  O uso do registro de origem é cobrado em relação ao compromisso monetário ou como excedente. O uso que ocorre após a data de início de vigência da transferência é transferido para o novo registro e cobrado de acordo.

Defina uma data retroativa para uma transferência de registro até a data de início do registro de destino. Ou pode fazê-lo até a data de início efetiva do registro de origem.

### <a name="monetary-commitment"></a>Compromisso monetário

O compromisso monetário não é transferível entre registros. Os saldos de compromisso monetário são associados contratualmente ao registro em que ele foi pedido. O compromisso monetário não é transferido como parte do processo de transferência do registro ou da conta.

### <a name="services-affected"></a>Serviços afetados

Não há tempo de inatividade durante a transferência da conta. Ela poderá ser concluída no mesmo dia da solicitação se todas as informações necessárias forem fornecidas.

### <a name="prerequisites"></a>Pré-requisitos

Ao solicitar uma transferência de conta, forneça as seguintes informações:


- Nome da conta e ID do proprietário da conta a ser transferida
- Para o registro de origem, o número de registro e a conta a serem transferidos
- Para o registro de destino, o número de registro para o qual transferir
- A data de início de vigência da transferência da conta pode ser uma data igual ou posterior à data de início do registro de destino

Outros pontos para ter em mente antes de uma transferência de conta:

- A aprovação de um administrador de EA é necessária para o registro de origem e o de destino
  - Em alguns casos, a Microsoft pode solicitar aprovação adicional de um administrador de EA do registro de origem
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de registro.
- A transferência de conta transfere todos os serviços e assinaturas relacionados às contas específicas.
- Depois que a transferência for concluída, a conta transferida será exibida como inativa no registro de origem.
- Uma transferência de conta pode ser pré-datada para qualquer data desde a data de início do registro de destino.
- A conta mostra a data de término correspondente à data de transferência efetiva no registro de origem e como uma data de início no registro de destino.
- Qualquer uso ocorrido para a conta antes da data de transferência efetiva permanece sob o registro de origem.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir Registro Enterprise para um novo registro

Quando você solicita a transferência de um Registro Enterprise inteiro para um registro, as seguintes ações ocorrem:

- Todos os serviços, assinaturas, contas, departamentos e toda a estrutura de registro do Azure, incluindo todos os administradores de departamento do EA, são transferidos.
- O status do registro é definido como _Transferido_. O registro transferido está disponível apenas para fins de relatório de uso histórico.
- Você não pode adicionar funções nem assinaturas a um registro transferido. O status de transferido impede o uso adicional em relação ao registro.
- Qualquer saldo de compromisso monetário restante no contrato é perdido, incluindo termos futuros.

### <a name="effective-transfer-date"></a>Data de início de vigência da transferência

A data de início de vigência da transferência pode ser uma data ou posterior à data de início do registro para o qual você deseja transferir ao registro de destino.

O uso do registro de origem é cobrado em relação ao compromisso monetário ou como excedente. O uso que ocorre após a data de início de vigência da transferência é transferido para o novo registro e cobrado de acordo.

### <a name="effective-transfer-date-in-the-past"></a>Data de início de vigência d transferência no passado

Você pode definir a data de uma transferência de conta retroativamente até a data de início do registro de destino. Ou pode fazê-lo até a data de início efetiva do registro de origem.

### <a name="monetary-commitment"></a>Compromisso monetário

O compromisso monetário não é transferível entre registros. Os saldos de compromisso monetário são associados contratualmente ao registro em que ele foi pedido. O compromisso monetário não é transferido como parte do processo de transferência do registro ou da conta.

### <a name="services-affected"></a>Serviços afetados

Não há tempo de inatividade durante a transferência da conta. Ela poderá ser concluída no mesmo dia da solicitação se todas as informações necessárias forem fornecidas.

### <a name="prerequisites"></a>Pré-requisitos

Ao solicitar uma transferência de registro, forneça as seguintes informações:

- Para o registro de origem, o número de registro e a conta a serem transferidos.
- Para o registro de destino, o número de registro no qual transferir.
- A data de início de vigência da transferência do registro pode ser uma data igual ou posterior à data de início do registro de destino. A data escolhida não pode afetar o uso de nenhuma fatura excedente já emitida.

Outros pontos para ter em mente antes de uma transferência de registro:

- A aprovação de um Administrador de EA é necessária para o registro de origem e o de destino.
  - Em alguns casos, a Microsoft pode solicitar aprovação adicional de um administrador de EA do registro de origem.
- Se uma transferência de registro não atender aos seus requisitos, considere uma transferência de conta.
- Somente as contas que você especificar serão transferidas. Você pode solicitar para transferir todas as suas contas.
- O registro de origem retém o próprio status como ativo/estendido. Você poderá continuar usando o registro até que ele expire.

## <a name="change-account-owner"></a>Alterar proprietário da conta

O Portal do EA do Azure pode transferir assinaturas de um proprietário de conta para outro. Para obter mais informações, confira [Alterar proprietário da conta](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Efeitos da transferência de assinatura

Quando uma assinatura do Azure é transferida para uma conta no mesmo locatário do Azure Active Directory, todos os usuários, grupos e entidades de serviço que tinham [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) para gerenciar recursos na assinatura retêm os respectivos acessos.

Para exibir os usuários com acesso RBAC à assinatura:

1. No portal do Azure, abra **Assinaturas**.
2. Selecione a assinatura que deseja exibir e, em seguida, selecione **Controle de Acesso (IAM)** .
3. Selecione **Atribuições de função**. A página de atribuições de função lista todos os usuários que têm acesso RBAC à assinatura.

Se a assinatura for transferida para uma conta em um locatário diferente do Azure AD, todos os usuários, grupos e entidades de serviço que tiverem [RBAC](../role-based-access-control/overview.md) para gerenciar recursos _perderão_ o acesso. Embora o acesso RBAC não esteja presente, o acesso à assinatura pode estar disponível por meio de mecanismos de segurança, incluindo:

- Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../cloud-services/cloud-services-certs-create.md).
- Teclas de acesso para serviços como Armazenamento. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
- Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se o destinatário precisar restringir o acesso a seus recursos do Azure, ele deverá considerar a atualização dos segredos associados ao serviço. A maioria dos recursos pode ser atualizada usando as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **Todos os recursos**.
3. Selecione o recurso.
4. Na página de recursos, clique em **Configurações** para exibir e atualizar os segredos existentes.



## <a name="close-an-azure-enterprise-enrollment"></a>Fechar um Registro Enterprise do Azure

Se quiser fechar seu registro de EA do Azure, você poderá:

- Cancelar todas as suas assinaturas associadas ao EA do Azure no portal do Azure.
- Entrar em contato com o parceiro ou consultor de software do cliente e solicitar a eles que fechem seu Contrato Enterprise do Azure.

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

## <a name="manage-partner-notifications"></a>Gerenciar notificações de parceiro

Os administradores parceiros podem gerenciar a frequência com que recebem notificações de uso para os respectivos registros. Eles recebem automaticamente notificações semanais do próprio saldo não cobrado. Eles podem alterar a frequência de notificações individuais para mensal, semanal ou diária, podendo ainda desligá-las completamente.

Se uma notificação não tiver sido recebida por um usuário, verifique se as configurações de notificação do usuário estão corretas com as etapas a seguir.

1. Entrar no Portal do EA do Azure como um administrador parceiro.
2. Clique em **Gerenciar** e, em seguida, clique na guia **Parceiro**.
3. Exiba a lista de administradores na seção **Administrador**.
4. Para editar as preferências de notificação, passe o mouse sobre o administrador apropriado e clique no símbolo de lápis.
5. Aumente a frequência de notificação e as notificações de ciclo de vida conforme necessário.
6. Adicione um contato se necessário e clique em **Adicionar**.
7. Clique em **Save** (Salvar).

![Exemplo mostrando a caixa Adicionar Contato ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Oferta do Azure Sponsorship

A oferta do Azure Sponsorship é uma conta limitada patrocinada do Microsoft Azure. Ela está disponível via convite por email, somente para clientes limitados selecionados pela Microsoft. Se estiver qualificado para a oferta do Microsoft Azure Sponsorship, você receberá um convite por email para aceitar a oferta para sua ID de conta.

Para obter mais informações, consulte:

- Solicitação de suporte para a ativação do patrocínio – http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Próximas etapas
- Leia sobre como as [reservas de máquina virtual](billing-ea-portal-vm-reservations.md) podem ajudá-lo a economizar dinheiro.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](billing-ea-portal-troubleshoot.md).
