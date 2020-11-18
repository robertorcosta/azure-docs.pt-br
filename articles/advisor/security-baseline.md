---
title: Linha de base de segurança do Azure para o Azure Advisor
description: A linha de base de segurança do assistente do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9ca8fbfb7f339c18f32379c64b40bc86e2bc47a6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810936"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Linha de base de segurança do Azure para o Azure Advisor

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 2,0](../security/benchmarks/overview.md) para o Azure Advisor. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Advisor. Os **controles** não aplicáveis ao Azure Advisor foram excluídos.

Para ver como o Azure Advisor é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do Azure Advisor completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar Azure Active Directory como o sistema de identidade e autenticação central

**Diretrizes**: o Azure Advisor usa o Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Padronize o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), os aplicativos Azure Key Vault, PaaS e SaaS
- Os recursos da sua organização, como aplicativos no Azure ou seus recursos de rede corporativa

Verifique se a proteção do Azure AD é uma prioridade alta na prática de segurança de nuvem de sua organização. O Azure AD também fornece uma pontuação segura de identidade para ajudá-lo a avaliar a postura de segurança de identidade em relação às recomendações de práticas recomendadas da Microsoft. Use a pontuação para medir a precisão de sua configuração correspondente às recomendações de práticas recomendadas e para fazer melhorias na sua postura de segurança.

Observe que o Azure AD dá suporte a identidades externas, que permitem aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa.

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usar provedores de identidade externos para o aplicativo](/azure/active-directory/b2b/identity-providers) 

