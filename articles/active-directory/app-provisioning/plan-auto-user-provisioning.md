---
title: Planeje uma implantação automática de provisionamento de usuários para o Azure Active Directory
description: Orientação para o planejamento e execução do provisionamento automático do usuário
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522403"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planejar uma implantação de provisionamento do usuário automática

Muitas organizações dependem de aplicativos de software como serviço (SaaS), como ServiceNow, Zscaler e Slack para produtividade do usuário final. Historicamente, a equipe de TI tem se apoiado em métodos de provisionamento manual, como carregar arquivos CSV ou usar scripts personalizados para gerenciar com segurança as identidades dos usuários em cada aplicativo SaaS. Esses processos são propensos a erros, inseguros e difíceis de gerenciar.

O provisionamento automático do Azure Active Directory (Azure AD) simplifica esse processo automatizando com segurança a criação, manutenção e remoção de identidades de usuários em aplicativos SaaS com base nas regras de negócios. Essa automação permite que você dimensione efetivamente seus sistemas de gerenciamento de identidade em ambientes híbridos e somente em nuvem à medida que você expande sua dependência de soluções baseadas em nuvem.

Consulte [Automatizar o provisionamento e o desprovisionamento do usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md) para entender melhor a funcionalidade.

## <a name="learn"></a>Saiba mais

O provisionamento de usuários cria uma base para a governança contínua da identidade e melhora a qualidade dos processos de negócios que dependem de dados de identidade autoritários.

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios de habilitar o provisionamento automático do usuário são:

* **Aumento da produtividade**. Você pode gerenciar identidades de usuário em aplicativos SaaS com uma única interface de gerenciamento de provisionamento de usuário. Esta interface tem um único conjunto de políticas de provisionamento.

* **Gerenciar riscos**. Você pode aumentar a segurança automatizando alterações com base no status dos funcionários ou membros de grupo que definem funções e/ou acesso.

* **Abordar conformidade e governança.** O Azure AD suporta logs de auditoria nativos para cada solicitação de provisionamento de usuário. As solicitações são executadas tanto nos sistemas de origem quanto de destino. Isso permite rastrear quem tem acesso a aplicativos a partir de uma única tela.

* **Reduzir custos**. O provisionamento automático do usuário reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Reduz a necessidade de soluções de provisionamento de usuários desenvolvidas medida, scripts e registros de auditoria.

### <a name="licensing"></a>Licenciamento

