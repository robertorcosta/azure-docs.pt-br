---
title: Gerenciar contas de administrador de acesso de emergência - Azure AD | Microsoft Docs
description: Este artigo descreve como usar contas de acesso de emergência para ajudar a evitar ser bloqueado inadvertidamente fora de sua organização Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028139"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory

É importante que você evite ser bloqueado acidentalmente fora da organização Azure Active Directory (Azure AD) porque você não pode fazer login ou ativar a conta de outro usuário como administrador. Você pode mitigar o impacto da falta acidental de acesso administrativo criando duas ou mais *contas de acesso de emergência* em sua organização.

Essas contas de acesso de emergência são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou "quebra de vidro", onde contas administrativas normais não podem ser usadas. Recomendamos que você mantenha uma meta de restringir o uso de conta de emergência apenas às vezes em que é absolutamente necessário.

Este artigo fornece diretrizes para gerenciar contas de acesso de emergência no Microsoft Azure Active Directory.

## <a name="why-use-an-emergency-access-account"></a>Por que usar uma conta de acesso de emergência

Uma organização pode precisar usar uma conta de acesso de emergência nas seguintes situações:

- As contas de usuário são federadas e a federação está indisponível no momento devido a uma interrupção de rede celular ou uma interrupção do provedor de identidade. Por exemplo, se o host de provedor de identidade em seu ambiente foi desligado, os usuários podem não conseguir entrar quando o Azure AD redireciona para seu provedor de identidade.
- Os administradores são registrados por meio da Autenticação Multifator do Azure e todos os seus dispositivos individuais ou o serviço não estão disponíveis. Os usuários podem não conseguir concluir a Autenticação Multifator para ativar uma função. Por exemplo, uma interrupção de rede celular está impedindo que eles atendam a chamadas telefônicas ou recebam mensagens de texto, os dois únicos dois mecanismos de autenticação registrados para os dispositivos.
- A pessoa com acesso administrativo global mais recente saiu da organização. O Microsoft Azure Active Directory impede que a conta do último Administrador Global seja excluída, mas não impede que a conta seja excluída ou desabilitada localmente. Qualquer situação pode fazer com que a organização não consiga recuperar a conta.
- Circunstâncias imprevisíveis, como uma emergência de desastre natural, em que um telefone celular ou outras redes podem não estar disponíveis. 

## <a name="create-emergency-access-accounts"></a>Criar contas de acesso de emergência

Crie duas ou mais contas de acesso de emergência. Elas devem ser contas somente de nuvem que usam o domínio \*.onmicrosoft.com e que não são federadas ou sincronizadas de um ambiente local.

Ao configurar essas contas, os seguintes requisitos devem ser atendidos:

- As contas de emergência não devem ser associadas a nenhum usuário individual na organização. Certifique-se de que suas contas não estejam conectadas a nenhum telefone celular fornecido pelo funcionário, tokens de hardware que viajam com funcionários específicos ou outras credenciais específicas do funcionário. Essa precaução abrange instâncias em que um funcionário individual está inacessível quando a credencial é necessária. É importante garantir que todos os dispositivos registrados sejam mantidos em uma localização segura e conhecida que tenha vários meios de comunicação com o Azure AD.
- O mecanismo de autenticação usado para uma conta de acesso de emergência deve ser diferente do usado por outras contas administrativas, incluindo outras contas de acesso de emergência.  Por exemplo, se sua entrada normal no administrador é por meio da Autenticação Multifator do Microsoft Azure (MFA) local, o Azure MFA seria um mecanismo diferente.  No entanto, se o Azure MFA é sua parte principal de autenticação para suas contas administrativas, então considere uma abordagem diferente para elas, como usar o Conditional Access com um provedor MFA de terceiros.
- O dispositivo ou a credencial não deve expirar ou estar no escopo de limpeza automatizado devido à falta de uso.  
- A atribuição de função de Administrador Global deve ser permanente para suas contas de acesso de emergência. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Excluir pelo menos uma conta de autenticação de multifator baseada em telefone

Para reduzir o risco de um ataque resultante de uma senha comprometida, o Microsoft Azure Active Directory recomenda que você exija a autenticação multifator para todos os usuários individuais. Este grupo deve incluir os administradores e todos os outros (por exemplo, gerentes financeiros) cuja conta comprometida teria um impacto significativo.