- [O que é a pontuação segura de identidade em Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: o Azure Advisor usa o Azure Active Directory (Azure AD) para fornecer gerenciamento de acesso e identidade para recursos do Azure, aplicativos de nuvem e aplicativos locais. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros, fornecedores e fornecedores. 

Use o logon único para gerenciar e proteger o acesso aos dados e recursos de sua organização locais e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo e seguro, além de maior visibilidade e controle.

- [Entender o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado em Azure Active Directory

**Diretrizes**: o Azure Advisor usa o Azure Active Directory (Azure AD), que dá suporte a controles de autenticação fortes por meio da autenticação multifator e métodos fortes de senha.
- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de identidade e acesso da central de segurança do Azure para obter algumas práticas recomendadas na configuração de autenticação multifator. A autenticação multifator pode ser imposta em todos, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.
- Autenticação com senha – três opções de autenticação com senha estão disponíveis. Eles são, Windows Hello para empresas, Microsoft Authenticator aplicativo e métodos de autenticação local, como cartões inteligentes.

Para administradores e usuários privilegiados, certifique-se de que o nível mais alto do método de autenticação forte seja usado, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução a opções de autenticação com senha para Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar e alertar em anomalias de conta

**Diretrizes**: o Azure Advisor é integrado ao Azure Active Directory (Azure AD) no, que fornece as seguintes fontes de dados:
- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entrada arriscada-uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Use essas fontes de dados para integrar com o Azure Monitor, o Azure Sentinel ou sistemas SIEM de terceiros. Alertas de instalação na central de segurança do Azure para determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha, contas preteridas na assinatura.

A ATP (proteção avançada contra ameaças) do Azure é uma solução de segurança que pode usar Active Directory sinais para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de insider mal-intencionadas.

- [Relatórios de atividade de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md) 

- [Alertas no módulo proteção contra ameaças da central de segurança do Azure](../security-center/alerts-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso a recursos do Azure com base em condições

**Diretrizes**: o Azure Advisor dá suporte ao recurso de acesso condicional do Azure Active Directory (Azure AD) para um controle de acesso mais granular com base em condições definidas pelo usuário. Por exemplo, logons de usuário de determinados intervalos de IP precisarão usar a autenticação multifator para logon. A política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md) 

- [Políticas de acesso condicional comum](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, consulte o [benchmark de segurança do Azure: acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Orientação**: o Azure Advisor usa o Azure RBAC (controle de acesso baseado em função) para isolar o acesso a sistemas críticos de negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e grupos de gerenciamento aos quais eles pertencem.

Restrinja o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso crítico aos negócios, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas de negócios críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: revisar e reconciliar o acesso do usuário regularmente

**Orientação**: o Azure Advisor usa contas do Azure Active Directory (Azure AD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos. Implemente revisões de acesso do Azure AD para revisar associações de grupo, acesso a aplicativos empresariais e atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. 

Além disso, use os recursos de Privileged Identity Management do Azure AD para criar o fluxo de trabalho de relatório de análise de acesso para facilitar o processo de revisão. Privileged Identity Management também pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar as contas de administrador que estão obsoletas ou configuradas incorretamente.

Observe que alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Os clientes precisarão gerenciar esses usuários separadamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](/azure/active-directory/governance/access-reviews-overvie)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. 

Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas. Escolha Azure Active Directory (Azure AD), a ATP (proteção avançada contra ameaças) do Microsoft defender, incluindo Microsoft Intune para implantar uma estação de trabalho de usuário segura e gerenciada para tarefas administrativas. 

Gerencie centralmente as estações de trabalho protegidas para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Implantar uma estação de trabalho com acesso privilegiado](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir apenas administração suficiente (princípio de privilégio mínimo) 

**Diretrizes**: o Azure Advisor é integrado ao Azure RBAC (controle de acesso baseado em função) para gerenciar seus recursos. Use o Azure RBAC para gerenciar o acesso a recursos do Azure por meio de atribuições de função. 

Atribua funções a usuários, grupos entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, Azure PowerShell ou portal do Azure. Os privilégios atribuídos aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just in time) do Azure AD Privileged Identity Management (PIM) e deve ser revisado periodicamente.

Use funções internas para alocar permissão e somente criar função personalizada quando necessário.

O que é o Azure RBAC (controle de acesso baseado em função). /role-based-access-control/overview.md 

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](/azure/active-directory/governance/access-reviews-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos dos ativos

**Orientação**: garanta que as equipes de segurança recebam permissões de leitor de segurança em seu locatário e assinaturas do Azure para que possam monitorar riscos de segurança usando a central de segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento de riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, as informações e os riscos de segurança sempre devem ser agregados centralmente dentro de uma organização. 

As permissões de leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou com escopo para grupos de gerenciamento ou assinaturas específicas. 

Observação: podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Visão geral da função de leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral do Azure Grupos de Gerenciamento](../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: o Azure Advisor usa o Azure Active Directory (Azure AD) para identidade e autenticação, enquanto portal do Azure e Azure Resource Manager são usados para gerenciar o Advisor. 

Use o acesso condicional do Azure para limitar a capacidade do usuário de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure", conforme necessário com base nos requisitos de negócios. 

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registro em log e detecção de ameaças

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: os logs de atividade estão disponíveis automaticamente e contêm todas as operações de gravação (put, post, Delete) para seus recursos do Azure Advisor, exceto operações de leitura (Get). 

Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros. Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção de armazenamento de log

**Orientação**: Verifique se as contas de armazenamento ou os espaços de trabalho log Analytics usados para armazenar os logs do supervisor do Azure têm o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.
No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Como configurar Log Analytics período de retenção do espaço de trabalho](../azure-monitor/platform/manage-cost-storage.md) 

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – processo de atualização de resposta a incidentes para o Azure

**Orientação**: Verifique se sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está experimentando-os regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente corporativo](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – notificação de incidente de instalação

**Orientação**: configurar informações de contato de incidente de segurança na central de segurança do Azure. Essas informações de contato são usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure com base em suas necessidades de resposta a incidentes. 

- [Como definir o contato da segurança da central de segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – crie incidentes com base em alertas de alta qualidade

**Diretrizes**: Verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isso permite que você aprenda as lições dos últimos incidentes e Priorize os alertas para analistas, para que eles não percam tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência dos últimos incidentes, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas, combinando e correlacionando diferentes fontes de sinal. 

A central de segurança do Azure fornece alertas de alta qualidade em muitos ativos do Azure. Você pode usar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite que você crie regras de alerta avançadas para gerar incidentes automaticamente para uma investigação. 

Exporte seus alertas e recomendações da central de segurança do Azure usando o recurso exportar para ajudar a identificar os riscos para os recursos do Azure. Exporte alertas e recomendações de forma manual ou contínua, de modo contínuo.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detecção e análise – investigue um incidente

**Orientação**: garanta que os analistas possam consultar e usar diversas fontes de dados à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversos devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de Kill para evitar manchas cegas.  Você também deve garantir que informações e aprendizados sejam capturados para outros analistas e para futura referência histórica.  

As fontes de dados para investigação incluem as fontes de registro em log centralizadas que já estão sendo coletadas dos serviços dentro do escopo e sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o observador de rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos de sistemas em execução: 

    - Use a capacidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Use o recurso de despejo de memória nativo do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a capacidade de seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Instantâneo do disco de um computador Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Instantâneo do disco de um computador Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Suporte Microsoft Azure informações de diagnóstico e coleta de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretrizes**: forneça o contexto para analistas nos quais os incidentes se concentram primeiro com base na severidade do alerta e na sensibilidade do ativo. 

A central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenção, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretrizes**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga de analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo Quantos incidentes um analista pode manipular. As tarefas manuais também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos e degrada a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho na central de segurança do Azure e o Azure Sentinel para disparar automaticamente ações ou executar um guia estratégico para responder a alertas de segurança de entrada. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação do fluxo de trabalho na central de segurança](../security-center/workflow-automation.md)

- [Configurar respostas de ameaças automatizadas na central de segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Postura e gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: postura e gerenciamento de vulnerabilidade](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: conduzir simulação de ataque regular

**Orientação**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas em seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas.
Siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud o agrupamento vermelho](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, consulte o [benchmark de segurança do Azure: governança e estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de gerenciamento de ativos e de proteção de dados 

**Orientação**: Certifique-se de documentar e comunicar uma estratégia clara para monitoramento contínuo e proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados críticos para os negócios. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio de seu ciclo de vida

-   Estratégia de controle de acesso necessária de acordo com a classificação de dados organizacionais

-   Uso de recursos de proteção de dados nativos e de terceiros do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – armazenamento, dados e criptografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Conceitos básicos de segurança do Azure-segurança de dados do Azure, criptografia e armazenamento](../security/fundamentals/encryption-overview.md)

- [Estrutura de adoção de nuvem – práticas recomendadas de segurança de dados do Azure e criptografia](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark de segurança do Azure – gerenciamento de ativos](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark de segurança do Azure-proteção de dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir estratégia de segmentação corporativa 

**Orientação**: estabeleça uma estratégia para toda a empresa para segmentar o acesso a ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento, entre outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitar a operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Certifique-se de que a estratégia de segmentação seja implementada consistentemente entre os tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, bem como permissão de aplicativo ou modelos de acesso e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir estratégia de gerenciamento de postura de segurança

**Diretrizes**: meça e reduza continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, os pontos de extremidade de usuário e administrador, etc.

- [Benchmark de segurança do Azure – gerenciamento de postura e vulnerabilidade](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções de organização, responsabilidades e responsabilidades

**Orientação**: Certifique-se de documentar e comunicar uma estratégia clara para funções e responsabilidades em sua organização de segurança. Priorize proporcionando responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instruir equipes técnicas sobre tecnologia para proteger a nuvem.

- [Prática recomendada de segurança do Azure 1 – pessoas: treinar equipes na jornada de segurança de nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Prática recomendada de segurança do Azure 2-pessoas: educar equipes sobre tecnologia de segurança de nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Prática recomendada de segurança do Azure 3-processo: atribuir responsabilidade por decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir a estratégia de segurança de rede

**Diretrizes**: estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Gerenciamento de rede centralizado e responsabilidade de segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaça e ataque

-   Borda da Internet e a estratégia de entrada e saída

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança do Azure-segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir a identidade e a estratégia de acesso privilegiado

**Orientação**: estabeleça uma identidade do Azure e uma abordagem de acesso privilegiado como parte da estratégia geral de controle de acesso empresarial da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Um sistema de identidade e autenticação centralizado e sua interconectividade com outros sistemas de identidade internos e externos

-   Métodos de autenticação fortes em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e manipulação de atividades de usuário de anomalias  

-   Identidade do usuário e processo de reavaliação e reconciliação de acesso

Examine os links referenciados para obter mais informações.

- [Benchmark de segurança do Azure-gerenciamento de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de segurança do Azure-acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../security/fundamentals/identity-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir a estratégia de resposta contra ameaças e registro

**Orientação**: estabeleça uma estratégia de resposta a ameaças e registro para detectar e corrigir ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de analistas com alertas de alta qualidade e experiências integradas para que eles possam se concentrar em ameaças em vez de integração e etapas manuais. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   A função e as responsabilidades da organização de operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

-   Captura de log e retenção para dar suporte à detecção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e informações de correlação sobre ameaças, usando SIEM, recursos nativos do Azure e outras fontes 

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas de seu interesse

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como registro em log e detecção de ameaças, análise forense e remediação de ataque e erradicação

-   Processos para lidar com incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:

- [Benchmark de segurança do Azure-registro em log e detecção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark de segurança do Azure-resposta de incidente](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Prática recomendada de segurança do Azure 4-Process. Atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de decisão da estrutura de adoção, registro em log e relatórios do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gerenciamento e monitoramento do Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Consulte a [visão geral do benchmark de segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
