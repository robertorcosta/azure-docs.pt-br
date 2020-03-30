---
title: Problema sao configurar o provisionamento do usuário em um aplicativo da Azure AD Gallery
description: Como solucionar problemas comuns enfrentados ao configurar provisionamento do usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522757"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD

Configurando [provisionamento automático de usuário](user-provisioning.md) para um aplicativo (onde houver suporte), requer que as instruções específicas ser seguidas para preparar o aplicativo para o provisionamento automático. Em seguida, você pode usar o portal do Azure para configurar o serviço de provisionamento para sincronizar contas de usuário ao aplicativo.

Você sempre deve começar encontrando o tutorial de instalação específica de configuração de provisionamento para seu aplicativo. Siga essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. Uma lista de tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Como saber se o provisionamento está funcionando 

Depois que o serviço estiver configurado, a maioria das informações sobre a operação do serviço podem ser obtidas de dois lugares:

-   **Registros de provisionamento (visualização)** – Os [registros de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registram todas as operações realizadas pelo serviço de provisionamento, incluindo a consulta do Azure AD para usuários atribuídos que estejam em escopo para provisionamento. O aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema de consulta. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação. Você pode acessar os logs de provisionamento no portal Azure selecionando os logs de provisionamento de &gt; aplicativos **corporativos** **do Azure Active Directory** &gt; **(visualização)** na seção **Atividade.**

-   **Status atual -** Um resumo da última execução de provisionamento para um determinado aplicativo pode ser visto na seção Provisionamento de **nomes\] &gt;de aplicativos do Azure Active &gt; Directory Enterprise Application, &gt; \[** na parte inferior da tela as configurações de serviço. A seção Status atual mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode observar o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas. Se houver algum erro, os detalhes podem ser encontrados nos registros de provisionamento (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar

Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

* [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
* Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando
* [Os registros de provisionamento dizem que os usuários são "ignorados" e não provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar

Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página depois recarregamentos subsequentes. É provável que o serviço esteja em execução, mas ainda não completou um ciclo inicial. Verifique os **registros de provisionamento descritos** acima para determinar quais operações o serviço está realizando e se há algum erro.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório Azure AD e do número de usuários em escopo para provisionamento. As sincronizações subseqüentes após o ciclo inicial serão mais rápidas, pois o serviço de provisionamento armazena marcas d'água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subseqüentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando

Para o provisionamento funcionar, Azure AD requer credenciais válidas que permitem a conexão com uma API fornecida pelo aplicativo de gerenciamento de usuário. Se as credenciais não funcionarem, ou você não souber quais devem ser, examine o tutorial de configuração do aplicativo, descrito anteriormente.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registros de provisionamento dizem que os usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos

Quando um usuário aparece como "ignorado" nos registros de provisionamento, é muito importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações, consulte [o provisionamento de aplicativos baseado em atributos com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento ser examinar e configurar os mapeamentos de atributo e fluxos de trabalho que definem quais fluxo propriedades de usuário (ou grupo) do Azure AD para o aplicativo. Isso inclui a configuração de "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalização de mapeamentos de atributos de provisionamento de usuários](../app-provisioning/customize-application-attributes.md).

  * **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode ativar ou desativar essa funcionalidade ativando ou desativando o **Mapeamento** para objetos de grupo mostrados na guia **Provisionamento.** Se os grupos de provisionamento forem habilitados, certifique-se de revisar os mapeamentos de atributos para garantir que um campo apropriado esteja sendo usado para o "ID correspondente". Este pode ser o nome de exibição ou alias de email, já que o grupo e seus membros não são provisionados se a propriedade correspondente está vazia ou não está preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Próximas etapas
[Automatize o provisionamento e o desprovisionamento do usuário para aplicativos SaaS com o Azure Active Directory](user-provisioning.md)
