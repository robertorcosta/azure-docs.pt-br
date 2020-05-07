---
title: Informações de acesso condicional e pasta de trabalho de relatórios-Azure Active Directory
description: Usando a pasta de trabalho de relatórios e informações de acesso condicional do Azure AD para solucionar problemas de políticas
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
ms.openlocfilehash: d4113328a5de02c36b7285c837bd5314d11e526b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690442"
---
# <a name="conditional-access-insights-and-reporting"></a>Relatórios e informações de acesso condicional

A pasta de trabalho informações de acesso condicional e relatórios permite que você entenda o impacto das políticas de acesso condicional em sua organização ao longo do tempo. Durante a entrada, uma ou mais políticas de acesso condicional podem se aplicar, concedendo acesso se determinados controles de concessão estiverem satisfeitos ou negando o acesso de outra forma. Como várias políticas de acesso condicional podem ser avaliadas durante cada entrada, as informações e a pasta de trabalho de relatórios permitem que você examine o impacto de uma política individual ou de um subconjunto de todas as políticas.  

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar as informações e a pasta de trabalho de relatórios, seu locatário deve ter um espaço de trabalho Log Analytics para manter os dados de logs de entrada. Os usuários devem ter Azure AD Premium licenças P1 ou P2 para usar o acesso condicional.

As funções a seguir podem acessar informações e relatórios:  

- Administrador de acesso condicional 
- Leitor de segurança 
- Administrador de segurança 
- Leitor global 
- Administrador global 

Os usuários também precisam de uma das seguintes Log Analytics funções de espaço de trabalho:  

- Leitor 
- Leitor de monitoramento 
- Leitor de Log Analytics 
- Colaborador  
- Proprietário 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Transmitir logs de entrada do Azure AD para Azure Monitor logs 

Se você não tiver integrado os logs do Azure AD aos logs de Azure Monitor, precisará executar as seguintes etapas antes que a pasta de trabalho seja carregada:  

