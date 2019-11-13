---
title: Auditoria de relatórios de atividade no portal do Azure Active Directory | Microsoft Docs
description: Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8883e3ade7e5d52f3a62346bf8be51ac36042f82
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008537"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de auditoria no portal do Azure Active Directory 

Com os relatórios do Azure AD (Azure Active Directory), é possível obter as informações necessárias para determinar o desempenho do ambiente.

A arquitetura de relatórios consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – O [relatório de entradas](concept-sign-ins.md) fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs de auditoria** – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- **Segurança** 
    - **Entradas arriscadas** - Uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador para uma tentativa de entrada que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador de uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções **administrador de segurança**, **leitor de segurança**, leitor de **relatório** ou **administrador global**

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do sistema para fins de conformidade. Para acessar o relatório de auditoria, selecione **Logs de auditoria** na seção **Atividade** do **Azure Active Directory**. Observe que os logs de auditoria podem ter uma latência de, no máximo, uma hora. Por isso, os dados da atividade de auditoria podem demorar esse período para aparecer no portal após a conclusão da tarefa.



Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência
- o serviço que registrou a ocorrência
- a categoria e o nome da atividade (*o que*) 
- o status da atividade (êxito ou falha)
- o destino
- o iniciador/ator (quem) de uma atividade

![Logs de auditoria](./media/concept-audit-logs/listview.png "Logs de auditoria")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/concept-audit-logs/columns.png "Logs de auditoria")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Logs de auditoria](./media/concept-audit-logs/columnselect.png "Logs de auditoria")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Logs de auditoria](./media/concept-audit-logs/details.png "Logs de auditoria")


## <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria

É possível filtrar os dados de auditoria nos seguintes campos:

- O Barramento de
- Categoria
- Atividade
- Status
- Destino
- Iniciado por (ator)
- Intervalo de datas

![Logs de auditoria](./media/concept-audit-logs/filter.png "Logs de auditoria")

O filtro de **serviço** permite que você selecione em uma lista suspensa dos seguintes serviços:

- Todos
- Revisões de acesso
- Provisionamento de conta de usuário 
- SSO de aplicativo
- Métodos de autenticação
- B2C
- Acesso Condicional
- Diretório principal
- Gerenciamento de direitos
- Identity Protection
- Usuários Convidados
- PIM
- Gerenciamento de grupos de autoatendimento
- Gerenciamento de senhas de auto-atendimento
- Termos de Uso

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- AdministrativeUnit
- ApplicationManagement
- Autenticação
- Autorização
- Contato
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- outro
- Política
- ResourceManagement
- RoleManagement
- UserManagement

O filtro de **atividade** é baseado na categoria e na seleção do tipo de recurso de atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

Você pode obter a lista de todas as atividades de auditoria usando o API do Graph: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

O filtro de **status** permite filtrar com base no status de uma operação de auditoria. O status pode ser um dos seguintes:

- Todos
- Sucesso
- Failure

O filtro de **destino** permite que você pesquise um destino específico por nome ou nome principal do usuário (UPN). O nome de destino e o UPN diferenciam maiúsculas de minúsculas. 

O filtro **iniciado por** permite que você defina o nome de um ator ou um nome de entidade universal (UPN). O nome e o UPN diferenciam maiúsculas de minúsculas.

O filtro de **intervalo de datas** permite que você defina um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

Você também pode optar por baixar os dados filtrados, até 250.000 registros, selecionando o botão **baixar** . Você pode baixar os logs no formato CSV ou JSON. O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).

![Logs de auditoria](./media/concept-audit-logs/download.png "Logs de auditoria")

## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos

Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

- Que tipos de atualizações foram aplicadas os usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador fez em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou a um usuário?

Se você quiser revisar apenas os dados de auditoria relacionados aos usuários, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **atividade** da guia **usuários** . Este ponto de entrada tem **usermanagement** como categoria preselecionada.

![Logs de auditoria](./media/concept-audit-logs/users.png "Logs de auditoria")

Se você quiser revisar apenas os dados de auditoria relacionados a grupos, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **atividade** da guia **grupos** . Este ponto de entrada tem **GroupManagement** como categoria preselecionada.

![Logs de auditoria](./media/concept-audit-logs/groups.png "Logs de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Logs de auditoria de aplicativos empresariais

Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Uma entidade de serviço para um aplicativo foi alterada?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se você quiser revisar os dados de auditoria relacionados aos aplicativos, poderá encontrar uma exibição filtrada em **Logs de auditorias** na seção **Atividade** da folha **Aplicativos empresariais**. Esse ponto de entrada tem **aplicativos empresariais** selecionados como o **tipo de aplicativo**.

![Logs de auditoria](./media/concept-audit-logs/enterpriseapplications.png "Logs de auditoria")

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

Você pode exibir os logs de atividade do Office 365 no [centro de administração do Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora a atividade do Office 365 e os logs de atividades do Azure AD compartilhem muitos recursos de diretório, somente o Microsoft 365 centro de administração fornece uma visão completa dos logs de atividade do Office 365. 

Você também pode acessar os logs de atividade do Office 365 programaticamente usando as [APIs de gerenciamento do office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

- [Referência das atividades de auditoria do Azure AD](reference-audit-activities.md)
- [Referência de retenção de relatórios do Azure AD](reference-reports-data-retention.md)
- [Referência de latências de log do Azure AD](reference-reports-latencies.md)
