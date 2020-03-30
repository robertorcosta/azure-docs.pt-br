---
title: Reporte o provisionamento automático da conta de usuário aos aplicativos SaaS
description: Saiba como verificar o status dos trabalhos de provisionamento de automático de conta de usuário e como solucionar problemas com o provisionamento de usuários individuais.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282181"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o provisionamento automático de conta de usuário

O Azure Active Directory (Azure AD) inclui um serviço de [provisionamento de contas](user-provisioning.md) de usuário que ajuda a automatizar o provisionamento de contas de usuário em aplicativos SaaS e outros sistemas, com o objetivo de gerenciamento de ciclo de vida de identidade de ponta a ponta. O Azure AD suporta conectores de provisionamento de usuários pré-integrados para todos os aplicativos e sistemas com tutoriais de provisionamento de usuários [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

Este artigo descreve como verificar o status de trabalhos de provisionamento depois de eles terem sido configurados e como solucionar problemas com o provisionamento de usuários e grupos individuais.

## <a name="overview"></a>Visão geral

Conectores de provisionamento são configurados e definidos usando o [Portal do Azure](https://portal.azure.com), seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para o aplicativo suportado. Depois de estarem configurados e em execução, os trabalhos de provisionamento podem ser informados usando um dos dois métodos:

* **Portal Azure** - Este artigo descreve principalmente a recuperação de informações do relatório do [portal Azure,](https://portal.azure.com)que fornece tanto um relatório de resumo de provisionamento como registros de auditoria de provisionamento detalhados para uma determinada aplicação.
* **API de auditoria** – o Azure Active Directory também fornece uma API de auditoria que permite a recuperação programática dos logs de auditoria de provisionamento detalhados. Consulte a [referência da API de auditoria do Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para a documentação específica para usar essa API. Embora este artigo não cubra especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos a seguir:

* **Sistema de origem** – o repositório de usuários do qual o serviço de provisionamento do Azure AD faz a sincronização. O Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento previamente integrados, no entanto, há algumas exceções (exemplo: Sincronização de Entrada do Workday).
* **Sistema de destino** – o repositório de usuários ao qual o serviço de provisionamento do Azure AD faz a sincronização. Este é tipicamente um aplicativo SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas em alguns casos pode ser um sistema local, como o Active Directory (exemplo: Sincronização de entrada no dia de trabalho para diretório ativo).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal Azure

Para obter informações de relatório de provisionamento para um determinado aplicativo, inicie o lançamento do [portal Azure](https://portal.azure.com) e do **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** na seção **Atividade.** Você também pode navegar até o Aplicativo Corporativo para o qual o provisionamento está configurado. Por exemplo, se você estiver provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos > LinkedIn Elevate**

A partir daqui, você pode acessar tanto a barra de progresso de provisionamento quanto os registros de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Barra de progresso do provisionamento

A [barra de progresso do provisionamento](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) é visível na guia **Provisionamento** para aplicação dada. Ele está localizado na seção **Status de corrente** abaixo de **Configurações**e mostra o status do ciclo inicial ou incremental atual. Esta seção também mostra:

* O número total de usuários e grupos que foram sincronizados e estão atualmente no escopo para provisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações normalmente ocorrem a cada 20-40 minutos, após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) ter sido concluído.
* Se um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi ou não concluído.
* Se o processo de provisionamento foi colocado em quarentena ou não e qual o motivo do status de quarentena (por exemplo, falha na comunicação com o sistema de destino devido a credenciais inválidas do administrador).

O **Estado Atual** deve ser o primeiro lugar que os admins procuram verificar a saúde operacional do trabalho de provisionamento.

 ![Relatório resumido](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Registros de provisionamento (visualização)

Todas as atividades realizadas pelo serviço de provisionamento são registradas nos registros de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)azure AD . Você pode acessar os logs de provisionamento no portal Azure selecionando os logs de provisionamento de &gt; aplicativos **corporativos** **do Azure Active Directory** &gt; **(visualização)** na seção **Atividade.** Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Registros de provisionamento (visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Alguns dos tipos de eventos de atividade incluídos no log são:

## <a name="troubleshooting"></a>Solução de problemas

O relatório de provisionamento e os registros de provisionamento desempenham um papel fundamental ajudando os administraçãos a solucionar problemas de provisionamento de várias contas de usuário.

Para obter diretrizes baseada em cenário sobre como solucionar problemas de provisionamento automático de usuário, consulte [Problemas ao configurar e provisionar usuários para um aplicativo](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