1. [Crie um espaço de trabalho log Analytics no Azure monitor](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integre logs do Azure ad a logs de Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Como ele funciona 

Para acessar a pasta de trabalho de informações e relatórios:  

1. Entre no **portal do Azure**.
1. Navegue até **Azure Active Directory** > **informações e relatórios**de**acesso** > condicional de**segurança** > .

### <a name="get-started-select-parameters"></a>Introdução: selecionar parâmetros 

O painel informações e relatórios permite que você veja o impacto de uma ou mais políticas de acesso condicional durante um período especificado. Comece definindo cada um dos parâmetros na parte superior da pasta de trabalho. 

![Painel de relatórios e informações de acesso condicional no portal do Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Política de acesso condicional**: selecione uma ou mais políticas de acesso condicional para exibir o impacto combinado. As políticas são separadas em dois grupos: políticas habilitadas e somente de relatório. Por padrão, todas as políticas habilitadas são selecionadas. Essas políticas habilitadas são as políticas atualmente impostas em seu locatário.  

**Intervalo de tempo**: selecione um intervalo de tempo de 4 horas até o máximo de 90 dias. Se você selecionar um intervalo de tempo mais para trás do que quando você integrou os logs do Azure AD ao Azure Monitor, somente as entradas após a hora da integração serão exibidas.  

**Usuário**: por padrão, o painel mostra o impacto das políticas selecionadas para todos os usuários. Para filtrar por um usuário individual, digite o nome do usuário no campo de texto. Para filtrar por todos os usuários, digite "todos os usuários" no campo de texto ou deixe o parâmetro vazio. 

**Aplicativo**: por padrão, o painel mostra o impacto das políticas selecionadas para todos os aplicativos. Para filtrar por um aplicativo individual, digite o nome do aplicativo no campo de texto. Para filtrar por todos os aplicativos, digite "todos os aplicativos" no campo de texto ou deixe o parâmetro vazio. 

**Exibição de dados**: selecione se você deseja que o painel mostre os resultados em termos de número de usuários ou número de entradas. Um usuário individual pode ter centenas de entradas para muitos aplicativos com muitos resultados diferentes durante um determinado intervalo de tempo. Se você selecionar a exibição de dados para ser usuários, um usuário poderá ser incluído nas contagens de êxito e falha (por exemplo, se houver 10 usuários, oito deles poderiam ter tido o sucesso nos últimos 30 dias e 9 deles poderiam ter tido um resultado de falha nos últimos 30 dias).

## <a name="impact-summary"></a>Resumo do impacto 

Depois que os parâmetros tiverem sido definidos, o resumo de impacto é carregado. O resumo mostra quantos usuários ou entradas durante o intervalo de tempo resultaram em "êxito", "falha", "ação do usuário necessária" ou "não aplicado" quando as políticas selecionadas foram avaliadas.  

![Resumo de impacto na pasta de trabalho de acesso condicional](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: o número de usuários ou entradas durante o período de tempo em que pelo menos uma das políticas selecionadas foi avaliada.

**Êxito**: o número de usuários ou entradas durante o período em que o resultado combinado das políticas selecionadas era "êxito" ou "somente relatório: sucesso".

**Falha**: o número de usuários ou entradas durante o período de tempo em que o resultado de pelo menos uma das políticas selecionadas era "falha" ou "somente relatório: falha".

**Ação do usuário necessária**: o número de usuários ou entradas durante o período de tempo em que o resultado combinado das políticas selecionadas era "somente relatório: ação do usuário necessária". A ação do usuário é necessária quando um controle de concessão interativo, como a autenticação multifator é exigido por uma política de acesso condicional somente de relatório. Como os controles de concessão interativa não são impostos por políticas somente de relatório, o êxito ou a falha não podem ser determinados.  

**Não aplicado**: o número de usuários ou entradas durante o período de tempo em que nenhuma das políticas selecionadas é aplicada.

### <a name="understanding-the-impact"></a>Entendendo o impacto 

![Divisão da pasta de trabalho por condição e status](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Exiba a divisão de usuários ou entradas para cada uma das condições. Você pode filtrar as entradas de um resultado específico (por exemplo, êxito ou falha) selecionando os blocos de resumo na parte superior da pasta de trabalho. Você pode ver a divisão de entradas para cada uma das condições de acesso condicional: estado do dispositivo, plataforma do dispositivo, aplicativo cliente, local, aplicativo e risco de entrada.  

## <a name="sign-in-details"></a>Detalhes de entrada 

![Detalhes de entrada da pasta de trabalho](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Você também pode investigar as entradas de um usuário específico procurando por entradas na parte inferior do painel. A consulta à esquerda exibe os usuários mais frequentes. A seleção de um usuário filtrará a consulta à direita.  

## <a name="troubleshooting"></a>Solução de problemas

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Por que a pasta de trabalho está demorando muito tempo para ser carregada?  

Dependendo do intervalo de tempo selecionado e do tamanho do seu locatário, a pasta de trabalho pode estar avaliando um número extremamente grande de eventos de entrada. Para locatários grandes, o volume de entradas pode exceder a capacidade de consulta de Log Analytics. Tente reduzir o intervalo de tempo para 4 horas para ver se a pasta de trabalho é carregada.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Após o carregamento por alguns minutos, por que a pasta de trabalho retorna zero resultados? 

Quando o volume de entradas excede a capacidade de consulta de Log Analytics, a pasta de trabalho retornará zero resultados. Tente reduzir o intervalo de tempo para 4 horas para ver se a pasta de trabalho é carregada.  

### <a name="can-i-save-my-parameter-selections"></a>Posso salvar minhas seleções de parâmetro?  

Você pode salvar suas seleções de parâmetro na parte superior da pasta de trabalho acessando **Azure Active Directory** > **pastas de trabalho** > **informações de acesso condicional e relatórios**. Aqui você encontrará o modelo de pasta de trabalho, no qual você pode editar a pasta de trabalho e salvar uma cópia em seu espaço de trabalho, incluindo as seleções de parâmetro, em **meus relatórios** ou **relatórios compartilhados**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Posso editar e personalizar a pasta de trabalho com consultas adicionais? 

Você pode editar e personalizar a pasta de trabalho acessando **Azure Active Directory** > **pastas de trabalho** > **informações e relatórios de acesso condicional**. Aqui você encontrará o modelo de pasta de trabalho, no qual você pode editar a pasta de trabalho e salvar uma cópia em seu espaço de trabalho, incluindo as seleções de parâmetro, em **meus relatórios** ou **relatórios compartilhados**. Para começar a editar as consultas, clique em **Editar** na parte superior da pasta de trabalho.  
 
## <a name="next-steps"></a>Próximas etapas

[Modo somente de relatório de acesso condicional](concept-conditional-access-report-only.md)