No entanto, pelo menos uma de suas contas de acesso de emergência não deve ter o mesmo mecanismo de autenticação multifator como outras contas não emergenciais. Isso inclui soluções de autenticação multifator de terceiros. Se você tiver uma política de Acesso Condicional para exigir [autenticação multifatorial para cada administrador](../authentication/howto-mfa-userstates.md) do Azure AD e outros aplicativos de software conectado como um serviço (SaaS), você deve excluir contas de acesso de emergência desse requisito e configurar um mecanismo diferente em vez disso. Além disso, verifique se que as contas não têm uma política de autenticação multifator por usuário.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Exclua pelo menos uma conta das políticas de acesso condicional

Durante uma emergência, não é desejável uma política para bloquear o acesso para corrigir um problema. Pelo menos uma conta de acesso de emergência deve ser excluída de todas as políticas de Acesso Condicional. Se tiver habilitado uma [política de linha de base](../conditional-access/baseline-protection.md), exclua as contas de acesso de emergência.

## <a name="federation-guidance"></a>Orientação da Federação

Uma opção adicional para organizações que usam os serviços de domínio do domínio do aplicativo (AD) e do Sistema de Arquivos Distribuído do Azure (ADFS) ou o provedor de identidade semelhante para federar ao Microsoft Azure Active Directory, é configurar uma conta de acesso de emergência cuja declaração da MFA pode ser fornecida por ser um provedor de identidade.  Por exemplo, a conta de acesso de emergência pode ser feita por um certificado e um par de chaves como uma armazenada em um cartão inteligente.  Quando o usuário é autenticado para o AD, o ADFS pode fornecer uma declaração ao Microsoft Azure Active Directory que indica que o usuário atende aos requisitos da MFA.  Mesmo com essa abordagem, as organizações ainda devem ter contas de acesso de emergência baseadas em nuvem no caso da federação não poder ser estabelecida. 

## <a name="store-account-credentials-safely"></a>Armazenar credenciais de conta com segurança

As organizações precisam garantir que as credenciais para essas contas de emergência sejam mantidas seguras e conhecidas apenas para os indivíduos que estão autorizados a usá-las. Alguns clientes usam um cartão inteligente e outros usam senhas. A senha para uma conta de acesso de emergência geralmente é separada em duas ou três partes, escrita em pedaços de papel separados e armazenada em cofres seguros à prova de incêndio que estão em locais separados e seguros.

Se usar as senhas, verifique se as contas têm senhas fortes que não façam a senha expirar. Idealmente, as senhas devem ter pelo menos 16 caracteres longos e gerados aleatoriamente.

## <a name="monitor-sign-in-and-audit-logs"></a>Exibir entradas e logs de auditoria

As organizações devem monitorar a atividade de login e auditoria das contas de emergência e disparar notificações para outros administradores. Quando você monitora a atividade em contas de vidro de quebra, você pode verificar se essas contas são usadas apenas para testes ou emergências reais. Você pode usar o Azure Log Analytics para monitorar os logs de login e ativar alertas de e-mail e SMS para seus administradores sempre que as contas de vidro quebrarem entrar.

### <a name="prerequisites"></a>Pré-requisitos

1. [Envie login sinuoso do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) para o Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Obtenha IDs de objeto das contas de vidro de quebra

