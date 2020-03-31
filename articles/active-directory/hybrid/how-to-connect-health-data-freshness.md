---
title: Azure AD Connect Health - Alerta "Os dados do serviço de integridade não estão atualizados" | Microsoft Docs
description: Este documento descreve a causa do alerta "Os dados do serviço de integridade não estão atualizados" e como solucioná-lo.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897171"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta "Os dados do serviço de integridade não estão atualizados"

## <a name="overview"></a>Visão geral

Os agentes nas máquinas locais que o Azure AD Connect Health monitora periodicamente enviam dados para o Serviço de Saúde Azure AD Connect. Se o serviço não receber dados de um agente, as informações que o portal apresenta serão obsoletas. Para destacar a questão, o serviço levantará os dados do **serviço de saúde não está atualizado.** Esse alerta é gerado quando o serviço não recebeu dados completos nas últimas duas horas.  

- O **alerta de** estado de alerta é acionado se o Serviço de Saúde receber apenas tipos **parciais** de dados enviados do servidor nas últimas duas horas. O alerta de status de aviso não aciona notificações de e-mail para destinatários configurados. 
- O **alerta de** estado de erro é acionado se o Serviço de Saúde não tiver recebido nenhum tipo de dados do servidor nas últimas duas horas. O alerta de status de erro aciona notificações de e-mail para destinatários configurados.

O serviço obtém os dados dos agentes que estão sendo executados nas máquinas locais, dependendo do tipo de serviço. A tabela a seguir lista os agentes que são executados na máquina, o que eles fazem e os tipos de dados que o serviço gera. Em alguns casos, há vários serviços envolvidos no processo, de modo que qualquer um deles pode ser o culpado. 

## <a name="understanding-the-alert"></a>Entendendo o alerta

A lâmina **Detalhes de Alerta** mostra quando o alerta ocorreu e foi detectado pela última vez. Um processo de fundo que é executado a cada duas horas gera e reavalia o alerta. No exemplo a seguir, o alerta inicial ocorreu em 03/10 às 9h59. O alerta ainda existia no dia 03/12 às 10:00 am quando o alerta foi avaliado novamente. A lâmina também detalha a hora em que o Serviço de Saúde recebeu pela última vez um tipo de dados específico. 
 
 ![Detalhes do alerta do Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
A tabela a seguir mapeia os tipos de serviço para os tipos de dados correspondentes necessários:

| Tipo de serviço | Agente (nome do Serviço do Windows) | Finalidade | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| Conexão Ad do Azure (Sincronização) | Serviço de Informações do Azure AD Connect Health para Sincronização | Coletar informações específicas do AAD Connect (conectores, regras de sincronização, etc.) | - AadSyncService-SynchronizationRules <br />  - Conectores AadSyncService <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResultados <br /> - Configurações de serviço de serviço aadSyncService <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitoramento do Azure AD Connect Health para Sincronização | Colete contadores perf específicos do AAD Connect, rastreamentos etw, arquivos | Contador de desempenho |
| AD DS | Serviço do Insights do AD DS do Azure AD Connect Health | Realizar testes sintéticos, coletar informações de topologia, metadados de replicação |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (cria os resultados do teste)   | 
|  | Serviço de Monitoramento do AD DS do Azure AD Connect Health | Coletar contadores perf específicos de ADDS, rastreamentos eTW, arquivos | - Contador de desempenho  <br /> - Common-TestData-Json (carrega os resultados do teste)  |
| AD FS | Serviço de diagnóstico do AD FS do Azure AD Connect Health | Realizar testes sintéticos | TestResult (cria os resultados do teste) | 
| | Serviço do Insights do AD FS do Azure AD Connect Health  | Coletar métricas de uso do ADFS | Adfs-UsageMetrics |
| | Serviço de Monitoramento do AD FS do Azure AD Connect Health | Coletar contadores perf específicos do ADFS, rastreamentos eTW, arquivos | TestResult (carrega os resultados do teste) |

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas 

As etapas necessárias para diagnosticar o problema são dadas a seguir. O primeiro é um conjunto de verificações básicas que são comuns a todos os Tipos de Serviço. 

> [!IMPORTANT] 
> Esse alerta segue a [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) do Connect Health

* Certifique-se de que as versões mais recentes dos agentes estão instaladas. Ver [histórico de lançamentos](reference-connect-health-version-history.md). 
* Certifique-se de que os serviços de agentes de saúde do Azure AD Connect estejam **sendo executados** na máquina. Por exemplo, o Connect Health para AD FS deve ter três serviços.
  ![Verifique o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Certifique-se de analisar e atender a [ seção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Use a [ferramenta de teste de conectividade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.
* Se você tiver um proxy HTTP, siga estas [etapas de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Próximas etapas
Se alguma das etapas acima identificar um problema, corrija-o e aguarde a resolução do alerta. O processo de fundo de alerta é executado a cada 2 horas, por isso levará até 2 horas para resolver o alerta. 

* [Política de retenção de dados do Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