O Azure AD oferece integração de autoatendimento de qualquer aplicativo usando modelos fornecidos no menu da galeria de aplicativos. Para obter uma lista completa de requisitos de licença, consulte a página de [licenciamento do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenciamento de aplicativos

Você precisará das licenças apropriadas para os aplicativos que deseja prover automaticamente. Discuta com os proprietários do aplicativo se os usuários atribuídos ao aplicativo têm as licenças adequadas para suas funções de aplicativo. Se o Azure AD gerenciar provisionamento automático com base em funções, as funções atribuídas no Azure AD devem estar alinhadas às licenças de aplicativos. Licenças incorretas de propriedade do aplicativo podem levar a erros durante o provisionamento/atualização de um usuário.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

* Operações CRUD - Ações tomadas em contas de usuário: Criar, Ler, Atualizar, Excluir.

* Logon único (SSO) - A capacidade de um usuário fazer logon uma vez e acessar todos os aplicativos habilitados para SSO. No contexto do provisionamento do usuário, o SSO é resultado de usuários que têm uma única conta para acessar todos os sistemas que utilizam provisionamento automático do usuário.

* Sistema de origem - O repositório de usuários que o Azure AD prossegue. O Azure AD é o sistema de origem da maioria dos conectores de provisionamento pré-integrados. No entanto, existem algumas exceções para aplicativos em nuvem, como SAP, Workday e AWS. Por exemplo, consulte [o provisionamento do usuário do Workday para o AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema de destino - O repositório de usuários para o que o Azure AD prossegue. O sistema Target é tipicamente um aplicativo SaaS, como ServiceNow, Zscaler e Slack. O sistema de destino também pode ser um sistema local, como o AD.

* [Sistema de Gerenciamento de Identidade Cross-domain (SCIM)](https://aka.ms/scimoverview) - Um padrão aberto que permite a automação do provisionamento do usuário. O SCIM comunica dados de identidade do usuário entre provedores de identidade, como a Microsoft, e provedores de serviços como o Salesforce ou outros aplicativos SaaS que requerem informações de identidade do usuário.

### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e Descrição |
| - | - |
| Webinars sob demanda| [Gerencie seus aplicativos corporativos com o Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Saiba como o Azure AD pode ajudá-lo a alcançar o SSO para seus aplicativos SaaS corporativos e práticas recomendadas para controlar o acesso. |
| vídeos| [O que é provisionamento de usuários no Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Como implantar o provisionamento do usuário no Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integrando o Salesforce com o Azure AD: como automatizar o provisionamento do usuário](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cursos online| SkillUp Online: [Gerenciando identidades](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Aprenda a integrar o Azure AD com muitos aplicativos SaaS e garantir o acesso do usuário a esses aplicativos. |
| Livros| [Autenticação moderna com o Azure Active Directory for Web Applications (Referência ao desenvolvedor) 1ª Edição](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Este é um guia autoritário e profundo para a construção de soluções de autenticação do Active Directory para esses novos ambientes. |
| Tutoriais| Veja a [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure AD](../saas-apps/tutorial-list.md). |
| Perguntas frequentes| [Perguntas frequentes](../app-provisioning/user-provisioning.md) sobre provisionamento automatizado de usuários |

### <a name="solution-architectures"></a>Arquiteturas de solução

O azure AD provisionando os usuários de serviços para aplicativos SaaS e outros sistemas, conectando-se aos pontos finais da API de gerenciamento de usuário fornecidos por cada fornecedor de aplicativos. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Provisionamento automático do usuário para empresas híbridas

Neste exemplo, usuários e grupos são criados em um banco de dados de RH conectado a um diretório local. O serviço de provisionamento Azure AD gerencia o provisionamento automático do usuário para os aplicativos SaaS de destino.

 ![provisionamento de usuários](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os usuários/grupos são criados em um aplicativo/sistema de RH local, como o SAP. 

1. **O agente Azure AD Connect** executa sincronizações programadas de identidades (usuários e grupos) do AD local para o Azure AD.

1. **O serviço de provisionamento Azure AD** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. **O serviço de provisionamento Azure AD** consulta o sistema de origem para quaisquer usuários e grupos alterados desde o ciclo inicial e empurra mudanças nos [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Provisionamento automático de usuários para empresas somente em nuvem

Neste exemplo, a criação do usuário ocorre no Azure AD e o serviço de provisionamento Azure AD gerencia o provisionamento automático do usuário para os aplicativos SaaS (Target).

![Figura 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os usuários/grupos são criados no Azure AD.

1. **O serviço de provisionamento Azure AD** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. **O serviço de provisionamento Azure AD** consulta o sistema de origem para quaisquer usuários e grupos atualizados desde o ciclo inicial e executa quaisquer [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Provisionamento automático do usuário para aplicativos de RH em nuvem 

Neste exemplo, os usuários e ou grupos são criados em um aplicativo de RH na nuvem, como Workday e SuccessFactors. O serviço de provisionamento Azure AD e o agente de provisionamento Azure AD Connect fornecem os dados do usuário do inquilino do aplicativo de RH na nuvem para a AD. Uma vez que as contas são atualizadas em AD, ela é sincronizada com o Azure AD através do Azure AD Connect, e os endereços de e-mail e atributos de nome de usuário podem ser gravados de volta para o inquilino do aplicativo de RH na nuvem.

![Figura 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **A equipe de RH** realiza as transações no inquilino do aplicativo de RH na nuvem.
2.  **O serviço de provisionamento Ad do Azure** executa os ciclos programados do inquilino do aplicativo de RH na nuvem e identifica alterações que precisam ser processadas para sincronização com a AD.
3.  **O serviço de provisionamento Azure AD** invoca o agente de provisionamento Azure AD Connect com uma carga útil de solicitação contendo operações de criação/atualização/habilitação/desativação da conta AD.
4.  **O agente de provisionamento Azure AD Connect** usa uma conta de serviço para gerenciar dados de contas AD.
5.  **O Azure AD Connect** executa o delta sync para puxar atualizações no AD.
6.  As atualizações **de AD** são sincronizadas com o Azure AD. 
7.  **O azure AD provisioning service** writebacks atributo de e-mail e nome de usuário do Azure AD para o inquilino do aplicativo de RH na nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais para determinar a estratégia de implantação do provisionamento do usuário em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, é normalmente devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando os stakeholders certos](https://aka.ms/deploymentplans) e que os papéis das partes interessadas no projeto são bem compreendidos documentando as partes interessadas e suas entradas e responsabilidades do projeto.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique proativamente com seus usuários como sua experiência mudará, quando mudará e como ganhar suporte se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planeje um piloto

Recomendamos que a configuração inicial do provisionamento automático do usuário seja em um ambiente de teste com um pequeno subconjunto de usuários antes de dimensioná-lo para todos os usuários em produção. Veja [as melhores práticas](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) para pilotar um piloto.

#### <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto  

Um piloto permite que você teste com um pequeno grupo antes de implantar um recurso para todos. Certifique-se de que, como parte de seus testes, cada caso de uso dentro de sua organização seja completamente testado.

Na sua primeira onda, direcione a TI, a usabilidade e outros usuários apropriados que podem testar e fornecer feedback. Use este feedback para desenvolver ainda mais as comunicações e instruções enviadas aos seus usuários e para dar insights sobre os tipos de problemas que sua equipe de suporte pode ver.

Amplie a distribuição para grupos maiores de usuários aumentando o escopo dos grupos direcionados. Isso pode ser feito através da [adesão dinâmica do grupo](../users-groups-roles/groups-dynamic-membership.md)ou adicionando manualmente os usuários ao grupo-alvo( s).

## <a name="plan-application-connections-and-administration"></a>Planejar conexões de aplicativos e administração

Use o portal Azure AD para visualizar e gerenciar todos os aplicativos que suportam provisionamento. Consulte [Encontrar seus aplicativos no portal](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Determine o tipo de conector a ser usado

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam dependendo do aplicativo. Se o aplicativo que você deseja provisionar automaticamente estiver listado na [galeria de aplicativos Azure AD SaaS,](../saas-apps/tutorial-list.md)então você deve selecionar o [tutorial de integração específico](../saas-apps/tutorial-list.md) do aplicativo para configurar seu conector de provisionamento de usuário pré-integrado.

Caso não, siga os passos abaixo:

1. [Crie uma solicitação](../develop/howto-app-gallery-listing.md) para um conector de provisionamento de usuário pré-integrado. Nossa equipe trabalhará com você e o desenvolvedor de aplicativos para embarcar em seu aplicativo para nossa plataforma se ele suportar SCIM.

1. Use o suporte de provisionamento genérico [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para o aplicativo. Este é um requisito para o Azure AD provisionar os usuários para o aplicativo sem um conector de provisionamento pré-integrado.

1. Se o aplicativo for capaz de utilizar o conector BYOA SCIM, consulte o tutorial de [integração BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para configurar o conector BYOA SCIM para o aplicativo.

Para obter mais informações, consulte [quais aplicativos e sistemas posso usar com o provisionamento automático do usuário Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Coletar informações para autorizar o acesso ao aplicativo

Configurar o provisionamento automático do usuário é um processo por aplicativo. Para cada aplicativo, você precisa fornecer [credenciais de administrador](../app-provisioning/configure-automatic-user-provisioning-portal.md) para se conectar ao ponto final de gerenciamento de usuário do sistema de destino.

A imagem abaixo mostra uma versão das credenciais de admin exigidas:

![Tela de provisionamento para gerenciar as configurações de provisionamento de contas de usuário](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Enquanto alguns aplicativos exigem o nome de usuário e a senha do admin, outros podem exigir um token do portador.

## <a name="plan-user-and-group-provisioning"></a>Planeje o provisionamento de usuários e grupos

Se você habilitar o provisionamento de usuários para aplicativos corporativos, o [portal Azure controlará](https://portal.azure.com/) seus valores de atributos através do mapeamento de atributos.

### <a name="determine-operations-for-each-saas-app"></a>Determine operações para cada aplicativo SaaS

Cada aplicativo pode ter atributos exclusivos de usuário ou grupo que devem ser mapeados para os atributos do Seu Azure AD. O aplicativo pode ter apenas um subconjunto de operações CRUD disponíveis.

Para cada aplicação, documente as seguintes informações:

* Operações de provisionamento CRUD a serem realizadas no usuário e ou objetos de grupo para os sistemas de destino. Por exemplo, cada proprietário de empresa de aplicativos SaaS pode não querer todas as operações possíveis.

* Atributos disponíveis no sistema de origem

* Atributos disponíveis no sistema de destino

* Mapeamento de atributos entre sistemas.

### <a name="choose-which-users-and-groups-to-provision"></a>Escolha quais usuários e grupos prover

Antes de implementar o provisionamento automático do usuário, você deve determinar os usuários e grupos a serem provisionados para o seu aplicativo.

* Use [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir regras baseadas em atributos que determinam quais usuários estão provisionados para um aplicativo.

* Em seguida, use [as atribuições do usuário e do grupo](../manage-apps/assign-user-or-group-access-portal.md) conforme necessário para filtragem adicional.

### <a name="define-user-and-group-attribute-mapping"></a>Defina o mapeamento de atributos de usuários e grupos

Para implementar o provisionamento automático do usuário, você precisa definir os atributos de usuário e grupo necessários para o aplicativo. Há um conjunto pré-configurado de atributos e [mapeamentos de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md) entre objetos de usuário AD do Azure e os objetos de usuário de cada aplicativo SaaS. Nem todos os aplicativos SaaS habilitam atributos de grupo.

O Azure AD é suportado por mapeamento direto de atributo a atributo, fornecendo valores constantes ou [escrevendo expressões para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá um bom controle do que será preenchido no atributo do sistema alvo. Você pode usar [a API do Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar mapeamentos e esquemas de provisionamento do usuário para um arquivo JSON e importá-lo de volta para o Azure AD.

Para obter mais informações, consulte [Personalização do provisionamento de mapeamentos de atributos para aplicativos SaaS no Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerações especiais para o provisionamento do usuário

Considere o seguinte para reduzir os problemas após a implantação:

* Certifique-se de que os atributos usados para mapear objetos de usuário/grupo entre aplicativos de origem e destino sejam resilientes. Eles não devem fazer com que os usuários/grupos sejam provisionados incorretamente se os atributos mudarem (por exemplo, um usuário se move para uma parte diferente da empresa).

* Os aplicativos podem ter restrições e/ou requisitos específicos que precisam ser cumpridos para que o provisionamento do usuário funcione corretamente. Por exemplo, o Slack trunca valores para certos atributos. Consulte [tutoriais de provisionamento automático do usuário](../saas-apps/tutorial-list.md) específicos para cada aplicativo.

* Confirme a consistência do esquema entre os sistemas de origem e de destino. Problemas comuns incluem atributos como UPN ou e-mail que não correspondem. Por exemplo, UPN no Azure *john_smith@contoso.com* AD definido como *jsmith@contoso.com*e no aplicativo, é . Para obter mais informações, consulte A [referência do esquema do Usuário e do grupo](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Teste de plano e segurança

Em cada etapa de sua implantação, certifique-se de que você está testando que os resultados são como esperado, e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o provisionamento automático do usuário para o aplicativo, você executará casos de teste para verificar se essa solução atende aos requisitos da sua organização.

| Cenários| Resultados esperados |
| - | - |
| O usuário é adicionado a um grupo atribuído ao sistema de destino | O objeto do usuário é provisionado no sistema de destino. <br>O usuário pode fazer login no sistema de destino e executar as ações desejadas. |
| O usuário é removido de um grupo atribuído ao sistema de destino | O objeto do usuário é desprovisionado no sistema de destino.<br>O usuário não pode entrar no sistema de destino. |
| As informações do usuário são atualizadas no Azure AD por qualquer método | Atributos de usuário atualizados são refletidos no sistema de destino após um ciclo incremental |
| O usuário está fora do escopo | O objeto do usuário é desativado ou excluído. <br>Nota: Este comportamento é substituído pelo [provisionamento do dia de trabalho](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Planejar segurança

É comum que uma revisão de segurança seja necessária como parte de uma implantação. Se você precisar de uma revisão de segurança, consulte os muitos [whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) do Azure AD que fornecem uma visão geral da identidade como serviço.

### <a name="plan-rollback"></a>Reversão do plano

Se a implementação automática de provisionamento do usuário não funcionar conforme desejado no ambiente de produção, as seguintes etapas de reversão abaixo podem ajudá-lo a reverter para um estado de bom estado conhecido anteriormente:

1. Revise o [relatório de resumo de provisionamento](../app-provisioning/check-status-user-account-provisioning.md) e os registros de provisionamento para determinar quais operações [incorretas](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) ocorreram nos usuários e/ou grupos afetados.

1. Use registros de auditoria de provisionamento para determinar o último estado bom conhecido dos usuários e/ou grupos afetados. Revise também os sistemas de origem (Azure AD ou AD).

1. Trabalhe com o proprietário do aplicativo para atualizar os usuários e/ou grupos afetados diretamente no aplicativo usando os últimos valores de estado de bom estado conhecidos.

## <a name="deploy-automatic-user-provisioning-service"></a>Implantar serviço automático de provisionamento de usuários

Escolha as etapas que se alinham aos requisitos da sua solução.

### <a name="prepare-for-the-initial-cycle"></a>Prepare-se para o ciclo inicial

Quando o serviço de provisionamento Azure AD é executado pela primeira vez, o ciclo inicial contra o sistema de origem e os sistemas de destino cria um instantâneo de todos os objetos de usuário para cada sistema de destino.

Ao ativar o provisionamento automático de uma aplicação, o ciclo inicial pode levar de 20 minutos a várias horas. A duração depende do tamanho do diretório Azure AD e do número de usuários em escopo para provisionamento. Veja [como melhorar o desempenho do provisionamento](../app-provisioning/application-provisioning-when-will-provisioning-finish.md).

O serviço de provisionamento armazena o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho dos ciclos incrementais subsequentes.

### <a name="configure-automatic-user-provisioning"></a>Configurar o provisionamento automático de usuário

Use o [portal Azure](https://portal.azure.com/) para gerenciar o provisionamento automático de contas de usuário e o desprovisionamento para aplicativos que o suportam. Siga as etapas em Como configurar o [provisionamento automático para um aplicativo?](../app-provisioning/user-provisioning.md)

O serviço de provisionamento de usuário do Azure AD também pode ser configurado e gerenciado usando-se a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gerenciar o provisionamento automático do usuário

Agora que você implantou, você precisa gerenciar a solução.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorar a saúde da operação de provisionamento do usuário

Após um [ciclo inicial](../app-provisioning/user-provisioning.md)bem-sucedido, o serviço de provisionamento Ad do Azure executará atualizações incrementais indefinidamente, em intervalos específicos para cada aplicativo, até que ocorra um dos seguintes eventos:

* O serviço é parado manualmente e um novo ciclo inicial é acionado usando o [portal Azure](https://portal.azure.com/), ou usando o comando [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) apropriado.

* Um novo ciclo inicial é desencadeado por uma alteração nos mapeamentos de atributos ou filtros de escopo.

* O processo de provisionamento entra em quarentena devido a uma alta taxa de erro e permanece em quarentena por mais de quatro semanas quando será automaticamente desativado.

Para revisar esses eventos e todas as outras atividades realizadas pelo serviço de provisionamento, consulte os [registros de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)do Azure AD .

Para entender quanto tempo os ciclos de provisionamento levam e monitorar o andamento do trabalho de provisionamento, você pode [verificar o status do provisionamento do usuário.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

### <a name="gain-insights-from-reports"></a>Obtenha insights a partir de relatórios

O Azure AD pode fornecer [insights adicionais](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) sobre o uso do provisionamento de usuários e a saúde operacional da sua organização através de registros de auditoria e relatórios.

Os admins devem verificar o relatório de sumário de provisionamento para monitorar a saúde operacional do trabalho de provisionamento. Todas as atividades realizadas pelo serviço de provisionamento são registradas nos registros de auditoria do Azure AD. Veja [tutorial: Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

Recomendamos que você assuma a propriedade e consuma esses relatórios em uma cadência que atenda aos requisitos da sua organização. O Azure AD retém a maioria dos dados de auditoria por 30 dias.

### <a name="troubleshoot"></a>Solução de problemas

Consulte os links a seguir para solucionar problemas que possam aparecer durante o provisionamento:

* [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem.md)

* [Sincronize um atributo do seu Active Directory no local para o Azure AD para provisionamento a um aplicativo](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [O provisionamento do usuário para um aplicativo da Galeria do Azure AD está levando horas ou mais](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Problema para salvar as credenciais de administrador ao configurar o provisionamento do usuário para um aplicativo de galeria do Azure Active Directory](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [O conjunto errado de usuários está sendo provisionado para um aplicativo da Galeria do Azure AD](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Documentação útil

* [Escrever expressões para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md)

* [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Pular a exclusão de contas de usuário que saem do escopo](skip-out-of-scope-deletions.md)

* [Azure AD Connect Provisioning Agent: Histórico de lançamento de versão](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Recursos

* [Fornecer feedback do produto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mantenha-se atualizado sobre as novidades do Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack overflow Azure AD fórum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Próximas etapas
* [Configurar o provisionamento automático do usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportar ou importar sua configuração de provisionamento usando a API do Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md)

* [Escrever expressões para mapeamentos de atributos no diretório Ativo do Azure](../app-provisioning/functions-for-customizing-application-data.md)
