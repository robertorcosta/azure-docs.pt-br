---
title: Relatórios de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/09/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 256194d8b0b5e6b08210e9338d945774603ac328
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429799"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - Os **logs de auditoria** - logs de [auditoria](concept-audit-logs.md) fornecem informações de atividade do sistema sobre gerenciamento de usuários e de grupos, aplicativos gerenciados e atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador para uma tentativa de entrada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador de uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de entradas.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções administrador de segurança, leitor de segurança, leitor global e leitor de relatório
* Administradores globais
* Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?

* O locatário deve ter uma licença do Azure AD Premium associada a ele para ver todo o relatório de atividade de entrada. Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory. Levará alguns dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium sem atividades de dados antes da atualização.

## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas de usuário fornece respostas para as seguintes perguntas:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

No menu [portal do Azure](https://portal.azure.com) , selecione **Azure Active Directory**ou pesquise e selecione **Azure Active Directory** em qualquer página.

![Selecionar Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Em **monitoramento**, selecione **entradas** para abrir o relatório de [entradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Atividade de entrada](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Atividade de entrada")

Pode levar até duas horas para que alguns registros de entrada sejam exibidos no Portal.

> [!IMPORTANT]
> O relatório de entradas exibe apenas os **logins** interativos, isto é, os logins nos quais um usuário faz login manualmente usando seu nome de usuário e senha. Os logins não interativos, como a autenticação de serviço a serviço, não são exibidos no relatório de logins. 

Um log de entradas tem um modo de exibição de lista padrão que mostra:

- A hora de entrada
- O usuário relacionado
- O aplicativo ao qual o usuário entrou
- O status de entrada
- O status da detecção de riscos
- O status do requisito de MFA (autenticação multifator)

![Atividade de entrada](./media/concept-sign-ins/sign-in-activity.png "Atividade de entrada")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Atividade de entrada](./media/concept-sign-ins/19.png "Atividade de entrada")

A caixa de diálogo **colunas** fornece acesso aos atributos selecionáveis. Em um relatório de entrada, você não pode ter campos que tenham mais de um valor para uma determinada solicitação de entrada como coluna. Isso é, por exemplo, verdadeiro para detalhes de autenticação, dados de acesso condicional e local de rede.   

![Atividade de entrada](./media/concept-sign-ins/columns.png "Atividade de entrada")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Atividade de entrada](./media/concept-sign-ins/basic-sign-in.png "Atividade de entrada")

> [!NOTE]
> Agora, os clientes podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de entrada. Ao clicar na guia **acesso condicional** para um registro de entrada, os clientes podem examinar o status de acesso condicional e aprofundar-se nos detalhes das políticas que foram aplicadas à entrada e ao resultado de cada política.
> Para obter mais informações, consulte as [Perguntas frequentes sobre as informações de CA em todos os logins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Primeiro, restringir os dados relatados a um nível que funciona para você. Em segundo lugar, filtre os dados de entrada usando o campo de data como filtro padrão. O Azure AD oferece uma ampla variedade de filtros adicionais que você pode definir.

![Atividade de entrada](./media/concept-sign-ins/04.png "Atividade de entrada")

O filtro **Usuário** permite que você especifique o nome ou o UPN (nome UPN) do usuário desejado.

O filtro **Aplicativo** permite que você especifique o nome do aplicativo desejado.

O filtro **status de entrada** permite que você selecione:

- Tudo
- Sucesso
- Falha

O filtro **Acesso Condicional** permite que você selecione o status da política de Autoridade de Certificação para a entrada:

- Tudo
- Não aplicado
- Sucesso
- Falha

O filtro **Data** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- Um mês
- 7 dias
- 24 horas
- Intervalo de tempo personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

Se você adicionar outros campos ao modo de exibição de entradas, esses campos serão adicionados automaticamente à lista de filtros. Por exemplo, ao adicionar o campo **Aplicativo Cliente** à sua lista, você também obtém outra opção de filtro que permite definir os seguintes filtros:  
![Atividade de entrada](./media/concept-sign-ins/12.png "Atividade de entrada")

- **Navegador**  
    Esse filtro mostra todos os eventos em que as tentativas de entrada foram tentadas usando fluxos de navegador.
- **Exchange ActiveSync (com suporte)**  
    Esse filtro mostra todas as tentativas de entrada em que o protocolo EAS (Exchange ActiveSync) foi tentado a partir de plataformas com suporte, como iOS, Android e Windows Phone.
- **Exchange ActiveSync (sem suporte)**  
    Esse filtro mostra todas as tentativas de entrada nas quais o protocolo EAS foi tentado a partir de plataformas sem suporte, como o Linux distribuições.
- **Aplicativos móveis e clientes de área de trabalho** O filtro mostra todas as tentativas de entrada que não estavam usando fluxos de navegador. Por exemplo, aplicativos móveis de qualquer plataforma usando qualquer protocolo ou de aplicativos cliente de desktop como Office no Windows ou MacOS.
  
- **Outros clientes**
    - **IMAP**  
        Um cliente de email herdado usando IMAP para recuperar email.
    - **MAPI**  
        Office 2013, onde a ADAL está habilitada e está usando MAPI.
    - **Clientes do Office antigos**  
        Office 2013 em sua configuração padrão em que a ADAL não está habilitada e está usando MAPI ou o Office 2016 em que a ADAL foi desabilitada.
    - **POP**  
        Um cliente de email herdado usando POP3 para recuperar email.
    - **SMTP**  
        Um cliente de email herdado usando SMTP para enviar email.

## <a name="download-sign-in-activities"></a>Baixar atividades de entrada

Clique na opção **baixar** para criar um arquivo CSV ou JSON dos registros 250.000 mais recentes. Comece com [baixar os dados de entradas](quickstart-download-sign-in-report.md) se quiser trabalhar com eles fora do portal do Azure.  

![Download](./media/concept-sign-ins/71.png "Baixar")

> [!IMPORTANT]
> O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos dos dados de entradas

O Azure AD e o portal do Azure fornecem pontos de entrada adicionais para os dados de entrada:

- Visão geral da proteção de segurança de identidade
- Usuários
- Grupos
- Aplicativos empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dados de entradas de usuário na proteção de segurança de identidade

O grafo de entrada do usuário na página Visão geral da **proteção de segurança de identidade** mostra as agregações semanais de entradas. O padrão para o período de tempo é de 30 dias.

![Atividade de entrada](./media/concept-sign-ins/06.png "Atividade de entrada")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada do dia.

Cada linha na lista de atividades de entrada mostra:

* Quem entrou?
* Qual aplicativo era o destino da entrada?
* Qual é o status da entrada?
* Qual é o status de MFA da entrada?

Ao clicar em um item, você verá mais detalhes sobre a operação de entrada:

- Id de Usuário
- Usuário
- Nome de Usuário
- ID do aplicativo
- Aplicativo
- Cliente
- Local
- Endereço IP
- Data
- MFA obrigatório
- Status de entrada

> [!NOTE]
> Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é dificultado pelo fato de que os provedores móveis e VPNs emitem endereços IP de pools centrais que geralmente estão muito longe de onde o dispositivo cliente realmente é usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.

Na página **Usuários**, você obtém uma visão geral completa de todas as entradas do usuário clicando em **Entradas** na seção **Atividade**.

![Atividade de entrada](./media/concept-sign-ins/08.png "Atividade de entrada")

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Como está o meu aplicativo mais recente?

O ponto de entrada para esses dados são os três principais aplicativos em sua organização. Os dados estão contidos no relatório últimos 30 dias na seção **visão geral** em **aplicativos empresariais**.

![Atividade de entrada](./media/concept-sign-ins/10.png "Atividade de entrada")

Os gráficos de uso de aplicativo geram agregações semanais de entradas para os três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/concept-sign-ins/graph-chart.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "Relatório")

Quando você clica em um dia no grafo de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

Você pode exibir os logs de atividade do Office 365 no [centro de administração do Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Considere o ponto que, a atividade do Office 365 e os logs de atividade do Azure AD compartilham um número significativo de recursos de diretório. Somente o centro de administração Microsoft 365 fornece uma visão completa dos logs de atividades do Office 365. 

Você também pode acessar os logs de atividade do Office 365 programaticamente usando as [APIs de gerenciamento do office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximos passos

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências do relatório do Azure AD](reference-reports-latencies.md)

