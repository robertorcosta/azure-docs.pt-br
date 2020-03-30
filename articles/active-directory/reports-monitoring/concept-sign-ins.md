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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246510"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs** - de[auditoria Os registros de auditoria](concept-audit-logs.md) fornecem informações sobre a atividade do sistema sobre usuários e gerenciamento de grupo, aplicativos gerenciados e atividades de diretório.
- **Segurança** 
    - **Logins arriscados** - Um [login arriscado](concept-risky-sign-ins.md) é um indicador para uma tentativa de login por alguém que não é o legítimo proprietário de uma conta de usuário.
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de logins.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções administrador de segurança, leitor de segurança, leitor global e leitor de relatórios
* Administradores globais
* Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?

- O relatório de atividade de login está disponível em [todas as edições do Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data).

- Se você quiser acessar os dados de login usando uma API, seu inquilino deve ter uma licença [Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) associada a ele.



## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas de usuário fornece respostas para as seguintes perguntas:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

No menu do [portal Azure,](https://portal.azure.com) selecione **O Diretório Ativo do Azure**ou procure e selecione **o Diretório Ativo do Azure** em qualquer página.

![Selecione Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Em **Monitoramento,** **selecione Logins** para abrir o [relatório De inscrições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Atividade de login](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Atividade de entrada")

Pode levar até duas horas para que alguns registros de login apareçam no portal.

> [!IMPORTANT]
> O relatório de entradas exibe apenas os **logins** interativos, isto é, os logins nos quais um usuário faz login manualmente usando seu nome de usuário e senha. Os logins não interativos, como a autenticação de serviço a serviço, não são exibidos no relatório de logins. 

Um log de entradas tem um modo de exibição de lista padrão que mostra:

- A hora de entrada
- O usuário relacionado
- O aplicativo que o usuário fez com
- O status de entrada
- O status da detecção de riscos
- O status do requisito de MFA (autenticação multifator)

![Atividade de login](./media/concept-sign-ins/sign-in-activity.png "Atividade de entrada")

Você pode personalizar a exibição da lista clicando em **Colunas** na barra de ferramentas.

![Atividade de login](./media/concept-sign-ins/19.png "Atividade de entrada")

A caixa de diálogo **Colunas** dá acesso aos atributos selecionáveis. Em um relatório de login, você não pode ter campos que tenham mais de um valor para uma determinada solicitação de login como coluna. Isso é, por exemplo, verdadeiro para detalhes de autenticação, dados de acesso condicional e localização da rede.   

![Atividade de login](./media/concept-sign-ins/columns.png "Atividade de entrada")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Atividade de login](./media/concept-sign-ins/basic-sign-in.png "Atividade de entrada")

> [!NOTE]
> Os clientes agora podem solucionar problemas nas políticas de acesso condicional através de todos os relatórios de login. Ao clicar na guia **Acesso Condicional** para um registro de login, os clientes podem rever o status de Acesso Condicional e mergulhar nos detalhes das políticas aplicadas ao login e no resultado de cada política.
> Para obter mais informações, consulte as [Perguntas frequentes sobre as informações de CA em todos os logins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Primeiro, reduzindo os dados relatados a um nível que funcione para você. Em segundo lugar, filtre os dados de login usando o campo de data sustais como filtro padrão. O Azure AD fornece uma ampla gama de filtros adicionais que você pode definir:

![Atividade de login](./media/concept-sign-ins/04.png "Atividade de entrada")

**Solicitação de ID** - O ID da solicitação que você se importa.

**Usuário** - O nome ou o nome principal do usuário (UPN) do usuário que você gosta.

**Aplicação** - O nome da aplicação de destino.
 
**Status** - O status de login que você se importa:

- Sucesso

- Falha

- Interrompido


**Endereço IP** - O endereço IP do dispositivo usado para se conectar ao seu inquilino.

A **Localização** - O local de onde a conexão foi iniciada:

- City

- Estado / Província

- País/Região


**Recurso** - O nome do serviço utilizado para o login.


**ID de recurso** - O ID do serviço usado para o login.


**Aplicativo cliente** - O tipo de aplicativo do cliente usado para se conectar ao seu inquilino:

![Filtro de aplicativo cliente](./media/concept-sign-ins/client-app-filter.png)


|Nome|Autenticação moderna|Descrição|
|---|:-:|---|
|SMTP autenticado| |Usado por clientes POP e IMAP para enviar mensagens de e-mail.|
|Autodiscover| |Usado pelos clientes Outlook e EAS para encontrar e conectar-se às caixas de correio no Exchange Online.|
|Exchange ActiveSync| |Este filtro mostra todas as tentativas de login onde o protocolo EAS foi tentado.|
|Navegador|![Verificação](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de login dos usuários usando navegadores da Web|
|Exchange ActiveSync| | Mostra todas as tentativas de login de usuários com aplicativos clientes usando o Exchange ActiceSync para se conectar ao Exchange Online|
|PowerShell do Exchange Online| |Usado para se conectar ao Exchange Online com powershell remoto. Se você bloquear a autenticação básica do Exchange Online PowerShell, você precisará usar o módulo Exchange Online PowerShell para se conectar. Para obter instruções, consulte [Conecte-se ao Exchange Online PowerShell usando autenticação multifatorial](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Serviços Web do Exchange| |Uma interface de programação usada pelo Outlook, Outlook para Mac e aplicativos de terceiros.|
|IMAP4| |Um cliente de e-mail legado usando o IMAP para recuperar e-mails.|
|MAPI sobre HTTP| |Usado pelo Outlook 2010 e posterior.|
|Aplicativos móveis e clientes de desktop|![Verificação](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de login dos usuários usando aplicativos móveis e clientes de desktop.|
|Catálogo de endereços offline| |Uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.|
|Outlook Anywhere (RPC over HTTP)| |Usado pelo Outlook 2016 e anterior.|
|Serviço Outlook| |Usado pelo aplicativo Mail and Calendar para Windows 10.|
|POP3| |Um cliente de e-mail legado usando pop3 para recuperar e-mails.|
|Relatórios de serviços da Web| |Usado para recuperar dados de relatórios no Exchange Online.|
|Outros clientes| |Mostra todas as tentativas de login dos usuários onde o aplicativo cliente não está incluído ou desconhecido.|



**Sistema operacional** - O sistema operacional em execução no dispositivo usou o login para o seu inquilino. 


**Navegador do dispositivo** - Se a conexão foi iniciada a partir de um navegador, este campo permite que você filtre pelo nome do navegador.


**ID de correlação** - O ID de correlação da atividade.




**Acesso condicional** - O status das regras de acesso condicional aplicadas

- **Não aplicado**: Nenhuma política aplicada ao usuário e aplicativo durante o login.

- **Sucesso**: Uma ou mais políticas de acesso condicional aplicadas ao usuário e aplicativo (mas não necessariamente às outras condições) durante o login. 

- **Falha**: Uma ou mais políticas de acesso condicional aplicadas e não foi satisfeita durante o login.









## <a name="download-sign-in-activities"></a>Baixar atividades de entrada

Clique na opção **Download** para criar um arquivo CSV ou JSON dos 250.000 registros mais recentes. Comece com [o download dos dados de login](quickstart-download-sign-in-report.md) se quiser trabalhar com ele fora do portal Azure.  

![Download](./media/concept-sign-ins/71.png "Baixar")

> [!IMPORTANT]
> O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos dos dados de entradas

O Azure AD e o portal Azure fornecem pontos de entrada adicionais aos dados de login:

- Visão geral da proteção de segurança de identidade
- Usuários
- Grupos
- Aplicativos empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dados de entradas de usuário na proteção de segurança de identidade

O gráfico de login do usuário na página visão geral de **proteção** de segurança de identidade mostra agregações semanais de logins. A inadimplência para o período é de 30 dias.

![Atividade de login](./media/concept-sign-ins/06.png "Atividade de entrada")

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
- Location
- Endereço IP
- Data
- MFA obrigatório
- Status de entrada

> [!NOTE]
> Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é dificultado pelo fato de que os provedores móveis e VPNs emitem endereços IP de pools centrais que geralmente estão muito longe de onde o dispositivo cliente realmente é usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.

Na página **Usuários**, você obtém uma visão geral completa de todas as entradas do usuário clicando em **Entradas** na seção **Atividade**.

![Atividade de login](./media/concept-sign-ins/08.png "Atividade de entrada")

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são as três principais aplicações da sua organização?
* Como está indo minha mais nova aplicação?

O ponto de entrada para esses dados são os três principais aplicativos da sua organização. Os dados estão contidos no relatório dos últimos 30 dias na seção **Visão geral** em **aplicativos Corporativos**.

![Atividade de login](./media/concept-sign-ins/10.png "Atividade de entrada")

O uso de aplicativos faz gráficos semanais de agregações de logins para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de login](./media/concept-sign-ins/graph-chart.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/concept-sign-ins/single-app-usage-graph.png "Relatório")

Quando você clica em um dia no grafo de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

Você pode visualizar os registros de atividades do Office 365 a partir do [centro de admin microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Considere o ponto que, as atividades do Office 365 e os registros de atividades do Azure AD compartilham um número significativo dos recursos do diretório. Apenas o centro de admin microsoft 365 fornece uma visão completa dos registros de atividades do Office 365. 

Você também pode acessar os registros de atividades do Office 365 de forma programática usando as APIs de gerenciamento do [Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências do relatório do Azure AD](reference-reports-latencies.md)

