---
title: Guia de planejamento e operações da central de segurança
description: Este documento ajuda você a planejar antes de adotar a Central de Segurança do Azure e fornece considerações sobre as operações diárias.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 45d3ec8dc5d819464046e40bab22491a4bccde63
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461316"
---
# <a name="planning-and-operations-guide"></a>Guia de planejamento e operações
Este guia é para profissionais de ti (tecnologia da informação), arquitetos de ti, analistas de segurança de informações e administradores de nuvem para usar a central de segurança do Azure.


## <a name="planning-guide"></a>Guia de planejamento
Este guia aborda as tarefas que você pode seguir para otimizar o uso da central de segurança com base nos requisitos de segurança e no modelo de gerenciamento de nuvem de sua organização. Para tirar total proveito da Central de Segurança, é importante entender como as pessoas ou equipes diferentes em sua organização usam o serviço para atender às necessidades de desenvolvimento e operações seguras, monitoramento, administração resposta a incidentes. As principais áreas a serem consideradas ao planejar o uso da Central de Segurança são:

* Funções de segurança e controles de acesso
* Políticas de segurança e recomendações 
* Coleta de dados e armazenamento
* Integração de recursos não Azure
* Monitoramento contínuo de segurança
* Resposta a incidentes

Na próxima seção, você aprenderá a planejar cada uma dessas áreas e aplicar essas recomendações de acordo com suas necessidades.


> [!NOTE]
> Leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](faq-general.md) para obter uma lista das perguntas comuns que também podem ser úteis durante as fases de design e planejamento.

## <a name="security-roles-and-access-controls"></a>Funções de segurança e controles de acesso
Dependendo do tamanho e da estrutura de sua organização, vários indivíduos e equipes podem usar a Central de Segurança para executar tarefas variadas relacionadas à segurança. No diagrama a seguir, você tem um exemplo de pessoas fictícias e suas respectivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

A Central de Segurança permite que essas pessoas atendam a várias responsabilidades. Por exemplo:

**Matheus (proprietário da carga de trabalho)**

* Gerenciar uma carga de trabalho de nuvem e seus recursos relacionados
* Responsáveis pela implementação e manutenção das proteções de acordo com a política de segurança da empresa

**Ellen (CISO/CIO)**

* Responsável por todos os aspectos de segurança da empresa
* Deseja entender a postura de segurança da empresa nas cargas de trabalho de nuvem
* Precisa ser informado dos riscos e principais ataques

**David (segurança de TI)**

* Define as políticas de segurança da empresa para garantir as devidas proteções no local
* Monitora a conformidade com as políticas
* Gera relatórios de liderança ou auditores

**Judy (Operações de Segurança)**

* Monitora e responde a alertas de segurança 24/7
* Escala para o Proprietário da Carga de Trabalho de Nuvem ou Analista de Segurança de TI

**Sam (Analista de Segurança)**

* Investiga os ataques
* Trabalhar com o Proprietário da Carga de Trabalho de Nuvem para aplicar a correção

