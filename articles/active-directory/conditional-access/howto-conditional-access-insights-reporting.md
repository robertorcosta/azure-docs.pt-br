---
title: Pasta de trabalho de insights e relatórios de acesso condicional - Microsoft Azure Active Directory
description: Uso da pasta de trabalho de insights e relatórios do Microsoft Azure Active Directory para solucionar problemas de políticas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4feeca1cbe7eb88aace811829e4d9c2db5f38e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641600"
---
# <a name="conditional-access-insights-and-reporting"></a>Insights e relatórios de acesso condicional

A pasta de trabalho de insights e relatórios de acesso condicional permite entender o impacto das políticas de Acesso Condicional em sua organização ao longo do tempo. Durante a entrada, uma ou mais políticas de Acesso Condicional podem ser aplicados, concedendo o acesso se determinados controles de concessão forem atendidos ou então negando o acesso. Como várias políticas de Acesso Condicional podem ser avaliadas durante cada entrada, a pasta de trabalho de insights e relatórios permite examinar o impacto de uma política individual ou de um subconjunto de todas as políticas.  

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar a pasta de trabalho de insights e relatórios, o locatário deve ter um workspace do Log Analytics para manter os dados de logs de entrada. Os usuários devem ter licenças P1 ou P2 do Microsoft Azure AD Premium para usar o Acesso Condicional.

As funções a seguir podem acessar os insights e relatórios:  

- Administrador de acesso condicional 
- Leitor de segurança 
- Administrador de segurança 
- Leitor global 
- Administrador global 

Os usuários também precisam de uma das seguintes funções do workspace do Log Analytics:  

- Colaborador  
- Proprietário 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Transmitir logs de entrada do Azure AD para os logs do Azure Monitor 

Se você ainda não integrou os logs do Azure AD aos logs do Azure Monitor, é necessário realizar as seguintes etapas antes que a pasta de trabalho seja carregada:  

