---
title: Livros de trabalho do Azure Monitor para relatórios | Microsoft Docs
description: Saiba como usar as regras do Azure Monitor para relatórios do Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014371"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como usar as regras do Azure Monitor para relatórios do Azure Active Directory

> [!IMPORTANT]
> Para otimizar as consultas subjacentes nesta carteira de trabalho, clique em "Editar", clique no ícone Configurações e selecione o espaço de trabalho onde deseja executar essas consultas. As regras de trabalho por padrão selecionarão todos os espaços de trabalho onde você está roteando seus logs AD do Azure. 

Você quer:

- Entende o efeito de suas políticas de [Acesso Condicional](../conditional-access/overview.md) na experiência de login de seus usuários?

- Solucionando falhas de login para ter uma melhor visão da saúde de login da sua organização e resolver problemas rapidamente?

- Sabe quem está usando autenticações herdadas para entrar no seu ambiente? (Ao [bloquear a autenticação do legado,](../conditional-access/block-legacy-authentication.md)você pode melhorar a proteção do seu inquilino.)

- Você precisa entender o impacto das políticas de Acesso Condicional em seu inquilino?

- Você gostaria da capacidade de rever: consultas de log de login, a carteira de trabalho relata quantos usuários receberam ou tiveram acesso negado, bem como quantos usuários ignoraram as políticas de Acesso Condicional ao acessar recursos?

- Interessado em desenvolver uma compreensão mais profunda de: os detalhes da carteira de trabalho por condição para que o impacto de uma política possa ser contextualizado por condição, incluindo plataforma do dispositivo, estado do dispositivo, aplicativo do cliente, risco de login, localização e aplicativo?

- Obtenha insights mais profundos sobre consultas de log de login, a carteira de trabalho relata quantos usuários receberam ou tiveram acesso negado, bem como quantos usuários ignoraram as políticas de Acesso Condicional ao acessar recursos.

