---
title: Use a contação de trabalho do Monitor Azure com serviços de domínio Azure AD | Microsoft Docs
description: Aprenda a usar o Azure Monitor Workbooks para revisar auditorias de segurança e entender problemas em um domínio gerenciado do Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654465"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Revise eventos de auditoria de segurança nos Serviços de Domínio azure AD usando workbooks do Monitor do Azure

Para ajudá-lo a entender o estado do domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS), você pode habilitar eventos de auditoria de segurança. Esses eventos de auditoria de segurança podem ser revisados usando workbooks do Monitor Do Azure que combinam texto, consultas de análise e parâmetros em relatórios interativos ricos. O Azure AD DS inclui modelos de pasta de trabalho para visão geral de segurança e atividades de conta que permitem que você investigue eventos de auditoria e gerencie seu ambiente.

Este artigo mostra como usar o Azure Monitor Workbooks para revisar eventos de auditoria de segurança no Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].
* Eventos de auditoria de segurança habilitados para o domínio gerenciado do Azure Active Directory Domain Services que transmitem dados para um espaço de trabalho do Log Analytics.
    * Se necessário, [habilite auditorias de segurança para os Serviços de Domínio do Diretório Ativo do Azure][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Visão geral da Pasta de Trabalho do Monitor do Azure

Quando os eventos de auditoria de segurança são ligados no Azure AD DS, pode ser difícil analisar e identificar problemas no domínio gerenciado. O Azure Monitor permite agregar esses eventos de auditoria de segurança e consultar os dados. Com o Azure Monitor Workbooks, você pode visualizar esses dados para tornar mais rápido e fácil identificar problemas.

Os modelos de livros de trabalho são relatórios curados que são projetados para reutilização flexível por vários usuários e equipes. Quando você abre um modelo de pasta de trabalho, os dados do ambiente do Monitor Do Azure são carregados. Você pode usar modelos sem impacto em outros usuários da sua organização e pode salvar suas próprias regras de trabalho com base no modelo.

O Azure AD DS inclui os dois modelos da pasta de trabalho a seguir:

* Relatório de visão geral da segurança
* Relatório de atividade supertino

Para obter mais informações sobre como editar e gerenciar livros de trabalho, consulte [a visão geral das livros de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Use a carteira de trabalho do relatório de visão geral de segurança

Para ajudá-lo a entender melhor o uso e identificar possíveis ameaças à segurança, o relatório de visão geral de segurança resume os dados de login e identifica contas que você pode querer verificar. Você pode visualizar eventos em um intervalo de datas específico e detalhar eventos específicos de login, como tentativas de senha ruins ou onde a conta foi desativada.

Para acessar o modelo da carteira de trabalho para o relatório de visão geral de segurança, complete as seguintes etapas:

1. Procure e selecione Os Serviços de Domínio do Diretório Ativo do **Azure** no portal Azure.
1. Selecione seu domínio gerenciado, como *aaddscontoso.com*
1. No menu do lado esquerdo, escolha **Monitoramento > Workbooks**

    ![Selecione a opção de menu Workbooks no portal Azure](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Escolha o **relatório de visão geral de segurança**.
1. Nos menus suspensos na parte superior da pasta de trabalho, selecione sua assinatura do Azure e, em seguida, o espaço de trabalho do Monitor Do Azure. Escolha um **intervalo de tempo,** como *os últimos 7 dias*.

    ![Selecione a opção de menu Workbooks no portal Azure](./media/use-azure-monitor-workbooks/select-query-filters.png)

    As opções **de visualização de tile** e **gráfico** também podem ser alteradas para analisar e visualizar os dados conforme desejado

1. Para detalhar um tipo de evento específico, selecione o dos cartões de resultado de **login,** como *Account Locked Out,* conforme mostrado no exemplo a seguir:

    ![Exemplo de visão geral de segurança Dados visualizados em workbooks do Monitor do Azure](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A parte inferior do relatório de visão geral de segurança abaixo do gráfico detalha o tipo de atividade selecionado. Você pode filtrar por nomes de usuário envolvidos no lado direito, conforme mostrado no relatório de exemplo a seguir:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Use a agenda de trabalho do relatório de atividades da conta

Para ajudá-lo a solucionar problemas para uma conta de usuário específica, o relatório de atividade suinotiva detalha informações detalhadas do registro de eventos de auditoria. Você pode rever quando um nome de usuário ou senha ruim foi fornecido durante o login e a origem da tentativa de login.

Para acessar o modelo da carteira de trabalho do relatório de atividade da conta, complete as seguintes etapas:

1. Procure e selecione Os Serviços de Domínio do Diretório Ativo do **Azure** no portal Azure.
1. Selecione seu domínio gerenciado, como *aaddscontoso.com*
1. No menu do lado esquerdo, escolha **Monitoramento > Workbooks**
1. Escolha o **relatório de atividade da conta**.
1. Nos menus suspensos na parte superior da pasta de trabalho, selecione sua assinatura do Azure e, em seguida, o espaço de trabalho do Monitor Do Azure. Escolha um **intervalo de tempo**, como os últimos *30 dias,* e como você deseja que a **exibição Tile** represente os dados. Você pode filtrar por **nome de usuário da conta,** como *felix,* como mostrado no seguinte relatório de exemplo:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A área abaixo do gráfico mostra eventos individuais de login, juntamente com informações como o resultado da atividade e a estação de trabalho de origem. Essas informações podem ajudar a determinar fontes repetidas de eventos de login que podem causar bloqueios de contas ou indicar um ataque em potencial.

Assim como no relatório de visão geral de segurança, você pode detalhar as diferentes telhas na parte superior do relatório para visualizar e analisar os dados conforme necessário.

## <a name="save-and-edit-workbooks"></a>Salvar e editar livros de trabalho

Os dois livros de trabalho de modelo fornecidos pelo Azure AD DS são um bom lugar para começar com sua própria análise de dados. Se você precisar obter mais granular nas consultas de dados e investigações, você pode salvar suas próprias pastas de trabalho e editar as consultas.

1. Para salvar uma cópia de um dos modelos da carteira de trabalho, **selecione Editar > Salvar como > Relatórios Compartilhados,** em seguida, forneça um nome e salve-o.
1. A partir de sua própria cópia do modelo, selecione **Editar** para entrar no modo de edição. Você pode escolher o **botão** editar azul ao lado de qualquer parte do relatório e alterá-lo.

Todos os gráficos e tabelas nos livros de trabalho do Monitor do Azure são gerados usando consultas kusto. Para obter mais informações sobre como criar suas próprias consultas, consulte [consultas de log do Azure Monitor][azure-monitor-queries] e tutorial de consultas do [Kusto][kusto-queries].

## <a name="next-steps"></a>Próximas etapas

Se você precisar ajustar as políticas de senha e bloqueio, consulte [políticas de bloqueio de senha e conta em domínios gerenciados][password-policy].

Para problemas com os usuários, saiba como solucionar [problemas de login de conta][troubleshoot-sign-in] ou [problemas de bloqueio de conta][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