1. [Criar um workspace do Log Analytics no Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integrar logs do Azure AD aos logs do Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Como ele funciona 

Para acessar a pasta de trabalho de insights e relatórios:  

1. Entre no **portal do Azure**.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional** > **Insights e relatórios**.

### <a name="get-started-select-parameters"></a>Introdução: Selecione os parâmetros 

O painel de insights e relatórios permite ver o impacto de uma ou mais políticas de Acesso Condicional durante um período especificado. Comece definindo cada um dos parâmetros na parte superior da pasta de trabalho. 

![Painel de insights e relatórios e de Acesso Condicional no portal do Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Política de Acesso Condicional**: Selecione uma ou mais políticas de Acesso Condicional para ver a combinação de impactos delas. As políticas são separadas em dois grupos: Políticas habilitadas e somente relatório. Por padrão, todas as políticas Habilitadas são selecionadas. Essas políticas habilitadas são as políticas aplicadas no locatário atualmente.  

**Intervalo de tempo**: Escolha um intervalo de tempo de 4 horas a 90 dias. Se você escolher um intervalo de tempo anterior à integração dos logs do Azure AD ao Azure Monitor, somente as entradas após o momento da integração serão exibidas.  

**Usuário**: Por padrão, o painel mostra o impacto das políticas selecionadas para todos os usuários. Para filtrar por um usuário individual, digite o nome do usuário no campo de texto. Para filtrar por todos os usuários, digite "Todos os usuários" no campo de texto ou deixe o parâmetro vazio. 

**Aplicativo**: Por padrão, o painel mostra o impacto das políticas selecionadas para todos os aplicativos. Para filtrar por um aplicativo individual, digite o nome do aplicativo no campo de texto. Para filtrar por todos os aplicativos, digite "Todos os aplicativos" no campo de texto ou deixe o parâmetro vazio. 

**Exibição de dados**: Escolha se deseja que o painel mostre os resultados em termos de número de usuários ou número de entradas. Um usuário individual pode ter centenas de entradas para muitos aplicativos com muitos resultados diferentes durante determinado intervalo de tempo. Se você escolher a exibição de dados como usuários, um usuário pode ser incluído nas contagens de Êxito e Falha (por exemplo, se houver 10 usuários, 8 deles podem ter resultado de êxito nos últimos 30 dias e 9 deles podem ter resultado de falha nos últimos 30 dias).

## <a name="impact-summary"></a>Resumo de impacto 

O resumo de impacto é carregado após definir os parâmetros. O resumo mostra quantos usuários ou entradas durante o intervalo de tempo resultaram em "Êxito", "Falha", "Requer ação do usuário" ou "Não aplicado", quando as políticas selecionadas foram avaliadas.  

![Resumo de impacto na pasta de trabalho de Acesso Condicional](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: O número de usuários ou entradas durante o período em que pelo menos uma das políticas selecionadas foi avaliada.

**Êxito**: O número de usuários ou entradas durante o período em que o resultado combinado das políticas selecionadas foi "Êxito" ou "Somente relatório: Êxito".

**Falha**: O número de usuários ou entradas durante o período em que o resultado de pelo menos uma das políticas selecionadas foi "Sucesso" ou "Somente relatório: Falha".

**Requer ação do usuário**: O número de usuários ou entradas durante o período em que o resultado combinado das políticas selecionadas foi "Somente relatório: Requer ação do usuário". A ação do usuário é necessária quando um controle de concessão interativo, como a autenticação multifator, é exigido por uma política de Acesso Condicional somente relatório. Como os controles de concessão interativos não são aplicados por políticas somente relatório, não é possível determinar o êxito ou a falha.  

**Não aplicado**: O número de usuários ou entradas durante o período em que nenhuma das políticas selecionadas foi aplicada.

### <a name="understanding-the-impact"></a>Reconhecimento do impacto 

![Detalhamento da pasta de trabalho por condição e status](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Veja o detalhamento de usuários ou entradas de cada uma das condições. Você pode filtrar as entradas de um resultado específico (por exemplo, êxito ou falha), selecionando os blocos de resumo na parte superior da pasta de trabalho. Você pode conferir o detalhamento das entradas de cada uma das condições de Acesso Condicional: estado do dispositivo, plataforma do dispositivo, aplicativo cliente, local, aplicativo e risco de entrada.  

## <a name="sign-in-details"></a>Detalhes de entrada 

![Detalhes de entrada da pasta de trabalho](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Você também pode investigar as entradas de um usuário específico procurando as entradas na parte inferior do painel. A consulta à esquerda exibe os usuários mais frequentes. Selecionar um usuário filtra a consulta à direita.  

## <a name="troubleshooting"></a>Solução de problemas

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Por que a pasta de trabalho está demorando para ser carregada?  

Dependendo do intervalo de tempo selecionado e do tamanho do locatário, a pasta de trabalho pode avaliar um número extremamente grande de eventos de entrada. Para locatários grandes, o volume de entradas pode exceder a capacidade de consulta do Log Analytics. Tente reduzir o intervalo de tempo para 4 horas para ver se a pasta de trabalho é carregada.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Após alguns minutos de carregamento, por que a pasta de trabalho não retorna resultados? 

Quando o volume de entradas excede a capacidade de consulta do Log Analytics, a pasta de trabalho não retorna resultados. Tente reduzir o intervalo de tempo para 4 horas para ver se a pasta de trabalho é carregada.  

### <a name="can-i-save-my-parameter-selections"></a>Posso salvar minhas seleções de parâmetro?  

Você pode salvar suas seleções de parâmetro na parte superior da pasta de trabalho, acessando **Azure Active Directory** > **Pastas de trabalho** > **Insights e relatórios de Acesso Condicional**. Aqui você encontra o modelo de pasta de trabalho em que pode editar a pasta de trabalho e salvar uma cópia no espaço de trabalho, incluindo as seleções de parâmetro, em **Meus relatórios** ou **Relatórios compartilhados**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Posso editar e personalizar a pasta de trabalho com consultas adicionais? 

Você pode editar e personalizar as pastas de trabalho, acessando **Azure Active Directory** > **Pastas de trabalho** > **Insights e relatórios de Acesso Condicional**. Aqui você encontra o modelo de pasta de trabalho em que pode editar a pasta de trabalho e salvar uma cópia no espaço de trabalho, incluindo as seleções de parâmetro, em **Meus relatórios** ou **Relatórios compartilhados**. Para começar a editar as consultas, clique em **Editar** na parte superior da pasta de trabalho.  
 
## <a name="next-steps"></a>Próximas etapas

[Modo somente relatório do Acesso Condicional](concept-conditional-access-report-only.md)
