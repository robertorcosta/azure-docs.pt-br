---
title: Configure uma diretiva de acesso condicional no modo somente relatório - Azure Active Directory
description: Usando o modo somente relatório no Acesso Condicional para ajudar na adoção
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295125"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Configure uma diretiva de acesso condicional no modo somente relatório (Visualização)

Para configurar uma diretiva de acesso condicional no modo somente de relatório:

> [!IMPORTANT]
> Se sua organização ainda não tiver, [configure a integração do Monitor Azure com o Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Este processo deve ocorrer antes que os dados estejam disponíveis para revisão.

1. Entre no **portal do Azure** como administrador de acesso condicional, administrador de segurança ou administrador global.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Configure as condições da política e requisite os controles de subvenção conforme necessário.
1. Em **'Habilitar',** definir o alterne irpara o modo **somente relatório.**
1. Selecione **Salvar**

> [!TIP]
> Você pode editar o estado de **diretiva Habilitar** de uma política existente somente **em** on para report, mas **fazê-lo** desativará a aplicação da diretiva. 

Exibir o resultado somente de relatório suscesem logs de login do Azure AD.

Para ver o resultado de uma política somente de relatório para um determinado login:

1. Entre no **portal do Azure** como leitor de relatórios, leitor de segurança, administrador de segurança ou administrador global.
1. Navegue até os > **logins**do **Diretório Ativo do Azure**.
1. Selecione um login ou adicione filtros para reduzir os resultados.
1. Na gaveta **Detalhes,** selecione a guia **Somente relatório (Visualização)** para exibir as políticas avaliadas durante o login.

> [!NOTE]
> Ao baixar os logs de login, escolha o formato JSON para incluir dados de resultados somente de relatório suscesal de acesso.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configure a integração do Azure Monitor com o Azure AD

Para visualizar o impacto agregado das políticas de acesso condicional usando a nova pasta de trabalho Conditional Access Insights, você deve integrar o Azure Monitor com o Azure AD e exportar os logs de login. Existem duas etapas para configurar essa integração: 

1. [Inscreva-se para uma assinatura do Azure Monitor e crie um espaço de trabalho](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exporte os logs de login do Azure AD para o Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mais informações sobre os preços do Azure Monitor podem ser encontradas na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Os recursos para estimar custos, definir um limite diário ou personalizar o período de retenção de dados podem ser encontrados no artigo, gerenciar o uso e os custos com o [Azure Monitor Logs](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Exibir a caderneta de visão do Acesso Condicional

Depois de integrar seus logs Ad do Azure com o Azure Monitor, você pode monitorar o impacto das políticas de acesso condicional usando as novas regras de visão do Acesso Condicional.

1. Entre no **portal do Azure** como administrador de segurança ou administrador global.
1. Navegue até as > **cadernetas**do diretório ativo do **Azure**.
1. Selecione **Insights de Acesso Condicional (Preview)**.
1. Selecione uma ou mais políticas na parada da política de **acesso condicional.** Todas as políticas habilitadas são selecionadas por padrão.
1. Selecione um intervalo de tempo (se o intervalo de tempo exceder o conjunto de dados disponível, o relatório mostrará todos os dados disponíveis). Depois de definir os parâmetros **de Política de acesso condicional** e intervalo de **tempo,** o relatório será carregado.
   1. Opcionalmente, procure **usuários** ou **aplicativos** individuais para estreitar o escopo do relatório.
1. Selecione entre a visualização dos dados no intervalo de tempo pelo número de usuários ou pelo número de logins.
1. Dependendo da **exibição de dados,** o **Resumo de Impacto** exibe o número de usuários ou logins no escopo dos parâmetros escolhidos, agrupados por número total, **sucesso,** **falha,** **ação do usuário necessária**e não **aplicada.** Selecione um azulejo para examinar as entradas de um determinado tipo de resultado. 
   1. Se você alterou os parâmetros da carteira de trabalho, você pode optar por salvar uma cópia para uso futuro. Selecione o ícone de salvamento na parte superior do relatório e forneça um nome e local para salvar.
1. Role para baixo para ver a decomposição dos logins para cada condição.
1. Veja os **Detalhes de login** na parte inferior do relatório para investigar eventos individuais de login filtrados por seleções acima.

> [!TIP] 
> Precisa detalhar uma consulta específica ou exportar os detalhes do login? Selecione o botão à direita de qualquer consulta para abrir a consulta no Log Analytics. Selecione Exportar para exportar para CSV ou Power BI.

## <a name="common-problems-and-solutions"></a>Problemas comuns e soluções

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Por que as consultas na carteira de trabalho estão falhando?

Os clientes notaram que as consultas às vezes falham se os espaços de trabalho errados ou múltiplos estão associados à carteira de trabalho. Para corrigir esse problema, clique em **Editar** na parte superior da carteira de trabalho e, em seguida, na engrenagem Configurações. Selecione e remova espaços de trabalho que não estejam associados à carteira de trabalho. Deve haver apenas um espaço de trabalho associado a cada carteira de trabalho.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Por que o parâmetro de isento de políticas de acesso condicional não contém minhas políticas?

A lista de entrada de Políticas de Acesso Condicional é preenchida consultando os logins mais recentes durante um período de 4 horas. Se um inquilino não tiver nenhum login nas últimas 4 horas, é possível que a estada esteja vazia. Se esse atraso for um problema persistente, como em pequenos inquilinos com logins pouco frequentes, os admins podem editar a consulta para a lista de parada das Políticas de Acesso Condicional e estender o tempo para a consulta para um tempo superior a 4 horas.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

Para obter mais informações sobre as regras de trabalho do Azure AD, consulte o artigo, [Como usar as regras do Azure Monitor para relatórios do Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