A central de segurança usa o [Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando um usuário abre a Central de Segurança, ele vê apenas as informações relacionadas aos recursos aos quais tem acesso. Isso significa que o usuário recebe a função de Leitor, Colaborador ou Proprietário para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- **Leitor de segurança**: um usuário com esta essa função consegue ver somente as configurações da Central de Segurança, que inclui recomendações, alertas, política e integridade, mas não consegue fazer alterações.
- **Administrador de segurança**: o mesmo que o leitor de segurança, mas ele também pode atualizar a política de segurança, ignorar as recomendações e alertas.

As funções da Central de Segurança descritas acima não têm acesso a outras áreas de serviço do Azure como Armazenamento, Web e Móveis ou Internet das Coisas.

Usando as pessoas explicadas no diagrama anterior, o seguinte RBAC seria necessário:

**Matheus (proprietário da carga de trabalho)**

* Proprietário/colaborador do grupo de recursos

**Ellen (CISO/CIO)**

* Proprietário/colaborador ou administrador de segurança da assinatura

**David (segurança de TI)**

* Proprietário/colaborador ou administrador de segurança da assinatura

**Judy (Operações de Segurança)**

* Leitor de assinatura ou o Leitor de segurança para exibir alertas
* Proprietário/colaborador ou administrador de segurança da assinatura necessário para ignorar alertas

**Sam (Analista de Segurança)**

* Leitor de assinatura para exibir alertas
* Proprietário/colaborador da assinatura necessário para ignorar alertas
* O acesso ao workspace poderá ser exigido

Algumas informações importantes a serem consideradas:

* Somente os Proprietários/Colaboradores da assinatura e Administradores de segurança podem editar uma política de segurança.
* Somente os Proprietários e os Colaboradores da assinatura e do grupo de recursos podem aplicar recomendações de segurança para um recurso.

Ao planejar o controle de acesso usando o RBAC para a Central de Segurança, compreenda quem em sua organização usará a Central de Segurança. Além disso, quais tipos de tarefas irão executar, em seguida, configurar o RBAC de acordo.

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, os usuários que precisarem apenas exibir informações sobre o estado de segurança dos recursos, mas não precisarem executar ações, por exemplo, aplicar recomendações ou editar políticas, deverão receber a função de Leitor.
>
>

## <a name="security-policies-and-recommendations"></a>Políticas de segurança e recomendações 
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. Na Central de Segurança, você pode definir políticas para suas assinaturas do Azure, que podem ser adaptadas para o tipo de carga de trabalho ou a confidencialidade dos dados.

As políticas de Central de Segurança contêm os seguintes componentes:
- [Coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): configurações de provisionamento de agente e de coleta de dados.
- [Política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies): um [Azure Policy](../governance/policy/overview.md) que determina quais controles são monitorados e recomendados pela central de segurança, ou use Azure Policy para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gerenciamento.
- [Notificações por email](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): configurações de contatos de segurança e de notificação.
- [Tipo de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing): seleção de preços Gratuito ou Standard, que determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo (pode ser especificado para assinaturas, grupos de recursos e workspaces).

> [!NOTE]
> A especificação de um contato de segurança faz com que o Azure possa falar com a pessoa certa na sua organização em caso de incidente de segurança. Leia [Fornecer detalhes de contato da segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) para obter mais informações sobre como habilitar essa recomendação.

### <a name="security-policies-definitions-and-recommendations"></a>Recomendações e definições de políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar a política na Central de Segurança ou usar a Política do Azure para criar novas definições, definir políticas adicionais e atribuir políticas entre Grupos de Gerenciamento (que pode representar toda a organização, uma unidade de negócios nela, etc.) e monitorar a conformidade com essas políticas nesses escopos.

Antes de configurar as políticas de segurança, examine cada uma das [recomendações de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations)e determine se essas políticas são apropriadas para suas várias assinaturas e grupos de recursos. Também é importante entender que ação deve ser realizada para lidar com as recomendações de segurança e quem na sua organização será responsável por monitorar novas recomendações e tomar as medidas necessárias.

## <a name="data-collection-and-storage"></a>Coleta de dados e armazenamento
A central de segurança do Azure usa o agente de Log Analytics – esse é o mesmo agente usado pelo serviço de Azure Monitor – para coletar dados de segurança de suas máquinas virtuais. Os [dados coletados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) por esse agente são armazenados nos seus espaços de trabalho do Log Analytics existentes.

### <a name="agent"></a>Agente

Quando o provisionamento automático está habilitado na política de segurança, o agente de Log Analytics (para [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) é instalado em todas as VMs do Azure com suporte e quaisquer novas que são criadas. Se a VM ou o computador já tiver o agente de Log Analytics instalado, a central de segurança do Azure aproveitará o agente instalado atual. O processo do agente foi projetado para ser não invasivo e ter um impacto muito mínimo sobre o desempenho da VM.

O agente do Log Analytics para Windows requer o uso da porta TCP 443. Confira o [artigo de solução de problemas](security-center-troubleshooting-guide.md) para obter mais detalhes.

Se em algum momento você quiser desabilitar a Coleta de dados, desative-a na política de segurança. No entanto, como o agente de Log Analytics pode ser usado por outros serviços de gerenciamento e monitoramento do Azure, o agente não será desinstalado automaticamente quando você desativar a coleta de dados na central de segurança. Você pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para encontrar uma lista de VMs com suporte, leia as perguntas frequentes [(FAQ) da central de segurança do Azure](faq-vms.md).

### <a name="workspace"></a>Workspace

Um workspace é um recurso do Azure que funciona como um contêiner para os dados. Você ou outros membros de sua organização podem usar vários workspaces para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Os dados coletados do agente de Log Analytics (em nome da central de segurança do Azure) serão armazenados em um espaço de trabalho Log Analytics existente associado à sua assinatura do Azure ou a um novo espaço de trabalho, levando em conta a geografia da VM.

No portal do Azure, você pode navegar para ver uma lista de workspaces do Log Analytics, incluindo aqueles criados pela Central de Segurança do Azure. Um grupo de recursos relacionados será criado para novos workspaces. Ambos seguirão esta convenção de nomenclatura:

* Espaço de trabalho: *defaultworkspace-[ID-da-assinatura]-[geo]*
* Grupo de recursos: *DefaultResourceGroup- [geo]*

No caso de workspaces criados pela Central de Segurança do Azure, os dados serão retidos por 30 dias. No caso dos workspaces existentes, a retenção ocorre com base no tipo de preço do workspace. Se desejar, você também pode usar um workspace existente.

> [!NOTE]
> A Microsoft está comprometida com a proteção da privacidade e da segurança dos dados. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço. Para saber mais sobre manipulação de dados e privacidade, leia [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Integração de recursos não Azure

A Central de Segurança pode monitorar a postura de segurança dos computadores não Azure, mas, antes, é necessário que você integre esses recursos. Leia [Integração com a Central de Segurança do Azure Standard para aumentar a segurança](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) para saber mais sobre como integrar recursos não Azure.

## <a name="ongoing-security-monitoring"></a>Monitoramento contínuo de segurança
Após a configuração inicial e a aplicação das recomendações da Central de Segurança, a próxima etapa será considerar os processos operacionais da Central de Segurança.

A Visão geral da Central de Segurança fornece um panorama unificado de segurança em todos os seus recursos do Azure e recursos não Azure conectados. O exemplo a seguir mostra um ambiente com muitos problemas a serem resolvidos:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> A Central de Segurança não interferirá nos seus procedimentos normais de operação. Ela vai monitorar passivamente as implantações e fornecerá recomendações com base nas políticas de segurança habilitadas.

Quando você optar pela primeira vez para usar a central de segurança para seu ambiente atual do Azure, certifique-se de examinar todas as recomendações, o que pode ser feito na página **recomendações** .

Planeje acessar a opção Inteligência contra ameaças como parte de suas operações diárias de segurança. Lá, você pode identificar ameaças à segurança do ambiente, como identificar se determinado computador faz parte de um botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoramento de recursos novos ou alterados

A maioria dos ambientes do Azure é dinâmica, com recursos sendo criados regularmente, girados ou reduzidos, reconfigurados e alterados. A Central de Segurança ajuda a garantir que você tenha visibilidade sobre o estado de segurança desses novos recursos.

Quando você adiciona novos recursos (VMs, Bancos de Dados SQL) ao seu Ambiente do Azure, a Central de Segurança descobre automaticamente esses recursos e começa a monitorar a segurança. Isso também inclui as funções Web do PaaS e as funções de trabalho. Se a Coleta de Dados estiver habilitada na [Política de Segurança](tutorial-security-policy.md), os recursos de monitoramento adicionais serão habilitados automaticamente para as máquinas virtuais.

Você também deve monitorar regularmente os recursos existentes para as alterações de configuração que poderiam ter criado riscos de segurança, descompasso das linhas de base recomendadas e alertas de segurança. 

### <a name="hardening-access-and-applications"></a>Proteção de acesso e aplicativos

Como parte de suas operações de segurança, você também deve adotar medidas preventivas para restringir o acesso às VMs e controlar os aplicativos que estão em execução nas VMs. Ao bloquear o tráfego de entrada para suas VMs do Azure, você estará reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para conexão com as VMs quando necessário. Use o recurso de acesso de [VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) para proteger o acesso às suas VMs.

Você pode usar [controles de aplicativo adaptáveis](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) para limitar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Entre outros benefícios, isso ajuda a proteger suas VMs contra malware. Usando o Machine Learning, a central de segurança analisa os processos em execução na VM para ajudá-lo a criar regras de lista de permissões.


## <a name="incident-response"></a>Resposta a incidentes
A Central de Segurança detecta e alerta você sobre as ameaças à medida que elas ocorrem. As organizações devem monitorar novos alertas de segurança e tomar as medidas necessárias para investigar com mais profundidade ou corrigir o ataque. Para obter mais informações sobre como a proteção contra ameaças da central de segurança funciona, leia [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

Embora este artigo não tenha a intenção de ajudá-lo a criar seu próprio plano de resposta a incidentes, vamos usar Microsoft Azure resposta de segurança no ciclo de vida da nuvem como a base para os estágios de resposta a incidentes. Os estágios são mostrados no diagrama a seguir:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Você pode usar o [Guia de Tratamento de Incidentes de Segurança do Computador](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) do NIST (Instituto Nacional de Padrões e Tecnologia) como uma referência para ajudar a criar seu próprio plano.
>

Você pode usar os Alertas da Central de Segurança durante os estágios a seguir:

* **Detectar**: identifica uma atividade suspeita em um ou mais recursos.
* **Avaliar**: realiza a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: usa as etapas de correção para realizar o procedimento técnico para endereçar o problema.

Cada Alerta de segurança fornece informações que podem ser usadas para entender melhor a natureza do ataque e sugerir possíveis atenuações. Alguns alertas também fornecem links para mais informações ou para outras fontes de informações no Azure. Você pode usar as informações fornecidas para pesquisa e para começar o processo de atenuação, e também pode pesquisar dados relacionados à segurança que fiquem armazenados no workspace.

O exemplo a seguir mostra uma atividade de RDP suspeita ocorrendo:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Essa página mostra os detalhes sobre o horário do ataque, o nome do host de origem, a VM de destino e também fornece etapas de recomendação. Em algumas circunstâncias, as informações de origem do ataque podem estar vazias. Leia [Informações de Origem Ausentes nos Alertas da Central de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações sobre esse tipo de comportamento.

Nessa página, você também pode iniciar uma investigação para entender melhor a linha do tempo do ataque, como o ataque ocorreu, quais sistemas foram possivelmente comprometidos, as credenciais que foram usadas, além de ver uma representação gráfica de toda a cadeia de ataque.

Depois de identificar o sistema comprometido, você pode executar uma [automação de fluxo de trabalho](workflow-automation.md) criada anteriormente. Esses são uma coleção de procedimentos que podem ser executados da central de segurança uma vez disparados por um alerta.

No [como aproveitar a central de segurança do Azure & Microsoft Operations Management Suite de um vídeo de resposta a incidentes](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) , você pode ver algumas demonstrações que podem ajudá-lo a entender como a central de segurança pode ser usada em cada uma dessas fases.

> [!NOTE]
> Leia [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como usar os recursos da central de segurança para ajudá-lo durante o processo de resposta a incidentes.
>
>

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a planejar a adoção da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da central de segurança do Azure](faq-general.md) – encontre perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/) — encontre postagens no blog sobre a segurança e a conformidade do Azure.