1. Faça login no [portal Do Zure](https://portal.azure.com) com uma conta atribuída à função de administrador do Usuário.
1. Selecione usuários de diretório ativo do >  **Azure****.**
1. Procure a conta de quebra-vidro e selecione o nome do usuário.
1. Copie e salve o atributo Object ID para que você possa usá-lo mais tarde.
1. Repita as etapas anteriores para a segunda conta de vidro de quebra.

### <a name="create-an-alert-rule"></a>Criar uma regra de alerta

1. Faça login no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função contribuinte de monitoramento no Monitor Do Azure.
1. Selecione **Todos os serviços",** digite "análise de log" na Pesquisa e selecione **espaços de trabalho do Log Analytics**.
1. Selecione um workspace.
1. Em seu espaço de trabalho, selecione **Alertas** > **Nova regra de alerta**.
    1. Em **Resource,** verifique se a assinatura é aquela com a qual você deseja associar a regra de alerta.
    1. Em **Condição,** selecione **Adicionar**.
    1. Selecione **pesquisa de log personalizado** com o nome **Signal**.
    1. Em **Pesquisa de consulta,** digite a seguinte consulta, inserindo os IDs de objeto das duas contas de vidro de quebra.
        > [!NOTE]
        > Para cada conta adicional de vidro de quebra que você deseja incluir, adicione outra "ou UserId == "ObjectGuid"" à consulta.

        ![Adicione os IDs de objeto das contas de vidro de quebra a uma regra de alerta](./media/directory-emergency-access/query-image1.png)

    1. Em **lógica de alerta,** digite o seguinte:

        - Baseado em: número de resultados
        - Operador: Maior que
        - Valor limite: 0

    1. Em **Avaliação com base em**, selecione o Período **(em minutos)** para quanto tempo deseja que a consulta seja executada e a **Freqüência (em minutos)** para quantas vezes você deseja que a consulta seja executada. A frequência deve ser menor ou igual ao período.

        ![lógica de alerta](./media/directory-emergency-access/alert-image2.png)

    1. Selecione **Feito**. Agora você pode ver o custo mensal estimado deste alerta.
1. Selecione um grupo de ação de usuários para ser notificado pelo alerta. Se você quiser criar um, consulte [Criar um grupo de ação](#create-an-action-group).
1. Para personalizar a notificação de e-mail enviada aos membros do grupo de ação, selecione ações em **Personalizar Ações**.
1. Em **Detalhes de alerta,** especifique o nome da regra de alerta e adicione uma descrição opcional.
1. Defina o **nível de gravidade** do evento. Recomendamos que você o configure como **Crítico (Sev 0).**
1. Em **Enable rule on creation**, leave it set as **yeah**.
1. Para desativar os alertas por um tempo, selecione a caixa de seleção **Alertas de supressão** e digite a duração de espera antes de alertar novamente e, em seguida, **selecione Salvar**.
1. Clique em **Criar regra de alerta**.

### <a name="create-an-action-group"></a>Criar um grupo de ações

1. Selecione **Criar um grupo de ação**.

    ![criar um grupo de ação para ações de notificação](./media/directory-emergency-access/action-group-image3.png)

1. Digite o nome do grupo de ação e um nome curto.
1. Verifique a assinatura e o grupo de recursos.
1. Em tipo de ação, selecione **E-mail/SMS/Push/Voice**.
1. Digite um nome de ação como **Notificar o admin global**.
1. Selecione o **tipo de ação** como **E-mail/SMS/Push/Voice**.
1. Selecione **Editar detalhes** para selecionar os métodos de notificação que deseja configurar e inserir as informações de contato necessárias e, em seguida, selecione **Ok** para salvar os detalhes.
1. Adicione quaisquer ações adicionais que você deseja desencadear.
1. Selecione **OK**.

## <a name="validate-accounts-regularly"></a>Validar contas regularmente

Quando você treina os funcionários para usar contas de acesso de emergência e validar as contas de acesso de emergência, no mínimo faça as seguintes etapas em intervalos regulares:

- Certifique-se de que a equipe de monitoramento de segurança esteja ciente de que a atividade de verificação de conta é contínua.
- Certifique-se de que o processo de quebra de vidro de emergência para usar essas contas é documentado e atual.
- Certifique-se de que os administradores e os agentes de segurança que talvez sejam necessários para executar essas etapas durante uma emergência recebam um treinamento sobre o processo.
- Atualize as credenciais da conta, em particular, todas as senhas para suas contas de acesso de emergência e, em seguida, confirme se as contas de acesso de emergência podem ser acessadas e as tarefas administrativas executadas.
- Certifique-se de que os usuários não tenham registrado a Autenticação Multifator ou o SSPR (redefinição de senha de autoatendimento) em nenhum dispositivo de usuário individual ou detalhes pessoais. 
- Se as contas forem registradas para a Autenticação Multifator para um dispositivo, para uso durante as entradas ou a ativação de função, certifique-se de que o dispositivo esteja acessível a todos os administradores que podem precisar usá-lo durante uma emergência. Verifique também se o dispositivo pode se comunicar por meio de pelo menos dois caminhos de rede que não compartilham um modo de falha comum. Por exemplo, o dispositivo pode se comunicar com a Internet por meio de rede sem fio da instalação e por uma rede de provedor celular.

Essas etapas devem ser realizadas em intervalos regulares e para alterações de chave:

- Pelo menos a cada 90 dias
- Quando houve uma alteração recente na equipe de TI, como uma alteração de cargo, uma saída ou uma nova contratação
- Quando as assinaturas do Microsoft Azure Active Directory da organização foram alteradas

## <a name="next-steps"></a>Próximas etapas

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](directory-admin-roles-secure.md)
- [Adicionar um usuário usando o Microsoft Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) e [atribuir ao novo usuário à função de administrador global](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Inscreva-se no Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), se você ainda não tiver se inscrito
- [Como exigir a verificação em duas etapas para um usuário](../authentication/howto-mfa-userstates.md)
- [Configurar as proteções adicionais para os Administradores Globais no Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se você estiver usando o Office 365
- [Executar uma análise de acesso de Administradores Globais](../privileged-identity-management/pim-how-to-start-security-review.md) e [transicionar os Administradores Globais existentes para funções de administrador mais específicas](directory-assign-admin-roles.md)