- Para ajudá-lo a responder a essas perguntas, o Active Directory fornece livros de trabalho para monitoramento. [As pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinam texto, consultas de análise, métricas e parâmetros em relatórios interativos ricos.



Este artigo:

- Presume que você está familiarizado com como [criar relatórios interativos usando as regras de trabalho do Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como usar as cadernetas do Monitor para entender o efeito de suas políticas de Acesso Condicional, para solucionar falhas de login e identificar autenticações legados.
 


## <a name="prerequisites"></a>Pré-requisitos

Para usar as cadernetas do Monitor, você precisa:

- Um inquilino do Active Directory com uma licença premium (P1 ou P2). Saiba como [obter uma licença premium.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)

- Um [espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Acesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) ao espaço de trabalho de análise de log
- A seguir funções no Azure Active Directory (se você estiver acessando o Log Analytics através do portal do Azure Active Directory)
    - Administrador de segurança
    - Leitor de segurança
    - Leitor de relatórios
    - Administrador global

## <a name="roles"></a>Funções
Você deve estar em uma das seguintes funções, bem como ter acesso ao espaço de trabalho [do Log Analytics subjacente](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) para gerenciar as regras de trabalho:
-   Administrador global
-   Administrador de segurança
-   Leitor de segurança
-   Leitor de relatórios
-   Administrador de aplicativos

## <a name="workbook-access"></a>Acesso à carteira de trabalho 

Para acessar as cadernetas:

1. Faça login no [portal Azure](https://portal.azure.com).

1. Navegue até as**cadernetas**de > **monitoramento** > do diretório ativo do **Azure**. 

1. Selecione um relatório ou modelo ou na barra de ferramentas selecione **Abrir**. 

![Encontre as contações de trabalho do Monitor Do Azure no Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Análise de login

Para acessar a caderneta de análise de login, na seção **Uso,** selecione **Logins**. 

Esta carteira de trabalho mostra as seguintes tendências de login:

- Todos os logins

- Sucesso

- Ação pendente do usuário

- Falha

Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Análise de login](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, você recebe um detalhamento pelas seguintes categorias:

- Location

    ![Logins por localização](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Logins por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logins usando autenticação legado 


Para acessar a caderneta de trabalho para logins que usam [autenticação legado,](../conditional-access/block-legacy-authentication.md)na seção **Uso,** selecione **Logins usando Autenticação Legado**. 

Esta carteira de trabalho mostra as seguintes tendências de login:

- Todos os logins

- Sucesso


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

- Protocolos

![Logins por autenticação de legado](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, você recebe uma quebra por aplicativo e protocolo.

![Logins de autenticação de legado por aplicativo e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logins por Acesso Condicional 


Para acessar a caderneta de trabalho para logins por [políticas de Acesso Condicional,](../conditional-access/overview.md)na seção **Acesso Condicional,** selecione **Logins por Acesso Condicional**. 

Esta carteira de trabalho mostra as tendências para logins desativados. Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Entradas usando o acesso condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para logins desativados, você recebe uma discriminação pelo status de Acesso Condicional.

![Status de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Insights de Acesso Condicional

### <a name="overview"></a>Visão geral

As pastas de trabalho contêm consultas de log de login que podem ajudar os administradores de TI a monitorar o impacto das políticas de acesso condicional em seu inquilino. Você tem a capacidade de relatar quantos usuários teriam sido concedidos ou negado acesso. A carteira de trabalho contém insights sobre quantos usuários teriam ignorado as políticas de Acesso Condicional com base nos atributos desses usuários no momento do login. Ele contém detalhes por condição para que o impacto de uma política possa ser contextualizado por condição, incluindo plataforma do dispositivo, estado do dispositivo, aplicativo do cliente, risco de login, localização e aplicativo.

### <a name="instructions"></a>Instruções 
Para acessar a caderneta de trabalho para insights de acesso condicional, selecione a caderneta **de acesso condicional Insights** na seção Acesso Condicional. Esta carteira de trabalho mostra o impacto esperado de cada política de Acesso Condicional em seu inquilino. Selecione uma ou mais políticas de acesso condicional na lista de paradas e reduza o escopo da carteira de trabalho aplicando os seguintes filtros: 

- **Intervalo de Tempo**

- **Usuário**

- **Aplicativos**

- **Visualização de dados**

![Status de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/access-insights.png)


O Resumo de Impacto mostra o número de usuários ou logins para os quais as políticas selecionadas tiveram um resultado específico. Total é o número de usuários ou logins para os quais as políticas selecionadas foram avaliadas no Intervalo de Tempo selecionado. Clique em um azulejo para filtrar os dados na caderneta de trabalho por esse tipo de resultado. 

![Status de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Esta carteira de trabalho também mostra o impacto das políticas selecionadas divididas por cada uma das seis condições: 
- **Estado do dispositivo**
- **Plataforma de dispositivos**
- **Aplicativos cliente**
- **Risco de login**
- **Local**
- **Aplicativos**

![Status de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Você também pode investigar logins individuais, filtrados pelos parâmetros selecionados na caderneta de trabalho. Procure usuários individuais, classificados por freqüência de login e visualize seus eventos de login correspondentes. 

![Status de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Logins por controles de subvenção

Para acessar a caderneta de trabalho para logins por controles de [subvenção,](../conditional-access/controls.md)na seção **Acesso Condicional,** selecione **Logins por Controles de Subvenção**. 

Esta carteira de trabalho mostra as seguintes tendências de login desativadas:

- Exigir MFA
 
- Requerer termos de uso

- Exigir declaração de privacidade

- Outros


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Logins por controles de subvenção](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, você recebe uma quebra por aplicativo e protocolo.

![Desmembrá-lo recentemente](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de logins

Use a **caderneta de análise de falhas de logins** para solucionar erros com:

- Entradas
- Políticas de Acesso Condicional
- Autenticação herdada 


Para acessar os logins por dados de acesso condicional, na seção **Solução de problemas,** selecione **Logins usando Autenticação Legado**. 

Esta carteira de trabalho mostra as seguintes tendências de login:

- Todos os logins

- Sucesso

- Ação pendente

- Falha


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Solucionadores de problemas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudá-lo a solucionar problemas de logins, o Azure Monitor oferece uma análise das seguintes categorias:

- Principais erros

    ![Resumo dos principais erros](./media/howto-use-azure-monitor-workbooks/53.png)

- Logins à espera da ação do usuário

    ![Resumo dos logins à espera da ação do usuário](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Próximas etapas

[Crie relatórios interativos usando as regras do Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
