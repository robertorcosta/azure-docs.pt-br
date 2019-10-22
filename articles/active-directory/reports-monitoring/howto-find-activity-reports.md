---
title: Localizar Azure Active Directory relatórios de atividade do usuário no portal do Azure | Microsoft Docs
description: Saiba onde os relatórios de atividade de usuário do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f1746b710acd24316de3d294c1822ba108a378
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70127384"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no portal do Azure

Neste artigo, você aprenderá a localizar os relatórios de atividade de usuário do Azure Active Directory (Azure AD) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de logs de auditoria

O relatório de logs de auditoria combina vários relatórios sobre as atividades do aplicativo em uma única exibição para relatórios baseados em contexto. Para acessar o relatório de logs de auditoria:

1. Navegue até a [portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** no painel de navegação à esquerda.
3. Selecione **logs de auditoria** na seção **atividade** da folha Azure Active Directory. 

    ![Logs de auditoria](./media/howto-find-activity-reports/482.png "Logs de auditoria")

O relatório de logs de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de redefinição de senha
* Atividade de registro de redefinição de senha
* Atividade de grupos de autoatendimento
* Alterações de nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de provisionamento de conta

### <a name="filtering-on-audit-logs"></a>Filtrando logs de auditoria

Você pode usar a filtragem avançada no relatório de auditoria para acessar uma categoria específica de dados de auditoria, especificando-a no filtro de **categoria** . Por exemplo, para exibir todas as atividades relacionadas a usuários, selecione a categoria **usermanagement** . 

As categorias incluem:

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
- Outros
- Política
- ResourceManagement
- RoleManagement
- UserManagement

Você também pode filtrar em um serviço específico usando o filtro suspenso de **serviço** . Por exemplo, para obter todos os eventos de auditoria relacionados ao gerenciamento de senhas de autoatendimento, selecione o filtro de **Gerenciamento de senhas de autoatendimento** .

Os serviços incluem:

- Todos
- Revisões de Acesso
- Provisionamento de conta 
- SSO de aplicativo
- Métodos de autenticação
- B2C
- Acesso condicional
- Diretório principal
- Gerenciamento de direitos
- Proteção de identidade
- Usuários convidados
- FABRICANTE
- Gerenciamento de grupo de autoatendimento
- Gerenciamento de senhas de autoatendimento
- Termos de Uso

## <a name="sign-ins-report"></a>Relatório de entradas 

A exibição de **entradas** inclui todas as entradas de usuário, bem como o relatório de **uso do aplicativo** . Você também pode exibir informações de uso do aplicativo na seção **gerenciar** da visão geral dos **aplicativos empresariais** .

Para acessar o relatório de entradas:

1. Navegue até a [portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** no painel de navegação à esquerda.
3. Selecione **entrada** na seção **atividade** da folha Azure Active Directory. 

    ![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")


### <a name="filtering-on-application-name"></a>Filtrando o nome do aplicativo

Você pode usar o relatório de entradas para exibir detalhes sobre o uso do aplicativo, filtrando o nome de usuário ou o nome do aplicativo.

![Página filtrar eventos de entrada](./media/howto-find-activity-reports/07.png "Página filtrar eventos de entrada")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividades anômalas

Os relatórios de atividade anômala fornecem informações sobre as detecções de riscos relacionadas à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança da atividade anômala do Azure AD e os tipos de detecção de risco correspondentes no portal do Azure. Para obter mais informações, consulte [Azure Active Directory as detecções de risco](concept-risk-events.md).  


| Relatório de atividade anômala do Azure AD |  Tipo de detecção de risco de proteção de identidade|
| :--- | :--- |
| Usuários com credenciais insuficientes | Credenciais vazadas |
| Atividades de entrada irregulares | Viagem impossível a locais atípicos |
| Entradas de dispositivos possivelmente infectados | Entradas de dispositivos infectados|
| Entradas de fontes desconhecidas | Entradas de endereços IP anônimos |
| Entradas de endereços IP com atividade suspeita | Entradas de endereços IP com atividade suspeita |
| - | Entradas de locais desconhecidos |

Os seguintes relatórios de segurança de atividade anômala do Azure AD não estão incluídos como detecções de risco no portal do Azure:

* Entradas após várias falhas
* Entradas de várias geografias


### <a name="detected-risk-detections"></a>Detecções de risco detectadas

Você pode acessar relatórios sobre as detecções de risco detectadas na seção **segurança** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). As detecções de risco detectadas são controladas nos seguintes relatórios:   

- [Usuários em risco](concept-user-at-risk.md)
- [Entradas de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Solucionar problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados ausentes nos logs de atividades baixados

#### <a name="symptoms"></a>Sintomas 

Baixei os logs de atividades (auditoria ou entradas) e não vejo todos os registros durante o tempo que escolhi. Por quê? 

 ![Relatório](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Faz

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala a 250000 registros, classificados por mais recentes primeiro. 

#### <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatório do Azure ad](concept-reporting-api.md) para buscar até um milhão de registros em qualquer momento determinado.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dados de auditoria ausentes para ações recentes no portal do Azure

#### <a name="symptoms"></a>Sintomas

Executei algumas ações na portal do Azure e esperava ver os logs de auditoria dessas ações na folha `Activity logs > Audit Logs`, mas não consigo encontrá-las.

 ![Relatório](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Faz

As ações não aparecem imediatamente nos logs de atividade. A tabela a seguir enumera os números de latência para os logs de atividade. 

| Relate | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 minutos |
| Atividade de entrada | &nbsp; | 2 minutos | 5 minutos | 

#### <a name="resolution"></a>Resolução

Aguarde 15 minutos a duas horas e veja se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, registre [um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e vamos examinar isso.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Logs ausentes para entradas recentes de usuário no log de atividades de entradas do Azure AD

#### <a name="symptoms"></a>Sintomas

Recentemente, entrei na portal do Azure e esperava ver os logs de entrada dessas ações na folha `Activity logs > Sign-ins`, mas não consigo encontrá-las.

 ![Relatório](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Faz

As ações não aparecem imediatamente nos logs de atividade. A tabela a seguir enumera os números de latência para os logs de atividade. 

| Relate | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 minutos |
| Atividade de entrada | &nbsp; | 2 minutos | 5 minutos | 

#### <a name="resolution"></a>Resolução

Aguarde 15 minutos a duas horas e veja se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, registre [um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e vamos examinar isso.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo exibir mais de 30 dias de dados de relatório no portal do Azure

#### <a name="symptoms"></a>Sintomas

Não consigo exibir mais de 30 dias de entrada e auditoria de dados do portal do Azure. Por quê? 

 ![Relatório](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Faz

Dependendo de sua licença, Azure Active Directory ações armazena relatórios de atividades para as seguintes durações:

| Relate           | &nbsp; |  Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Auditoria de Diretório  | &nbsp; |   7 dias     | 30 dias             | 30 dias             |
| Atividade de entrada | &nbsp; | Não disponível. Você pode acessar suas próprias entradas por 7 dias a partir da folha perfil de usuário individual | 30 dias | 30 dias             |

Para obter mais informações, consulte [Azure Active Directory relatar políticas de retenção](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Resolução

Você tem duas opções para reter os dados por mais de 30 dias. Você pode usar as [APIs de relatório do AD do Azure](concept-reporting-api.md) para recuperar os dados programaticamente e armazená-los em um banco de dados. Como alternativa, você pode integrar os logs de auditoria em um sistema SIEM de terceiros, como Splunk ou SumoLogic.

## <a name="next-steps"></a>Próximos passos

* [Visão geral dos logs de auditoria](concept-audit-logs.md)
* [Visão geral de entradas](concept-sign-ins.md)
* [Visão geral dos eventos arriscados](concept-risk-events.md)
