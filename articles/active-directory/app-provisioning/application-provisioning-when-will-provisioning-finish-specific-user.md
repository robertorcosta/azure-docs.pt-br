---
title: Descubra quando um usuário específico poderá acessar um aplicativo
description: Como saber quando um usuário extremamente importante é capaz de acessar um aplicativo que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264135"
---
# <a name="check-the-status-of-user-provisioning"></a>Verifique o status do provisionamento do usuário

O serviço de provisionamento Azure AD executa um ciclo inicial de provisionamento contra o sistema de origem e o sistema de destino, seguido por ciclos incrementais periódicos. Ao configurar o provisionamento de um aplicativo, você pode verificar o status atual do serviço de provisionamento e ver quando um usuário poderá acessar um aplicativo.

## <a name="view-the-provisioning-progress-bar"></a>Ver a barra de progresso do provisionamento

 Na página **provisionamento** de um aplicativo, você pode visualizar o status do serviço de provisionamento Azure AD. A **seção Status atual** na parte inferior da página mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode observar o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas.

Quando você configura o provisionamento automático pela primeira vez, a seção **Status de corrente** na parte inferior da página mostra o status do ciclo inicial de provisionamento. Esta seção é atualizada cada vez que um ciclo incremental é executado. Os seguintes detalhes são mostrados:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está em execução ou foi concluído pela última vez.
- Uma **barra de progresso** mostrando a porcentagem do ciclo de provisionamento que foi concluído. A porcentagem reflete a contagem de páginas provisionadas. Observe que cada página pode conter vários usuários ou grupos, de modo que a porcentagem não se correlaciona diretamente com o número de usuários, grupos ou funções provisionadas.
- Um botão **Atualizar** você pode usar para manter a exibição atualizada.
- O número de **Usuários** e **Grupos** no armazenamento de dados do conector. A contagem aumenta sempre que um objeto é adicionado ao escopo do provisionamento. A contagem não será diminuindo se um usuário for excluído ou excluído, pois isso não remove o objeto do armazenamento de dados do conector. A contagem será recaculada a primeira sincronização após o CDS ser [redefinido](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- Um link De registro de auditoria de **exibição,** que abre os logs de provisionamento do Azure AD para obter detalhes sobre todas as operações executadas pelo serviço de provisionamento do usuário, incluindo status de provisionamento para usuários individuais (consulte a seção [Desprovisionamento de registros](#use-provisioning-logs-to-check-a-users-provisioning-status) de uso abaixo).

Após a conclusão de um ciclo de provisionamento, a seção **Estatísticas até** o momento mostra o número cumulativo de usuários e grupos que foram provisionados até o momento, juntamente com a data de conclusão e duração do último ciclo. O **ID de atividade** identifica exclusivamente o ciclo de provisionamento mais recente. O **Job ID** é um identificador exclusivo para o trabalho de provisionamento, e é específico para o aplicativo em seu inquilino.

O progresso do provisionamento pode ser visualizado no portal Azure, na guia Provisionamento do **aplicativo\] &gt; Azure Active Directory &gt; &gt; \[Enterprise Apps.**

![Barra de progresso da página de provisionamento](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Use registros de provisionamento para verificar o status de provisionamento de um usuário

Para ver o status de provisionamento de um usuário selecionado, consulte os [logs de provisionamento (visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Todas as operações executadas pelo serviço de provisionamento do usuário são registradas nos registros de provisionamento do Azure AD. Isso inclui todas as operações de leitura e gravação feitas para os sistemas de origem e destino, e os dados do usuário que foram lidos ou gravados durante cada operação.

Você pode acessar os logs de provisionamento no portal Azure selecionando os logs de provisionamento de &gt; aplicativos **corporativos** **do Azure Active Directory** &gt; **(visualização)** na seção **Atividade.** Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Registros de provisionamento (visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registros de provisionamento registram todas as operações realizadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo de provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando objetos de usuário entre o sistema
* Adicionar, atualizar ou desabilitar a conta de usuário no sistema de destino com base na comparação

Para obter mais informações sobre como ler os registros de provisionamento no portal Azure, consulte o [guia de relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo levará para provisionar os usuários?
Ao usar o provisionamento automático do usuário com um aplicativo, o Azure AD fornece e atualiza automaticamente as contas de usuário em um aplicativo com base em coisas como [atribuição de usuário e grupo](../manage-apps/assign-user-or-group-access-portal.md) em um intervalo de tempo regularmente programado, normalmente a cada 40 minutos.

O tempo necessário para um determinado usuário ser provisionado depende principalmente se o seu trabalho de provisionamento está executando um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial,** o tempo de trabalho depende de muitos fatores, incluindo o número de usuários e grupos em escopo para o provisionamento, e o número total de usuários e grupo no sistema de origem. A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Uma lista abrangente de fatores que afetam o desempenho inicial do ciclo é resumida mais tarde nesta seção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, em 10 minutos), pois o serviço de provisionamento armazena marcas d'água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subseqüentes. O tempo de trabalho depende do número de alterações detectadas nesse ciclo de provisionamento. Se houver menos de 5.000 alterações de membros de usuários ou grupos, o trabalho pode terminar dentro de um único ciclo de provisionamento incremental. 

A tabela a seguir resume os tempos de sincronização para cenários comuns de provisionamento. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização são derivados de uma análise estatística de trabalhos de sincronização para os aplicativos SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo inicial do ciclo | Tempo de ciclo incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  Menos de 1.000 |  Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1.000 a 10.000 | 142 a 708 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000 a 100.000 | 1.170 a 2.340 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  Menos de 1.000 | Menos de 30 minutos  | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1.000 a 10.000 | Menos de 30 minutos a 120 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000 a 100.000  | 713 minutos a 1.425 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD|  Menos de 1.000  | Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1.000 a 10.000  | 43 a 86 minutos | Menos de 30 minutos |


Somente para a configuração **Sincronização e grupos atribuídos,** você pode usar as seguintes fórmulas para determinar os tempos **mínimos** e máximos de ciclo inicial esperados:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo necessário para completar um **ciclo inicial**:

- O número total de usuários e grupos no escopo para provisionamento.

- O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os usuários no escopo do provisionamento são compatíveis com os usuários existentes no aplicativo-alvo ou precisam ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os usuários são criados pela primeira vez levam cerca de *duas vezes mais tempo do* que os trabalhos de sincronização para os quais todos os usuários são compatíveis com os usuários existentes.

- Número de erros nos [registros de provisionamento](check-status-user-account-provisioning.md). O desempenho será mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em estado de quarentena. 

- Limites de taxa de solicitação implementados pelo sistema de destino. Alguns sistemas-alvo implementam limites de taxa de solicitação e estrangulamento, o que pode afetar o desempenho durante grandes operações de sincronização. Sob essas condições, um aplicativo que recebe muitas solicitações de forma muito rápida pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa fazer ajustes para não enviar as solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

- O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos demora mais do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se um aplicativo tiver [mapeamentos habilitados para sincronização de objeto de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo como nomes de grupo e associações serão sincronizadas, além dos usuários. Essas sincronizações adicionais tomarão mais tempo do que apenas sincronizar os objetos de usuário.

- Se o desempenho se tornar um problema e você estiver tentando provisionar a maioria dos usuários e grupos em seu inquilino, use filtros de escopo. Filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Active Directory do Azure](user-provisioning.md)
