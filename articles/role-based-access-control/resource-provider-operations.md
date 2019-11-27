---
title: Operações do provedor de recursos do Azure Resource Manager | Microsoft Docs
description: Lista as operações disponíveis nos provedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546331"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos do Azure Resource Manager

Este artigo lista as operações disponíveis para cada provedor de recursos do Azure Resource Manager. Essas operações podem ser usadas em [funções personalizadas](custom-roles.md) para fornecer [RBAC (Controle de acesso baseado em função)](overview.md) granular para recursos no Azure. As cadeias de caracteres de operação têm o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`. Para obter uma lista de como os namespaces do provedor de recursos são mapeados para os serviços do Azure, consulte [corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md).

As operações do provedor de recursos estão sempre em evolução. Para obter as operações mais recentes, use [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AAD/domainServices/delete | Excluir Serviço de Domínio |
> | Ação | Microsoft.AAD/domainServices/oucontainer/delete | Excluir contêiner de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/oucontainer/read | Ler contêineres de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/oucontainer/write | Gravar contêiner de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/read | Ler serviços de domínio |
> | Ação | Microsoft.AAD/domainServices/replicaSets/delete | Excluir site de cluster |
> | Ação | Microsoft.AAD/domainServices/replicaSets/read | Ler site do cluster |
> | Ação | Microsoft.AAD/domainServices/replicaSets/write | Gravar site de cluster |
> | Ação | Microsoft.AAD/domainServices/write | Gravar Serviço de Domínio |
> | Ação | Microsoft.AAD/locations/operationresults/read |  |
> | Ação | Microsoft.AAD/Operations/read |  |
> | Ação | Microsoft.AAD/register/action | Registrar Serviço de Domínio |
> | Ação | Microsoft.AAD/unregister/action | Cancelar registrar Serviço de Domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.aadiam/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Addons/operations/read | Obtém as operações de RP compatíveis. |
> | Ação | Microsoft.Addons/register/action | Registra a assinatura especificada com Microsoft.Addons |
> | Ação | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista informações do plano de suporte atual para a assinatura especificada. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte da Canonical especificado |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obtém o estado do plano de suporte da Canonical especificado. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte da Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ADHybridHealthService/addsservices/action | Cria uma nova floresta para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém detalhes de alertas da floresta como alertid, data da geração do alerta, último alerta detectado, descrição do alerta, última atualização, nível do alerta, estado do alerta, links de solução de problemas do alerta etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a Configuração de Serviço da floresta. Exemplo - Nome da floresta, nível funcional, função FSMO principal de nomeação de domínio, função FSMO principal do esquema etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/delete | Exclui um serviço e seu servidores junto com os dados de integridade. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de sites e domínios da floresta. Exemplo: status da integridade, alertas ativos, alertas resolvidos, propriedades como Nível Funcional do Domínio, Floresta, Mestre de Infraestrutura, Controlador de Domínio Primário, Mestre RID etc.  |
> | Ação | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a configuração de preferência do usuário da floresta.<br>Exemplo: MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configurações para Gráficos da Interface do Usuário etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém o resumo da floresta especificada, como o nome da floresta, o número de domínios sob essa floresta, o número de sites e detalhes do site etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Essa API obtém a lista de todos os ADDomainServices incorporados para um locatário premium. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/read | Obtém os detalhes do serviço para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém detalhes de replicação de todos os servidores para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e seus erros de replicação, se houver. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém a lista completa de controladores de domínio juntamente com detalhes de replicação da floresta especificada. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adiciona uma instância de servidor ao serviço. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registro do servidor de ADDomainService, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Exclui um servidor para um determinado serviço e locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/action | Atualizar a configuração do locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/read | Ler a configuração de locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/write | Criar uma configuração de locatário. |
> | Ação | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo da instalação do agente e os logs de registro armazenados no blob. |
> | Ação | Microsoft.ADHybridHealthService/logs/read | Obtém os logs de registro e instalação do agente para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações compatíveis com o sistema. |
> | Ação | Microsoft.ADHybridHealthService/register/action | Registra o Provedor de Recursos do Serviço de Integridade ADHybrid e habilita a criação do recurso de Serviço de Integridade ADHybrid. |
> | Ação | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, usada pelo DevOps para dar suporte a incidentes de cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas com senhas incorretas de todos os usuários no Serviço de Federação do Active Directory. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS do Blob que contém o status e o resultado eventual do trabalho de relatório recém-enfileirado para a frequência de Tentativas de Nome de Usuário/Senha incorretas por UserId por IPAddress por dia para um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de locatários permitidos do DevOps. Normalmente usado para suporte ao cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor indicando se o inquilino é DevOps consentido ou não. |
> | Ação | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Atualiza o Userid(objectid) para o locatário do DevOps selecionado. |
> | Ação | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implantação selecionada para o locatário determinado. |
> | Ação | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Dada uma ID de locatário, obtém o local de armazenamento do locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém a localização geográfica da qual os dados serão acessados. |
> | Ação | Microsoft.ADHybridHealthService/services/action | Atualizar uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dado um nome de recurso, verifica se um serviço tem tudo o que é necessário para usar tal recurso. |
> | Ação | Microsoft.ADHybridHealthService/services/delete | Excluir uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um determinado serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém os status de exportação para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém os comentários de alertas para um determinado serviço e servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Dado um serviço, essa API obtém a média das métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Dado um serviço, essa API obtém a exibição agregada da métrica para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adiciona ou atualiza a configuração de monitoramento de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitoramento de um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adiciona ou atualiza as configurações de monitoramento de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/premiumcheck/read | Essa API obtém a lista de todos os serviços incorporados para um locatário premium. |
> | Ação | Microsoft.ADHybridHealthService/services/read | Ler as instâncias de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Obtém todos os URIs de relatório IP arriscados nos últimos 7 dias. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/details/read | Obtém o relatório dos primeiros 50 usuários com erros de senhas incorretas dos últimos sete dias |
> | Ação | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Gera um relatório IP arriscado e retorna um URI apontando para ele. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas de um servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registro do servidor, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, essa API obtém uma lista dos tipos de dados que estão sendo carregados pelos servidores e a hora mais recente de cada upload. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/delete | Exclui uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do Erro de Exportação de Sincronização para um determinado Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Obtém a lista de conectores e executa nomes de perfil para o serviço e o membro de serviço fornecidos. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço de um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o status de lista de permissões de recurso de um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/write | Criar uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/unregister/action | Cancela o registro da assinatura do Provedor de Recursos do Serviço de Integridade ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Advisor/configurations/read | Obter configurações |
> | Ação | Microsoft.Advisor/configurations/write | Criar/atualizar configuração |
> | Ação | Microsoft.Advisor/generateRecommendations/action | Gera recomendações |
> | Ação | Microsoft.Advisor/generateRecommendations/read | Obter o status de gerar recomendações |
> | Ação | Microsoft.Advisor/metadata/read | Obter Metadados |
> | Ação | Microsoft.Advisor/operations/read | Obter as operações para Assistente do Azure |
> | Ação | Microsoft.Advisor/recommendations/available/action | Nova recomendação está disponível no Microsoft advisor |
> | Ação | Microsoft.Advisor/recommendations/read | Ler recomendações |
> | Ação | Microsoft.Advisor/recommendations/suppressions/delete | Excluir supressão |
> | Ação | Microsoft.Advisor/recommendations/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/recommendations/suppressions/write | Criar/atualizar supressões |
> | Ação | Microsoft.Advisor/register/action | Registra a assinatura do Microsoft Advisor |
> | Ação | Microsoft.Advisor/suppressions/delete | Excluir supressão |
> | Ação | Microsoft.Advisor/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/suppressions/write | Criar/atualizar supressões |
> | Ação | Microsoft.Advisor/unregister/action | Cancelar o registro da assinatura do Assistente do Azure |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AlertsManagement/actionRules/delete | Exclua regra de ação em uma determinada assinatura. |
> | Ação | Microsoft.AlertsManagement / actionRules / read | Obter todas as regras de ação para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement / actionRules / write | Criar ou atualizar regra de ação em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/alerts/changestate/action | Altera o estado do alerta. |
> | Ação | Microsoft.AlertsManagement/alerts/diagnostics/read | Obtém todos os diagnósticos para o alerta |
> | Ação | Microsoft.AlertsManagement/alerts/history/read | Obtém o histórico do alerta |
> | Ação | Microsoft.AlertsManagement/alerts/read | Obter todos os alertas para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsList/read | Obtenha todos os alertas para os filtros de entrada nas assinaturas |
> | Ação | Microsoft. AlertsManagement/alertsMetaData/Read | Obter metadados de alertas para o parâmetro de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsSummary/read | Obter o resumo dos alertas |
> | Ação | Microsoft.AlertsManagement / alertsSummaryList / read | Obtenha o resumo de alertas entre assinaturas |
> | Ação | Microsoft.AlertsManagement/Operations/read | Ler as operações fornecidas |
> | Ação | Microsoft.AlertsManagement/register/action | Registra a assinatura para o Gerenciamento de Alertas da Microsoft |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Excluir regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Obter todas as regras de alerta do detector inteligente para os filtros de entrada |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Criar ou atualizar a regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/smartGroups/changestate/action | Altera o estado do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/history/read | Obtém o histórico do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/read | Obtém todos os grupos inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verificar se determinado nome do Analysis Server é válido e não está em uso. |
> | Ação | Microsoft.AnalysisServices/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Ação | Microsoft.AnalysisServices/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Ação | Microsoft.AnalysisServices/operations/read | Recuperar as informações das operações |
> | Ação | Microsoft.AnalysisServices/register/action | Registrar o provedor de recursos do Analysis Services. |
> | Ação | Microsoft.AnalysisServices/servers/delete | Excluir o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Listar o status do gateway associado ao servidor. |
> | Ação | Microsoft.AnalysisServices/servers/read | Recupera as informações do Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/skus/read | Recuperar informações de SKU disponíveis para o servidor |
> | Ação | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/write | Criar ou atualizar o Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ApiManagement/checkNameAvailability/read | Verificar se o nome do serviço fornecido está disponível |
> | Ação | Microsoft.ApiManagement/operations/read | Ler todas as operações de API disponíveis para o recurso Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/register/action | Registrar a assinatura para o provedor de recursos Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/reports/read | Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest. |
> | Ação | Microsoft.ApiManagement/service/apis/delete | Exclui a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/delete | Exclui o diagnóstico especificado de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/read | Lista todos os diagnósticos de uma API. ou obtém os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/write | Cria um novo diagnóstico para uma API ou atualiza um existente. ou atualiza os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/read | Lista todos os anexos para o problema associado à API especificada. ou obtém os detalhes do anexo do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/write | Cria um novo anexo para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/read | Lista todos os comentários para o problema associado à API especificada. ou obtém os detalhes do comentário do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/write | Cria um novo comentário para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/delete | Exclui o problema especificado de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/read | Lista todos os problemas associados à API especificada. ou obtém os detalhes do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/write | Cria um novo problema para uma API ou atualiza um existente. ou atualiza um problema existente para uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/delete | Exclui a operação especificada na API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/delete | Exclui a configuração de política na operação de API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/read | Obtenha a lista de configurações de política no nível de operação da API. ou obtenha a configuração de política no nível de operação da API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/write | Cria ou atualiza a configuração de política para o nível de operação da API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/delete | Excluir a configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter a configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/write | Criar configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/read | Lista uma coleção das operações para a API especificada. ou obtém os detalhes da operação de API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/delete | Desanexe a marca da operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/read | Lista todas as marcas associadas à operação. ou obter marca associada à operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/write | Atribua a marcação à operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/write | Cria uma nova operação na API ou atualiza uma existente. ou atualiza os detalhes da operação na API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/operationsByTags/read | Lista uma coleção de operações associadas a marcas. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/delete | Exclui a configuração de política na API. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/read | Obtenha a configuração de política no nível da API. ou obtenha a configuração de política no nível da API. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/write | Cria ou atualiza a configuração de política para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/policy/delete | Excluir a configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/read | Obter a configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/write | Criar configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/products/read | Lista todos os produtos dos quais a API faz parte. |
> | Ação | Microsoft.ApiManagement/service/apis/read | Lista todas as APIs da instância do serviço de gerenciamento de API. ou obtém os detalhes da API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/delete | Remove todas as versões da API ou exclui a versão especificada na API. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/read | Lista todas as versões de uma API.<br>Uma versão de API é criada ao tornar a revisão da API atual.<br>As versões também são usadas para reverter para revisões anteriores.<br>Os resultados serão paginados e poderão ser restritos pelos parâmetros $top e $skip.<br>ou retorna os detalhes de uma versão de API. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/write | Cria uma nova versão para a API. ou atualiza os detalhes da versão da API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/delete | Remover todas as revisões de uma API |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/read | Lista todas as revisões de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/delete | Exclui a configuração de esquema na API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/read | Obtenha a configuração do esquema no nível da API. ou obtenha a configuração do esquema no nível da API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/write | Cria ou atualiza a configuração de esquema para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Exclua a descrição da marca para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Lista todas as descrições de marcas no escopo da API. O modelo semelhante ao Swagger-tagDescription é definido no nível da API, mas a marca pode ser atribuída às operações ou obter a descrição da marca no escopo da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Criar/atualizar a descrição da marca no escopo da API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/delete | Desanexe a marca da API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/read | Lista todas as marcas associadas à API. ou obter marca associada à API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/write | Atribua a marca à API. |
> | Ação | Microsoft.ApiManagement/service/apis/write | Cria novas ou atualiza a API especificada existente da instância do serviço de gerenciamento de API. ou atualiza a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/apisByTags/read | Lista uma coleção de APIs associadas a marcas. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/delete | Exclui o conjunto de versão de API específico. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/read | Lista uma coleção de conjuntos de versão de API na instância de serviço especificada. ou obtém os detalhes do conjunto de versão de API especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obter lista de entidades de versão |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/write | Cria ou atualiza um conjunto de versões de API. ou atualiza os detalhes do Versionset de API especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos Microsoft.ApiManagement em execução na rede virtual para obter configurações de rede atualizadas. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/delete | Exclui instância de servidor de autorização específica. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/read | Lista uma coleção de servidores de autorização definidos em uma instância de serviço. ou obtém os detalhes do servidor de autorização especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/write | Cria um novo servidor de autorização ou atualiza um servidor de autorização existente. ou atualiza os detalhes do servidor de autorização especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/backends/delete | Exclui o back-end especificado. |
> | Ação | Microsoft.ApiManagement/service/backends/read | Lista uma coleção de back-ends na instância de serviço especificada. ou obtém os detalhes do back-end especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/backends/reconnect/action | Notifica o proxy APIM para criar uma nova conexão com o back-end após o tempo limite especificado. Se nenhum tempo limite for especificado, o tempo limite de 2 minutos será usado. |
> | Ação | Microsoft.ApiManagement/service/backends/write | Cria ou atualiza um back-end. ou atualiza um back-end existente. |
> | Ação | Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/caches/delete | Exclui o cache específico. |
> | Ação | Microsoft.ApiManagement/service/caches/read | Lista uma coleção de todos os caches externos na instância de serviço especificada. ou obtém os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/caches/write | Cria ou atualiza um cache externo a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/certificates/delete | Exclui um certificado específico. |
> | Ação | Microsoft.ApiManagement/service/certificates/read | Lista uma coleção de todos os certificados na instância de serviço especificada. ou obtém os detalhes do certificado especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/certificates/write | Cria ou atualiza o certificado que está sendo usado para autenticação com o back-end. |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Remove o item de conteúdo especificado. |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Retorna a lista de itens de conteúdo ou retorna os detalhes do item de conteúdo |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Cria um item de conteúdo ou atualiza o item de conteúdo especificado |
> | Ação | Microsoft.ApiManagement/service/contentTypes/read | Retorna a lista de tipos de conteúdo |
> | Ação | Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/diagnostics/delete | Exclui o diagnóstico especificado. |
> | Ação | Microsoft.ApiManagement/service/diagnostics/read | Lista todos os diagnósticos da instância do serviço de gerenciamento de API. ou obtém os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/diagnostics/write | Cria um novo diagnóstico ou atualiza um existente. ou atualiza os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/ação | Recupera a configuração do gateway. ou atualiza a pulsação do gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Delete | Exclui gateway específico. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/chaves/ação | Recupera as chaves de gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/leitura | Lista uma coleção de gateways registrados com a instância de serviço. ou obtém os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Regenera a chave primária do gateway invalidationg todos os tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Regenera a chave do gateway secundário invalidationg quaisquer tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/token/ação | Obtém o token de autorização de acesso compartilhado para o gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Write | Cria ou atualiza um gateway a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/getssotoken/action | Obter token de SSO que pode ser usado para fazer logon no portal de herdado do Serviço de Gerenciamento de API como administrador |
> | Ação | Microsoft.ApiManagement/service/groups/delete | Exclui o grupo específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/groups/read | Lista uma coleção de grupos definidos em uma instância de serviço. ou obtém os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/groups/users/delete | Remover usuário existente do grupo existente. |
> | Ação | Microsoft.ApiManagement/service/groups/users/read | Lista uma coleção de entidades de usuário associadas ao grupo. |
> | Ação | Microsoft.ApiManagement/service/groups/users/write | Adicionar usuário existente a grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/write | Cria ou atualiza um grupo. ou atualiza os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/delete | Exclui a configuração do provedor de identidade especificado. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/read | Lista uma coleção de provedor de identidade configurada na instância de serviço especificada. ou obtém os detalhes de configuração do provedor de identidade configurado na instância de serviço especificada. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/write | Cria ou atualiza a configuração Identityprovider. ou atualiza uma configuração existente do Identityprovider. |
> | Ação | Microsoft.ApiManagement/service/issues/read | Lista uma coleção de problemas na instância de serviço especificada. ou obtém detalhes do problema de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/locations/networkstatus/read | Obter o status de acesso à rede dos recursos em que o serviço depende da localização. |
> | Ação | Microsoft.ApiManagement/service/loggers/delete | Exclui o agente de log especificado. |
> | Ação | Microsoft.ApiManagement/service/loggers/read | Lista uma coleção de agentes de log na instância de serviço especificada. ou obtém os detalhes do agente de log especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/loggers/write | Cria ou atualiza um agente de log. ou atualiza um agente de log existente. |
> | Ação | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/networkstatus/read | Obter o status de acesso à rede dos recursos dos quais o serviço depende. |
> | Ação | Microsoft.ApiManagement/service/notifications/action | Envia uma notificação para um usuário especificado |
> | Ação | Microsoft.ApiManagement/service/notifications/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da notificação especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remove o email da lista de notificações. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtém a lista dos emails de destinatário da notificação inscritos em uma notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Adiciona o endereço de email à lista de destinatários da notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Remove o usuário de gerenciamento de API da lista de notificações. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtém a lista do usuário do destinatário da notificação inscrito na notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adiciona o usuário de gerenciamento de API à lista de destinatários da notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/write | Criar ou atualizar a notificação do editor de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/delete | Exclui o provedor do OpenID Connect específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/read | Listas de todos os provedores do OpenId Connect. ou Obtém um provedor do OpenID Connect específico. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/write | Cria ou atualiza o provedor do OpenID Connect. ou atualiza o provedor do OpenID Connect específico. |
> | Ação | Microsoft.ApiManagement/service/operationresults/read | Obter o status atual da operação de longa duração |
> | Ação | Microsoft.ApiManagement/service/policies/delete | Exclui a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policies/read | Lista todas as definições de política global do serviço de gerenciamento de API. ou obtenha a definição de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policies/write | Cria ou atualiza a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policy/delete | Excluir a configuração de política no nível do locatário |
> | Ação | Microsoft.ApiManagement/service/policy/read | Obter a configuração de política no nível do locatário |
> | Ação | Microsoft.ApiManagement/service/policy/write | Criar configuração de política no nível do locatário |
> | Ação | Microsoft. ApiManagement/Service/policyDescriptions/Read | Lista todas as descrições de política. |
> | Ação | Microsoft.ApiManagement/service/policySnippets/read | Lista todos os trechos de política. |
> | Ação | Microsoft.ApiManagement/service/portalsettings/read | Obtenha configurações de entrada para o portal ou obtenha configurações de inscrição para o portal ou obtenha configurações de delegação para o Portal. |
> | Ação | Microsoft.ApiManagement/service/portalsettings/write | Atualize as configurações de entrada. ou criar ou atualizar configurações de entrada. ou atualize as configurações de inscrição ou atualize as configurações de inscrição ou atualize as configurações de delegação. ou criar ou atualizar configurações de delegação. |
> | Ação | Microsoft.ApiManagement/service/products/apis/delete | Exclui a API especificada do produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/apis/read | Lista uma coleção das APIs associadas a um produto. |
> | Ação | Microsoft.ApiManagement/service/products/apis/write | Adiciona uma API ao produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/delete | Excluir produto. |
> | Ação | Microsoft.ApiManagement/service/products/groups/delete | Exclui a associação entre o grupo e o produto especificados. |
> | Ação | Microsoft.ApiManagement/service/products/groups/read | Lista a coleção de grupos de desenvolvedores associados ao produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/groups/write | Adiciona a associação entre o grupo de desenvolvedores especificado com o produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/policies/delete | Exclui a configuração de política no produto. |
> | Ação | Microsoft.ApiManagement/service/products/policies/read | Obtenha a configuração de política no nível do produto. ou obtenha a configuração de política no nível do produto. |
> | Ação | Microsoft.ApiManagement/service/products/policies/write | Cria ou atualiza a configuração de política para o produto. |
> | Ação | Microsoft.ApiManagement/service/products/policy/delete | Excluir a configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/read | Obter a configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/write | Criar configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/read | Lista uma coleção de produtos na instância de serviço especificada. ou obtém os detalhes do produto especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/products/subscriptions/read | Lista a coleção de assinaturas para o produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/tags/delete | Desanexe a marca do produto. |
> | Ação | Microsoft.ApiManagement/service/products/tags/read | Lista todas as marcas associadas ao produto. ou obtenha a marca associada ao produto. |
> | Ação | Microsoft.ApiManagement/service/products/tags/write | Atribua a marca ao produto. |
> | Ação | Microsoft.ApiManagement/service/products/write | Cria ou atualiza um produto. ou atualize os detalhes do produto existente. |
> | Ação | Microsoft.ApiManagement/service/productsByTags/read | Lista uma coleção de produtos associados às marcas. |
> | Ação | Microsoft.ApiManagement/service/properties/delete | Exclui a propriedade específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/properties/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da propriedade especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/properties/write | Cria ou atualiza uma propriedade. ou atualiza a propriedade específica. |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/read | Obter o valor do contador de cota para o período |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/write | Definir o valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/quotas/read | Obter valores para cota |
> | Ação | Microsoft.ApiManagement/service/quotas/write | Definir o valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/regions/read | Lista todas as regiões do Azure nas quais o serviço existe. |
> | Ação | Microsoft.ApiManagement/service/reports/read | Obter relatório agregado por períodos de tempo, Obter relatório agregado por região geográfica ou Obter relatório agregado pelos desenvolvedores.<br>ou Obter relatório agregado por produtos.<br>ou Obter relatório agregado por APIs, Obter relatório agregado por operações ou Obter relatório agregado por assinatura.<br>ou Obter dados de relatórios de solicitações |
> | Ação | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/subscriptions/delete | Exclui a assinatura especificada. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/read | Lista todas as assinaturas da instância do serviço de gerenciamento de API. ou obtém a entidade de assinatura especificada. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenera a chave primária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenera a chave secundária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/write | Cria ou atualiza a assinatura do usuário especificado para o produto especificado. ou atualiza os detalhes de uma assinatura especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/tagResources/read | Lista uma coleção de recursos associados com marcas. |
> | Ação | Microsoft.ApiManagement/service/tags/delete | Exclui a marca específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/tags/read | Lista uma coleção de marcas definidas em uma instância de serviço. ou obtém os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/tags/write | Cria uma marca. ou atualiza os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/templates/delete | Redefinir o modelo padrão de email de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/templates/read | Obter todos os modelos de email ou Obter detalhes do modelo de email do Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar o modelo de email de Gerenciamento de API ou o modelo de email de Gerenciamento de APIs de atualização |
> | Ação | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/deploy/action | Executar uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados. |
> | Ação | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter o resultado de uma operação específica |
> | Ação | Microsoft.ApiManagement/service/tenant/read | Obtenha a definição de política global do serviço de gerenciamento de API. ou obter detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar a chave de acesso primária |
> | Ação | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar a chave de acesso secundária |
> | Ação | Microsoft.ApiManagement/service/tenant/save/action | Criar confirmação com instantâneo de configuração para uma ramificação específica no repositório |
> | Ação | Microsoft.ApiManagement/service/tenant/syncState/read | Obter status da última sincronização git |
> | Ação | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações da ramificação git especificada |
> | Ação | Microsoft.ApiManagement/service/tenant/write | Definir a configuração de política para o locatário ou Atualizar detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado SSL para um Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/users/action | Registrar um novo usuário |
> | Ação | Microsoft.ApiManagement/service/users/confirmations/send/action | Envia uma confirmação |
> | Ação | Microsoft.ApiManagement/service/users/delete | Exclui um usuário específico. |
> | Ação | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Recupera uma URL de redirecionamento que contém um token de autenticação para assinar um determinado usuário no portal do desenvolvedor. |
> | Ação | Microsoft.ApiManagement/service/users/groups/read | Lista todos os grupos de usuários. |
> | Ação | Microsoft.ApiManagement/service/users/identities/read | Lista de todas as identidades de usuário. |
> | Ação | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | Ação | Microsoft.ApiManagement/service/users/read | Lista uma coleção de usuários registrados na instância de serviço especificada. ou obtém os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/users/subscriptions/read | Lista a coleção de assinaturas do usuário especificado. |
> | Ação | Microsoft.ApiManagement/service/users/token/action | Obtém o token de autorização de acesso compartilhado para o usuário. |
> | Ação | Microsoft.ApiManagement/service/users/write | Cria ou atualiza um usuário. ou atualiza os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/write | Criar uma nova instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/unregister/action | Cancelar o registro da assinatura para o provedor de recursos Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Authorization/classicAdministrators/delete | Remover o administrador da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtém o administrador do status de operação da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/read | Ler os administradores para obter a assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/write | Adicionar ou modificar o administrador de uma assinatura. |
> | Ação | Microsoft.Authorization/denyAssignments/delete | Excluir uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/denyAssignments/read | Obter informações sobre uma atribuição de negação. |
> | Ação | Microsoft.Authorization/denyAssignments/write | Criar uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/elevateAccess/action | Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário |
> | Ação | Microsoft.Authorization/locks/delete | Excluir bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/read | Obter bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/write | Adicionar bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Ação | Microsoft.Authorization/permissions/read | Listar todas as permissões que o chamador tem em determinado escopo. |
> | Ação | Microsoft. Authorization/Policies/Audit/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito de ' auditoria ' |
> | Ação | Microsoft. Authorization/Policies/auditIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' auditIfNotExists ' |
> | Ação | Microsoft. Authorization/Policies/Deny/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' negar ' |
> | Ação | Microsoft. Authorization/Policies/deployIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' deployIfNotExists ' |
> | Ação | Microsoft.Authorization/policyAssignments/delete | Excluir uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyAssignments/read | Obter informações sobre uma atribuição de política. |
> | Ação | Microsoft.Authorization/policyAssignments/write | Criar uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyDefinitions/delete | Excluir uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/read | Obter informações sobre uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/write | Criar uma definição de política personalizada. |
> | Ação | Microsoft.Authorization/policySetDefinitions/delete | Excluir uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/read | Obter informações sobre uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/write | Criar uma definição de conjunto de políticas personalizada. |
> | Ação | Microsoft.Authorization/providerOperations/read | Obter operações para todos os provedores de recursos que podem ser usados em definições de função. |
> | Ação | Microsoft.Authorization/roleAssignments/delete | Exclua uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleAssignments/read | Obter informações sobre uma atribuição de função. |
> | Ação | Microsoft.Authorization/roleAssignments/write | Criar uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleDefinitions/delete | Excluir a definição de função personalizada especificada. |
> | Ação | Microsoft.Authorization/roleDefinitions/read | Obter informações sobre uma definição de função. |
> | Ação | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar uma definição de função personalizada com permissões especificadas e escopos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler uma informação de registro do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Gravar uma solicitação para regenerar chaves DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/delete | Excluir um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Ação | Microsoft.Automation/automationAccounts/certificates/read | Obter um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/write | Criar ou atualizar um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo de mídia de configuração |
> | Ação | Microsoft.Automation/automationAccounts/configurations/delete | Excluir um conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/getCount/action | Ler a contagem de um conteúdo do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/read | Obter o conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/write | Gravar um conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/delete | Excluir um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de conexões |
> | Ação | Microsoft.Automation/automationAccounts/connections/read | Obter um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/write | Criar ou atualizar um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/delete | Excluir um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/read | Obter um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/write | Criar um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/delete | Excluir um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Ação | Microsoft.Automation/automationAccounts/credentials/read | Obter um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/write | Criar ou atualizar um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/delete | Excluir uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Excluir recursos do Hybrid Runbook Worker |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Ação | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/delete | Excluir uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o workspace vinculado à conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves para a conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/activities/read | Obter atividades de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/delete | Excluir um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos Powershell dentro da conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/read | Obtém um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/write | Cria ou atualiza um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Excluir uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Ler um conteúdo de configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Ler uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Gravar uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo da contagem de nós para o tipo especificado |
> | Ação | Microsoft.Automation/automationAccounts/nodes/delete | Excluir nós DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/read | Ler os nós DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê os conteúdos do relatório do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/read | Ler relatórios do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/write | Cria ou atualiza nós do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obter TypeFields de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/delete | Exclui um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/read | Obtém um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/write | Cria ou atualiza um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/delete | Exclui um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/read | Obtém um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/write | Cria ou atualiza um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/content/read | Obter o conteúdo de um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/delete | Excluir um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Criar o conteúdo de um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtém resultados de operação de rascunho do runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obter um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obter um trabalho de teste de rascunho do runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Interrompe um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Criar um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Desfazer edições em um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém a contagem de runbooks da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/write | Criar ou atualizar um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/delete | Excluir um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendamentos da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Obter uma execução da máquina de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Obter uma execução de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Excluir uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Excluir uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/statistics/read | Obter Estatísticas de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obtém um computador de implantação de atualização da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtém um trabalho de patch de gerenciamento de atualização da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/usages/read | Obter o uso de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/delete | Excluir um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/read | Ler um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/write | Criar ou atualizar um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/delete | Exclui um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/read | Obtém um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/start/action | Inicia um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/stop/action | Para um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/streams/read | Obter um fluxo de trabalho do observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Exclui ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtém ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Cria ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/write | Cria um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/delete | Excluir um webhook da Automação do Azure  |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/read | Ler um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/write | Criar ou atualizar um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/write | Criar ou atualizar uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/operations/read | Obtém as operações disponíveis para os recursos da Automação do Azure |
> | Ação | Microsoft.Automation/register/action | Registra a assinatura à Automação do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Excluir recurso do Active Directory B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/read | Exibir recurso do Active Directory B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar o recurso Diretório B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2ctenants/read | Lista todos os locatários B2C nos quais o usuário é membro |
> | Ação | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o provedor de recursos Microsoft.AzureActiveDirectory |
> | Ação | Microsoft.AzureActiveDirectory/register/action | Registrar a assinatura do provedor de recursos Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AzureStack/Operations/read | Obter as propriedades de uma operação do provedor de recursos |
> | Ação | Microsoft.AzureStack/register/action | Registrar assinatura com o provedor de recursos Microsoft.AzureStack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Excluir uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obter as propriedades uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/write | Criar ou atualizar uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/delete | Excluir um registro do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/getActivationKey/action | Obter a última chave de ativação do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalhes estendidos de um produto do Marketplace do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Marketplace do Azure Stack |
> | Ação | Microsoft. AzureStack/inscrições/produtos/uploadProductLog/ação | Registrar Azure Stack status de operação do produto Marketplace e carimbo de data/hora |
> | Ação | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registro do Marketplace do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/write | Criar ou atualizar um registro do Microsoft Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Batch/batchAccounts/applications/delete | Excluir um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/read | Listar aplicativos ou obtém as propriedades de um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/delete | Excluir um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/read | Obter as propriedades de um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/write | Criar um novo pacote de aplicativos ou atualizar um pacote de aplicativos existente |
> | Ação | Microsoft.Batch/batchAccounts/applications/write | Criar um novo aplicativo ou atualizar um aplicativo existente |
> | Ação | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obter os resultados de uma operação de certificado de execução longa em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancelar a exclusão de falha de um certificado em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/delete | Excluir um certificado de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/certificates/read | Listar certificados em uma conta do Lote ou obter as propriedades de um certificado |
> | Ação | Microsoft.Batch/batchAccounts/certificates/write | Criar um novo certificado em uma conta do Lote ou atualizar um certificado existente |
> | Ação | Microsoft.Batch/batchAccounts/delete | Excluir uma conta do Lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/excluir | Exclui um trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/ler | Listar trabalhos em uma conta do lote ou obter as propriedades de um trabalho |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Write | Cria um novo trabalho em uma conta do lote ou atualiza um trabalho existente |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Excluir um plano de trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Listar agendas de trabalho em uma conta do lote ou obter as propriedades de uma agenda de trabalho |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Cria uma nova agenda de trabalho em uma conta do lote ou atualiza uma agenda de trabalho existente |
> | Ação | Microsoft.Batch/batchAccounts/listkeys/action | Listar chaves de acesso para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/operationResults/read | Obter os resultados de uma operação de execução longa da conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obter os resultados de uma operação de pool de execução longa em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/delete | Excluir um pool de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desabilitar o escalonamento automático de um pool de conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/read | Listar pools em uma conta do Batch ou obter as propriedades de um pool |
> | Ação | Microsoft.Batch/batchAccounts/pools/stopResize/action | Interromper uma operação de redimensionamento em andamento em um pool de conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/write | Criar um novo pool em uma conta do Lote ou atualizar um pool existente |
> | Ação | Microsoft.Batch/batchAccounts/read | Listar contas do Lote ou obtém as propriedades de uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera chaves de acesso para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza as chaves de acesso da conta de armazenamento automática configurada para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/write | Criar uma nova conta do Lote ou atualizar uma conta do Lote existente |
> | Ação | Microsoft.Batch/locations/accountOperationResults/read | Obter os resultados de uma operação de execução longa da conta do Lote |
> | Ação | Microsoft.Batch/locations/checkNameAvailability/action | Verificar se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft.Batch/locations/quotas/read | Obter cotas do Lote da assinatura especificada na região do Azure especificada |
> | Ação | Microsoft.Batch/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Batch |
> | Ação | Microsoft.Batch/register/action | Registra a assinatura do provedor de recursos de lote e permite a criação de contas do Lote |
> | Ação | Microsoft.Batch/unregister/action | Cancelar o registro da assinatura do Provedor de Recursos do Lote impedindo a criação de contas do Lote |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. billing/billingAccounts/Agreements/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Customers/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/faturas/pricesheet/download/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/move/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transferência/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/move/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/transferência/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/pricesheet/download/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingSubscriptions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Customers/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Customers/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/departments/read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/Action |  |
> | Ação | Microsoft. billing/billingAccounts/Products/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/read |  |
> | Ação | Microsoft. billing/billingAccounts/Write |  |
> | Ação | Microsoft.Billing/departments/read |  |
> | Ação | Microsoft.Billing/register/action |  |
> | Ação | Microsoft. billing/validateAddress/Action |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.BingMaps/mapApis/Delete | Operação de Exclusão |
> | Ação | Microsoft.BingMaps/mapApis/listSecrets/action | Listar os segredos |
> | Ação | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Ler o token de autorização de logon único para o recurso |
> | Ação | Microsoft.BingMaps/mapApis/Read | Operação de Leitura |
> | Ação | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a chave |
> | Ação | Microsoft.BingMaps/mapApis/Write | Operação de Gravação |
> | Ação | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Blockchain/blockchainMembers/delete | Exclui um membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Obtém ou lista as chaves de API de membro Blockchain existentes. |
> | Ação | Microsoft.Blockchain/blockchainMembers/read | Obtém ou lista os membros Blockchain existentes. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Conecta-se a um nó de transação de membro Blockchain. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Exclui um nó de transação de membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Obtém ou lista as chaves de API do nó de transação de membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Cria ou atualiza um nó de transação de membro Blockchain. |
> | Ação | Microsoft.Blockchain/blockchainMembers/write | Cria ou atualiza um membro Blockchain. |
> | Ação | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Obtém os resultados da operação de membros do Blockchain. |
> | Ação | Microsoft.Blockchain/locations/checkNameAvailability/action | Verifica se o nome do recurso é válido e não está em uso. |
> | Ação | Microsoft.Blockchain/operations/read | Listar todas as operações no provedor de recursos do Microsoft Blockchain. |
> | Ação | Microsoft. Blockchain/registro/ação | Registra a assinatura para o provedor de recursos Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Ler qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/delete | Excluir quaisquer artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/read | Ler qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Obtenha a ID de objeto da entidade de serviço de plantas do Azure. |
> | Ação | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/delete | Excluir quaisquer artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/read | Ler qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprints/delete | Excluir quaisquer modelos |
> | Ação | Microsoft.Blueprint/blueprints/read | Leia qualquer modelo |
> | Ação | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler qualquer artefato do blueprint |
> | Ação | Microsoft.Blueprint/blueprints/versions/delete | Excluir quaisquer modelos |
> | Ação | Microsoft.Blueprint/blueprints/versions/read | Leia qualquer modelo |
> | Ação | Microsoft.Blueprint/blueprints/versions/write | Crie ou atualize qualquer blueprint |
> | Ação | Microsoft.Blueprint/blueprints/write | Crie ou atualize qualquer blueprint |
> | Ação | Microsoft.Blueprint/register/action | Registra o Provedor de Recursos Blueprints do Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.BotService/botServices/channels/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft.BotService/Operations/read | Ler as operações para todos os tipos de recursos |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Cache/checknameavailability/action | Verificar se um nome está disponível para uso com um novo Cache Redis |
> | Ação | Microsoft.Cache/locations/operationresults/read | Obter o resultado de uma operação de execução longa para a qual o cabeçalho 'Localização' foi retornado anteriormente para o cliente |
> | Ação | Microsoft.Cache/operations/read | Listar as operações que o provedor 'Microsoft.Cache' fornece suporte. |
> | Ação | Microsoft.Cache/redis/delete | Excluir todo o Cache Redis |
> | Ação | Microsoft.Cache/redis/export/action | Exportar dados do Redis para blobs de armazenamento prefixados no formato especificado |
> | Ação | Microsoft.Cache/redis/firewallRules/delete | Excluir regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/write | Editar as regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/forceReboot/action | Forçar a reinicialização de uma instância de cache, possivelmente com perda de dados. |
> | Ação | Microsoft.Cache/redis/import/action | Importar dados em um formato especificado de vários blobs para o Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/delete | Excluir servidor vinculado de um Cache Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/read | Obter servidores vinculados associados a um Cache Redis. |
> | Ação | Microsoft.Cache/redis/linkedservers/write | Adicionar um servidor vinculado a um Cache Redis |
> | Ação | Microsoft.Cache/redis/listKeys/action | Exibir o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/listUpgradeNotifications/read | Listar as notificações de atualização mais recentes do locatário de cache. |
> | Ação | Microsoft.Cache/redis/metricDefinitions/read | Obter a métrica disponível para um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/delete | Excluir a agenda de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/read | Obter o agendamento de aplicação de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/write | Modificar o agendamento de aplicação de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/read | Exibir as configurações do Cache Redis e a configuração no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/regenerateKey/action | Alterar o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/start/action | Iniciar uma instância de cache. |
> | Ação | Microsoft.Cache/redis/stop/action | Interromper uma instância de cache. |
> | Ação | Microsoft.Cache/redis/write | Modificar as configurações do Cache Redis e a configuração no portal de gerenciamento |
> | Ação | Microsoft.Cache/register/action | Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura |
> | Ação | Microsoft.Cache/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.Cache' com uma assinatura |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Capacity/appliedreservations/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/calculateexchange/action | Computa a quantidade e o preço do Exchange de novos erros de compra e de política de retorno. |
> | Ação | Microsoft.Capacity/calculateprice/action | Calcula o Preço de qualquer Reserva |
> | Ação | Microsoft.Capacity/catalogs/read | Lê o catálogo de Reservas |
> | Ação | Microsoft.Capacity/checkoffers/action | Verifica as Ofertas de Assinatura |
> | Ação | Microsoft.Capacity/checkscopes/action | Verifica qualquer Assinatura |
> | Ação | Microsoft.Capacity/commercialreservationorders/read | Obtém Pedidos de Reserva criados em qualquer Locatário |
> | Ação | Microsoft.Capacity/exchange/action | Trocar qualquer reserva |
> | Ação | Microsoft.Capacity/operations/read | Lê qualquer Operação |
> | Ação | Microsoft.Capacity/register/action | Registrar o provedor de recursos de Capacidade e permitir a criação de recursos de Capacidade. |
> | Ação | Microsoft.Capacity/reservationorders/action | Atualizar qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/availablescopes/action | Encontra qualquer Escopo Disponível |
> | Ação | Microsoft.Capacity/reservationorders/calculaterefund/action | Computa a quantidade de restituição e o preço de novos erros de compra e de política de retorno. |
> | Ação | Microsoft.Capacity/reservationorders/delete | Excluir qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/merge/action | Mescla qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer Reserva |
> | Ação | Microsoft. Capacity/reservationorders/reservas/availablescopes/Action | Encontra qualquer Escopo Disponível |
> | Ação | Microsoft.Capacity/reservationorders/reservations/delete | Excluir qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/write | Criar qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/return/action | Retorna qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/split/action | Divide qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/swap/action | Troca qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/write | Criar qualquer Reserva |
> | Ação | Microsoft.Capacity/tenants/register/action | Registra qualquer Locatário |
> | Ação | Microsoft.Capacity/unregister/action | Cancela o registro de qualquer Locatário |
> | Ação | Microsoft.Capacity/validatereservationorder/action | Valida qualquer Reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Ação | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/edgenodes/delete |  |
> | Ação | Microsoft.Cdn/edgenodes/read |  |
> | Ação | Microsoft.Cdn/edgenodes/write |  |
> | Ação | Microsoft.Cdn/operationresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/write |  |
> | Ação | Microsoft.Cdn/operations/read |  |
> | Ação | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/write |  |
> | Ação | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/profiles/read |  |
> | Ação | Microsoft.Cdn/profiles/write |  |
> | Ação | Microsoft.Cdn/register/action | Registrar a assinatura no provedor de recursos CDN e habilitar a criação de perfis CDN. |
> | Ação | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Excluir um certificado existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Delete | Excluir um AppServiceCertificate existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Emitir novamente um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar email de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar emails de solicitação para outro endereço de email |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recuperar o selo de site para um certificado do Serviço de Aplicativo emitido |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recuperar a lista de ações de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recuperar o histórico de email do certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade de domínio |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar uma nova certificateOrder ou atualizar uma existente |
> | Ação | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações do registro de certificado de serviço de aplicativo |
> | Ação | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provisionar entidade de serviço para entidade de serviço de aplicativo |
> | Ação | Microsoft.CertificateRegistration/register/action | Registrar o provedor de recursos do Microsoft Certificates da assinatura |
> | Ação | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar objeto de compra de certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicCompute/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verificar a disponibilidade de um nome de domínio específico. |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Obtém a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/active/write | Define o nome de domínio ativo. |
> | Ação | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostra a conjunto de disponibilidade para o recurso. |
> | Ação | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra os recursos de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/delete | Remover os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter a função no slot de implantação de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter a instância de função para a função no slot de implantação de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obtenha o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicione o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Criar ou atualizar a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/delete | Remover as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Ler o status da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/read | Retornar extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/write | Adicionar as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remover um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Ler o status da operação para os balanceadores de carga internos dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obter os balanceadores de carga internos. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Criar um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Ler o status da operação para os conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obtenha os conjuntos de ponto de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicione o conjunto de ponto de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obtenha o status da operação do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Ler o status da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/read | Retornar os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Excluir os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Ler o status da operação para os certificados de serviço dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Retornar todos os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adicionar ou modificar os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Aborta a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Confirma a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/delete | Excluir determinado slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Ler o status da operação dos slots de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remover a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Ler o status da operação de referências de extensão das funções de slot dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retornar a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicionar ou modificar a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obtenha a definição de métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obtenha a métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obtenha o status da operação para a função de slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obter a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Baixa o arquivo de conexão de área de trabalho remota para a instância de função na função do slot de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o status da operação para a instância de função na função do slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obter a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recompilar a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Refaz a imagem da instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia as instâncias de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obtenha o SKU de função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicione uma função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do slot de implantação para interrompido. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do slot de implantação para iniciado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Atualizar o domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Validar a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/write | Criar ou atualizar a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/swap/action | Alterna o slot de preparo para o slot de produção. |
> | Ação | Microsoft.ClassicCompute/domainNames/write | Adicionar ou modificar os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mover todos os recursos clássicos para uma assinatura diferente. |
> | Ação | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listar as famílias de sistemas operacionais convidados disponíveis no Microsoft Azure e também listar as versões dos sistemas operacionais disponíveis para cada família. |
> | Ação | Microsoft.ClassicCompute/operatingSystems/read | Listar as versões do sistema operacional convidado disponíveis atualmente no Microsoft Azure. |
> | Ação | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.ClassicCompute/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicCompute/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicCompute/register/action | Registrar para computação clássica |
> | Ação | Microsoft.ClassicCompute/resourceTypes/skus/read | Obter a lista de SKU para tipos de recursos com suporte. |
> | Ação | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da assinatura para a operação de movimentação clássica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Excluir o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obter o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obter as operações assíncronas possíveis |
> | Ação | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/capture/action | Capture uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/delete | Remover as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obtenha as configurações de diagnóstico de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera a lista de discos de dados |
> | Ação | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Baixa o arquivo RDP na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Ler o status da operação das extensões das máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obter a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obtenha a definição de métrica de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obter a métrica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Excluir um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obter um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Ler o status da operação das máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executar a manutenção na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/read | Retorna a lista de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplanta a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Desliga a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/start/action | Iniciar a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/stop/action | Interrompe a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obtenha um status de operação de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Exclua o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obtenha um status de operação de emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obtenha o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicione o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obtenha conexões cruzadas do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicione conexões cruzadas do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera a lista de dispositivos com suporte. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Excluir o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Ler o status da operação do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico dos Grupos de Segurança de Rede |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as configurações de diagnóstico dos Grupos de segurança de rede, essa operação é complementada pelo provedor de recursos de insights. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o grupo de segurança de rede |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obter o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Excluir a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Ler o status da operação das regras de segurança do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obter a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/operations/read | Obtenha operações de rede clássicas. |
> | Ação | Microsoft.ClassicNetwork/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicNetwork/register/action | Registrar-se na rede clássica |
> | Ação | Microsoft.ClassicNetwork/reservedIps/delete | Excluir um IP reservado. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/join/action | Ingressar em um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Ler o status da operação dos IPs reservados. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
> | Ação | Microsoft.ClassicNetwork/reservedIps/write | Adicionar um novo IP reservado |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Anula a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verificar a disponibilidade de determinado endereço IP em uma rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Confirma a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/delete | Excluir a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cancela a revogação de um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Ler os certificados de cliente revogados. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Excluir o certificado de cliente do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Baixa o certificado por impressão digital. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listar o pacote de certificado do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localizar os certificados raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Conecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desconecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera a lista de conexões. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Excluir o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Baixa o script de configuração do dispositivo. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Baixa o diagnóstico de gateway. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera a chave de serviço do circuito. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Ler o status da operação dos gateways de redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obter o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obter os gateways de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar na rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Ler o status da operação das redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Coloca uma rede virtual com outra rede virtual de mesmo nível. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Exclui o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Excluir o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Ler o status da operação do grupo de segurança de rede associado à sub-rede da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obter um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o emparelhamento de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/write | Adicionar uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicStorage/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verificar a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obtenha a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/disks/read | Retornar o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o Status da Operação de Imagem. |
> | Ação | Microsoft.ClassicStorage/images/read | Retornar a imagem. |
> | Ação | Microsoft.ClassicStorage/operations/read | Obtém as operações de armazenamento clássicas |
> | Ação | Microsoft.ClassicStorage/osImages/read | Retornar a imagem do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem da plataforma do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/publicImages/read | Obter a imagem pública da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicStorage/register/action | Registrar para armazenamento clássico |
> | Ação | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Anula a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/metricDefinitions/Read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Confirma a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/delete | Excluir a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/delete | Excluir um disco de conta de armazenamento específico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/read | Retornar o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco de conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/metricDefinitions/Read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/delete | Excluir uma imagem da conta de armazenamento específica. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retorna o status da operação da imagem da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Excluir uma imagem do sistema operacional da conta de armazenamento específica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retornar a imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obter as definições de métrica. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/metricDefinitions/Read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Regenera as chaves de acesso existentes da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obter a métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/read | Obter os serviços disponíveis. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/metricDefinitions/Read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Valida a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Exclui uma imagem de máquina virtual fornecida. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém o status da operação de imagem de uma determinada máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Retorna a imagem da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona a imagem de uma determinada máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/vmImages/read | Lista imagens da máquina virtual. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | DataAction | Microsoft. Cognitivaservices/contas/autosugestão/pesquisa/ação | Esta operação fornece sugestões para uma determinada consulta ou consulta parcial. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Esta operação extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Esta operação retorna uma caixa delimitadora em volta da área mais importante da imagem. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Esta operação gera uma descrição de uma imagem no idioma legível por humanos com sentenças completas.<br> A descrição se baseia em uma coleção de marcas de conteúdo, que também são retornadas pela operação.<br>Mais de uma descrição pode ser gerada para cada imagem.<br> As descrições são ordenadas por sua pontuação de confiança.<br>Todas as descrições estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Esta operação executa a detecção de objetos na imagem especificada.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Esta operação gera uma imagem em miniatura com a largura e a altura especificadas pelo usuário.<br> Por padrão, o serviço analisa a imagem, identifica a região de interesse (ROI) e gera coordenadas de corte inteligente com base no ROI.<br> O corte inteligente ajuda quando você especifica uma taxa de proporção que difere da imagem de entrada |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Essa operação reconhece o conteúdo em uma imagem aplicando um modelo específico de domínio.<br> A lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional pode ser recuperada usando a solicitação GET/Models.<br> Atualmente, a API fornece os seguintes modelos específicos de domínio: celebridades, pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Essa operação retorna a lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional.  Atualmente, a API dá suporte aos seguintes modelos específicos de domínio: celebridade Recognizer, Recognizer de pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | O OCR (reconhecimento óptico de caracteres) detecta o texto em uma imagem e extrai os caracteres reconhecidos em um fluxo de caracteres utilizável por máquina.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Use essa interface para obter o resultado de uma operação de Reconhecimento de Texto. Quando você usa a interface Reconhecimento de Texto, a resposta contém um campo chamado "Operation-Location". O campo "operação-local" contém a URL que você deve usar para a operação obter Reconhecimento de Texto resultado da operação. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Esta operação gera uma lista de palavras ou marcas que são relevantes para o conteúdo da imagem fornecida.<br>O API da Pesquisa Visual Computacional pode retornar marcas com base em objetos, seres de vida, cenário ou ações encontradas em imagens.<br>Diferentemente das categorias, as marcas não são organizadas de acordo com um sistema de classificação hierárquica, mas correspondem ao conteúdo da imagem.<br>As marcas podem conter dicas para evitar ambigüidade ou fornecer contexto, por exemplo, a marca "Cello" pode ser acompanhada pela dica "instrumento musical".<br>Todas as marcas estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Essa interface é usada para obter o resultado da operação de reconhecimento de texto. A URL para essa interface deve ser recuperada do campo <b>"Operation-Location"</b> retornado da interface reconhecimento de texto. |
> | Ação | Microsoft.CognitiveServices/accounts/delete | Excluir contas de API |
> | DataAction | Microsoft. Cognitivaservices/accounts/EntitySearch/Search/Action | Obter entidades e colocar os resultados para uma determinada consulta. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detecte faces humanas em uma imagem, retorne retângulos de face e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Excluir uma lista de face especificada. As imagens de face relacionadas na lista de face também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Excluir uma face de uma lista de rosto por faceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Adicione uma face a uma lista de face especificada, até 1.000 rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Recupere faceListId, Name, userData e faces da lista facial na lista de face. Liste as listas de face ' faceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crie uma lista de face vazia com faceListId especificado pelo usuário, o nome e um userData opcional. Até 64 listas de rosto são permitidas informações de atualização de uma lista de face, incluindo nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Tabela de face de consulta fornecida para pesquisar os rostos de aparência semelhante de uma matriz faceid, uma lista de face ou uma lista de rosto grande. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divida os rostos candidatas em grupos com base na semelhança de face. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | identificação de um-para-muitos para localizar as correspondências mais próximas da pessoa de consulta específica que se encontram de um grupo de pessoas ou grupo de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Excluir uma lista de face grande especificada. As imagens de face relacionadas na lista de rosto grande também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Exclua uma face de uma lista de rosto grande por largeFaceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Recuperar a face persistente em uma lista de face grande por largeFaceListId e persistedFaceId. Liste faces ' persistedFaceId e userData ' em uma lista de face grande especificada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Adicione uma face a uma lista de face grande especificada, até 1 milhão rostos. Atualizar o campo userData de uma face especificada em uma lista de rosto grande por seu persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Recupere largeFaceListId, Name, userData de uma lista de rosto grande. Listar as informações de grandes listas ' de largeFaceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Envie uma tarefa de treinamento de lista de rosto grande. O treinamento é uma etapa crucial que só pode ser usada por uma lista de rosto grande treinada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Para verificar se o status de treinamento da lista de rosto grande foi concluído ou ainda está em andamento. O treinamento do LargeFaceList é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crie uma lista de face grande vazia com largeFaceListId especificado pelo usuário, o nome e um userData opcional. Atualize as informações de uma lista de rosto grande, incluindo Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Excluir um grupo de pessoas grandes existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas grandes serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crie uma nova pessoa em um grupo de pessoas grandes especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas grandes. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas grandes. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu largePersonGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas grandes para identificação ou verificação facial. Para lidar com a imagem da atualização, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar todas as informações de todas as pessoas no grupo de pessoas grandes especificado, incluindo PERSONID, Name, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Recupere as informações de um grupo de pessoas grandes, incluindo seu nome e userData. Essa API retorna a lista de informações de grupo de pessoas grandes todos os grupos de pessoas grandes existentes largePesonGroupId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Envie uma tarefa de treinamento de grupo de pessoas grandes. O treinamento é uma etapa crucial que só pode ser usada por um grupo treinado de grande pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Para verificar se o status de treinamento de grupo de pessoa grande foi concluído ou ainda está em andamento. O treinamento do LargePersonGroup é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crie um novo grupo de pessoas grandes com largePersonGroupId especificado pelo usuário, nome e userData opcional. Atualizar nome e userData de um grupo de pessoas grandes existentes. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Excluir um grupo de pessoas existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Criar uma nova pessoa em um grupo de pessoas especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu personGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas para identificação ou verificação facial. Para lidar com a imagem de várias atualizações, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar as informações de todas as pessoas no grupo de pessoas especificado, incluindo PERSONID, Name, userData e persistedFaceIds de Registered. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Recupere o nome e o userData do grupo de pessoas. Para obter informações de pessoas sob esse grupo de pessoas, use listar pesonGroupId, nome e userData de grupos de pessoas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Envie uma tarefa de treinamento de grupo Person. O treinamento é uma etapa crucial que apenas um grupo de pessoas treinado pode usar. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Para verificar se o status de treinamento do grupo de pessoas foi concluído ou ainda está em andamento. O treinamento do Person é uma operação assíncrona disparada |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crie um novo grupo de pessoas com personGroupId especificado, nome e userData fornecido pelo usuário. Atualize o nome e o userData de um grupo de pessoas existente. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Verifique se duas faces pertencem a uma mesma pessoa ou se uma face pertence a uma pessoa. |
> | DataAction | Microsoft. Cognitivaservices/contas/ImageSearch/detalhes/ação | Retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ImageSearch/Search/Action | Obter imagens relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/contas/ImageSearch/tendência/ação | Obtenha imagens em tendências no momento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Cria uma sessão de leitor de imersão |
> | DataAction | Microsoft. Cognitivaservices/accounts/InkRecognizer/Recognize/Action | Dado um conjunto de dados de traço analisa o conteúdo e gera uma lista de entidades reconhecidas, incluindo texto reconhecido. |
> | Ação | Microsoft.CognitiveServices/accounts/listKeys/action | Listar chaves |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Obtém a previsão de ponto de extremidade publicado para a consulta fornecida. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/categorysearch/Action | Retorna notícias para uma categoria fornecida. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/Search/Action | Obtenha artigos de notícias relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/trendingtopics/Action | Obtenha tópicos de tendência identificados pelo Bing. Esses são os mesmos tópicos mostrados na faixa na parte inferior do home page do Bing. |
> | Ação | Microsoft.CognitiveServices/accounts/read | Ler as contas de API. |
> | Ação | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar chave |
> | Ação | Microsoft.CognitiveServices/accounts/skus/read | Ler SKUs disponíveis para um recurso existente. |
> | DataAction | Microsoft. Cognitivaservices/contas/verificação ortográfica/verificação ortográfica/ação | Obtenha o resultado de uma consulta de verificação ortográfica por meio de GET ou POST. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | A API retorna uma lista de entidades conhecidas e entidades nomeadas gerais (\"Person\", \"local\", \"Organization\", etc) em um determinado documento. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | A API retorna uma lista de cadeias de caracteres representando os principais pontos de discussão no texto de entrada. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | A API retorna o idioma detectado e uma pontuação numérica entre 0 e 1. Classificações próximas a 1 indicam 100% de certeza de que o idioma identificado é verdadeiro. Há 120 idiomas com suporte no total. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | A API retorna uma pontuação numérica entre 0 e 1.<br>Classificações próximas de 1 indicam sentimento positivo, enquanto as classificações próximas de 0 indicam sentimento negativo.<br>Uma pontuação de 0,5 indica a falta de sentimentos (por exemplo,<br>uma instrução factoname). |
> | Ação | Microsoft.CognitiveServices/accounts/usages/read | Obter a utilização de cota de um recurso existente. |
> | DataAction | Microsoft. Cognitivaservices/contas/VideoSearch/detalhes/ação | Obtenha informações sobre um vídeo, como vídeos relacionados. |
> | DataAction | Microsoft. Cognitivaservices/accounts/VideoSearch/Search/Action | Obter vídeos relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/contas/VideoSearch/tendência/ação | Obtenha vídeos de tendência no momento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/VisualSearch/Search/Action | Retorna uma lista de marcas relevantes para a imagem fornecida |
> | DataAction | Microsoft. Cognitivaservices/contas/WebSearch/pesquisa/ação | Obtenha resultados da Web, de imagem, de notícias & vídeos para uma determinada consulta. |
> | Ação | Microsoft.CognitiveServices/accounts/write | Grava as contas de API. |
> | Ação | Microsoft.CognitiveServices/checkDomainAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notificação da Microsoft. Network de exclusão de VirtualNetworks ou sub-redes. |
> | Ação | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Ação | Microsoft.CognitiveServices/register/action | Registrar assinatura para Serviços Cognitivos |
> | Ação | Microsoft.CognitiveServices/register/action | Registrar assinatura para Serviços Cognitivos |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Commerce/RateCard/read | Retornar dados da oferta, metadados de recurso/medidor e tarifas da assinatura especificada. |
> | Ação | Microsoft.Commerce/UsageAggregates/read | Recupera o consumo do Microsoft Azure para uma assinatura. O resultado contém dados totais de uso, assinatura e informações relativas a recursos em determinado intervalo de tempo. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Compute/availabilitySets/delete | Excluir o conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/write | Criar um novo conjunto de disponibilidade ou atualizar um existente |
> | Ação | Microsoft. Compute/diskEncryptionSets/Delete | Excluir um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Read | Obter as propriedades de um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Write | Criar um novo conjunto de criptografia de disco ou atualizar um existente |
> | Ação | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI de SAS do disco para acesso de blob |
> | Ação | Microsoft.Compute/disks/delete | Excluir o disco |
> | Ação | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI de SAS do disco |
> | Ação | Microsoft.Compute/disks/read | Obter as propriedades de um disco |
> | Ação | Microsoft.Compute/disks/write | Criar um novo disco ou atualizar um existente |
> | Ação | Microsoft.Compute/galleries/delete | Exclui a Galeria |
> | Ação | Microsoft.Compute/galleries/images/delete | Exclui a Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/read | Obtém as propriedades da Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Exclui a Versão da Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Obtém as propriedades da Versão de Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/write | Cria uma nova Versão da Imagem da Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/images/write | Cria uma nova Imagem da Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/read | Obtém as propriedades da Galeria |
> | Ação | Microsoft.Compute/galleries/write | Cria uma nova Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/hostGroups/delete | Exclui o grupo de hosts |
> | Ação | Microsoft. Compute/hosts/hosts/Delete | Exclui o host |
> | Ação | Microsoft. Compute/hosts/hosts/Read | Obter as propriedades de um host |
> | Ação | Microsoft. Compute/hosts/hosts/Write | Cria um novo host ou atualiza um host existente |
> | Ação | Microsoft.Compute/hostGroups/read | Obter as propriedades de um grupo de hosts |
> | Ação | Microsoft.Compute/hostGroups/write | Cria um novo grupo de hosts ou atualiza um grupo de hosts existente |
> | Ação | Microsoft.Compute/images/delete | Excluir a imagem |
> | Ação | Microsoft.Compute/images/read | Obter as propriedades da imagem |
> | Ação | Microsoft.Compute/images/write | Criar uma nova imagem ou atualizar uma existente |
> | Ação | Microsoft.Compute/locations/capsOperations/read | Obtém o status de uma operação de Limites assíncrona |
> | Ação | Microsoft.Compute/locations/diskOperations/read | Obtém o status de uma operação de Disco assíncrona |
> | Ação | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Criar logs para mostrar o total de solicitações por intervalo de tempo para auxiliar no diagnóstico de limitação. |
> | Ação | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Criar logs para mostrar agregações das solicitações limitadas, agrupadas por ResourceName, OperationName ou a política de limitação aplicada. |
> | Ação | Microsoft.Compute/locations/operations/read | Obter o status de uma operação assíncrona |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma Oferta de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma SKU de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma SKU de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um tipo de VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma versão do VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Publicador |
> | Ação | Microsoft.Compute/locations/runCommands/read | Listar os comandos de execução disponíveis no local |
> | Ação | Microsoft.Compute/locations/usages/read | Obter as quantidades de uso atual e limites de serviço para os recursos de computação da assinatura em determinado local |
> | Ação | Microsoft.Compute/locations/vmSizes/read | Listar os tamanhos de máquina virtual disponível em um local |
> | Ação | Microsoft.Compute/operations/read | Listar as operações disponíveis no provedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/proximityPlacementGroups/delete | Exclui o grupo de posicionamento de proximidade |
> | Ação | Microsoft.Compute/proximityPlacementGroups/read | Obter as propriedades de um grupo de posicionamento de proximidade |
> | Ação | Microsoft.Compute/proximityPlacementGroups/write | Cria um novo grupo de posicionamento de proximidade ou atualiza um existente |
> | Ação | Microsoft.Compute/register/action | Registra a assinatura com o provedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/restorePointCollections/delete | Excluir a coleção de pontos de restauração e os pontos contidos nela |
> | Ação | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de pontos de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/delete | Excluir o ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades de um ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauração com URIs de SAS do blob |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/write | Criar um novo ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/write | Criar uma nova coleção de pontos de restauração ou atualizar uma existente |
> | Ação | Microsoft.Compute/sharedVMImages/delete | Exclui o SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de um SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/versions/delete | Excluir um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar um SharedVMImageVersion para direcionar regiões |
> | Ação | Microsoft.Compute/sharedVMImages/versions/write | Criar um novo SharedVMImageVersion ou atualizar um existente |
> | Ação | Microsoft.Compute/sharedVMImages/write | Criar um novo SharedVMImage ou atualizar um existente |
> | Ação | Microsoft.Compute/skus/read | Obter a lista de SKUs do Microsoft.Compute disponíveis para sua Assinatura |
> | Ação | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do Instantâneo para acesso de blob |
> | Ação | Microsoft.Compute/snapshots/delete | Excluir um instantâneo |
> | Ação | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do Instantâneo |
> | Ação | Microsoft.Compute/snapshots/read | Obter as propriedades de um instantâneo |
> | Ação | Microsoft.Compute/snapshots/write | Criar um novo instantâneo ou atualizar um existente |
> | Ação | Microsoft.Compute/unregister/action | Cancela o registro da assinatura com o provedor de recursos Microsoft. Compute |
> | Ação | Microsoft.Compute/virtualMachines/capture/action | Capturar a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes |
> | Ação | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converter os discos baseados em blob da máquina virtual em discos gerenciados |
> | Ação | Microsoft.Compute/virtualMachines/deallocate/action | Desligar a máquina virtual e liberar os recursos de computação |
> | Ação | Microsoft.Compute/virtualMachines/delete | Excluir a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/delete | Excluir a extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/write | Criar uma nova extensão da máquina virtual ou atualizar uma existente |
> | Ação | Microsoft.Compute/virtualMachines/generalize/action | Definir o estado da máquina virtual como Generalizada e preparar a máquina virtual para captura |
> | Ação | Microsoft.Compute/virtualMachines/instanceView/read | Obter o status de runtime detalhado da máquina virtual e seus recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | Ação | Microsoft.Compute/virtualMachines/performMaintenance/action | Executar a Operação de Manutenção na VM. |
> | Ação | Microsoft.Compute/virtualMachines/powerOff/action | Desligar a máquina virtual. Observe que a máquina virtual continuará a ser cobrada. |
> | Ação | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/redeploy/action | Reimplantar a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/reimage/action | Refazer a imagem da máquina virtual que está usando o disco diferencial. |
> | Ação | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/runCommand/action | Executar um script predefinido na máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/vmSizes/read | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |
> | Ação | Microsoft.Compute/virtualMachines/write | Criar uma nova máquina virtual ou atualizar uma máquina virtual existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desligar e liberar os recursos de computação para as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete | Excluir o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete/action | Excluir as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Excluir a Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obter as propriedades de uma Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Criar uma nova Extensão de Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Percorrer manualmente os domínios de atualização de plataforma de um conjunto de dimensionamento de máquinas virtuais do Service Fabric para concluir uma atualização pendente que está paralisada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obter a exibição de instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualizar manualmente as instâncias para o modelo mais recente do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obter propriedades de todas as interfaces de rede de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Iniciar uma atualização sem interrupção para mover todas as instâncias de Conjunto de Dimensionamento de Máquinas Virtuais para a última versão disponível do sistema operacional de Imagem de Plataforma. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obter o histórico de atualizações do SO para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Realizar a manutenção planejada nas instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desligar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obter propriedades de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/read | Obter as propriedades de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Reimplantar as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Refazer a imagem das instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) das instâncias de um Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reiniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancelar a atualização sem interrupção de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obter o status mais recente de Atualização Sem Interrupção para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verificar se um Conjunto de Dimensionamento de Máquinas Virtuais existente pode Reduzir Horizontalmente/Escalar Horizontalmente para a contagem de instâncias especificada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/skus/read | Listar as SKUs válidas para um Conjunto de Dimensionamento de Máquinas Virtuais existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/start/action | Iniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desligar e liberar os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Excluir uma máquina virtual específica em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recuperar a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obter propriedades do endereço IP público criado usando o Conjunto de Dimensionamento de Máquinas Virtuais. O Conjunto de Dimensionamento de Máquinas Virtuais pode criar no máximo um IP público por ipconfiguration (IP privado) |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou de todas as configurações IP de uma interface de rede criada usando o Conjunto de Dimensionamento de Máquinas Virtuais. Configurações de IP representam IPs privados |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obter propriedades de uma ou de todas as interfaces de rede de uma máquina virtual criada usando o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Realizar manutenção planejada em uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desligar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recuperar as propriedades de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Reimplantar uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Refazer a imagem de uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) da instância da Máquinas Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reiniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido em uma instância da Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Iniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualizar as propriedades de uma Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/write | Criar um novo Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Consumption/balances/read | Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/delete | Exclui os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/read | Listar os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/write | Cria ou atualiza os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/charges/read | Lista os encargos |
> | Ação | Microsoft.Consumption/credits/read | Lista os créditos |
> | Ação | Microsoft.Consumption/events/read | Lista os eventos |
> | Ação | Microsoft.Consumption/forecasts/read | Lista as previsões |
> | Ação | Microsoft.Consumption/lots/read | Lista os lotes |
> | Ação | Microsoft.Consumption/marketplaces/read | Listar os detalhes de uso do recurso do marketplace para um escopo para assinaturas do EA e WebDirect. |
> | Ação | Microsoft.Consumption/operationresults/read | Lista os resultados da operação |
> | Ação | Microsoft.Consumption/operations/read | Listar todas as operações com suporte pelo provedor de recursos Microsoft.Consumption. |
> | Ação | Microsoft.Consumption/operationstatus/read | Lista o status da operação |
> | Ação | Microsoft.Consumption/pricesheets/read | Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/register/action | Registra-se no RP de Consumo |
> | Ação | Microsoft.Consumption/reservationDetails/read | Listar os detalhes de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados detalhados são por instância por nível diário. |
> | Ação | Microsoft.Consumption/reservationRecommendations/read | Lista recomendações únicas ou compartilhadas para Instâncias Reservadas para uma assinatura. |
> | Ação | Microsoft.Consumption/reservationSummaries/read | Listar o resumo de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo serão no nível mensal ou diário. |
> | Ação | Microsoft.Consumption/reservationTransactions/read | Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento. |
> | Ação | Microsoft.Consumption/tags/read | Lista as marcas do EA e das assinaturas. |
> | Ação | Microsoft.Consumption/tenants/read | Listar locatários |
> | Ação | Microsoft.Consumption/tenants/register/action | Registre a ação de escopo do Microsoft.Consumption por um locatário. |
> | Ação | Microsoft.Consumption/terms/read | Listar os termos de uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/usageDetails/read | Listar os detalhes de uso de um escopo para assinaturas do EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Execução em um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obter logs para um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/delete | Excluir o grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Obter resultado da operação assíncrona |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/read | Obtém todos os grupos de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/start/action | Inicia um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/stop/action | Interrompe um grupo de contêineres específico. Os recursos de computação serão desalocados e a cobrança será interrompida. |
> | Ação | Microsoft.ContainerInstance/containerGroups/write | Criar ou atualizar um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/locations/cachedImages/read | Obtém as imagens armazenadas em cache da assinatura em uma região. |
> | Ação | Microsoft.ContainerInstance/locations/capabilities/read | Obtém as funcionalidades para uma região. |
> | Ação | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.ContainerInstance de que uma rede virtual ou uma sub-rede está sendo excluída. |
> | Ação | Microsoft. ContainerInstance/localizações/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft.ContainerInstance/locations/usages/read | Obtém o uso para uma região específica. |
> | Ação | Microsoft. ContainerInstance/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft.ContainerInstance/register/action | Registra a assinatura para o provedor de recursos da instância do contêiner e habilita a criação de grupos de contêineres. |
> | Ação | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Exclua o link de associação de serviço criado pelo provedor de recursos da instância de contêiner do Azure em uma sub-rede. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerRegistry/checkNameAvailability/read | Verificar se o nome do registro de contêiner está disponível para uso. |
> | Ação | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.ContainerRegistry sobre a exclusão de uma rede virtual ou sub-rede |
> | Ação | Microsoft.ContainerRegistry/locations/operationResults/read | Obter um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerRegistry/operations/read | Listar todas as operações da API REST do Registro de Contêiner do Azure disponíveis |
> | Ação | Microsoft.ContainerRegistry/register/action | Registra a assinatura do provedor de recursos de registro de contêiner e permite a criação de registros de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/artifacts/delete | Exclua o artefato em um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancelar um build existente. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obter um link para baixar os logs de build. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/read | Obter as propriedades de build especificado ou lista todos os builds para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/write | Atualizar um build para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/delete | Excluir uma tarefa de build de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Listar as propriedades de controle de origem para uma tarefa de build. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/read | Obter as propriedades da tarefa de build especificada ou lista todas as tarefas de build para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Excluir uma etapa de build de uma tarefa de build. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Listar os argumentos de build para uma etapa de build, incluindo os argumentos secretos. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obter as propriedades da etapa de build especificada ou listar todas as etapas de build da tarefa de build especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Criar ou atualizar uma etapa de build para uma tarefa de build com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/write | Criar ou atualizar uma tarefa de build para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/delete | Excluir um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Excluir um filtro de grade de eventos de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obter as propriedades do filtro de grade de eventos especificado ou listar todos os filtros de grade de eventos para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Criar ou atualizar um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obter o local de upload para o usuário poder carregar a fonte. |
> | Ação | Microsoft.ContainerRegistry/registries/importImage/action | Importa a imagem para o registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obtenha o local de URL de upload de origem para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/listCredentials/action | Listar as credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/listUsages/read | Listar os usos de cotas para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/metadata/read | Obtém os metadados de um repositório específico para um registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/metadata/write | Atualiza os metadados de um repositório para um registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obter um status de operação assíncrona do registro |
> | Ação | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/push/write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | Ação | Microsoft.ContainerRegistry/registries/queueBuild/action | Criar um novo build com base nos parâmetros da solicitação e adicionar à fila de build. |
> | Ação | Microsoft.ContainerRegistry/registries/read | Obter as propriedades do registro de contêiner especificado ou listar todos os registros de contêiner sob o grupo de recursos ou assinatura especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenerar uma das credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/delete | Excluir uma replicação de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obter um status de operação assíncrona de replicação |
> | Ação | Microsoft.ContainerRegistry/registries/replications/read | Obter as propriedades da replicação especificada ou listar todas as replicações do registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/write | Criar ou atualizar uma replicação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/cancel/action | Cancele um tempo de execução existente. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtém o log da URL do SAS para uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/read | Obtém as propriedades de uma execução em um registro de contêiner ou lista é executado. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/write | Atualiza uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/scheduleRun/action | Agende uma execução em um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/sign/write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/delete | Exclui uma tarefa para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Liste todos os detalhes de uma tarefa para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/read | Obtém uma tarefa para um registro de contêiner ou lista todas as tarefas. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/write | Cria ou atualiza uma tarefa para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/delete | Excluir um webhook de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obter a configuração de URI de serviço e cabeçalhos personalizados para o webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Listar eventos recentes para o webhook especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obter um status de operação assíncrona do webhook |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Disparar um evento de ping para ser enviado ao webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/read | Obter as propriedades do webhook especificado ou listar todos os webhooks para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/write | Criar ou atualizar um webhook para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/write | Criar ou atualizar um registro de contêiner com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerService/containerServices/delete | Exclui um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/read | Obtém um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contêiner ou atualiza um existente |
> | Ação | Microsoft.ContainerService/locations/operationresults/read | Obtém o status de um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/operations/read | Obter o status de uma operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores com suporte |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtém um perfil de acesso do cluster gerenciado por nome de função usando a credencial de lista |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obtém um perfil de acesso do cluster gerenciado por nome de função |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/delete | Exclui um pool de agentes |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/read | Obter um pool de agentes |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Obtém o perfil de atualização do pool de agentes |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/write | Cria um novo pool de agentes ou atualiza um existente |
> | Ação | Microsoft.ContainerService/managedClusters/delete | Exclui um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/detectores/leitura | Obter o detector de cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/diagnosticsstate/Read | Obtém o estado de diagnóstico do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.ContainerService/managedClusters/read | Obtém um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Redefine o perfil do AAD de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Redefine o perfil da entidade de serviço de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Obtém o perfil de atualização do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerenciado ou atualiza um existente |
> | Ação | Microsoft.ContainerService/openShiftClusters/delete | Excluir um cluster de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftClusters/read | Obter um cluster de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftClusters/write | Cria um novo Open Shift Cluster ou atualiza um já existente |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/delete | Excluir um cluster gerenciado de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/read | Obter um cluster gerenciado de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/write | Cria um novo Cluster Gerenciado de Turno Aberto ou atualiza um existente |
> | Ação | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/register/action | Registra a assinatura com o provedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/unregister/action | Remove o registro da assinatura com o provedor de recursos Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContentModerator/applications/delete | Operação de Exclusão |
> | Ação | Microsoft.ContentModerator/applications/listSecrets/action | Listar segredos |
> | Ação | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Ler tokens de logon único |
> | Ação | Microsoft.ContentModerator/applications/read | Operação de Leitura |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Ação | Microsoft.ContentModerator/listCommunicationPreference/action | Listar preferência de comunicação |
> | Ação | Microsoft.ContentModerator/operations/read | operações de leitura |
> | Ação | Microsoft.ContentModerator/updateCommunicationPreference/action | Atualizar as preferências de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CostManagement/cloudConnectors/delete | Exclua o Cloudconnector for especificado. |
> | Ação | Microsoft.CostManagement/cloudConnectors/read | Liste o cloudConnectors para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/cloudConnectors/write | Criar ou atualizar o Cloudconnector for especificado. |
> | Ação | Microsoft.CostManagement/dimensions/read | Listar todas as dimensões com suporte por um escopo. |
> | Ação | Microsoft.CostManagement/exports/action | Executa a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/delete | Exclui a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/read | Lista as exportações por escopo. |
> | Ação | Microsoft.CostManagement/exports/run/action | Execute exportações. |
> | Ação | Microsoft.CostManagement/exports/write | Cria ou atualiza a exportação especificada. |
> | Ação | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Liste o externalSubscriptions em um externalBillingAccount para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/externalBillingAccounts/read | Liste o externalBillingAccounts para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/externalSubscriptions/read | Liste o externalSubscriptions para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/externalSubscriptions/write | Atualizar grupo de gerenciamento associado de externalSubscription |
> | Ação | Microsoft.CostManagement/query/action | Consultar os dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/query/read | Consultar os dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por uma assinatura. |
> | Ação | Microsoft.CostManagement/reports/action | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/reports/read | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/tenants/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por um locatário. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite agendar uma retirada para remessas de devolução. |
> | Ação | Microsoft.DataBox/jobs/cancel/action | Cancela um pedido em andamento. |
> | Ação | Microsoft.DataBox/jobs/delete | Excluir as Ordens |
> | Ação | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Ação | Microsoft.DataBox/jobs/read | Liste ou obtenha as Ordens |
> | Ação | Microsoft.DataBox/jobs/write | Cria ou atualiza a Ordens |
> | Ação | Microsoft.DataBox/locations/availableSkus/action | Este método retorna a lista de SKUs disponíveis. |
> | Ação | Microsoft. Data Box/Locations/availableSkus/Read | Listar ou obter os SKUs disponíveis |
> | Ação | Microsoft.DataBox/locations/operationResults/read | Lista ou obtém os Resultados da Operação |
> | Ação | Microsoft. Data Box/Locations/regionConfiguration/Action | Esse método retorna as configurações para a região. |
> | Ação | Microsoft.DataBox/locations/validateAddress/action | Validará o endereço de entrega e fornecerá endereços alternativos, se houver algum. |
> | Ação | Microsoft. Data Box/Locations/validateInputs/Action | Esse método faz todo o tipo de validações. |
> | Ação | Microsoft. Data Box/operações/leitura | Listar ou obter as operações |
> | Ação | Microsoft.DataBox/register/action | Registra o Provedor Microsoft.Databox |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/moveResources/Action | Esse método executa a movimentação de recursos. |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/validateMoveResources/Action | Esse método valida se a movimentação de recursos é permitida ou não. |
> | Ação | Microsoft. Data Box/cancelar registro/ação | Provedor de cancelamento de registro Microsoft. Data Box |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Exclui os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Exclui os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Baixar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Recupera informações estendidas de recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Lista ou obtém os trabalhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Lista ou obtém as configurações de rede do dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nós/leitura | Listar ou obter os nós |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Listar ou obter o status da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Exclui os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Criar ou atualizar os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Exclui as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Examinar se há atualizações |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar as configurações de segurança |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Exclui os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Atualizar os metadados de compartilhamento com os dados da nuvem |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Cria ou atualiza os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Cria ou atualiza as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Exclui os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Criar ou atualizar os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Lista ou obtém o resumo de atualização |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Fazer upload do certificado para registro do dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Exclui os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Cria ou atualiza os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Databricks/locations/getNetworkPolicies/action | Obter políticas de intenção de rede para uma sub-rede com base no local usado pelo NRP |
> | Ação | Microsoft.Databricks/register/action | Registro para Databricks. |
> | Ação | Microsoft.Databricks/workspaces/delete | Remove um workspace do Databricks. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Define as configurações de diagnóstico disponíveis para o workspace Databricks |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Obtém as definições de log disponíveis para o workspace do Databricks |
> | Ação | Microsoft.Databricks/workspaces/read | Recupera uma lista de workspaces do Databricks. |
> | Ação | Microsoft.Databricks/workspaces/write | Cria um workspace do Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataCatalog/catalogs/delete | Excluir recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/catalogs/read | Ler recurso de catálogos para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/catalogs/write | Gravar recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/checkNameAvailability/read | Verifique a disponibilidade do nome do catálogo para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/delete | Excluir recurso datacatalog do provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/read | Ler recurso datacatalog para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/write | Gravar recurso do datacatalog para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/operations/read | Lê todas as operações disponíveis no provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/register/action | Registrar a assinatura para o provedor de recursos do catálogo de dados |
> | Ação | Microsoft.DataCatalog/unregister/action | Cancelar o registro da assinatura do provedor de recursos do catálogo de dados |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Verifica se o Nome do Data Factory está disponível para uso. |
> | Ação | Microsoft.DataFactory/datafactories/activitywindows/read | Lê Janelas de Atividade no Data Factory com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê Janelas de Atividade para a Atividade do Pipeline com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê Janelas de Atividade do Pipeline com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/delete | Exclui qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausa qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê Janelas de Atividade para o Conjunto de Dados com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a Execução da Fatia de Dados do conjunto de dados especificado com a hora de início especificada. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém as Fatias de Dados no período determinado. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualiza o Status da Fatia de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/delete | Exclui o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as Informações de Conexão para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/delete | Exclui qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as Chaves de Autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as Chaves de Autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/delete | Exclui qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/read | Lê qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/read | Lê o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contêiner de blob que contém os logs. |
> | Ação | Microsoft.DataFactory/datafactories/tables/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/write | Cria ou atualiza o Data Factory. |
> | Ação | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft. datafactory/fábricas/cancelSandboxPipelineRun/Action | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Cria uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/delete | Exclui o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/read | Lê o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/write | Criar ou atualizar o fluxo de dados |
> | Ação | Microsoft.DataFactory/factories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft. datafactory/fábricas/debugpipelineruns/cancelamento/ação | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/delete | Excluir o data factory. |
> | Ação | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Exclui uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Obtém acesso ao serviço DataPlane do ADF. |
> | Ação | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Lê o acesso ao serviço DataPlane do ADF. |
> | Ação | Microsoft.DataFactory/factories/getFeatureValue/action | Obtém o valor do recurso de controle de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/factories/getFeatureValue/read | Lê o valor do recurso de controle de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Obtém o token de acesso do GitHub. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/delete | Exclui qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê Informações de Conexão do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obtém os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê o Status do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Cria uma Referência do Integration Runtime Vinculado ao Integration Runtime Compartilhado especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as Chaves de Autenticação para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os Dados de Monitoramento para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Exclui o nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retorna o endereço IP para o nó especificado do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o Nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um Nó do Integration Runtime auto-hospedado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Atualiza os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as Chaves de Autenticação para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove as Referências do Tempo de Execução de Integração do Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia a qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Interrompe qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as Credenciais para o Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Exclui o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/delete | Exclui o Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/read | Lê o Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/write | Cria ou atualiza o Serviço Vinculado |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Consulta as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Ler o resultado das execuções de atividade de consulta para a ID de execução do pipeline especificado. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução para o Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/delete | Excluir o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obter o progresso das execuções de atividade. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/read | Ler pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/criação/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/execução/ação | Cria uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar pipeline |
> | Ação | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Consulta as Execuções de Pipeline de Depuração. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/read | Ler o resultado das execuções de pipeline de consulta. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/read | Ler o resultado das execuções de gatilho. |
> | Ação | Microsoft.DataFactory/factories/read | Ler Data Factory. |
> | Ação | Microsoft. datafactory/factories/sandboxpipelineruns/sandboxActivityRuns/Read | Obtém as informações de execução de depuração para a atividade. |
> | Ação | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Inicia uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/submitDataFlowForPreview/Action | Enviar o fluxo de dados para obter a visualização de dados usando uma sessão de depuração. |
> | Ação | Microsoft.DataFactory/factories/triggerruns/read | Lê as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/delete | Exclui qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/read | Lê qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/stop/action | Interrompe qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/write | Criar ou atualizar data factory |
> | Ação | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/action | Obtém o valor do recurso de controle de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/read | Lê o valor do recurso de controle de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/operations/read | Ler todas as operações no provedor do Microsoft Azure Data Factory. |
> | Ação | Microsoft.DataFactory/register/action | Registra a assinatura para o Provedor de Recursos do Data Factory. |
> | Ação | Microsoft.DataFactory/unregister/action | Cancela o registro da assinatura para o Provedor de Recursos do Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Criar uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obter informações sobre uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desvincular uma conta DataLakeStore de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obter informações sobre uma conta vinculada do Data Lake Store de uma conta do Data Lake Analytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/delete | Excluir uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/read | Obter informações sobre uma conta DataLakeAnalytics existente. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contêineres de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obter informações sobre uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transfere SystemMaxAnalyticsUnits entre contas do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso de DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/usages/read | Obtém informações de usos de cota de uma assinatura referentes ao uso do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/operations/read | Obter operações disponíveis do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/register/action | Registrar a assinatura no DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeStore/accounts/delete | Excluir uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Habilitar o Key Vault para uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Exclui um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtém um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Cria ou atualiza um Filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/read | Obter informações sobre uma conta DataLakeStore existente. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/excluir | Excluir um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/leitura | Obter informações sobre um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/gravação | Criar ou atualizar um compartilhamento. |
> | Ação | Microsoft.DataLakeStore/accounts/Superuser/action | Conceder Superusuário no Data Lake Store quando concedido com o Microsoft.Authorization/roleAssignments/write. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Excluir um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obter informações sobre um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/usages/read | Obtém informações de usos de cota de uma assinatura referentes ao uso do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/operations/read | Obter operações disponíveis de DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/register/action | Registrar assinatura para DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataMigration/locations/operationResults/read | Obtém o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Ação | Microsoft.DataMigration/locations/operationStatuses/read | Obtém o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Ação | Microsoft.DataMigration/register/action | Registra a assinatura com o provedor do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/addWorker/action | Adiciona um trabalho do DMS aos trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implantado e em execução |
> | Ação | Microsoft.DataMigration/services/configureWorker/action | Configura um trabalho do DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gere uma URL que possa ser usada para executar GET ou PUT nos artefatos do projeto |
> | Ação | Microsoft.DataMigration/services/projects/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/files/read/action | Obter uma URL que pode ser usada para ler o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/readWrite/action | Obter uma URL que pode ser usada para ler ou gravar o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/projects/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancela a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft.DataMigration/services/projects/tasks/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/write | Executa tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/projects/write | Executa tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/removeWorker/action | Remove um trabalho DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/serviceTasks/cancel/action | Cancela a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft.DataMigration/services/serviceTasks/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/serviceTasks/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/serviceTasks/write | Executa tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/slots/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/slots/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/start/action | Inicie o serviço do DMS para permitir que ele processe as migrações novamente |
> | Ação | Microsoft.DataMigration/services/stop/action | Pare o serviço do DMS para minimizar seu custo |
> | Ação | Microsoft.DataMigration/services/updateAgentConfig/action | Atualiza a configuração do agente DMS com os valores fornecidos. |
> | Ação | Microsoft.DataMigration/services/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs compatíveis com os recursos do DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMariaDB/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Ação | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar os resultados da operação do servidor MariaDB com base em resourcegroup |
> | Ação | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMariaDB/operations/read | Retornar a lista de operações de MariaDB. |
> | Ação | Microsoft.DBforMariaDB/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/register/action | Registrar provedor de recursos MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/delete | Exclui um administrador existente do MariaDB Server. |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/read | Obtém uma lista de administradores do servidor MariaDB. |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/write | Cria ou atualiza o administrador do servidor MariaDB com os parâmetros especificados. |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/read | Retorna a lista de assistentes |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/read | Retornar um supervisor |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMariaDB/servers/databases/delete | Exclui um banco de dados MariaDB existente. |
> | Ação | Microsoft.DBforMariaDB/servers/databases/read | Retornar a lista de bancos de dados MariaDB ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/databases/write | Cria um banco de dados MariaDB com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforMariaDB/servers/logFiles/read | Retornar a lista de arquivos de log MariaDB. |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforMariaDB/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/recoverableServers/read | Devolver as informações recuperáveis do MariaDB Server |
> | Ação | Microsoft.DBforMariaDB/servers/replicas/read | Obter réplicas de leitura de um servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Ação | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Ação | Microsoft.DBforMariaDB/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Ação | Microsoft.DBforMariaDB/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMySQL/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Ação | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL com base no resourcegroup |
> | Ação | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft.DBforMySQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMySQL/operations/read | Retornar a lista de operações do MySQL. |
> | Ação | Microsoft.DBforMySQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/register/action | Registrar provedor de recursos MySQL |
> | Ação | Microsoft.DBforMySQL/servers/administrators/delete | Exclui um administrador existente do MySQL Server. |
> | Ação | Microsoft.DBforMySQL/servers/administrators/read | Obtém uma lista de administradores do servidor MySQL. |
> | Ação | Microsoft.DBforMySQL/servers/administrators/write | Cria ou atualiza o administrador do servidor MySQL com os parâmetros especificados. |
> | Ação | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft.DBforMySQL/servers/advisors/read | Retorna a lista de assistentes |
> | Ação | Microsoft.DBforMySQL/servers/advisors/read | Retornar um supervisor |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Ação | Microsoft.DBforMySQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMySQL/servers/databases/delete | Exclui um banco de dados MySQL existente. |
> | Ação | Microsoft.DBforMySQL/servers/databases/read | Retornar a lista de bancos de dados MySQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMySQL/servers/databases/write | Cria um banco de dados MySQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMySQL/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforMySQL/servers/logFiles/read | Retornar a lista de arquivos de log PostgreSQL. |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores MySQL |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforMySQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMySQL/servers/recoverableServers/read | Retornar as informações recuperáveis do MySQL Server |
> | Ação | Microsoft.DBforMySQL/servers/replicas/read | Obter réplicas de leitura de um servidor MySQL |
> | Ação | Microsoft.DBforMySQL/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Ação | Microsoft.DBforMySQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Ação | Microsoft.DBforMySQL/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Ação | Microsoft.DBforMySQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Ação | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL com base no resourcegroup |
> | Ação | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforPostgreSQL/operations/read | Retornar a lista de operações PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/register/action | Registrar provedor de recursos PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/delete | Exclui um administrador existente do servidor PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/read | Obtém uma lista de administradores do servidor PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/write | Cria ou atualiza o administrador do servidor PostgreSQL com os parâmetros especificados. |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/read | Retorna a lista de assistentes |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/delete | Exclui um banco de dados PostgreSQL existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/read | Retornar a lista de bancos de dados PostgreSQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/write | Cria um banco de dados PostgreSQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/logFiles/read | Retornar a lista de arquivos de log PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/Read | Obter os recursos de link privado para o servidor PostgreSQL correspondente |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores Postgres |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforPostgreSQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Retornar as informações recuperáveis do PostgreSQL Server |
> | Ação | Microsoft.DBforPostgreSQL/servers/replicas/read | Obter réplicas de leitura de um servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Ação | Microsoft.DBforPostgreSQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores Postgres |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/logDefinitions/Read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/Account/metricDefinitions/Read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verificar se o nome do IotHub está disponível |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verificar se o nome do IotHub está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verificar se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verificar se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft. Devices/digitalTwins/Delete | Excluir uma conta gêmeos digital existente e todos os seus filhos |
> | Ação | Microsoft. Devices/digitalTwins/operationresults/ler | Obter o status de uma operação em uma conta do gêmeos digital |
> | Ação | Microsoft. Devices/digitalTwins/Read | Obtém uma lista das contas do digital gêmeos associadas a uma assinatura |
> | Ação | Microsoft. Devices/digitalTwins/SKUs/leitura | Obter uma lista de SKUs válidos para contas do digital gêmeos |
> | Ação | Microsoft. Devices/digitalTwins/Write | Criar uma nova conta de gêmeos de dígito |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Excluir o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obter o recurso de estatísticas do locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obter a chave de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obter chaves de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métrica de cota |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obter o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/iotHubs/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/iotHubs/Delete | Excluir recurso IotHub |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Excluir o filtro de Grade de Eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obter o filtro de Grade de Eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar novo ou atualizar filtro de Grade de Eventos existente |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obter a chave IotHub para o nome fornecido |
> | Ação | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas de IotHub |
> | Ação | Microsoft.Devices/iotHubs/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves IotHub |
> | Ação | Microsoft.Devices/IotHubs/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/IotHubs/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/operationresults/Read | Obtém o resultado da operação (API obsoleta) |
> | Ação | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métrica de cota |
> | Ação | Microsoft.Devices/iotHubs/Read | Obter os recursos IotHub |
> | Ação | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Ação | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Ação | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft.Devices/iotHubs/skus/Read | Obter SKUs IotHub válidos |
> | Ação | Microsoft.Devices/iotHubs/Write | Criar ou atualizar recursos IotHub |
> | Ação | Microsoft.Devices/locations/operationresults/Read | Obter resultado da operação baseado na localização |
> | Ação | Microsoft.Devices/operationresults/Read | Obtém o resultado da operação |
> | Ação | Microsoft.Devices/operations/Read | Obter todas as operações de ResourceProvider |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/Delete | Excluir recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter Chaves IotDps para nome da chave |
> | Ação | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves IotDps |
> | Ação | Microsoft.Devices/provisioningServices/logDefinitions/read | Obter as definições de log disponíveis para o Serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obter as métricas disponíveis para o serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/operationresults/Read | Obtém o resultado da operação DPS |
> | Ação | Microsoft.Devices/provisioningServices/Read | Obter recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/skus/Read | Obter SKUs IotDps válido |
> | Ação | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Ação | Microsoft.Devices/register/action | Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub |
> | Ação | Microsoft.Devices/usages/Read | Obter detalhes de uso de assinatura para esse provedor. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DevSpaces/controllers/delete | Excluir o Controlador do Azure Dev Spaces e os serviços do plano de dados |
> | Ação | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Listar detalhes de conexão para a infraestrutura do Controlador de Infraestrutura do Azure Dev Spaces |
> | Ação | Microsoft.DevSpaces/controllers/read | Leia as propriedades do Controlador do Azure Dev Spaces |
> | Ação | Microsoft.DevSpaces/controllers/write | Criar ou atualizar as propriedades do Controlador do Azure Dev Spaces |
> | Ação | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Verificar o mapeamento de controlador existente para um host de contêiner |
> | Ação | Microsoft.DevSpaces/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft.DevSpaces/register/action | Registre o provedor de recursos do Microsoft Dev Spaces com uma assinatura |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DevTestLab/labCenters/delete | Excluir centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/read | Ler centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/write | Adicionar ou modificar as centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Ler modelos do Azure Resource Manager. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo do ARM para o artefato determinado, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefatos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/delete | Excluir fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/read | Ler fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/write | Adicionar ou modificar fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/costs/read | Ler custos. |
> | Ação | Microsoft.DevTestLab/labs/costs/write | Adicionar ou modificar custos. |
> | Ação | Microsoft.DevTestLab/labs/CreateEnvironment/action | Criar máquinas virtuais em um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/customImages/delete | Excluir imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/read | Ler imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/write | Adicionar ou modificar imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportar o uso de recursos de laboratório para uma conta de armazenamento |
> | Ação | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gerar um URI para carregar imagens de disco personalizadas para um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importar uma máquina virtual para um laboratório diferente. |
> | Ação | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponíveis para a criação de imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/delete | Excluir canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Enviar notificação ao canal fornecido. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/read | Ler canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/write | Adicionar ou modificar canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avaliar a política de laboratório. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/delete | Excluir políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/read | Ler políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/read | Leia conjuntos de políticas. |
> | Ação | Microsoft.DevTestLab/labs/read | Ler laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Listar todas as agendas aplicáveis |
> | Ação | Microsoft.DevTestLab/labs/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/delete | Excluir executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/read | Ler os executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/write | Adicionar ou modificar os executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/delete | Excluir galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/read | Ler galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Excluir imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Ler imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Adicionar ou modificar imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/write | Adicionar ou modificar galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/users/delete | Excluir perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexar e criar a concessão do disco para a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/delete | Excluir discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexar e interromper a concessão do disco anexado à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/read | Ler discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/write | Adicionar ou modificar discos. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/read | Ler ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/read | Ler perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/delete | Excluir segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/read | Ler segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/write | Adicionar ou modificar segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Excluir malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Ler malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Iniciar uma malha de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Parar uma malha de serviço |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adicionar ou modificar malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexar um disco de dados novo ou existente à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicar artefatos à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Assumir o controle de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/delete | Excluir as máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexar o disco especificado da máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/read | Ler máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Reimplanta uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensiona a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reiniciar uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Iniciar uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transferências de todos os discos de dados anexados à máquina virtual a ser possuída pelo usuário atual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Liberar propriedade de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Delete | Exclua bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Read | Leia bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Write | Adicionar ou modificar bastionhosts. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/delete | Excluir redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/read | Ler redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/write | Adicionar ou modificar redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/delete | Exclui pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/read | Lê pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/write | Adiciona ou modifica pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/write | Adicionar ou modificar os laboratórios. |
> | Ação | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.DevTestLab/register/action | Registra a assinatura |
> | Ação | Microsoft.DevTestLab/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Retarget/action | Atualizar ID de recurso de destino de uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/write | Adicionar ou modificar agendamentos. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DocumentDB/databaseAccountNames/read | Verificar a disponibilidade do nome. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Excluir uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Ler uma coleção ou listar todas as coleções. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Ler uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Atualizar uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Criar ou atualizar uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Excluir um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Ler um contêiner ou listar todos os contêineres. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Ler uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Atualizar uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Criar ou atualizar um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Excluir um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Excluir um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Ler um grafo ou listar todos os grafos. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Ler uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Atualizar uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Criar ou atualizar um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Ler um banco de dados ou listar todos os bancos. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Ler uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Atualizar uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Criar um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Excluir um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Ler um keyspace ou listar todos os keyspaces. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Ler uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Atualize uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Crie um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/backup/action | Envie uma solicitação para configurar o backup |
> | Ação | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar o grupo de recursos de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Ler a coleção de definições de métrica. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Ler a métrica da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler métricas de nível de chave de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas de nível de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Ler as partições da conta do banco de dados em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler usos do nível de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Ler os usos da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Ler as definições de métrica do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Ler a métrica do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Ler os usos do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/delete | Excluir as contas de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de failover das regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual |
> | Ação | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Obter a política de backup da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de caracteres de conexão para uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Ler o banco de dados de definições de métrica da conta. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metrics/read | Ler a métrica da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Torne offline uma região de uma conta de banco de dados.  |
> | Ação | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Torne online uma região de uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Ler métricas de latência |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/read | Lê percentis de latências de replicação |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região específica de origem e destino |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Ler métricas de latência para uma região de destino específica |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Excluir um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Ler um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Validar um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Criar ou atualizar um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/read | Ler uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Ler as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Girar chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Ler as métricas de coleção regional. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler as métricas de nível de chave de partição da conta do banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Lear as métricas de nível de partição da conta do banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Lê as partições da conta do banco de dados regional em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Ler a região e as métricas da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | Ação | Microsoft.DocumentDB/databaseAccounts/usages/read | Ler os usos da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/write | Atualizar uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notificar o Microsoft.DocumentDB que a VirtualNetwork ou Sub-rede está sendo excluída |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Lê o Status da operação assíncrona deleteVirtualNetworkOrSubnets |
> | Ação | Microsoft.DocumentDB/locations/operationsStatus/read | Lê o status de operações assíncronas |
> | Ação | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/operations/read | Operações de leitura disponíveis para o Microsoft DocumentDB  |
> | Ação | Microsoft.DocumentDB/register/action |  Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verificar se um domínio está disponível para compra |
> | Ação | Microsoft.DomainRegistration/domains/Delete | Excluir um modo de exibição existente. |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Excluir o identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Listar identificadores de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar identificador |
> | Ação | Microsoft.DomainRegistration/domains/operationresults/Read | Obter uma operação de domínio |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter domínio |
> | Ação | Microsoft.DomainRegistration/domains/renew/Action | Renovar um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um existente |
> | Ação | Microsoft.DomainRegistration/generateSsoRequest/Action | Gerar uma solicitação para entrar no centro de controle de domínio. |
> | Ação | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recuperar as recomendações de domínio da lista com base em palavras-chave |
> | Ação | Microsoft.DomainRegistration/operations/Read | Listar todas as operações do registro de domínio do serviço de aplicativo |
> | Ação | Microsoft.DomainRegistration/register/action | Registrar o provedor de recursos Microsoft Domains para a assinatura |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listar ação de Contrato |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínios toplevel |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínio toplevel |
> | Ação | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.EventGrid/domains/delete | Excluir um domínio |
> | Ação | Microsoft.EventGrid/domains/listKeys/action | Listar chaves para um domínio |
> | Ação | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para domínios |
> | Ação | Microsoft.EventGrid/domains/read | Ler um domínio |
> | Ação | Microsoft.EventGrid/domains/regenerateKey/action | Regenerar chave para um domínio |
> | Ação | Microsoft. EventGrid/Domains/topics/Delete | Tópico excluir um domínio |
> | Ação | Microsoft.EventGrid/domains/topics/read | Ler um tópico de domínio |
> | Ação | Microsoft. EventGrid/domínios/tópicos/gravação | Criar ou atualizar um tópico de domínio |
> | Ação | Microsoft.EventGrid/domains/write | Criar ou atualizar um domínio |
> | Ação | Microsoft.EventGrid/eventSubscriptions/delete | Excluir um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter a URL completa para a assinatura de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para assinaturas de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para assinaturas de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventSubscriptions |
> | Ação | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/read | Lê um extensionTopic. |
> | Ação | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Ação | Microsoft.EventGrid/locations/operationResults/read | Ler o resultado de uma operação de regional |
> | Ação | Microsoft.EventGrid/locations/operationsStatus/read | Ler o status de uma operação de regional |
> | Ação | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Ação | Microsoft.EventGrid/operationResults/read | Ler o resultado de uma operação |
> | Ação | Microsoft.EventGrid/operations/read | Lista as operações do EventGrid. |
> | Ação | Microsoft.EventGrid/operationsStatus/read | Leia o status de uma operação de leitura |
> | Ação | Microsoft.EventGrid/register/action | Registra a assinatura para o provedor de recursos do EventGrid. |
> | Ação | Microsoft.EventGrid/topics/delete | Excluir um tópico |
> | Ação | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/topics/read | Ler um tópico |
> | Ação | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Ação | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventtypes/read | Ler eventTypes compatíveis com um topictype |
> | Ação | Microsoft.EventGrid/topictypes/read | Ler um topictype |
> | Ação | Microsoft.EventGrid/unregister/action | Cancela o registro da assinatura no provedor de recursos EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.EventHub/availableClusterRegions/read | Operação de leitura para listar clusters pré-configurados disponíveis pela região do Azure. |
> | Ação | Microsoft.EventHub/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.EventHub/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API foi preterida. Use CheckNameAvailability. |
> | Ação | Microsoft.EventHub/clusters/delete | Exclui um recurso de cluster existente. |
> | Ação | Microsoft.EventHub/clusters/namespaces/read | Listar IDs de ARM de namespace para namespaces em um cluster. |
> | Ação | Microsoft.EventHub/clusters/operationresults/read | Obtenha o status de uma operação de cluster assíncrona. |
> | Ação | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém a lista de Descrições de Recursos de Métrica de Cluster |
> | Ação | Microsoft.EventHub/clusters/read | Obtém a Descrição do Recurso de Cluster |
> | Ação | Microsoft.EventHub/clusters/write | Cria ou modifica um recurso de cluster existente. |
> | Ação | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras VNet no Provedor de Recursos do EventHub para o VNet especificado |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.EventHub/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar EventHub. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para excluir regras de autorização EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Criar regras de autorização EventHub e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para excluir o recurso ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Criar ou atualizar propriedades ConsumerGroup. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para excluir o recurso EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou atualizar propriedades EventHub. |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/delete | Excluir recurso de filtro IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/read | Obter recurso de filtro IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/write | Criar Recurso de Filtro IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Enviar mensagens |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.EventHub/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.EventHub/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obtém o recurso de regra de rede virtual |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.EventHub/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.EventHub/operations/read | Obter Operações |
> | Ação | Microsoft.EventHub/register/action | Registrar a assinatura do provedor de recursos EventHub e permitir a criação de recursos EventHub |
> | Ação | Microsoft.EventHub/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Ação | Microsoft.EventHub/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Ação | Microsoft.EventHub/unregister/action | Registrar o Provedor de Recursos EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Features/features/read | Obter os recursos de uma assinatura. |
> | Ação | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.Features/providers/features/read | Obter o recurso de uma assinatura em determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/register/action | Registrar o recurso de uma assinatura em determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/unregister/action | Cancelar o registro do recurso de uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft.Features/register/action | Registra o recurso de uma assinatura. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Excluir atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obter atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Obtenha o relatório de atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Criar uma nova atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/operações/leitura | Obtém as operações para o provedor de recursos Microsoft. GuestConfiguration |
> | Ação | Microsoft.GuestConfiguration/register/action | Registra a assinatura para o provedor de recursos Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.HDInsight/clusters/applications/delete | Excluir o aplicativo do cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/read | Obter aplicativo para o cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar aplicativo para o cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar configuração de RDP para Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Obter configurações de Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/delete | Excluir um cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/excluir | Excluir a extensão de cluster do cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/leitura | Obter a extensão do cluster para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/gravação | Criar extensão de cluster para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter configurações de gateway para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as configurações do gateway para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/write | Criar ou atualizar o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/locations/capabilities/read | Obter recursos de assinatura |
> | Ação | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar disponibilidade do nome |
> | Ação | Microsoft. HDInsight/registro/ação | Registrar o provedor de recursos do HDInsight para a assinatura |
> | Ação | Microsoft. HDInsight/cancelar registro/ação | Cancelar registro do provedor de recursos do HDInsight para a assinatura |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ImportExport/jobs/delete | Excluir um trabalho existente. |
> | Ação | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obter as chaves do BitLocker para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/jobs/read | Obter as propriedades para o trabalho especificado ou retornar a lista de trabalhos. |
> | Ação | Microsoft.ImportExport/jobs/write | Criar um trabalho com os parâmetros especificados ou atualizar as propriedades ou marcações do trabalho especificado. |
> | Ação | Microsoft.ImportExport/locations/read | Obter as propriedades do local especificado ou retornar a lista de locais. |
> | Ação | Microsoft. ImportExport/operações/leitura | Obtém as operações com suporte do provedor de recursos. |
> | Ação | Microsoft.ImportExport/register/action | Registra a assinatura do provedor de recursos importar/exportar e habilita a criação de trabalhos de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft.insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Insights/ActionGroups/Delete | Excluir um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do Log de Atividades ativado |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Delete | Excluir um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta do log de atividades |
> | Ação | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássico ativado |
> | Ação | Microsoft.Insights/AlertRules/Delete | Excluir alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Incidents/Read | Ler incidente de alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Read | Ler alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássico resolvido |
> | Ação | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássico acelerada |
> | Ação | Microsoft.Insights/AlertRules/Write | Criar ou atualizar o alerta de métrica clássico |
> | Ação | Microsoft.Insights/AutoscaleSettings/Delete | Excluir uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar um atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/AutoscaleSettings/Read | Ler uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução do dimensionamento automático iniciada |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução do dimensionamento automático concluída |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Aumento do dimensionamento automático iniciado |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Aumento do dimensionamento automático concluído |
> | Ação | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/Baseline/Read | Ler uma linha de base de métrica (versão prévia) |
> | Ação | Microsoft.Insights/CalculateBaseline/Read | Calcular a linha de base para valores de métrica (versão prévia) |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Delete | Excluindo um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Read | Lendo um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Write | Gravando um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Action | Ação da tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Delete | Excluindo um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Read | Lendo um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Write | Gravando um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Delete | Excluindo uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Read | Lendo uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Write | Gravação de anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Api/Read | Lendo a API de dados do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Action | Gerando uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Delete | Excluindo uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Read | Lendo uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lendo um plano de cobrança para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lendo os recursos de cobrança atuais para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Gravando recursos de cobrança atuais para componente do Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapReached/Action | Atingido o limite diário para o componente Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapWarningThresholdReached/Action | Limite de aviso de limite diário atingido para o componente Application Insights |
> | Ação | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lendo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/Delete | Excluindo uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Events/Read | Obtém logs do Application Insights usando o formato de consulta OData |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Action | Ação de configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Delete | Excluindo as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Read | Lendo as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Write | Gravando as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExtendQueries/Read | Lendo os resultados da consulta estendida do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Delete | Excluindo favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Read | Lendo um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Write | Gravando um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/FeatureCapabilities/Read | Lendo as capacidades do recurso do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lendo os recursos de cobrança disponíveis do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetToken/Read | Lendo um token do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/MetricDefinitions/Read | Lendo as definições de métrica do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Metrics/Read | Lendo as métricas do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Move/Action | Mover um componente do Application Insights para outro grupo de recursos ou assinatura |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Excluindo um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lendo um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Write | Gravando um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyFavorites/Read | Lendo um favorito pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/Operations/Read | Obtém o status de operações de longa duração no Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Read | Lendo um plano de preços do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Write | Gravando um plano de preços do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lendo a configuração de detecção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Gravando a configuração de detecção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Components/Purge/Action | Limpe dados do Application Insights |
> | Ação | Microsoft.Insights/Components/Query/Read | Execute consultas em logs do Application Insights |
> | Ação | Microsoft.Insights/Components/QuotaStatus/Read | Lendo o status de cota do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Read | Lendo uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lendo as localizações de teste na Web do Application Insights |
> | Ação | Microsoft.Insights/Components/Webtests/Read | Lendo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Delete | Excluindo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Read | Lendo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Write | Gravando uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/Write | Gravando em uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/DiagnosticSettings/Delete | Excluir configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Write | Criar um atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/EventCategories/Read | Ler categorias de evento do Log de Atividades disponíveis |
> | Ação | Microsoft.Insights/eventtypes/digestevents/Read | Ler resumo do tipo de evento de gerenciamento |
> | Ação | Microsoft.Insights/eventtypes/values/Read | Ler eventos do Log de Atividades |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Excluir uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ListMigrationDate/Action | Recuperar a data de migração da Assinatura |
> | Ação | Microsoft.Insights/ListMigrationDate/Read | Recuperar a data de migração da assinatura |
> | Ação | Microsoft.Insights/LogDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/LogProfiles/Delete | Excluir um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Read | Ler um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Lê dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ADReplicationResult/Read | Lê dados da tabela ADReplicationResult |
> | Ação | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Lê dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Alert/Read | Lê dados da tabela de Alertas |
> | Ação | Microsoft.Insights/Logs/AlertHistory/Read | Lê dados da tabela AlertHistory |
> | Ação | Microsoft.Insights/Logs/ApplicationInsights/Read | Lê dados da tabela ApplicationInsights |
> | Ação | Microsoft.Insights/Logs/AzureActivity/Read | Lê dados da tabela AzureActivity |
> | Ação | Microsoft.Insights/Logs/AzureMetrics/Read | Lê dados da tabela AzureMetrics |
> | Ação | Microsoft.Insights/Logs/BoundPort/Read | Lê dados da tabela BoundPort |
> | Ação | Microsoft.Insights/Logs/CommonSecurityLog/Read | Lê dados da tabela CommonSecurityLog |
> | Ação | Microsoft.Insights/Logs/ComputerGroup/Read | Lê dados da tabela ComputerGroup |
> | Ação | Microsoft.Insights/Logs/ConfigurationChange/Read | Lê dados da tabela ConfigurationChange |
> | Ação | Microsoft.Insights/Logs/ConfigurationData/Read | Lê dados da tabela ConfigurationData |
> | Ação | Microsoft.Insights/Logs/ContainerImageInventory/Read | Lê dados da tabela ContainerImageInventory |
> | Ação | Microsoft.Insights/Logs/ContainerInventory/Read | Lê dados da tabela ContainerInventory |
> | Ação | Microsoft.Insights/Logs/ContainerLog/Read | Lê dados da tabela ContainerLog |
> | Ação | Microsoft.Insights/Logs/ContainerServiceLog/Read | Lê dados da tabela ContainerServiceLog |
> | Ação | Microsoft.Insights/Logs/DeviceAppCrash/Read | Lê dados da tabela DeviceAppCrash |
> | Ação | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Lê dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.Insights/Logs/DeviceCalendar/Read | Lê dados da tabela DeviceCalendar |
> | Ação | Microsoft.Insights/Logs/DeviceCleanup/Read | Lê dados da tabela DeviceCleanup |
> | Ação | Microsoft.Insights/Logs/DeviceConnectSession/Read | Lê dados da tabela DeviceConnectSession |
> | Ação | Microsoft.Insights/Logs/DeviceEtw/Read | Lê dados da tabela DeviceEtw |
> | Ação | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Lê dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHealth/Read | Lê dados da tabela DeviceHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Lê dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Lê dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Lê dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.Insights/Logs/DeviceSleepState/Read | Lê dados da tabela DeviceSleepState |
> | Ação | Microsoft.Insights/Logs/DHAppFailure/Read | Lê dados da tabela DHAppFailure |
> | Ação | Microsoft.Insights/Logs/DHAppReliability/Read | Lê dados da tabela DHAppReliability |
> | Ação | Microsoft.Insights/Logs/DHDriverReliability/Read | Lê dados da tabela DHDriverReliability |
> | Ação | Microsoft.Insights/Logs/DHLogonFailures/Read | Lê dados da tabela DHLogonFailures |
> | Ação | Microsoft.Insights/Logs/DHLogonMetrics/Read | Lê dados da tabela DHLogonMetrics |
> | Ação | Microsoft.Insights/Logs/DHOSCrashData/Read | Lê dados da tabela DHOSCrashData |
> | Ação | Microsoft.Insights/Logs/DHOSReliability/Read | Lê dados da tabela DHOSReliability |
> | Ação | Microsoft.Insights/Logs/DHWipAppLearning/Read | Lê dados da tabela DHWipAppLearning |
> | Ação | Microsoft.Insights/Logs/DnsEvents/Read | Lê dados da tabela DnsEvents |
> | Ação | Microsoft.Insights/Logs/DnsInventory/Read | Lê dados da tabela DnsInventory |
> | Ação | Microsoft.Insights/Logs/ETWEvent/Read | Lê dados da tabela ETWEvent |
> | Ação | Microsoft.Insights/Logs/Event/Read | Lê dados da tabela Event |
> | Ação | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Lê dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler os dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Heartbeat/Read | Lê dados da tabela Heartbeat |
> | Ação | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Lê dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/InboundConnection/Read | Lê dados da tabela InboundConnection |
> | Ação | Microsoft.Insights/Logs/KubeNodeInventory/Read | Lê dados da tabela KubeNodeInventory |
> | Ação | Microsoft.Insights/Logs/KubePodInventory/Read | Lê dados da tabela KubePodInventory |
> | Ação | Microsoft.Insights/Logs/LinuxAuditLog/Read | Lê dados da tabela LinuxAuditLog |
> | Ação | Microsoft.Insights/Logs/MAApplication/Read | Lê dados da tabela MAApplication |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealth/Read | Lê dados da tabela MAApplicationHealth |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstance/Read | Lê dados da tabela MAApplicationInstance |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Lê dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.Insights/Logs/MADeploymentPlan/Read | Lê dados da tabela MADeploymentPlan |
> | Ação | Microsoft.Insights/Logs/MADevice/Read | Lê dados da tabela MADevice |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.Insights/Logs/MADeviceReadiness/Read | Lê dados da tabela MADeviceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverReadiness/Read | Lê dados da tabela MADriverReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddin/Read | Lê dados da tabela MAOfficeAddin |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Lê dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Lê dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Lê dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeApp/Read | Lê dados da tabela MAOfficeApp |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Lê dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Lê dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Lê dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Lê dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Lê dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Lê dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Lê dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Lê dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Lê dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuite/Read | Lê dados da tabela MAOfficeSuite |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Lê dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Lê dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Lê dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Lê dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Lê dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Lê dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/NetworkMonitoring/Read | Lê dados da tabela NetworkMonitoring |
> | Ação | Microsoft.Insights/Logs/OfficeActivity/Read | Lê dados da tabela OfficeActivity |
> | Ação | Microsoft.Insights/Logs/Operation/Read | Lê dados da tabela Operation |
> | Ação | Microsoft.Insights/Logs/OutboundConnection/Read | Lê dados da tabela OutboundConnection |
> | Ação | Microsoft.Insights/Logs/Perf/Read | Lê dados da tabela Perf |
> | Ação | Microsoft.Insights/Logs/ProtectionStatus/Read | Lê dados da tabela ProtectionStatus |
> | Ação | Microsoft.Insights/Logs/Read | Lendo dados de todos os seus logs |
> | Ação | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Lê dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.Insights/Logs/ReservedCommonFields/Read | Lê dados da tabela ReservedCommonFields |
> | Ação | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Lê dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Lê dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SecurityAlert/Read | Lê dados da tabela SecurityAlert |
> | Ação | Microsoft.Insights/Logs/SecurityBaseline/Read | Lê dados da tabela SecurityBaseline |
> | Ação | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Lê dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.Insights/Logs/SecurityDetection/Read | Lê dados da tabela SecurityDetection |
> | Ação | Microsoft.Insights/Logs/SecurityEvent/Read | Lê dados da tabela SecurityEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Lê dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Lê dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Lê dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Lê dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Ler os dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Ler os dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Lê dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Lê dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Lê dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.Insights/Logs/Syslog/Read | Lê dados da tabela Syslog |
> | Ação | Microsoft.Insights/Logs/SysmonEvent/Read | Lê dados da tabela SysmonEvent |
> | Ação | Microsoft. insights/logs/tabelas. personalizado/lido | Lê dados de qualquer log personalizado |
> | Ação | Microsoft.Insights/Logs/UAApp/Read | Lê dados da tabela UAApp |
> | Ação | Microsoft.Insights/Logs/UAComputer/Read | Lê dados da tabela UAComputer |
> | Ação | Microsoft.Insights/Logs/UAComputerRank/Read | Lê dados da tabela UAComputerRank |
> | Ação | Microsoft.Insights/Logs/UADriver/Read | Lê dados da tabela UADriver |
> | Ação | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Lê dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.Insights/Logs/UAFeedback/Read | Lê dados da tabela UAFeedback |
> | Ação | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Lê dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Lê dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Lê dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Lê dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.Insights/Logs/UASysReqIssue/Read | Lê dados da tabela UASysReqIssue |
> | Ação | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Lê dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.Insights/Logs/Update/Read | Lê dados da tabela Update |
> | Ação | Microsoft.Insights/Logs/UpdateRunProgress/Read | Lê dados da tabela UpdateRunProgress |
> | Ação | Microsoft.Insights/Logs/UpdateSummary/Read | Lê dados da tabela UpdateSummary |
> | Ação | Microsoft.Insights/Logs/Usage/Read | Lê dados da tabela Usage |
> | Ação | Microsoft.Insights/Logs/W3CIISLog/Read | Lê dados da tabela W3CIISLog |
> | Ação | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Lê dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Lê dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Lê dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.Insights/Logs/WDAVStatus/Read | Lê dados da tabela WDAVStatus |
> | Ação | Microsoft.Insights/Logs/WDAVThreat/Read | Lê dados da tabela WDAVThreat |
> | Ação | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Lê dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WindowsFirewall/Read | Lê dados da tabela WindowsFirewall |
> | Ação | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Lê dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WireData/Read | Lê dados da tabela WireData |
> | Ação | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Lê dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.Insights/Logs/WUDOStatus/Read | Lê dados da tabela WUDOStatus |
> | Ação | Microsoft.Insights/MetricAlerts/Delete | Excluir um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Read | Ler um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Status/Read | Ler status de alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Ação | Microsoft.Insights/MetricBaselines/Read | Ler linhas de base de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Metricnamespaces/Read | Ler namespaces de métrica |
> | Ação | Microsoft.Insights/Metrics/Action | Ação de Métrica |
> | Ação | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Ler métrica |
> | Ação | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler métrica |
> | Ação | Microsoft.Insights/Metrics/Read | Ler métrica |
> | DataAction | Microsoft.Insights/Metrics/Write | Gravar métricas |
> | Ação | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar a assinatura para o novo modelo de preços |
> | Ação | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Ação | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Reverter a assinatura para o modelo de preços herdado |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Delete | Excluindo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Read | Lendo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Write | Gravando uma regra de consulta agendada |
> | Ação | Microsoft.Insights/Tenants/Register/Action | Inicializa o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Unregister/Action | Registrar o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Webtests/Delete | Excluindo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Webtests/GetToken/Read | Lendo um token de teste na Web |
> | Ação | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lendo as definições de métrica do teste na Web |
> | Ação | Microsoft.Insights/Webtests/Metrics/Read | Lendo as métricas de um teste na Web |
> | Ação | Microsoft.Insights/Webtests/Read | Lendo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Webtests/Write | Gravando em uma configuração de teste na Web |
> | Ação | Microsoft. insights/pastas de trabalho/exclusão | Excluir uma pasta de trabalho |
> | Ação | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Ação | Microsoft. insights/pastas de trabalho/gravação | Criar ou atualizar uma pasta de trabalho |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Intune/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/delete | Leitura de uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/delete | Gravação de uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.IoTCentral/appTemplates/action | Obtém todos os modelos de aplicativo disponíveis no Azure IoT Central |
> | Ação | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se um nome de aplicativo do IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se um subdomínio do aplicativo do IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/IoTApps/delete | Exclui um aplicativo do IoT Central |
> | Ação | Microsoft.IoTCentral/IoTApps/read | Obtém um único aplicativo do IoT Central |
> | Ação | Microsoft.IoTCentral/IoTApps/write | Cria ou atualiza um aplicativo do IoT Central |
> | Ação | Microsoft.IoTCentral/operations/read | Obtém todas as operações disponíveis em aplicativos de IoT Central |
> | Ação | Microsoft.IoTCentral/register/action | Registrar a assinatura do provedor de recursos do IoT Central do Azure |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.IoTSpaces/Graph/delete | Exclui o recurso Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/Graph/read | Obtém os recursos Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/Graph/write | Cria o recurso Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/register/action | Registra a assinatura no provedor de recursos Microsoft.IoTSpaces Graph para habilitar a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.KeyVault/checkNameAvailability/read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | Ação | Microsoft.KeyVault/deletedVaults/read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/hsmPools/delete | Excluir um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/joinVault/action | Ingressar em um cofre de chaves para um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/read | Exibir as propriedades de um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/write | Criar um novo pool de HSM de atualização das propriedades de um pool de HSM existente |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/read | Exibir as propriedades de um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.KeyVault que uma rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft.KeyVault/locations/operationResults/read | Verificar o resultado de uma operação de longo prazo |
> | Ação | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | Ação | Microsoft.KeyVault/register/action | Registrar uma assinatura |
> | Ação | Microsoft.KeyVault/unregister/action | Cancela uma assinatura |
> | Ação | Microsoft.KeyVault/vaults/accessPolicies/write | Atualizar uma política de acesso existente por mesclagem ou substituição, ou adicionar uma nova política de acesso a um cofre. |
> | Ação | Microsoft.KeyVault/vaults/delete | Excluir um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/deploy/action | Permite acesso aos segredos em um cofre de chaves durante a implantação de recursos do Azure |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo excluída |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo exibida |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifica o Microsoft. keyvault de que uma nova assinatura do EventGrid para Key Vault está sendo criada |
> | Ação | Microsoft.KeyVault/vaults/read | Exibir as propriedades de um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/secrets/read | Exiba as propriedades de um segredo, mas não seu valor. |
> | Ação | Microsoft.KeyVault/vaults/secrets/write | Crie um novo segredo ou atualize o valor de um segredo existente. |
> | Ação | Microsoft.KeyVault/vaults/write | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Kusto/Clusters/Activate/action | Inicia o cluster. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Exclui um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Lê um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Grava um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Verifica a disponibilidade do nome do cluster. |
> | Ação | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adiciona entidades de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Verifica a disponibilidade do nome de um determinado tipo. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Exclui uma resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lê uma resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Grava um resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valida a conexão de dados do banco de dado. |
> | Ação | Microsoft.Kusto/Clusters/Databases/delete | Exclui um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Exclui uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Grava um resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valida a conexão do hub de eventos do banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Lista as entidades de segurança do banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/read | Lê um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Remove as entidades de segurança do banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/write | Grava um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Deactivate/action | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/delete | Exclui um resourceCopy de cluster. |
> | Ação | Microsoft.Kusto/Clusters/read | Lê um resourceCopy de cluster. |
> | Ação | Microsoft.Kusto/Clusters/SKUs/read | Lê um resourceCopy de SKU de cluster. |
> | Ação | Microsoft. Kusto/clusters/iniciar/ação | Inicia o cluster. |
> | Ação | Microsoft. Kusto/clusters/parar/ação | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/write | Grava um resourceCopy de cluster. |
> | Ação | Microsoft. Kusto/DetachFollowerDatabases/Action | Desanexa os bancos de dados do seguidor. |
> | Ação | Microsoft. Kusto/ListFollowerDatabases/Action | Lista os bancos de dados do seguidor. |
> | Ação | Microsoft.Kusto/Locations/CheckNameAvailability/action | Verifica a disponibilidade do nome do resourceCopy. |
> | Ação | Microsoft.Kusto/locations/operationresults/read | ResourceCopys operações de leitura |
> | Ação | Microsoft.Kusto/Operations/read | ResourceCopys operações de leitura |
> | Ação | Microsoft. Kusto/registro/ação | Ação de registro de assinatura |
> | Ação | Microsoft. Kusto/registro/ação | Registra a assinatura no provedor de recursos Kusto. |
> | Ação | Microsoft.Kusto/SKUs/read | Lê um SKU resourceCopy. |
> | Ação | Microsoft. Kusto/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.LabServices/labAccounts/CreateLab/action | Criar um laboratório em uma conta de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/delete | Excluir contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/delete | Excluir imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/write | Adicionar ou modificar imagens da Galeria. |
> | Ação | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obter informações sobre disponibilidade regional para cada categoria de tamanho configurado em uma conta de laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar usuários a um laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Excluir configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ler ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Reiniciar a senha do usuário em um ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisionar/desprovisionar recursos necessários para uma configuração de ambiente com base no estado atual da configuração de ambiente/laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Redefine a senha na máquina virtual do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Salva a imagem de modelo atual na Galeria compartilhada na conta do laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Ler agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Interrompe um modelo interrompendo todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/read | Ler laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Envia um email com o link de registro para o laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/delete | Excluir usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/read | Ler usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/write | Adicionar ou modificar usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar os laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/read | Ler contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Exclui as galerias compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/read | Lê as galerias compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/write | Adiciona ou modifica as galerias compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/delete | Exclui as imagens compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/read | Lê as imagens compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/write | Adiciona ou modifica as imagens compartilhadas. |
> | Ação | Microsoft.LabServices/labAccounts/write | Adicionar ou modificar contas de laboratório. |
> | Ação | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.LabServices/register/action | Registra a assinatura |
> | Ação | Microsoft.LabServices/users/GetOperationBatchStatus/action | Obter o status da operação de imagem |
> | Ação | Microsoft.LabServices/users/GetOperationStatus/action | Obtém o status da operação de execução longa |
> | Ação | Microsoft.LabServices/users/GetPersonalPreferences/action | Obter preferências pessoais para um usuário |
> | Ação | Microsoft.LabServices/users/ListAllEnvironments/action | Listar todos os ambientes do usuário |
> | Ação | Microsoft.LabServices/users/Register/action | Registra um usuário em um laboratório gerenciado |
> | Ação | Microsoft.LabServices/users/ResetPassword/action | Reiniciar a senha do usuário em um ambiente |
> | Ação | Microsoft.LabServices/users/StartEnvironment/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/users/StopEnvironment/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/delete | Excluir o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/read | Ler o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/write | Criar ou atualizar o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/delete | Exclui o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/read | Ler o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/write | Criar ou atualizar o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Excluir a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Ler a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Criar ou atualizar a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/delete | Excluir o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/read | Ler o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/write | Criar ou atualizar o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/delete | Excluir a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/join/action | Ingressa na Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém a URL de retorno de chamada para a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obter as chaves no cofre de chaves. |
> | Ação | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra os eventos de acompanhamento na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/delete | Excluir o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/read | Ler o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/write | Criar ou atualizar o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/delete | Excluir o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/read | Lê o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/write | Criar ou atualizar o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Ler as definições de log da Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/read | Ler a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/delete | Excluir o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/read | Ler o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/write | Criar ou atualizar o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/delete | Excluir a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/read | Ler a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/write | Criar ou atualizar a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/write | Criar ou atualizar a conta de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/delete | Exclui o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/join/action | Une o Ambiente do Serviço de Integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação de API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerenciada do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lê os status de operação do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operação de recomendação do fluxo de trabalho. |
> | Ação | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/operations/read | Obter a operação. |
> | Ação | Microsoft.Logic/register/action | Registrar o provedor de recursos Microsoft.Logic de uma determinada assinatura. |
> | Ação | Microsoft.Logic/workflows/accessKeys/delete | Excluir a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/list/action | Listar os segredos de chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/read | Ler a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/write | Criar ou atualizar a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/delete | Excluir o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/disable/action | Desabilitar o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/enable/action | Permitir o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listCallbackUrl/action | Obter a URL de retorno de chamada para o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listSwagger/action | Obter as definições do Swagger para o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/move/action | Mover o fluxo de trabalho de sua id de assinatura, grupo de recursos e nome existente para uma id de assinatura, grupo de recursos e/ou nome diferente. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Ler as configurações de diagnóstico de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Ler as definições de log de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Ler as definições de métrica de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/read | Ler o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/run/action | Iniciar uma execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obter os rastreamentos de expressão da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/read | Ler a ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obter os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/read | Ler a repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de solicitações da ação de repetição de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de solicitações da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Ler a repetição do escopo da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/cancel/action | Cancelar a execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/delete | Exclui uma execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/operations/read | Ler o status da operação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/read | Ler a execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/suspend/action | Suspender o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/read | Ler os históricos de gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Reenviar o gatilho do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obter a URL de retorno de chamada do gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/read | Ler o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/reset/action | Reiniciar o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/run/action | Executar o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/setState/action | Definir o estado do gatilho. |
> | Ação | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/read | Ler a versão do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obter a URL de retorno de chamada do gatilho. |
> | Ação | Microsoft.Logic/workflows/write | Criar ou atualizar o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover associações de plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler associações de plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/delete | Excluir plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/join/action | Ingressar em um plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/read | Ler um plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationresults/read | Obter o resultado de uma operação do Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o status de uma operação do Machine Learning em andamento |
> | Ação | Microsoft.MachineLearning/operations/read | Obter operações do Machine Learning |
> | Ação | Microsoft.MachineLearning/register/action | Registra a assinatura do provedor de recursos do serviço Web do Machine Learning e permite a criação de serviços Web. |
> | Ação | Microsoft.MachineLearning/skus/read | Obter SKUs do plano de compromisso do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/action | Criar propriedades do serviço Web regionais para regiões com suporte |
> | Ação | Microsoft.MachineLearning/webServices/delete | Excluir um serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/listkeys/read | Obtém chaves para um Serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/read | Ler um serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/write | Criar ou atualizar serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/delete | Criar um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/read | Ler um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar as chaves da conta de armazenamento configurada para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar Workspace do Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtém o status de uma operação de computação específica |
> | Ação | Microsoft.MachineLearningServices/locations/usages/read | Relatório de uso dos recursos de computação do AML em uma assinatura |
> | Ação | Microsoft.MachineLearningServices/locations/vmsizes/read | Obtém os tamanhos de VM compatíveis |
> | Ação | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtém o status de uma operação de workspace específica |
> | Ação | Microsoft.MachineLearningServices/register/action | Registra a assinatura para o Provedor de Recursos de Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/delete | Exclui os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista segredos para recursos de computação no Workspace dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista os nós dos recursos de computação no Workspace dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/read | Obtém os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/write | Cria ou atualiza os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/delete | Exclui os Workspaces dos Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/excluir | Exclui experimentos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/leitura | Obtém experimentos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Cria ou atualiza experimentos em Serviços de Machine Learning espaço (s) de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista segredos para um Workspace dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/read | Obtém os Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza Workspaces dos Serviços de Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. ManagedIdentity/Identities/Read | Obtém uma identidade atribuída do sistema existente |
> | Ação | Microsoft.ManagedIdentity/register/action | Registra a assinatura no provedor de recursos para a identidade gerenciada |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação RBAC para atribuir uma identidade atribuída ao usuário existente a um recurso |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Excluir uma identidade atribuída ao usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obter uma identidade atribuída ao usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/write | Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. Managedservices/marketplaceRegistrationDefinitions/Read | Recupera uma lista de definições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/operações/leitura | Recupera uma lista de operações de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/operationStatuses/Read | Ler o status da operação do recurso. |
> | Ação | Microsoft. Managedservices/registrar/ação | Registre-se nos serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Delete | Remove a atribuição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Read | Recupera uma lista de atribuições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Write | Adicionar ou modificar a atribuição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Delete | Remove a definição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Read | Recupera uma lista de definições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Write | Adicionar ou modificar a definição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/cancelar registro/ação | Cancelar o registro dos serviços gerenciados. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Management/checkNameAvailability/action | Verificar se o nome do grupo de gerenciamento especificado é válido e exclusivo. |
> | Ação | Microsoft.Management/getEntities/action | Listar todas as entidades (Grupos de Gerenciamento, Assinaturas e etc.) para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/delete | Excluir um grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/delete | Desassociar a assinatura do grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/write | Associar a assinatura existente ao grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | Ação | Microsoft.Management/register/action | Registra a assinatura especificada com Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados a uma conta de mapas. |
> | Ação | Microsoft.Maps/accounts/delete | Exclui uma Conta de Mapas. |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/delete | Excluir um filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/read | Obtenha um filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/listKeys/action | Lista chaves da Conta de Mapas |
> | Ação | Microsoft.Maps/accounts/read | Obtém uma Conta de Mapas. |
> | Ação | Microsoft.Maps/accounts/regenerateKey/action | Gera uma nova chave primária ou secundária da Conta de Mapas |
> | Ação | Microsoft.Maps/accounts/write | Cria ou atualiza uma Conta de Mapas. |
> | Ação | Microsoft.Maps/operations/read | Ler as operações do provedor |
> | Ação | Microsoft.Maps/register/action | Registrar o provedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Retorna um Contrato. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem para o ACR do usuário final. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retorna uma configuração. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Salva uma configuração. |
> | Ação | Microsoft.Marketplace/register/action | Registra o provedor de recursos Microsoft.Marketplace na assinatura. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação DELETE em um recurso de serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obter as chaves de gerenciamento de recursos de serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obter a URL de Logon Único para um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação GET em um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gerar chaves clássicas de gerenciamento de recursos do serviço de desenvolvimento. |
> | Ação | Microsoft.MarketplaceApps/Operations/read | Ler as operações para todos os tipos de recursos. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Retornar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/read | Retornar todos os contratos sob determinada assinatura |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um contrato para um determinado item de máquina virtual do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Assinar ou Cancelar um contrato para um determinado item de máquina virtual do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/operations/read | Listar todas as operações possíveis na API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Media/checknameavailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Ação | Microsoft.Media/locations/checkNameAvailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Ação | Microsoft.Media/mediaservices/accountfilters/delete | Exclui qualquer Filtro de Conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/read | Lê qualquer Filtro de Conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/write | Cria ou atualiza qualquer Filtro de Conta |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/delete | Exclui qualquer Filtro de Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/read | Lê qualquer Filtro de Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/write | Cria ou atualiza qualquer Filtro de Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/delete | Excluir qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obtém a Chave de Criptografia do Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista URLs da SAS de Contêiner de Ativos |
> | Ação | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lista os Localizadores de Streaming para o Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/read | Lê qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/write | Cria ou atualiza qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Exclui qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtém Propriedades da Política com Segredos |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/read | Lê qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/write | Cria ou atualiza qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/delete | Excluir qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/delete | Exclui qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/read | Lê qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/liveEventOperations/read | Lê qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/delete | Exclui qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Exclui qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Lê qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Cria ou atualiza qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/read | Lê qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/reset/action | Redefine qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/start/action | Inicia qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/stop/action | Interrompe qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/write | Cria ou atualiza qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveOutputOperations/read | Lê qualquer Operação de Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/read | Ler qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Lê qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/delete | Exclui qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/read | Lê qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensiona qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Inicia qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Interrompe qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/write | Cria ou atualiza qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/delete | Exclui qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista Chaves de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista Caminhos |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/read | Lê qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/write | Cria ou atualiza qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/delete | Exclui qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/read | Lê qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/write | Cria ou atualiza qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as Chaves de Armazenamento para uma conta de Armazenamento do Microsoft Azure anexada |
> | Ação | Microsoft.Media/mediaservices/transforms/delete | Exclui qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/delete | Exclui qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/read | Lê qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/write | Cria ou atualiza qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/read | Lê qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/write | Cria ou atualiza qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Media/register/action | Registra a assinatura do provedor de recursos dos Serviços de Mídia e habilitar a criação de contas dos Serviços de Mídia |
> | Ação | Microsoft.Media/unregister/action | Cancela o registro da assinatura para o provedor de recursos do Serviços de Mídia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. Migration/assessmentprojects/assessmentoptions/Read | Obtém as opções de avaliação disponíveis no local fornecido |
> | Ação | Microsoft.Migrate/assessmentprojects/assessments/read | Lista avaliações dentro de um projeto |
> | Ação | Microsoft.Migrate/assessmentprojects/delete | Excluir o projeto de avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Obter as propriedades de um computador avaliado |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Exclui uma avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Baixa uma URL do relatório de avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Cria uma avaliação ou atualiza uma existente |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/delete | Exclui um grupo |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Atualizar grupo adicionando ou removendo computadores |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Exclui o coletor HyperV |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Obter as propriedades do coletor HyperV |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Cria um novo coletor do HyperV ou atualiza um coletor HyperV existente |
> | Ação | Microsoft.Migrate/assessmentprojects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/assessmentprojects/read | Obter as propriedades do projeto de avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Exclui o coletor do VMware |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Obter as propriedades do coletor do VMware |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Cria um novo coletor VMware ou atualiza um coletor VMware existente |
> | Ação | Microsoft.Migrate/assessmentprojects/write | Cria um novo projeto de avaliação ou atualiza um projeto de avaliação existente |
> | Ação | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação disponíveis no local fornecido |
> | Ação | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a assinatura fornecida no local determinado |
> | Ação | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Obtém as propriedades de uma instância de banco de dados |
> | Ação | Microsoft.Migrate/migrateprojects/Databases/read | Obtém as propriedades de um banco de dados |
> | Ação | Microsoft.Migrate/migrateprojects/delete | Excluir um projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Exclui um evento de migração |
> | Ação | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Obtém as propriedades de um evento de migração. |
> | Ação | Microsoft.Migrate/migrateprojects/read | Obtém as propriedades do projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/RefreshSummary/Action | Atualiza o resumo do projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/registerTool/action | Registra a ferramenta para um projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Limpar os dados de solução de projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/Delete | Exclui uma solução de migração de projeto |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obtém a configuração da solução do projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/read | Obtém as propriedades da solução migrar projeto |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/write | Cria uma nova solução de migração de projeto ou atualiza uma solução de projeto de migração existente |
> | Ação | Microsoft.Migrate/migrateprojects/write | Cria um novo projeto de migração ou atualiza um projeto de migração existente |
> | Ação | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Migrate |
> | Ação | Microsoft.Migrate/projects/assessments/read | Lista avaliações dentro de um projeto |
> | Ação | Microsoft.Migrate/projects/delete | Exclui o projeto |
> | Ação | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de um computador avaliado |
> | Ação | Microsoft.Migrate/projects/groups/assessments/delete | Exclui uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Baixa uma URL do relatório de avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/write | Cria uma avaliação ou atualiza uma existente |
> | Ação | Microsoft.Migrate/projects/groups/delete | Exclui um grupo |
> | Ação | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/projects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/projects/keys/action | Obtém as chaves compartilhadas para o projeto |
> | Ação | Microsoft.Migrate/projects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Ação | Microsoft.Migrate/projects/write | Cria um projeto ou atualiza um existente |
> | Ação | Microsoft.Migrate/register/action | Registra a Assinatura com o provedor de recursos Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MixedReality/register/action | Registra uma assinatura para o provedor de recursos de realidade misturada. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Excluir âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as métricas disponíveis para Microsoft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Verificar se o caminho do arquivo está disponível |
> | Ação | Microsoft. NetApp/Locations/checknameavailability/Action | Verifique se o nome do recurso está disponível |
> | Ação | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado da operação. |
> | Ação | Microsoft.NetApp/locations/read | Lê um recurso de verificação de disponibilidade. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Lê um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lê um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Exclui um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lê um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Grava um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Grava um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/write | Grava um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/delete | Exclui um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/write | Grava um recurso de conta. |
> | Ação | Microsoft.NetApp/Operations/read | Lê recursos de operação. |
> | Ação | Microsoft. NetApp/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. NetApp |
> | Ação | Microsoft. NetApp/cancelar registro/ação | Cancela o registro de assinatura com o provedor de recursos Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obtém os Cabeçalhos de Solicitação disponíveis do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obtém os Cabeçalhos de Resposta disponíveis do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obtém as Variáveis de Servidor disponíveis do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Política predefinida SSL do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções SSL disponíveis do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obter conjuntos de regras WAF disponíveis para o Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationGateways/backendhealth/action | Obter uma integridade de back-end do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/delete | Excluir um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Obtém uma integridade de back-end do gateway de aplicativo sob demanda para determinada configuração de http e pool de back-end |
> | Ação | Microsoft.Network/applicationGateways/read | Obter um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/stop/action | Interrompe um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/write | Criar ou atualizar um gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Exclui uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obter uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Cria uma política de WAF do gateway de aplicativo ou atualiza uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/delete | Excluir um grupo de segurança de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Adicionar uma configuração IP aos grupos de segurança de aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Adicionar uma regra de segurança aos grupos de segurança de aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lista as Configurações de IP no ApplicationSecurityGroup |
> | Ação | Microsoft.Network/applicationSecurityGroups/read | Obter uma ID do grupo de segurança de aplicativo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/write | Criar um grupo de segurança de aplicativo ou atualizar um grupo de segurança de aplicativo existente. |
> | Ação | Microsoft.Network/azureFirewallFqdnTags/read | Obtém as marcas FQDN do Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/delete | Excluir o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/write | Cria ou atualiza um Firewall do Azure |
> | Ação | Microsoft.Network/bastionHosts/delete | Exclui um host bastião |
> | Ação | Microsoft.Network/bastionHosts/read | Obtém um host bastião |
> | Ação | Microsoft.Network/bastionHosts/write | Criar ou atualizar um host bastião |
> | Ação | Microsoft.Network/bgpServiceCommunities/read | Obter comunidades do serviço BGP |
> | Ação | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se um nome de porta frontal está disponível |
> | Ação | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verificar a disponibilidade de um nome DNS relativo do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/connections/delete | Excluir VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/read | Obter o VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/revoke/action | Marca um status de Conexão do ExpressRoute como Revogado |
> | Ação | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/read | Obter SharedKey da VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/sharedKey/write | Criar ou atualizar uma VirtualNetworkGatewayConnection SharedKey existente |
> | Ação | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obter configuração do dispositivo VPN do VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/write | Criar ou atualizar uma VirtualNetworkGatewayConnection existente |
> | Ação | Microsoft.Network/ddosCustomPolicies/delete | Exclui uma política personalizada de DDoS |
> | Ação | Microsoft.Network/ddosCustomPolicies/read | Obtém uma definição de política personalizada de DDoS |
> | Ação | Microsoft.Network/ddosCustomPolicies/write | Cria uma política personalizada de DDoS ou atualiza uma política personalizada de DDoS existente |
> | Ação | Microsoft.Network/ddosProtectionPlans/delete | Excluir um Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/join/action | Une um plano de proteção contra DDoS. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/ddosProtectionPlans/read | Obter um Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/write | Criar um Plano de Proteção contra DDoS ou atualizar um Plano de Proteção contra DDoS  |
> | Ação | Microsoft.Network/dnsoperationresults/read | Obter os resultados de uma operação de DNS |
> | Ação | Microsoft.Network/dnsoperationstatuses/read | Obter o status de uma operação de DNS  |
> | Ação | Microsoft.Network/dnszones/A/delete | Remover o conjunto de registros de determinado nome e tipo ‘A’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/A/read | Obter o conjunto de registros do tipo 'A', no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registros do tipo 'A' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/AAAA/delete | Remover o conjunto de registros de determinado nome e digite ‘AAAA’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/AAAA/read | Obter o conjunto de registros do tipo 'AAAA' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo 'AAAA' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/all/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/CAA/delete | Remover o conjunto de registros de um determinado nome e digitar "CAA" de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CAA/read | Obter o conjunto de registros do tipo ‘CAA’ no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Ação | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registros do tipo ‘CAA’ em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/CNAME/delete | Remover o conjunto de registros de determinado nome e tipo ‘CNAME’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CNAME/read | Obter o conjunto de registros do tipo 'CNAME' no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Ação | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo 'CNAME' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/delete | Excluir a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Ação | Microsoft.Network/dnszones/MX/delete | Remover o conjunto de registros de determinado nome e tipo ‘MX’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/MX/read | Obter o conjunto de registros do tipo 'MX' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registros do tipo 'MX' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/NS/delete | Excluir o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/read | Obter o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/write | Criar ou atualizar o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/PTR/delete | Remover o conjunto de registros de determinado nome e tipo ‘PTR’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/PTR/read | Obter o conjunto de registros do tipo 'PTR' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registros do tipo 'PTR' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/read | Obter a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera conjuntos de registros contidos na zona. |
> | Ação | Microsoft.Network/dnszones/recordsets/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/SOA/read | Obter o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SOA/write | Criar ou atualizar o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SRV/delete | Remover o conjunto de registros de determinado nome e tipo ‘SRV’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/SRV/read | Obter o conjunto de registros do tipo 'SRV' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar o conjunto de registros do tipo SRV |
> | Ação | Microsoft.Network/dnszones/TXT/delete | Remover o conjunto de registros de determinado nome e tipo ‘TXT’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/TXT/read | Obter o conjunto de registros do tipo 'TXT' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registros do tipo 'TXT' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS em um grupo de recursos.  Usado para atualizar as marcações em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros na zona. |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/delete | Excluir uma autorização ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/read | Obter uma autorização ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/write | Criar ou atualizar uma autorização ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/delete | Excluir um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/join/action | Une um circuito de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/arpTables/leitura | Obter ArpTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Excluir uma conexão de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obter uma conexão de circuito de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Criar ou atualizar um Recurso de Conexão de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/delete | Excluir um emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtém a Conexão de Circuito Par do ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/read | Obter um emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTables/leitura | Obter RouteTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTablesSummary/leitura | Obter um resumo de RouteTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obter status de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/write | Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/read | Obter um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/stats/read | Obter status de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/write | Criar ou atualizar um ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/join/action | Une uma conexão cruzada de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/arpTables/leitura | Obter uma Tabela ARP de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Excluir um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obter um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTables/leitura | Obter uma Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTableSummary/leitura | Obter uma Resumo de Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/write | Criar um emparelhamento de conexão cruzada de rota expressa ou atualizar um emparelhamento de conexão cruzada de rota expressa existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/read | Excluir Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Adicionar uma Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Adicionar uma Conexão de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma Conexão de Rota Expressa ou Atualiza uma Conexão de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/join/action | Une um gateway de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/expressRouteGateways/read | Obtém o Gateway de Rota Expressa |
> | Ação | Microsoft.Network/expressRoutePorts/delete | Exclui ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/join/action | Une portas de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Ação | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/write | Cria ou atualiza o ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePortsLocations/read | Obter Localizações de Porta do ExpressRoute |
> | Ação | Microsoft.Network/expressRouteServiceProviders/read | Obter os provedores de serviços do ExpressRoute |
> | Ação | Microsoft. Network/firewallPolicies/Delete | Exclui uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/junção/ação | Une uma política de firewall. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/firewallPolicies/Read | Obter uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Delete | Excluir um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Read | Obter um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Write | Cria um grupo de regras de política de firewall ou atualiza um grupo de regras de política de firewall existente |
> | Ação | Microsoft. Network/firewallPolicies/Write | Cria uma política de firewall ou atualiza uma política de firewall existente |
> | Ação | Microsoft.Network/frontDoors/backendPools/delete | Exclui um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/read | Obtém um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/read | Cria ou atualiza um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/delete | Exclui uma Porta Frontal |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/delete | Exclui um ponto de extremidade de frontend |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Desabilita o HTTPS em um ponto de extremidade de frontend |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Ativa o HTTPS em um ponto de extremidade de frontend |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtém um ponto de extremidade de frontend |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/write | Cria ou atualiza um ponto de extremidade de frontend |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/delete | Configurações da investigação de integridade personalizada |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtém as configurações do probe de integridade |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/write | Cria ou atualiza as configurações de investigação de integridade |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Cria ou atualiza as configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtém configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/write | Cria ou atualiza as configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/purge/action | Eliminar conteúdo em cache de uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/read | Obtém uma Porta Frontal |
> | Ação | Microsoft.Network/frontDoors/routingRules/delete | Exclui uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/read | Obtém uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/write | Obtém uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/validateCustomDomain/action | Valida um endpoint frontend para uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/write | Cria ou atualiza uma Porta Frontal |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Obter conjuntos de regras gerenciadas pelo firewall do aplicativo Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Exclui uma política de firewall de aplicativo da Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obtém uma política de firewall de aplicativo da Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Cria ou atualiza uma política de firewall de aplicativo da Web |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/read | Obter uma definição de pool de endereços de back-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/delete | Excluir um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Une uma configuração de IP de front-end do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obter uma definição de configuração de IP de front-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/read | Obter uma definição de pool NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/delete | Excluir uma regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/read | Obter uma definição de regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/write | Criar uma regra NAT de entrada do balanceador de carga ou atualizar uma regra NAT de entrada do balanceador de carga existente |
> | Ação | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obter uma definição regra de balanceamento de carga do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/networkInterfaces/read | Obter as referências a todos os adaptadores de rede em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/outboundRules/read | Obtém uma definição de regra NAT de saída do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/probes/join/action | Permite usar investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento da VM pode referenciar a investigação. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/probes/read | Obter uma investigação do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/virtualMachines/read | Obter as referências a todas as máquinas virtuais em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/write | Criar um balanceador de carga ou atualizar um balanceador de carga existente |
> | Ação | Microsoft.Network/localnetworkgateways/delete | Excluir LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/read | Obter o LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/write | Criar ou atualizar uma LocalNetworkGateway existente |
> | Ação | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Obter serviços de vínculo privado aprovados automaticamente |
> | Ação | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Ação | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Obtém recursos de ponto de extremidade privados disponíveis |
> | Ação | Microsoft. Network/Locations/availableServiceAliases/Read | Obtém os aliases de serviço disponíveis |
> | Ação | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um locatário do Bare Metal |
> | Ação | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte à Rede Acelerada |
> | Ação | Microsoft.Network/locations/checkDnsNameAvailability/read | Verificar se o rótulo de DNS está disponível no local especificado |
> | Ação | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Verifica a visibilidade do serviço de vínculo privado |
> | Ação | Microsoft.Network/locations/operationResults/read | Obter o resultado de uma operação POST ou DELETE assíncrona |
> | Ação | Microsoft.Network/locations/operations/read | Obter o recurso de operação que representa o status de uma operação assíncrona |
> | Ação | Microsoft.Network/locations/serviceTags/read | Obter marcas de serviço |
> | Ação | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém os tamanhos de máquinas virtuais compatíveis |
> | Ação | Microsoft.Network/locations/usages/read | Obter a métrica de uso dos recursos |
> | Ação | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obter uma lista de serviços de ponto de extremidade de Rede Virtual disponíveis |
> | Ação | Microsoft.Network/networkIntentPolicies/delete | Exclui uma política de intenção de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição de intenção de diretiva de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/write | Cria uma interface de rede ou atualiza uma política de intenção de rede existente |
> | Ação | Microsoft.Network/networkInterfaces/delete | Excluir um adaptador de rede |
> | Ação | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de configurados no adaptador de rede da máquina virtual |
> | Ação | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter tabela de rota configurada no adaptador de rede da máquina virtual |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Une uma configuração IP do adaptador de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obter uma definição de configuração de IP do adaptador de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/join/action | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkInterfaces/loadBalancers/read | Obter todos os balanceadores de carga dos quais o adaptador de rede faz parte |
> | Ação | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Exclui uma configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma configuração de toque do adaptador de rede ou atualiza uma configuração de toque do adaptador de rede existente. |
> | Ação | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Ação | Microsoft.Network/networkProfiles/delete | Excluir um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/read | Obtém um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/removeContainers/action | Remove os contêineres |
> | Ação | Microsoft.Network/networkProfiles/setContainers/action | Definir contêineres |
> | Ação | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as Interfaces de rede de contêiner |
> | Ação | Microsoft.Network/networkProfiles/write | Criar ou atualizar um perfil de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obter uma definição padrão da regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/delete | Excluir um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/delete | Excluir uma regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/read | Obter uma definição da regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/write | Criar uma regra de segurança ou atualizar uma regra de segurança existente |
> | Ação | Microsoft.Network/networkSecurityGroups/write | Criar um grupo de segurança de rede ou atualizar um grupo de segurança de rede existente |
> | Ação | Microsoft.Network/networkWatchers/availableProvidersList/action | Retornar todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada. |
> | Ação | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retornar a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure. |
> | Ação | Microsoft.Network/networkWatchers/configureFlowLog/action | Configurar o registro de fluxo em log para um recurso de destino. |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/delete | Excluir um Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Consultar monitoramento de conectividade entre os pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter detalhes do Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Iniciar monitoramento de conectividade entre pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar monitoramento de conectividade entre pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/write | Criar um Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectivityCheck/action | Verificar a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário. |
> | Ação | Microsoft.Network/networkWatchers/delete | Excluir um observador de rede |
> | Ação | Microsoft.Network/networkWatchers/ipFlowVerify/action | Retornar se o pacote é permitido ou negado em relação a um destino específico. |
> | Ação | Microsoft.Network/networkWatchers/lenses/delete | Excluir uma Lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/query/action | Consultar monitoramento de tráfego em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/read | Obter os detalhes de Lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar monitoramento de tráfego em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar o monitoramento do tráfego de rede em um terminal especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/write | Criar uma lente |
> | Ação | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico de configuração de rede. |
> | Ação | Microsoft.Network/networkWatchers/nextHop/action | Para um destino especificado e endereço IP de destino especificados, retornar o tipo do próximo salto e próximo endereço IP esperado. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/delete | Excluir uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obter informações sobre propriedades e status de um recurso de captura de pacote. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Interromper a sessão de captura de pacote em execução. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/write | Criar uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/delete | Exclui um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/read | Obtenha detalhes de PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Iniciar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Consultar em lote o monitoramento de conectividade entre os pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obter o status do registro do fluxo em log em um recurso. |
> | Ação | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obter o resultado da solução de problemas da operação de solução de problemas executada ou atualmente em execução. |
> | Ação | Microsoft.Network/networkWatchers/read | Obter a definição de observador de rede |
> | Ação | Microsoft.Network/networkWatchers/securityGroupView/action | Exibir as regras de grupo de segurança de rede configuradas e em vigor aplicadas a uma máquina virtual. |
> | Ação | Microsoft.Network/networkWatchers/topology/action | Obter uma exibição dos recursos em nível de rede e de suas relações em um grupo de recursos. |
> | Ação | Microsoft.Network/networkWatchers/troubleshoot/action | Iniciar a solução de problemas em um recurso de rede no Azure. |
> | Ação | Microsoft.Network/networkWatchers/write | Criar um observador de rede ou atualizar um observador de rede existente |
> | Ação | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Network/p2sVpnGateways/delete | Exclui um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar o perfil de Vpn para o P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obtém uma Conexão P2S VPN íntegra para o P2SVpnGateway |
> | Ação | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Obter uma integridade de conexão VPN P2S detalhada para P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Ação | Microsoft.Network/privateDnsOperationResults/read | Obtém os resultados de uma operação de DNS privado |
> | Ação | Microsoft.Network/privateDnsOperationStatuses/read | Obtém o status de uma operação de DNS privado |
> | Ação | Microsoft.Network/privateDnsZones/A/delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/A/read | Obter o conjunto de registros do tipo ' A ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/A/write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/read | Obtenha o conjunto de registros do tipo ' AAAA ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/ALL/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/read | Obtenha o conjunto de registros do tipo ' CNAME ' dentro de uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/delete | Excluir uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/MX/delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/MX/read | Obtenha o conjunto de registros do tipo ' MX ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/MX/write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/read | Obter o conjunto de registros do tipo ' PTR ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/read | Obtenha as propriedades da zona de DNS privado, no formato JSON. Observe que esse comando não recupera as redes virtuais às quais a zona de DNS privado está vinculada ou os conjuntos de registros contidos na zona. |
> | Ação | Microsoft.Network/privateDnsZones/recordsets/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft.Network/privateDnsZones/SOA/read | Obter o conjunto de registros do tipo ' SOA ' em uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/SOA/write | Atualizar um conjunto de registros do tipo ' SOA ' em uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/delete | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/read | Obtenha o conjunto de registros do tipo ' SRV ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/write | Criar ou atualizar um conjunto de registros do tipo ' SRV ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/read | Obtenha o conjunto de registros do tipo ' TXT ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Exclua um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Obtenha o link de zona de DNS privado para as propriedades de rede virtual, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Crie ou atualize um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft.Network/privateDnsZones/write | Criar ou atualizar uma zona de DNS privado dentro de um grupo de recursos. Observe que esse comando não pode ser usado para criar ou atualizar links de rede virtual ou conjuntos de registros dentro da zona. |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Read | Obtém um ponto de extremidade privado RedirectMap |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Write | Cria um ponto de extremidade privado RedirectMap ou atualiza um ponto de extremidade privado existente RedirectMap |
> | Ação | Microsoft.Network/privateEndpoints/delete | Exclui um recurso de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateEndpoints/read | Obtém um recurso de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateEndpoints/write | Cria um novo ponto de extremidade privado ou atualiza um ponto de extremidade privado existente. |
> | Ação | Microsoft.Network/privateLinkServices/delete | Exclui um recurso do serviço de link privado. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Obtém uma definição de conexão de ponto de extremidade particular. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Cria uma nova conexão de ponto de extremidade privado ou atualiza uma conexão de ponto de extremidade privada existente. |
> | Ação | Microsoft.Network/privateLinkServices/read | Obtém um recurso do serviço de link privado. |
> | Ação | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de link privado ou atualiza um serviço de link privado existente. |
> | Ação | Microsoft.Network/publicIPAddresses/delete | Excluir um endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/write | Criar um endereço IP público ou atualizar um endereço IP público existente.  |
> | Ação | Microsoft.Network/publicIPPrefixes/delete | Exclui um prefixo IP público A |
> | Ação | Microsoft.Network/publicIPPrefixes/join/action | Une um PublicIPPrefix. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/publicIPPrefixes/read | Obtém uma definição de prefixo IP público |
> | Ação | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo IP público ou atualiza um prefixo IP público existente |
> | Ação | Microsoft.Network/register/action | Registra a assinatura |
> | Ação | Microsoft.Network/routeFilters/delete | Excluir uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/join/action | Une um filtro de rota. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/routeFilters/read | Obter uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/delete | Excluir uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/read | Obter uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/write | Criar uma regra de filtro de rota ou atualizar uma regra de filtro de rota existente |
> | Ação | Microsoft.Network/routeFilters/write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Ação | Microsoft.Network/routeTables/delete | Excluir uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/join/action | Une uma tabela de rotas. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/routeTables/read | Obter uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/routes/delete | Excluir uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/read | Obter uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/write | Criar uma rota ou atualizar uma rota existente |
> | Ação | Microsoft.Network/routeTables/write | Cria uma tabela de rotas ou atualiza uma tabela de rotas existente |
> | Ação | Microsoft.Network/securegateways/delete | Excluir um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/read | Obter um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/write | Criar ou atualizar um Gateway de Segurança |
> | Ação | Microsoft.Network/serviceEndpointPolicies/delete | Excluir uma política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/join/action | Une uma política de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Une uma sub-rede a políticas de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/read | Obter uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Excluir uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtém uma descrição da definição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Criar uma definição de política de ponto de extremidade de serviço ou atualizar uma definição de política de ponto de extremidade de serviço existente |
> | Ação | Microsoft.Network/serviceEndpointPolicies/write | Criar uma política de ponto de extremidade de serviço ou atualizar uma política de ponto de extremidade de serviço existente |
> | Ação | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obter a hierarquia de geográfica do Gerenciador de Tráfego contendo regiões que podem ser usadas com o método de roteamento de tráfego geográfico |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Excluir um Ponto de Extremidade do Azure de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o Ponto de Extremidade do Azure excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obter um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicionar um novo Ponto de Extremidade do Azure em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/delete | Excluir o perfil do Gerenciador de Tráfego. Todas as configurações associadas ao perfil do Gerenciador de Tráfego serão perdidas e o perfil não poderá ser usado para rotear o tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Excluir um ponto de extremidade externo de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego do Microsoft Azure interromperá o tráfego de roteamento para o ponto de extremidade externo excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obter um ponto de extremidade externo que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade externo. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicionar um novo ponto de extremidade externo em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obter o mapa de calor do Gerenciador de Tráfego para o perfil do Gerenciador de Tráfego fornecido, que contém contagens de consulta e dados de latência por local e IP de origem. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Excluir um ponto de extremidade aninhado de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o ponto de extremidade aninhado excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicionar um novo ponto de extremidade aninhado em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade aninhado existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/read | Obter a configuração de perfil do Gerenciador de Tráfego. Isso inclui configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e lista de pontos de extremidade roteados por esse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gerenciador de Tráfego ou modificar a configuração de um perfil do Gerenciador de Tráfego existente.<br>Isso inclui habilitar ou desabilitar um perfil e modificar as configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento do ponto de extremidade.<br>Pontos de extremidade roteados pelo perfil do Gerenciador de Tráfego podem ser adicionados, removidos, habilitados ou desabilitados. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Excluir a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obter a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/write | Criar uma nova chave do nível de assinatura a ser usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/unregister/action | Cancelar o registro da assinatura |
> | Ação | Microsoft.Network/virtualHubs/delete | Excluir um Hub Virtual |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Excluir um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/read | Obter um Hub Virtual |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Delete | Excluir um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Read | Obter um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Write | Criar ou atualizar um VirtualHubRouteTableV2 |
> | Ação | Microsoft.Network/virtualHubs/write | Criar ou atualizar um Hub Virtual |
> | Ação | microsoft.network/virtualnetworkgateways/connections/read | Obter um VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/virtualNetworkGateways/delete | Excluir um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar pacote de VpnClient para virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar pacote de VpnProfile para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obter rotas anunciadas do virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obter status do Par de BGP do virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obter rotas aprendidas do virtualnetworkgateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter por integridade de conexão de cliente Vpn para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obter a URL de um pacote de perfil de cliente VPN gerado previamente |
> | Ação | Microsoft.Network/virtualNetworkGateways/read | Obter um VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/reset/action | Reiniciar um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reiniciar a chave compartilhada Vpnclient para o cliente P2S do VirtualNetworkGateway. |
> | Ação | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Configurar parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Ação | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listar dispositivos VPN com suporte |
> | Ação | Microsoft.Network/virtualNetworkGateways/write | Criar ou atualizar um VirtualNetworkGateway |
> | Ação | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtém referências de Host Bastião em uma Rede Virtual. |
> | Ação | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Obtém referências de Host Bastião em uma Rede Virtual. |
> | Ação | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verificar se o endereço IP está disponível na rede virtual especificada |
> | Ação | Microsoft.Network/virtualNetworks/delete | Excluir uma rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/peer/action | Colocar uma rede virtual com outra rede virtual de mesmo nível |
> | Ação | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Delete | Exclui uma política de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Read | Obtém políticas de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Write | Cria uma política de ponto de extremidade de serviço contextual ou atualiza uma política de ponto de extremidade de serviço contextual existente |
> | Ação | Microsoft.Network/virtualNetworks/subnets/delete | Excluir uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Une recursos, como conta de armazenamento ou banco de dados SQL, a uma sub-rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara uma sub-rede por meio da aplicação das Políticas de Rede necessárias |
> | Ação | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/unprepareNetworkPolicies/Action | Despreparar uma sub-rede removendo as políticas de rede aplicadas |
> | Ação | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/write | Criar uma sub-rede de rede virtual ou atualizar uma sub-rede de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/usages/read | Obter os usos de IP para cada sub-rede da rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um emparelhamento de redes virtuais |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obter uma definição de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Criar um emparelhamento de rede virtual ou atualizar um emparelhamento de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/write | Criar uma rede virtual ou atualizar uma rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworkTaps/delete | Excluir Toque de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/join/action | Une um toque de rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworkTaps/read | Obter Toque de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar Toque de Rede Virtual |
> | Ação | Microsoft. Network/virtualRouters/Delete | Exclui um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/junção/ação | Une um VirtualRouter. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualRouters/Read | Obter um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Exclui um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Read | Obter um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Cria um VirtualRouterPeering ou atualiza um VirtualRouterPeering existente |
> | Ação | Microsoft. Network/virtualRouters/Write | Cria um VirtualRouter ou atualiza um VirtualRouter existente |
> | Ação | Microsoft.Network/virtualWans/delete | Excluir uma WAN Virtual |
> | Ação | Microsoft. Network/virtualwans/generateVpnProfile/Action | Gerar VirtualWanVpnServerConfiguration VpnProfile |
> | Ação | Microsoft.Network/virtualWans/read | Obter uma WAN Virtual |
> | Ação | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém os provedores de segurança de VirtualWan com suporte. |
> | Ação | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os Hubs Virtuais que fazem referência a uma WAN Virtual. |
> | Ação | Microsoft.Network/virtualwans/vpnconfiguration/action | Obter uma configuração de VPN |
> | Ação | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Obter VirtualWanVpnServerConfigurations |
> | Ação | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os Sites VPN que fazem referência a uma WAN Virtual. |
> | Ação | Microsoft.Network/virtualWans/write | Criar ou atualizar um uma WAN Virtual |
> | Ação | Microsoft.Network/vpnGateways/delete | Exclui um VpnGateway. |
> | Ação | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém a integridade da conexão para todas ou um subconjunto das conexões em um VpnGateway |
> | Ação | Microsoft.Network/vpnGateways/read | Obter um VpnGateway. |
> | Ação | microsoft.network/vpngateways/reset/action | Reinicia um VpnGateway |
> | Ação | microsoft.network/vpnGateways/vpnConnections/delete | Exclui um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/read | Obter um VpnConnection. |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Obter uma conexão de link VPN |
> | Ação | microsoft.network/vpnGateways/vpnConnections/write | Implementar um VpnConnection. |
> | Ação | Microsoft.Network/vpnGateways/write | Implementar um VpnGateway. |
> | Ação | Microsoft. Network/vpnServerConfigurations/Delete | Excluir VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Read | Obter VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Write | Criar ou atualizar VpnServerConfiguration |
> | Ação | Microsoft.Network/vpnsites/delete | Excluir um recurso de VPN site. |
> | Ação | Microsoft.Network/vpnsites/read | Obter um recurso de VPN site. |
> | Ação | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Obter um link de site VPN |
> | Ação | Microsoft.Network/vpnsites/write | Criar ou atualizar um recurso de VPN site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verificar se determinado nome de recurso do Namespace está disponível no serviço NotificationHub. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regra de autorização de namespace regenera chave primária/secundária; especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verificar se determinado nome de NotificationHub está disponível em um Namespace. |
> | Ação | Microsoft.NotificationHubs/Namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter a lista de regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Excluir regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para o Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter a lista de regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regra de autorização de Hub de notificação regenera chave primária/secundária; especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar regras de autorização do Hub de notificação e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Enviar notificação por push de teste. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Excluir recurso de Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obter todas as credenciais PNS do Hub de notificação. Isso inclui credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de descrições de recursos do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar um Hub de notificação e atualizar suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Tempo de vida útil e regras de autorização |
> | Ação | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/operationResults/read | Retorna os resultados da operação para o provedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/operations/read | Retorna uma lista de operações com suporte para o provedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/register/action | Registra a assinatura no provedor de recursos NotificationHubs e permite a criação de Namespaces e NotificationHubs |
> | Ação | Microsoft.NotificationHubs/unregister/action | Cancela o registro da assinatura no provedor de recursos NotificationHubs e permite a criação de Namespaces e NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza do cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Exclui o site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Obter o resumo de integridade do recurso Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um host Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o host do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de trabalhos Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de status de operação Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um Hyper-V como contas |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém os usos de um site HYPER-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site do Hyper-V |
> | Ação | Microsoft.OffAzure/Operations/read | Ler as operações expostas |
> | Ação | Microsoft.OffAzure/register/action | Registra a Assinatura no provedor de recursos Microsoft.OffAzure |
> | Ação | Microsoft. OffAzure/ServerSites/Jobs/ler | Obtém as propriedades de um servidor de trabalhos |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Read | Obter as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Write | Gravar as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Obtém as propriedades de um status de operação do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Read | Obtém as propriedades de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/atualização/ação | Atualiza os objetos em um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Obtém as propriedades de uma conta Executar como do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/uso/leitura | Obtém os usos de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Write | Criar ou atualizar o site do servidor |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Exclui o site de VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Obter o resumo de integridade do recurso do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de trabalhos VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de máquinas VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/start/action | Inicia computadores da VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Interrompe as máquinas de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de status de operação VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware como contas |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém os usos de um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OperationalInsights/linkTargets/read | Listar as contas existentes que não estão associadas uma assinatura do Azure. Para vincular essa assinatura do Azure a um workspace, use uma ID de cliente retornada pela operação na propriedade customer id da operação Criar workspace. |
> | Ação | microsoft.operationalinsights/operations/read | Lista todas as operações da API de Rest OperationalInsights disponíveis. |
> | Ação | microsoft.operationalinsights/register/action | Rergisters a assinatura. |
> | Ação | Microsoft.OperationalInsights/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Ação | microsoft.operationalinsights/unregister/action | Cancela o registro da assinatura. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter o esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter o esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Excluir escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir o escopo da configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/delete | Excluir fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/read | Obter fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/delete | Excluir um workspace. Se o workspace foi vinculado a um workspace existente no momento da criação, o workspace a que ele foi vinculado não será excluído. |
> | Ação | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gerar certificado de registro para o workspace. Esse certificado é usado para conectar o Microsoft System Center Operations Manager ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desabilitar um pacote de inteligência para determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Habilitar um pacote de inteligência para determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lista todos os pacotes de inteligência visíveis para um determinado workspace e também indica se o pacote está habilitado ou desabilitado para esse workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Excluir serviços vinculados em um determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços vinculados em um determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços vinculados em determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/action | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/read | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obter os nomes e os metadados para grupos de gerenciamento do System Center Operations Manager conectados ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições métricas no workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Excluir as configurações de notificação do usuário para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obter as configurações de notificação do usuário para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Definir as configurações de notificação do usuário para o workspace. |
> | Ação | microsoft.operationalinsights/workspaces/operations/read | Obtém o status de uma operação de espaço de trabalho OperationalInsights. |
> | Ação | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Ler dados da tabela AADDomainServicesAccountLogon |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Ler dados da tabela AADDomainServicesAccountManagement |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Ler dados da tabela AADDomainServicesDirectoryServiceAccess |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Ler dados da tabela AADDomainServicesLogonLogoff |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Ler dados da tabela AADDomainServicesPolicyChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Ler dados da tabela AADDomainServicesPrivilegeUse |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Ler dados da tabela AADDomainServicesSystemSecurity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Lê dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Ler dados da tabela AddonAzureBackupAlerts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Ler dados da tabela AddonAzureBackupJobs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Ler dados da tabela AddonAzureBackupPolicy |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Ler dados da tabela AddonAzureBackupProtectedInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Ler dados da tabela AddonAzureBackupStorage |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Ler dados da tabela ADFActivityRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Ler dados da tabela ADFPipelineRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Ler dados da tabela ADFTriggerRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Lê dados da tabela ADReplicationResult |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Lê dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Alert/read | Lê dados da tabela de Alertas |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Lê dados da tabela AlertHistory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Ler dados da tabela AmlComputeClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Ler dados da tabela AmlComputeClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Ler dados da tabela AmlComputeJobEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Ler dados da tabela ApiManagementGatewayLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Ler dados da tabela AppCenterError |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Lê dados da tabela ApplicationInsights |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Ler dados da tabela AppPlatformLogsforSpring |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Ler dados da tabela AppServiceEnvironmentPlatformLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Ler dados da tabela AWSCloudTrail |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Lê dados da tabela AzureActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Ler dados da tabela AzureAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Lê dados da tabela AzureMetrics |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Ler dados da tabela BaiClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Ler dados da tabela BaiClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Ler dados da tabela BaiJobEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Ler dados da tabela BlockchainApplicationLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Ler dados da tabela BlockchainProxyLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Lê dados da tabela BoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Lê dados da tabela CommonSecurityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Lê dados da tabela ComputerGroup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Lê dados da tabela ConfigurationChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Lê dados da tabela ConfigurationData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Lê dados da tabela ContainerImageInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Lê dados da tabela ContainerInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Lê dados da tabela ContainerLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Ler dados da tabela ContainerNodeInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Ler dados da tabela ContainerRegistryLoginEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Ler dados da tabela ContainerRegistryRepositoryEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Lê dados da tabela ContainerServiceLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Ler dados da tabela CoreAzureBackup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Ler dados da tabela DatabricksAccounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Ler dados da tabela DatabricksClusters |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Ler dados da tabela DatabricksDBFS |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Ler dados da tabela DatabricksInstancePools |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Ler dados da tabela DatabricksJobs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Ler dados da tabela DatabricksNotebook |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Ler dados da tabela DatabricksSecrets |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Ler dados da tabela DatabricksSQLPermissions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Ler dados da tabela DatabricksSSH |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Ler dados da tabela DatabricksTables |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Ler dados da tabela DatabricksWorkspace |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Lê dados da tabela DeviceAppCrash |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Lê dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Lê dados da tabela DeviceCalendar |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Lê dados da tabela DeviceCleanup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Lê dados da tabela DeviceConnectSession |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Lê dados da tabela DeviceEtw |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Lê dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Lê dados da tabela DeviceHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Lê dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Lê dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Lê dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Lê dados da tabela DeviceSleepState |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Lê dados da tabela DHAppFailure |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Lê dados da tabela DHAppReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Lê dados da tabela DHDriverReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Lê dados da tabela DHLogonFailures |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Lê dados da tabela DHLogonMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Lê dados da tabela DHOSCrashData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Lê dados da tabela DHOSReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Lê dados da tabela DHWipAppLearning |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Lê dados da tabela DnsEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Lê dados da tabela DnsInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Lê dados da tabela ETWEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Event/read | Lê dados da tabela Event |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Lê dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler os dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Ler dados da tabela FailedIngestion |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Lê dados da tabela Heartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Ler dados da tabela HuntingBookmark |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Lê dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Lê dados da tabela InboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Ler dados da tabela InsightsMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Lê dados da tabela IntuneAuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Lê dados da tabela IntuneOperationalLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Ler dados da tabela KubeHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Lê dados da tabela KubeNodeInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Lê dados da tabela KubePodInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Lê dados da tabela LinuxAuditLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Lê dados da tabela MAApplication |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Lê dados da tabela MAApplicationHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Lê dados da tabela MAApplicationInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Lê dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Lê dados da tabela MADeploymentPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Lê dados da tabela MADevice |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Ler dados da tabela MADeviceNotEnrolled |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Ler dados da tabela MADeviceNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Lê dados da tabela MADeviceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Lê dados da tabela MADriverReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Lê dados da tabela MAOfficeAddin |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Ler dados da tabela MAOfficeAddinEntityHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Lê dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Ler dados da tabela MAOfficeAddinHealthEventNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Lê dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Lê dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Lê dados da tabela MAOfficeApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Ler dados da tabela MAOfficeAppCrashesNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Lê dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Lê dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Ler dados da tabela MAOfficeAppInstanceHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Lê dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Ler dados da tabela MAOfficeAppSessionsNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Lê dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Lê dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Lê dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Ler dados da tabela MAOfficeDeploymentStatusNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Ler dados da tabela MAOfficeMacroErrorNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Ler dados da tabela MAOfficeMacroGlobalHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Lê dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Lê dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Lê dados da tabela MAOfficeSuite |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Lê dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Lê dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Lê dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Lê dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Lê dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Ler dados da tabela MAWindowsDeploymentStatusNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Ler dados da tabela McasShadowItReporting |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Ler dados da tabela MicrosoftDataShareShareLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Ler dados da tabela MicrosoftHealthcareApisAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Ler dados da tabela MicrosoftInsightsAzureActivityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | Ler dados da tabela MicrosoftWebApplicationLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | Ler dados da tabela MicrosoftWebFunctionExecutionLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Ler dados da tabela MicrosoftWebStdOutStdErrLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Ler dados da tabela MicrosoftWebW3CLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Lê dados da tabela NetworkMonitoring |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Lê dados da tabela OfficeActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Operation/read | Lê dados da tabela Operation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Lê dados da tabela OutboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Perf/read | Lê dados da tabela Perf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Lê dados da tabela ProtectionStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Lê dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Lê dados da tabela ReservedCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Lê dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Lê dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Lê dados da tabela SecurityAlert |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Lê dados da tabela SecurityBaseline |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Lê dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Lê dados da tabela SecurityDetection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Lê dados da tabela SecurityEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Ler dados da tabela SecurityIoTRawEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Ler dados da tabela SecurityRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Lê dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Lê dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Lê dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Lê dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Ler os dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Ler os dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Lê dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Lê dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Lê dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Ler dados da tabela SqlThreatProtectionLoginAudits |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Ler dados da tabela SqlVulnerabilityAssessmentResult |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Ler dados da tabela SucceededIngestion |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Lê dados da tabela Syslog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Lê dados da tabela SysmonEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Lê dados de qualquer log personalizado |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Ler dados da tabela ThreatIntelligenceIndicator |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Lê dados da tabela UAApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Lê dados da tabela UAComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Lê dados da tabela UAComputerRank |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Lê dados da tabela UADriver |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Lê dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Lê dados da tabela UAFeedback |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Lê dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Lê dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Lê dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Lê dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Lê dados da tabela UASysReqIssue |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Lê dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Update/read | Lê dados da tabela Update |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Lê dados da tabela UpdateRunProgress |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Lê dados da tabela UpdateSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Usage/read | Lê dados da tabela Usage |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Lê dados da tabela VMBoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Lê dados da tabela VMConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Lê dados da tabela W3CIISLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Lê dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Lê dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Lê dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Lê dados da tabela WDAVStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Lê dados da tabela WDAVThreat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Lê dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Lê dados da tabela WindowsFirewall |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Lê dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WireData/read | Lê dados da tabela WireData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Dados de leitura da tabela WorkloadMonitoringPerf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Lê dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Lê dados da tabela WUDOStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/read | Obter um workspace existente |
> | Ação | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave compartilhada do workspace especificado |
> | Ação | microsoft.operationalinsights/workspaces/rules/read | Obtenha todas as regras de alerta. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Excluir uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obter uma consulta de pesquisa salva |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/results/read | Obter resultados de pesquisas salvos. Preteridos |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Excluir ações de pesquisa programadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obtenha ações de pesquisa programadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Crie ou atualize ações de pesquisa agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Excluir pesquisas agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obtenha pesquisas agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Crie ou atualize pesquisas agendadas. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/write | Criar uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/schema/read | Obter o esquema de pesquisa do workspace.  O esquema de pesquisa inclui os campos expostos e seus tipos. |
> | Ação | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Ação | microsoft.operationalinsights/workspaces/search/read | Obtenha os resultados da pesquisa. Preterido. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Excluir uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelo Insights Operacionais. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obter uma configuração de armazenamento. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Criar uma nova configuração de armazenamento. Essas configurações são usadas para extrair dados de um local em uma conta de armazenamento existente. |
> | Ação | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter o log de falha de tradução de atualização da pesquisa para o workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/usages/read | Obter dados de uso de um workspace e a quantidade de dados lidos pelo workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/write | Criar um novo workspace ou links para um workspace existente fornecendo a ID do cliente do workspace existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OperationsManagement/managementAssociations/delete | Excluir a Associação de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/read | Obter Associação de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/write | Crie uma nova Associação de Gerenciamento |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/delete | Exclui a Configuração de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/read | Obter Configuração de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova Configuração de Gerenciamento |
> | Ação | Microsoft.OperationsManagement/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Ação | Microsoft.OperationsManagement/solutions/delete | Excluir a solução do OMS existente |
> | Ação | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Ação | Microsoft.OperationsManagement/solutions/write | Criar nova solução do OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verifique o status de conformidade de um determinado componente em relação a políticas de dados. |
> | Ação | Microsoft.PolicyInsights/operations/read | Obtém operações com suporte no namespace Microsoft. PolicyInsights |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registre os eventos de política do componente de recurso. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/action | Consulta informações sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/read | Consulta informações sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/action | Consulta informações sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/read | Consulta informações sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/action | Consulta informações de resumo sobre os últimos estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/read | Consulta informações de resumo sobre os últimos estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Dispara uma nova avaliação de conformidade para o escopo selecionado. |
> | Ação | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Consultar informações sobre os recursos exigidos por políticas de DeployIfNotExists. |
> | Ação | Microsoft.PolicyInsights/register/action | Registra o provedor de recursos de informações da política da Microsoft e habilita ações nele. |
> | Ação | Microsoft.PolicyInsights/remediations/cancel/action | Cancelar as correções de política da Microsoft em andamento. |
> | Ação | Microsoft.PolicyInsights/remediations/delete | Excluir correções da política. |
> | Ação | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implantações exigidas por uma correção de política. |
> | Ação | Microsoft.PolicyInsights/remediations/read | Obter correções de política. |
> | Ação | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar as correções da política da Microsoft. |
> | Ação | Microsoft.PolicyInsights/unregister/action | Cancela o registro do provedor de recursos de informações da política da Microsoft. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Portal/consoles/delete | Remove a instância de Cloud Shell. |
> | Ação | Microsoft.Portal/consoles/read | Lê a instância de Cloud Shell. |
> | Ação | Microsoft.Portal/consoles/write | Criar ou atualizar uma instância de Cloud Shell. |
> | Ação | Microsoft.Portal/dashboards/delete | Remover o painel da assinatura. |
> | Ação | Microsoft.Portal/dashboards/read | Ler os painéis da assinatura. |
> | Ação | Microsoft.Portal/dashboards/write | Adicionar ou modificar o painel para uma assinatura. |
> | Ação | Microsoft.Portal/register/action | Registra-se no Portal |
> | Ação | Microsoft.Portal/usersettings/delete | Remove as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft.Portal/usersettings/read | Lê as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft.Portal/usersettings/write | Criar ou atualizar Cloud Shell configuração de usuário. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.PowerBIDedicated/capacities/delete | Excluir a capacidade dedicada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/capacities/read | Recuperar as informações da Capacidade Dedicada especificada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a Capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/skus/read | Recuperar informações de SKU disponíveis para a Capacidade |
> | Ação | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a Capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/write | Criar ou atualizar a capacidade dedicada especificada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Verificar se o nome da capacidade dedicada do Power BI é válido e não está em uso. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Ação | Microsoft.PowerBIDedicated/operations/read | Recuperar as informações das operações |
> | Ação | Microsoft.PowerBIDedicated/register/action | Registra o provedor de recursos dedicado do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Ação | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Criar um item protegido de backup |
> | Ação | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validar recursos |
> | Ação | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Verifica se a Disponibilidade do Nome do Recurso é uma API para verificar se o nome do recurso está disponível |
> | Ação | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Ação | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Ação | Microsoft.RecoveryServices/register/action | Registrar assinatura de um determinado provedor de recursos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/write | Atualizar configuração para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Excluir uma Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/operationsStatus/Read | Retornar o status da operação |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Exclui o Contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Consultar cargas de trabalho em um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationsStatus/Read | Obtém o status da operação executada no contêiner de proteção. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executar um backup para um item protegido. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Excluir item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Criar um contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar o trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupJobs/operationsStatus/Read | Retorna o status da operação de trabalho. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Excluir uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Retornar a lista de todos os itens que podem ser protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Retornar a informação de PIN de segurança para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Atualizar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/delete | A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obter a configuração de notificação do cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configurar notificações por email para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação Cancelar o registro de contêiner pode ser usada para cancelar o registro de um contêiner. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificar consistência da malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Excluir malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implantar imagem do servidor de processo |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar do Fabric para o AAD |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas malhas de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler quaisquer redes lógicas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Excluir quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Descobrir item que pode ser protegido |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos contêineres de proteção de recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover o contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Exclui os Itens de Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migra o Item |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Lê os Pontos de Recuperação de Migração |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens de migração de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Lê os Itens de Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testa a Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testa a Limpeza de Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Cria ou atualiza os Itens de Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Adicionar discos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Excluir quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens protegidos por recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planejado |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remover discos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Enviar Comentário |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler qualquer tamanhos da computação de destino |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Excluir quaisquer mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de contêineres de proteção de recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisqur mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover o mapeamento de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar os mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Alterar contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Excluir quaisquer provedores dos Serviços de Recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos provedores de serviços de recuperação de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover o provedor dos Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar Provedores de Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Excluir mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de classificação de armazenamento de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar os mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ler quaisquer vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/operationresults/Read | Acompanhar os resultados de uma operação assíncrona no recurso vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Cria ou atualiza quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar trabalho |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationJobs/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos trabalhos de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar o trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar o trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Lê os Itens de Migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Excluir políticas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationPolicies/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas políticas de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar as políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler quaisqur mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Excluir planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos planos de recuperação de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Lê qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler usos de replicação do cofre |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultHealth/operationresults/Read | Acompanhar os resultados de uma operação assíncrona na integridade da replicação do cofre de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler qualquer integridade de replicação do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar integridade de cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Lê qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Criar ou atualizar qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/usages/read | Ler usos de cofre |
> | Ação | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Relay/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.Relay/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API foi preterida. Use CheckNameAvailability. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.Relay/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar o HybridConnection. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para excluir regras de autorização HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de conexão para HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista das Regras d Autorização do HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Criar regras de autorização HybridConnection e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para excluir o recurso HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou atualizar propriedades HybridConnection. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/logDefinitions/Read | Obter lista de descrições do recurso de log do namespace |
> | Ação | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.Relay/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar o WcfRelay. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para excluir regras de autorização WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de conexão para WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de Regras de Autorização do WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Criar regras de autorização WcfRelay e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para excluir o recurso WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou atualizar propriedades WcfRelay. |
> | Ação | Microsoft.Relay/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.Relay/operations/read | Obter Operações |
> | Ação | Microsoft.Relay/register/action | Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay |
> | Ação | Microsoft.Relay/unregister/action | Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obter o status de disponibilidade do recurso especificado |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Ação | Microsoft.ResourceHealth/events/read | Obtém Eventos de Integridade do Serviço para uma determinada assinatura |
> | Ação | Microsoft.Resourcehealth/healthevent/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Activated/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/InProgress/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Pending/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Resolved/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Updated/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.ResourceHealth/impactedResources/read | Obtém os Recursos Afetados para uma determinada assinatura |
> | Ação | Microsoft.ResourceHealth/metadata/read | Obtém metadados |
> | Ação | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/register/action | Registrar a assinatura do Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/unregister/action | Cancela o registro da assinatura para o Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. Resources/calculateTemplateHash/Action | Calcule o hash do modelo fornecido. |
> | Ação | Microsoft.Resources/checkPolicyCompliance/action | Verifica o status de conformidade de um determinado recurso em relação às políticas de recurso. |
> | Ação | Microsoft.Resources/checkResourceName/action | Verificar a validade do nome do recurso. |
> | Ação | Microsoft.Resources/deployments/cancel/action | Cancelar uma implantação. |
> | Ação | Microsoft.Resources/deployments/delete | Excluir uma implantação. |
> | Ação | Microsoft. Resources/Implantations/exportatemplate/ação | Exportar modelo para uma implantação |
> | Ação | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Ação | Microsoft. Resources/Implantations/operationstatuses/Read | Obter ou listar o status da operação de implantação. |
> | Ação | Microsoft.Resources/deployments/read | Obter ou lista implantações. |
> | Ação | Microsoft.Resources/deployments/validate/action | Validar uma implantação. |
> | Ação | Microsoft.Resources/deployments/whatIf/action | Prevê alterações de implantação de modelo. |
> | Ação | Microsoft.Resources/deployments/write | Criar ou atualizar uma implantação. |
> | Ação | Microsoft.Resources/links/delete | Excluir um link de recurso. |
> | Ação | Microsoft.Resources/links/read | Obter ou listar links de recursos. |
> | Ação | Microsoft.Resources/links/write | Criar ou atualizar um link de recurso. |
> | Ação | Microsoft.Resources/marketplace/purchase/action | Comprar um recurso do marketplace. |
> | Ação | Microsoft.Resources/providers/read | Obter a lista de provedores. |
> | Ação | Microsoft.Resources/resources/read | Obter a lista de recursos com base em filtros. |
> | Ação | Microsoft.Resources/subscriptions/locations/read | Obter a lista de locais com suporte. |
> | Ação | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Ação | Microsoft.Resources/subscriptions/providers/read | Obter ou listar provedores de recursos. |
> | Ação | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/delete | Excluir um grupo de recursos e todos os seus recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obter ou lista operações de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obter ou listar o status da operação de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obter ou lista implantações. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Criar ou atualizar uma implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Mover recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obter os recursos do grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/write | Criar ou atualizar um grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resources/read | Obter os recursos de uma assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/delete | Excluir uma marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/read | Obter ou listar marcações de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Excluir um valor de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obter ou listar valores de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adicionar um valor de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/write | Adicionar uma marcação de assinatura. |
> | Ação | Microsoft.Resources/tags/delete | Remove todas as marcas em um recurso. |
> | Ação | Microsoft.Resources/tags/read | Obtém todas as marcas em um recurso. |
> | Ação | Microsoft.Resources/tags/write | Atualiza as marcas em um recurso substituindo ou mesclando as marcas existentes por um novo conjunto de marcas ou removendo as marcas existentes. |
> | Ação | Microsoft.Resources/tenants/read | Obter a lista de locatários. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Scheduler/jobcollections/delete | Excluir coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/disable/action | Desabilita coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/enable/action | Habilita coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/delete | Excluir trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gerar a definição de aplicativo lógico com base em um trabalho do Agendador. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtém o histórico de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/read | Obter o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/write | Criar ou atualizar o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/read | Obter coleção de trabalhos |
> | Ação | Microsoft.Scheduler/jobcollections/write | Criar ou atualizar a coleção de trabalhos. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Search/checkNameAvailability/action | Verificar a disponibilidade do nome do serviço. |
> | Ação | Microsoft.Search/operations/read | Lista todas as operações disponíveis do provedor Microsoft.Search. |
> | Ação | Microsoft.Search/register/action | Registrar a assinatura do provedor de recursos de pesquisa e permitir a criação de serviços de pesquisa. |
> | Ação | Microsoft.Search/searchServices/createQueryKey/action | Criar a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/delete | Excluir o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/deleteQueryKey/delete | Excluir a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/listAdminKeys/action | Ler as chaves de administração. |
> | Ação | Microsoft. Search/SearchServices/listQueryKeys/Action | Retorna a lista de chaves de API de consulta para o serviço de Pesquisa Cognitiva do Azure fornecido. |
> | Ação | Microsoft.Search/searchServices/listQueryKeys/read | Retorna a lista de chaves de API de consulta para o serviço de Pesquisa Cognitiva do Azure fornecido. |
> | Ação | Microsoft.Search/searchServices/read | Ler o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenerar a chave de administração. |
> | Ação | Microsoft.Search/searchServices/start/action | Iniciar o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/stop/action | Interromper o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/write | Criar ou atualizar o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Impõe as regras de proteção de tráfego determinadas criando regras de segurança correspondentes nos grupos de segurança de rede determinados |
> | Ação | Microsoft.Security/adaptiveNetworkHardenings/read | Obtém recomendações de proteção de rede adaptável de um recurso protegido do Azure |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/read | Obtém as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/write | Atualiza as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Ação | Microsoft.Security/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/applicationWhitelistings/read | Obter a lista de liberações do aplicativo |
> | Ação | Microsoft.Security/applicationWhitelistings/write | Criar uma nova lista de exceções do aplicativo ou atualizar uma existente |
> | Ação | Microsoft. Security/assessmentMetadata/Read | Obter metadados de avaliação de segurança disponíveis em sua assinatura |
> | Ação | Microsoft. Security/assessmentMetadata/Write | Criar ou atualizar metadados de avaliação de segurança |
> | Ação | Microsoft. Security/avaliações/leitura | Obtenha avaliações de segurança em sua assinatura |
> | Ação | Microsoft. Security/avaliações/gravação | Criar ou atualizar avaliações de segurança em sua assinatura |
> | Ação | Microsoft.Security/complianceResults/read | Obter os resultados de conformidade para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/read | Obtém as políticas de proteção de informações para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/write | Atualiza as políticas de proteção de informações para o recurso |
> | Ação | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/dismiss/action | Ignorar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Exclui a política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia uma solicitação de política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obter as políticas de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Criar uma nova política de acesso de rede just-in-time ou atualizar uma existente |
> | Ação | Microsoft.Security/locations/read | Obter o local dos dados de segurança |
> | Ação | Microsoft.Security/locations/tasks/activate/action | Ativar recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/dismiss/action | Ignorar recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Ação | Microsoft.Security/policies/read | Obter a política de segurança |
> | Ação | Microsoft.Security/policies/write | Atualizar a política de segurança |
> | Ação | Microsoft.Security/pricings/delete | Excluir as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/read | Obter as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/write | Atualizar as configurações de preço para o escopo |
> | Ação | Microsoft.Security/register/action | Registrar a assinatura da Central de Segurança do Azure |
> | Ação | Microsoft.Security/securityContacts/delete | Excluir o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/read | Obter o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/write | Atualizar o contato de segurança |
> | Ação | Microsoft.Security/securitySolutions/delete | Excluir uma solução de segurança |
> | Ação | Microsoft.Security/securitySolutions/read | Obter as soluções de segurança |
> | Ação | Microsoft.Security/securitySolutions/write | Criar uma nova solução de segurança ou atualizar uma existente |
> | Ação | Microsoft.Security/securitySolutionsReferenceData/read | Obter dados de referência das soluções de segurança |
> | Ação | Microsoft.Security/securityStatuses/read | Obter status de integridade da segurança para recursos do Azure |
> | Ação | Microsoft.Security/securityStatusesSummaries/read | Obter os resumos de status de segurança para o escopo |
> | Ação | Microsoft.Security/settings/read | Obtém as configurações para o escopo |
> | Ação | Microsoft.Security/settings/write | Atualiza as configurações do escopo |
> | Ação | Microsoft.Security/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/unregister/action | Cancelar a assinatura da Central de Segurança do Azure |
> | Ação | Microsoft.Security/webApplicationFirewalls/delete | Excluir um firewall do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/read | Obter firewalls do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/write | Criar um novo firewall do aplicativo Web ou atualizar um existente |
> | Ação | Microsoft.Security/workspaceSettings/connect/action | Alterar configurações de reconexão das configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/delete | Excluir as configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/read | Obter as configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/write | Atualizar as configurações do workspace |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceBus/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API foi preterida. Use CheckNameAvailability. |
> | Ação | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras VNet no ServiceBus Resource Provider para o VNet especificado |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Excluir o filtro de Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obter o filtro de Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Criar ou atualizar o filtro da Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Excluir recurso de filtro IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obter recurso de filtro IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Criar Recurso de Filtro IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Enviar mensagens |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/migrate/action | Migrar operação de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Exclui a Configuração de migração. |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtém a Configuração de migração que indica o estado da migração e operações de replicação pendentes |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverte a migração do namespace padrão ao premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Atribui o DNS associado com o namespace padrão ao namespace premium que conclui a migração e interrompe os recursos sincronizando do namespace padrão ao premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Cria ou Atualiza a Configuração de migração. Isso iniciará a sincronização de recursos do namespace padrão ao premium |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a Fila. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para excluir regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de conexão para fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Criar regras de autorização de fila e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para excluir o recurso Queue |
> | Ação | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições do recurso Queue |
> | Ação | Microsoft.ServiceBus/namespaces/queues/write | Criar ou atualizar propriedades Queue. |
> | Ação | Microsoft.ServiceBus/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o Tópico. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para excluir regras de autorização do tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de conexão para tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização do tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Criar regras de autorização do tópico e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para excluir o recurso Topic |
> | Ação | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recurso Topic |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para excluir o recurso TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições do recurso TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para excluir o recurso Rule |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições do recurso Rule |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou atualizar propriedades Rule. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou atualizar propriedades TopicSubscription. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/write | Criar ou atualizar propriedades Topic. |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Excluir Recurso de Regra VNET |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obtém o recurso de regra de rede virtual |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Criar Recurso de Regra VNET |
> | Ação | Microsoft.ServiceBus/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/operations/read | Obter Operações |
> | Ação | Microsoft.ServiceBus/register/action | Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus |
> | Ação | Microsoft.ServiceBus/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Ação | Microsoft.ServiceBus/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Ação | Microsoft.ServiceBus/unregister/action | Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceFabric/clusters/applications/delete | Excluir qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/delete | Excluir qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer Partição |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer Réplica |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer Status de Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou atualizar qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/write | Criar ou atualizar qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Excluir qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Excluir qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou atualizar qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou atualizar qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/delete | Excluir qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer Nó |
> | Ação | Microsoft.ServiceFabric/clusters/read | Ler qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer Status do Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/write | Criar ou atualizar qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer Versão do Cluster |
> | Ação | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer Versão do Cluster de um ambiente específico |
> | Ação | Microsoft.ServiceFabric/locations/operationresults/read | Ler qualquer Resultado de Operação |
> | Ação | Microsoft.ServiceFabric/locations/operations/read | Ler qualquer Operação por Local |
> | Ação | Microsoft.ServiceFabric/operations/read | Ler qualquer Operação Disponível |
> | Ação | Microsoft.ServiceFabric/register/action | Registrar qualquer Ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se um nome está disponível para uso com um novo serviço SignalR |
> | Ação | Microsoft.SignalRService/locations/operationresults/signalr/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/locations/usages/read | Obtém os usos de cota para o Serviço do Azure SignalR |
> | Ação | Microsoft.SignalRService/operationresults/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/operações/leitura | Listar as operações do serviço de Signaler do Azure. |
> | Ação | Microsoft.SignalRService/operationstatus/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/register/action | Registra o provedor de recursos 'Microsoft.SignalRService' com uma assinatura |
> | Ação | Microsoft.SignalRService/SignalR/delete | Exclui todo o Serviço do SignalR |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Excluir um filtro de grade de eventos de um Signalr. |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/read | Obtenha as propriedades do filtro de grade de eventos especificado ou liste todos os filtros de grade de eventos para o Signalr especificado. |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/write | Crie ou atualize um filtro de grade de eventos para um Signalr com os parâmetros especificados. |
> | Ação | Microsoft.SignalRService/SignalR/listkeys/action | Exibe o valor das chaves de acesso do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/read | Exibe as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/regeneratekey/action | Altera o valor das chaves de acesso do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/restart/action | Reinicia um Serviço do Azure SignalR no portal de gerenciamento ou por meio da API. Haverá algum tempo de inatividade. |
> | Ação | Microsoft.SignalRService/SignalR/write | Modifica as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.SignalRService' com uma assinatura |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lista os artefatos de aplicativo da definição de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/delete | Remover uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/read | Recuperar uma lista de definições de aplicativos. |
> | Ação | Microsoft.Solutions/applicationDefinitions/write | Adicionar ou modificar uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applications/applicationArtifacts/read | Lista os artefatos de aplicativo. |
> | Ação | Microsoft.Solutions/applications/delete | Remover um aplicativo. |
> | Ação | Microsoft.Solutions/applications/read | Recuperar uma lista de aplicativos. |
> | Ação | Microsoft.Solutions/applications/refreshPermissions/action | Atualiza permissão (ões) de aplicativo. |
> | Ação | Microsoft.Solutions/applications/updateAccess/action | Atualiza o acesso ao aplicativo. |
> | Ação | Microsoft.Solutions/applications/write | Criar um aplicativo. |
> | Ação | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Ação | Microsoft.Solutions/jitRequests/read | Recupera uma lista de JitRequests |
> | Ação | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Ação | Microsoft.Solutions/locations/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.Solutions/register/action | Registrar para Soluções. |
> | Ação | Microsoft.Solutions/unregister/action | Cancela o registro de soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Sql/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Ação | Microsoft.Sql/instancePools/delete | Exclui um pool de instância |
> | Ação | Microsoft.Sql/instancePools/read | Obtém um pool de instância |
> | Ação | Microsoft.Sql/instancePools/usages/read | Obtém as informações de uso do pool de instâncias |
> | Ação | Microsoft.Sql/instancePools/write | Cria ou atualiza um pool de instância |
> | Ação | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Ação | Microsoft.Sql/locations/capabilities/read | Obter as capacidades para essa assinatura em um determinado local |
> | Ação | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/databaseOperationResults/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServerOperationResults/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServers/read | Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado. |
> | Ação | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor excluído |
> | Ação | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obter a operação assíncrona do Azure para uma operação assíncrona de pool elástico |
> | Ação | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obter o resultado de uma operação de pool elástico. |
> | Ação | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/delete | Exclui um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa o failover planejado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa o failover forçado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/read | Retorna a lista de grupos de failover de instância ou obtém as propriedades do grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de failover de instância com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtém o status de uma operação de pool de instâncias |
> | Ação | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtém o resultado de uma operação de pool de instâncias |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Devolve os detalhes de uma operação assíncrona do Azure de ponto de extremidade de interface específico |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Devolve os detalhes da operação de perfil de ponto de extremidade de interface específico |
> | Ação | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o status de uma operação do agente de trabalho. |
> | Ação | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação do agente de trabalho. |
> | Ação | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os servidores de um local |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os bancos de dados em um servidor |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Exclui um backup de retenção de longo prazo |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para um banco de dados |
> | Ação | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Concluir a operação de restauração do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/locations/read | Obter os locais disponíveis para uma determinada assinatura |
> | Ação | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/serverKeyOperationResults/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/syncAgentOperationResults/read | Recuperar resultado da operação do recurso do agente de sincronização |
> | Ação | Microsoft.Sql/locations/syncDatabaseIds/read | Recuperar as IDs do banco de dados de sincronização para uma assinatura e região específica |
> | Ação | Microsoft.Sql/locations/syncGroupOperationResults/read | Recuperar resultado da operação do recurso de grupo de sincronização |
> | Ação | Microsoft.Sql/locations/syncMemberOperationResults/read | Recuperar resultado da operação do recurso de membro de sincronização |
> | Ação | Microsoft.Sql/locations/usages/read | Obter uma coleção de métricas de uso para essa assinatura em um local |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retornar os detalhes da operação assíncrona de regras de rede virtual especificada do Azure  |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Retornar os detalhes da operação de regras de rede virtual especificada  |
> | Ação | Microsoft.Sql/managedInstances/administrators/delete | Excluir um administrador existente da instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/read | Obter uma lista de administradores de instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/write | Criar ou atualizar o administrador da instância gerenciada com os parâmetros especificados. |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft.Sql/managedInstances/databases/delete | Excluir um banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para os bancos de dados de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/databases/read | Obter o banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/databases/recommendedSensitivityLabels/Write | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/restoreDetails/leitura | Retorna detalhes da restauração do banco de dados gerenciado enquanto a restauração está em andamento. |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/read | Obtenha um esquema de banco de dados gerenciado. |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Obter uma tabela de banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados gerenciadas configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recuperar os eventos de segurança do banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um givendatabase |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/write | Cria um novo banco de dados ou atualiza um banco de dados existente. |
> | Ação | Microsoft.Sql/managedInstances/delete | Excluir uma instância gerenciada existente. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/keys/delete | Excluir uma chave de Instância Gerenciada do Azure SQL. |
> | Ação | Microsoft.Sql/managedInstances/keys/read | Retornar as chaves da lista de instância gerenciada ou obter as propriedades para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições de métrica de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Devolver os tipos de métricas que estão disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/read | Retornar a lista de instâncias gerenciadas ou obter as propriedades para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/recoverableDatabases/read | Retorna uma lista de bancos de dados gerenciados recuperáveis |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Devolve uma lista de bancos de dados gerenciados soltos restauráveis. |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor gerenciado configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado |
> | Ação | Microsoft.Sql/managedInstances/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em uma determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/write | Criar uma instância gerenciada com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/operations/read | Obter as operações REST disponíveis |
> | Ação | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/register/action | Registrar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Ação | Microsoft.Sql/servers/administrators/delete | Exclui um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/administrators/read | Obtém um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/administrators/write | Adiciona ou atualiza um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/advisors/read | Retornar a lista de consultores disponíveis para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Ação | Microsoft.Sql/servers/advisors/write | Atualizar status de execução automática de um assistente no nível do servidor. |
> | Ação | Microsoft.Sql/servers/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela do servidor padrão configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingPolicies/write | Alterar a auditoria da tabela de servidor padrão para determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/read | Recuperar detalhes da política de auditoria de blob do servidor configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de blob de servidor para determinado servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/read | Retornar as configurações de ajuste automático do servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/write | Atualizar as configurações de ajuste automático do servidor e retornar as configurações atualizadas |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Excluir um cofre de arquivos de backup existente. |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Essa operação é usada para obter um cofre de retenção de backup de longo prazo. Ela retorna informações sobre o cofre registrado para esse servidor |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é utilizada para registrar uma retenção de backup em longo prazo em um servidor |
> | Ação | Microsoft.Sql/servers/communicationLinks/delete | Excluir um link de comunicação com o servidor existente. |
> | Ação | Microsoft.Sql/servers/communicationLinks/read | Retornar a lista de links de comunicação de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar um link de comunicação com o servidor. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/read | Retornar a lista de política de conexão do servidor de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de conexão do servidor. |
> | Ação | Microsoft.Sql/servers/databases/advisors/read | Retornar a lista de consultores disponíveis para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/write | Atualizar status de um assistente no nível do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/read | Recuperar detalhes da política de auditoria de blob configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de blob para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/read | Retornar as configurações de ajuste automático de um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/write | Atualizar as configurações de ajuste automático de um banco de dados e retornar as configurações atualizadas |
> | Ação | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivamento de backup de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/read | Recuperar detalhes da política de conexão configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar política de conexão para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Retornar a lista de políticas de máscara de dados do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Excluir regra de política de máscara de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recuperar detalhes da regra de política de mascaramento de dados configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Alterar regra de política de mascaramento de dados para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar política de mascaramento de dados para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Retornar as informações de etapa de consulta distribuída da consulta de data warehouse para ID da etapa selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retornar as informações de consulta de distribuição do data warehouse para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recuperar as atividades do usuário de uma instância do SQL Data Warehouse que inclui consultas suspensas e em execução |
> | Ação | Microsoft.Sql/servers/databases/delete | Excluir um banco de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/export/action | Exportar Banco de Dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob estendida para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extensions/read | Obter uma coleção de extensões para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/extensions/write | Alterar a extensão de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/failover/action | Failover de banco de dados iniciado pelo cliente. |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recuperar políticas do backup geográfico para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política do backup geográfico de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém a manutenção de configurações do windows para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define a manutenção de configurações do windows para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/metrics/read | Métricas de retorno para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/move/action | Altere o nome de um banco de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/operationResults/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/operations/cancel/action | Cancelar a operação assíncrona pendente do Banco de Dados SQL do Azure que ainda não está concluída. |
> | Ação | Microsoft.Sql/servers/databases/operations/read | Retornar a lista de operações realizadas no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/pause/action | Pausar Banco de Dados do DataWarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Retornar a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/read | Retornar os valores atuais das configurações do Repositório de Consultas do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/write | Atualizar a configuração do repositório de consultas do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/gravação | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/delete | Encerrar a relação de replicação de modo forçado e com potencial perda de dados |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na relação de replicação\u0027s primária e tornando o primário remoto em um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover imediatamente com possibilidade de perda de dados, tornando esse banco de dados o principal da relação de replicação\u0027s e tornando o primário remoto um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para o vincular ao modo síncrono ou assíncrono |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/action | Criar um novo ponto de restauração |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/delete | Exclui um ponto de restauração do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/read | Retornar os pontos de restauração do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/resume/action | Retomar o Banco de Dados do Datawarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/schemas/read | Obtenha um esquema de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recuperar a lista de recomendações de índice em um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar a recomendação de índice |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/securityMetrics/read | Obter uma coleção de métricas de segurança de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/sensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retornar sugestão sobre escalar ou reduzir o banco de dados verticalmente com base nas estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos |
> | Ação | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de SKUs disponíveis para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar a sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/delete | Excluir um grupo de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Retornar a lista de esquemas do banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/logs/read | Retornar a lista de logs do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/read | Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar o esquema do banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Excluir um membro de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar o esquema do membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Retornar a lista de esquemas do banco de dados de membros de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Criar um membro de sincronização com os parâmetros especificados ou atualiza as propriedades do membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Disparar a sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/write | Criar um grupo de sincronização com os parâmetros especificados ou atualizar as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/topQueries/queryText/action | Retornar o texto Transact-SQL para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/topQueries/read | Retorna estatísticas de runtime agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/topQueries/statistics/read | Retorna estatísticas de runtime agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obter operações em andamento na criptografia de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar o estado de criptografia de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar o Banco de Dados do Datawarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/usages/read | Obter as informações de uso do Banco de Dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um givendatabase |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recuperar o resultado de exame de avaliação de vulnerabilidade do banco de dados Executar operação |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/write | Criar um banco de dados com os parâmetros especificados ou atualizar as propriedades ou marcas para o banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Excluir as configurações de recuperação de desastre existentes para um determinado servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obter uma coleção de configurações de recuperação de desastre que incluem esse servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar configuração de recuperação de desastre do servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/read | Retornar a lista de estimativas de pool elástico já criado para o servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/write | Criar nova estimativa de pool elástico para a lista de bancos de dados fornecida |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/read | Retornar a lista de consultores disponíveis para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/write | Atualizar status de execução automática de um assistente no nível do pool elástico. |
> | Ação | Microsoft.Sql/servers/elasticPools/databases/read | Obter uma lista de bancos de dados para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/delete | Excluir pool elástico existente |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recuperar atividades e detalhes em um pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recuperar atividades e detalhes em um banco de dados que faz parte do pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/failover/action | Failover de pool elástico iniciado pelo cliente. |
> | Ação | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/metrics/read | Métricas de retorno para conjuntos de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela o pool elástico do Azure SQL pendente de operação assíncrona que ainda não está concluída. |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/read | Retornar a lista de operações realizadas no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/read | Recuperar detalhes do pool elástico em um determinado servidor |
> | Ação | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de SKUs disponíveis para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/write | Criar um novo ou alterar propriedades do pool elástico existente |
> | Ação | Microsoft.Sql/servers/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/servers/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de blob de servidor estendida para um determinado servidor |
> | Ação | Microsoft.Sql/servers/failoverGroups/delete | Excluir um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/failover/action | Executar o failover planejado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executar o failover forçado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/read | Retornar a lista de grupos de failover ou obter as propriedades do grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/failoverGroups/write | Criar um grupo de failover com os parâmetros especificados ou atualizar as propriedades ou marcas para o grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/delete | Excluir uma regra de firewall de servidor existente. |
> | Ação | Microsoft.Sql/servers/firewallRules/read | Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/write | Criar uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades da regra especificada ou substituir todas as regras existentes pelas novas regras de firewall do servidor. |
> | Ação | Microsoft.Sql/servers/import/action | Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac |
> | Ação | Microsoft.Sql/servers/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Exclui o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Devolve as propriedades para o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto de extremidade de interface com os parâmetros especificados ou atualiza as propriedades ou marcas para o ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/jobAgents/delete | Exclui um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/read | Obtém um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/write | Cria ou atualiza um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/keys/delete | Excluir uma chave do servidor existente. |
> | Ação | Microsoft.Sql/servers/keys/read | Retornar a lista de chaves do servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft.Sql/servers/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a chave do servidor especificada. |
> | Ação | Microsoft.Sql/servers/operationResults/read | Obter operações do servidor em andamento |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/servers/privateLinkResources/read | Obter os recursos de link privado para o SQL Server correspondente |
> | Ação | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas disponíveis para servidores |
> | Ação | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recuperar a métrica de pools de banco de dados elástico recomendados para determinado servidor |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/read | Recupera a recomendação para pools de banco de dados elástico a fim de reduzir custos ou melhorar o desempenho com base no histórico de utilização de recursos |
> | Ação | Microsoft.Sql/servers/recoverableDatabases/read | Essa operação é usada para a recuperação de desastres do banco de dados dinâmico a fim de restaurar o banco de dados no último ponto de backup bom conhecido. Ela retorna informações sobre o último backup, mas não\u0027t restaura o banco de dados efetivamente. |
> | Ação | Microsoft.Sql/servers/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Ação | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obter uma lista de bancos de dados que foram removidos em determinado servidor ainda na política de retenção. |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.Sql/servers/serviceObjectives/read | Recuperar a lista de objetivos de nível de serviço (também conhecido como níveis de desempenho) disponíveis em determinado servidor |
> | Ação | Microsoft.Sql/servers/syncAgents/delete | Excluir um agente de sincronização existente. |
> | Ação | Microsoft.Sql/servers/syncAgents/generateKey/action | Gera a chave de registro do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Retornar a lista de bancos de dados vinculados do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/read | Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/syncAgents/write | Criar um agente de sincronização com os parâmetros especificados ou atualizar as propriedades do agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/servers/usages/read | Retornar a cota de DTU do servidor e o consumo atual de DTU por todos os bancos de dados no servidor |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade de determinado servidor |
> | Ação | Microsoft.Sql/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft.Sql/unregister/action | Cancelar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Ação | Microsoft.Sql/virtualClusters/delete | Exclui um cluster virtual existente. |
> | Ação | Microsoft.Sql/virtualClusters/read | Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado. |
> | Ação | Microsoft.Sql/virtualClusters/write | Atualiza marcas de cluster virtual. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Storage/checknameavailability/read | Verificar se o nome dessa conta é válido e não está em uso. |
> | Ação | Microsoft.Storage/locations/checknameavailability/read | Verificar se o nome dessa conta é válido e não está em uso. |
> | Ação | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notificar o Microsoft.Storage sobre a exclusão de uma rede virtual ou sub-rede |
> | Ação | Microsoft.Storage/locations/usages/read | Retornar o limite e a contagem atual de uso de recursos na assinatura especificada |
> | Ação | Microsoft.Storage/operations/read | Monitorar o status de uma operação assíncrona. |
> | Ação | Microsoft.Storage/register/action | Registra a assinatura do provedor de recursos de armazenamento e permite a criação de contas de armazenamento. |
> | Ação | Microsoft.Storage/skus/read | Listar os SKUs com suporte pelo Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retorna o resultado da adição de conteúdo do blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Devolve o resultado da exclusão de um instantâneo automático |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Retorna a lista de BLOBs em uma conta com filtro de marcas correspondentes |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Retorna o resultado do comando de blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Retorna o resultado da leitura de marcas de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Retorna o resultado da gravação de marcas de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar a retenção legal do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retornar o resultado da exclusão de um contêiner |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Excluir a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Estender a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Inserir a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Retorna o resultado do contêiner de blob de concessão |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna um contêiner |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna a lista de contêineres |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir a retenção legal do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob de patch |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob de colocação |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço Blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read | Retornar propriedades ou estatísticas do serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/write | Retornar o resultado da inserção de propriedades do serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/delete | Excluir uma conta de armazenamento existente. |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/failover/action | O cliente é capaz de controlar o failover em caso de problemas de disponibilidade |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Obter privilégios de administrador de arquivos |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retorna o resultado da exclusão de um arquivo/pasta |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retorna o resultado da permissão de modificação em um arquivo/pasta |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retorna um arquivo/pasta ou uma lista de arquivos/pastas |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/read | Obter propriedades do serviço de arquivo |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/listAccountSas/action | Retornar o token SAS da conta para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listServiceSas/action | Retornar o token SAS de serviço para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/delete | Excluir políticas de gerenciamento de conta de armazenamento |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/read | Obter políticas de conta de gerenciamento de armazenamento |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/write | Políticas de gerenciamento de conta de armazenamento put |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Excluir proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Read | Obter proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Colocar proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Excluir conexão de ponto de extremidade particular |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Obter conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Colocar conexão de ponto de extremidade particular |
> | Ação | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateLinkResources/read | Obter StorageAccount groupids |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retornar o resultado da exclusão de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retorna o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retorna o resultado da exclusão de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retorna o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retorna uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retorna o resultado da gravação de uma mensagem |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retornar o resultado da gravação de uma fila |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Obter serviço Fila Propriedades |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Retornar propriedades ou estatísticas do serviço de fila. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/write | Retornar o resultado da configuração das propriedades do serviço de fila |
> | Ação | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerar as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Restaurar intervalos de BLOB para o estado do tempo especificado |
> | Ação | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revoga todas as chaves de delegação do usuário para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Criar/atualizar definições de diagnóstico da conta de armazenamento. |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/read | Obter propriedades do serviço tabela |
> | Ação | Microsoft.Storage/storageAccounts/write | Criar uma conta de armazenamento com os parâmetros especificados, atualizar as propriedades ou marcas ou adicionar um domínio personalizado à conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/usages/read | Retornar o limite e a contagem atual de uso de recursos na assinatura especificada |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.storagesync/locations/checkNameAvailability/action | Verifica se o nome do serviço de sincronização de armazenamento é válido e não está em uso. |
> | Ação | microsoft.storagesync/locations/workflows/operations/read | Obter o status de uma operação assíncrona |
> | Ação | Microsoft. storagesync/operações/leitura | Obtém uma lista das operações com suporte |
> | Ação | Microsoft. storagesync/registro/ação | Registra a assinatura para o provedor de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/delete | Excluir os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/read | Ler os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/delete | Excluir um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Servidor Registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Excluir Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o status de uma operação de backup/restauração assíncrona. |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar essa ação após o backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar essa ação após restaurar |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar essa ação antes do backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar essa ação antes de restaurar |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar pulsação |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/delete | Excluir quaisquer Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/read | Lera quaisquer Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Excluir qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Pontos de Extremidade do Servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar essa ação para recuperar arquivos para um servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar qualquer ponto de extremidade de servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obter o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obter o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/read | Ler fluxos de trabalho |
> | Ação | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar Serviços de Sincronização de Armazenamento |
> | Ação | Microsoft. storagesync/cancelar registro/ação | Cancela o registro da assinatura para o provedor de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/delete | Excluir os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/read | Listar ou obter os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/alerts/read | Listar ou obter os alertas |
> | Ação | Microsoft.StorSimple/managers/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/delete | Excluir uma configuração de largura de banda existente (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/read | Listar as configurações de largura de banda (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/write | Criar novas configurações de largura de banda ou atualizá-las (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Ação | Microsoft.StorSimple/Managers/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Ação | Microsoft.StorSimple/managers/clearAlerts/action | Limpar todos os alertas associados ao Gerenciador de Dispositivos. |
> | Ação | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Listar configurações com suporte pelo dispositivo de nuvem |
> | Ação | Microsoft.StorSimple/managers/configureDevice/action | Configurar um dispositivo |
> | Ação | Microsoft.StorSimple/managers/delete | Excluir os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/delete | A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/read | Listar ou obter as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar para sobreposição da chave de criptografia de serviço de dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Fazer um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política. |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Excluir políticas de backup existentes (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listar as políticas de backup (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Excluir agendas existentes |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Listar as agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Criar ou atualizar agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/write | Criar novas ou atualizar as políticas de Backup (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backups/delete | Excluir o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clonar um compartilhamento ou volume usando um elemento de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaurar todos os volumes de um conjunto de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Excluir os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Listar ou obter os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/delete | Excluir as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/read | Listar ou obter as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/deactivate/action | Desativar um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/delete | Excluir os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/download/action | Baixar atualizações para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/action | Failover do dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/failoverTargets/read | Lista ou obtém os destinos de failover dos dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Fazer backup de um servidor de arquivos. |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/delete | Excluir servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Excluir os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar o estado de energia do controlador dos grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/install/action | Instalar atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala as atualizações nos dispositivos (apenas série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Fazer backup de um servidor iSCSI. |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Excluir servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Excluir os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar um trabalho em execução |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Listar os conjuntos de failover para um dispositivo existente (apenas série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Listar destinos de failover dos dispositivos (apenas Série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirmar uma migração bem-sucedida. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Listar o status de miração de confirmação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Buscar o status de confirmação da migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Buscar o status do trabalho de estimativa de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Buscar o status para a migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Listar o status de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar migração usando as configurações de origem |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Iniciar um trabalho para estimar a duração do processo de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/read | Listar ou obter as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/write | Criar novas ou atualizar as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Listar chave de criptografia pública do Gerenciador de Dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publica o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para ajudar o Suporte da Microsoft na solução de problemas do dispositivo StorSimple. |
> | Ação | Microsoft.StorSimple/managers/devices/read | Listar ou obter os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Verificar se há atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/read | Listar as configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronizar o certificado de gerenciamento remoto de um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualizar as configurações de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/write | Criar ou atualizar as configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Enviar email de alerta de teste para destinatários de email configurados. |
> | Ação | Microsoft.StorSimple/managers/devices/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/read | Listar ou obter as configurações de tempo |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/write | Criar ou atualizar as configurações de tempo |
> | Ação | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/updateSummary/read | Listar ou obter o resumo de atualização |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Excluir contêineres de Volume existentes (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contêineres de volume (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Excluir um volume existente |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Criar ou atualizar volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/write | Criar novos ou atualizar os contêineres de volume (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Ação | Microsoft.StorSimple/managers/encryptionSettings/read | Listar ou obter as configurações de criptografia |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/delete | Exclui as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/read | Lista ou obtém as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Ação | Microsoft.StorSimple/managers/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/getEncryptionKey/action | Obter a chave de criptografia para o gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/listActivationKey/action | Obter a chave de ativação do Gerenciador de Dispositivo StorSimple. |
> | Ação | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de criptografia públicas de um Gerenciador de Dispositivo StorSimple. |
> | Ação | Microsoft.StorSimple/managers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar do Clássico para o Gerenciador de Recursos dos Gerenciadores |
> | Ação | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Listar as configurações de origem de migração (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Criar um novo dispositivo de nuvem. |
> | Ação | Microsoft.StorSimple/managers/read | Listar ou obter os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenera a chave de ativação para um StorSimple Device Manager existente. |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Excluir as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/delete | Excluir os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageDomains/read | Listar ou obter os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/write | Criar ou atualizar os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.StorSimple/operations/read | Lista ou obtém as operações |
> | Ação | Microsoft.StorSimple/register/action | Registrar o provedor Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.StreamAnalytics/locations/quotas/Read | Ler Cota de assinatura do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/operations/Read | Ler Operações do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/Register/action | Registrar assinatura com provedor de recursos do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Delete | Excluir trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Excluir função de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Ler os resultados da operação para Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Ler Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recuperar a definição padrão de uma Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testar Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Gravar Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Excluir entrada de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Ler os resultados da operação para Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Ler entrada do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Exemplo de Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testar Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Gravar entrada de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler Definições de Métrica |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Ler resultados da operação para Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Excluir saída de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Ler resultados da operação para Saída do Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Ler saída do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testar Saída do Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Gravar saída de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Gravar configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obter a métrica disponível para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Read | Ler trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Interromper trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Excluir transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Ler transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Gravar transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Write | Gravar trabalho de Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft. Subscription/cancelamento/ação | Cancela a assinatura |
> | Ação | Microsoft.Subscription/CreateSubscription/action | Cria uma assinatura do Azure |
> | Ação | Microsoft.Subscription/register/action | Registra a Assinatura no provedor de recursos Microsoft.Subscription |
> | Ação | Microsoft. Subscription/renomear/ação | Renomeia a assinatura |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/read | Obter uma definição de assinatura do Azure em um grupo de gerenciamento. |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de assinatura do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Support/register/action | Registrar para dar suporte ao provedor de recursos |
> | Ação | Microsoft.Support/supportTickets/read | Obter detalhes de tíquete de suporte (incluindo status, severidade, detalhes de contato e comunicações) ou obtém a lista de tíquetes de suporte entre assinaturas. |
> | Ação | Microsoft.Support/supportTickets/write | Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Excluir a política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Criar uma nova política de acesso para um ambiente ou atualizar uma política de acesso existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/delete | Excluir o ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Excluir a origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/write | Criar uma nova origem do evento para um ambiente ou atualizar uma origem do evento existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Excluir o conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Criar um novo conjunto de dados de referência para um ambiente ou atualizar um conjunto de dados de referência existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/status/read | Obter o status do ambiente, estado das operações associadas, como ingresso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/write | Criar um novo ambiente ou atualizar um ambiente existente. |
> | Ação | Microsoft.TimeSeriesInsights/register/action | Registrar a assinatura do provedor de recursos do Time Series Insights e habilitar a criação de ambientes do Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.VisualStudio/Account/Delete | Exclui a Conta |
> | Ação | Microsoft.VisualStudio/Account/Extension/Read | Conta de Leitura/Extensão |
> | Ação | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Read | Lê a Conta |
> | Ação | Microsoft.VisualStudio/Account/Write | Define a Conta |
> | Ação | Microsoft.VisualStudio/Extension/Delete | Exclui a Extensão |
> | Ação | Microsoft.VisualStudio/Extension/Read | Lê a Extensão |
> | Ação | Microsoft.VisualStudio/Extension/Write | Define a Extensão |
> | Ação | Microsoft.VisualStudio/Project/Delete | Exclui o Projeto |
> | Ação | Microsoft.VisualStudio/Project/Read | Lê o Projeto |
> | Ação | Microsoft.VisualStudio/Project/Write | Define o Projeto |
> | Ação | Microsoft.VisualStudio/Register/Action | Registrar a assinatura do Azure com o provedor de Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.web/apimanagementaccounts/apiacls/read | Obter Apiacls de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Excluir Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obter Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualizar Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obter Connectionacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirmar código de consentimento de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Excluir Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualizar Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/delete | Excluir Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obter links de consentimento para conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Listar chaves de conexão de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Listar Segredos de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/read | Obter conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/write | Atualizar conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/delete | Excluir APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Excluir definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obter definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Atualizar definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/read | Obter APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/write | Atualizar APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/connectionacls/read | Obter Connectionacls de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/availablestacks/read | Obter pilhas disponíveis. |
> | Ação | Microsoft.Web/certificates/Delete | Excluir um certificado existente. |
> | Ação | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Ação | Microsoft.Web/certificates/Write | Adicionar um novo certificado ou atualizar um existente. |
> | Ação | microsoft.web/checknameavailability/read | Verificar se o nome do recurso está disponível. |
> | Ação | microsoft.web/classicmobileservices/read | Obter Serviços Móveis clássicos. |
> | Ação | Microsoft.Web/connectionGateways/Delete | Excluir um Gateway de Conexão. |
> | Ação | Microsoft.Web/connectionGateways/Join/Action | Adicionar um Gateway de Conexão. |
> | Ação | Microsoft.Web/connectionGateways/ListStatus/Action | Listar status de um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Move/Action | Mover um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Read | Obter a lista de gateways de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Write | Criar ou atualizar um Gateway de Conexão. |
> | Ação | microsoft.web/connections/confirmconsentcode/action | Confirmar o código de autorização de conexões. |
> | Ação | Microsoft.Web/connections/Delete | Excluir uma conexão. |
> | Ação | Microsoft.Web/connections/Join/Action | Ingressar em uma conexão. |
> | Ação | microsoft.web/connections/listconsentlinks/action | Listar os links de autorização para conexões. |
> | Ação | Microsoft.Web/connections/Move/Action | Mover uma conexão. |
> | Ação | Microsoft.Web/connections/Read | Obter a lista de conexões. |
> | Ação | Microsoft.Web/connections/Write | Criar ou atualizar uma conexão. |
> | Ação | Microsoft.Web/customApis/Delete | Excluir uma API personalizada. |
> | Ação | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrair a definição de API de uma WSDL. |
> | Ação | Microsoft.Web/customApis/Join/Action | Adicionar uma API personalizada. |
> | Ação | Microsoft.Web/customApis/listWsdlInterfaces/Action | Listar interfaces WSDL para uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Move/Action | Mover uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Read | Obter a lista de API personalizada. |
> | Ação | Microsoft.Web/customApis/Write | Criar ou atualizar uma API personalizada. |
> | Ação | Microsoft.Web/deletedSites/Read | Obtém as propriedades de um aplicativo Web excluído |
> | Ação | microsoft.web/deploymentlocations/read | Obter locais de implantação. |
> | Ação | Microsoft.Web/geoRegions/Read | Obter a lista de regiões geográficas. |
> | Ação | microsoft.web/hostingenvironments/capacities/read | Obter as capacidades dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/Delete | Excluir ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/detectors/read | Obtém detectores de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/diagnostics/read | Obter diagnóstico dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de entrada. |
> | Ação | Microsoft.Web/hostingEnvironments/Join/Action | Une um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métrica de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métrica de pools multifunção dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter a métrica de pools multifunção em Ambientes de Hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de pools multifunção dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter usos dos pools multifunção dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/hostingenvironments/operations/read | Obter operações dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de saída. |
> | Ação | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicializar todas as máquinas em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/resume/action | Retomar ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/serverfarms/read | Obter Planos do Serviço de Aplicativo dos Ambientes de Hospedagem. |
> | Ação | microsoft.web/hostingenvironments/sites/read | Obter aplicativos Web dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/suspend/action | Suspender ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/usages/read | Obter usos dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter definições de métrica de pools de trabalho dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter a métrica de pools de trabalho de Ambientes de Hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/workerpools/skus/read | Obter SKUs de pools de trabalho dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/usages/read | Obter uso dos pools de trabalho dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft.Web/hostingEnvironments/Write | Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/ishostingenvironmentnameavailable/read | Obter confirmação se o nome do ambiente de hospedagem está disponível. |
> | Ação | microsoft.web/ishostnameavailable/read | Verificar se o nome do host está disponível. |
> | Ação | microsoft.web/isusernameavailable/read | Verificar se o nome de usuário está disponível. |
> | Ação | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes dos sites atribuídos ao nome de host. |
> | Ação | microsoft.web/locations/apioperations/read | Obter operações API dos locais. |
> | Ação | microsoft.web/locations/connectiongatewayinstallations/read | Obter as instalações de gateway de conexão locais. |
> | Ação | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Notificação de exclusão de Vnet ou sub-rede para Locais. |
> | Ação | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrair a definição de API da WSDL para locais. |
> | Ação | microsoft.web/locations/listwsdlinterfaces/action | Listar interfaces WSDL para locais. |
> | Ação | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerenciadas dos locais. |
> | Ação | Microsoft.Web/locations/managedapis/Join/Action | Adicionar uma API gerenciada. |
> | Ação | microsoft.web/locations/managedapis/read | Obter as APIs gerenciadas dos locais. |
> | Ação | Microsoft. Web/Locations/operationResults/Read | Obter Operações. |
> | Ação | Microsoft. Web/Locations/Operations/Read | Obter Operações. |
> | Ação | microsoft.web/operations/read | Obter Operações. |
> | Ação | microsoft.web/publishingusers/read | Obter usuários de publicação. |
> | Ação | microsoft.web/publishingusers/write | Atualizar usuários de publicação. |
> | Ação | Microsoft.Web/recommendations/Read | Obter a lista de recomendações para assinaturas. |
> | Ação | microsoft.web/register/action | Registrar o provedor de recursos Microsoft.Web para a assinatura. |
> | Ação | microsoft.web/resourcehealthmetadata/read | Obter metadados do Resource Health. |
> | Ação | microsoft.web/serverfarms/capabilities/read | Obter recursos do Planos do Serviço de Aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Delete | Excluir um Plano do Serviço de Aplicativo existente |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Delete | Excluir filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/ler | Obter filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Write | Colocar filtro de grade de eventos no farm de servidores. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/delete | Excluir configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualizar configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter aplicativos Web das retransmissões dos namespaces da conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obter limites do plano de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionrelays/read | Obter transmissões de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/metricdefinitions/read | Obter definições de métrica dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/metrics/read | Obter métrica dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/operationresults/read | Obter resultados de operação dos Planos do Serviço de Aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Ação | Microsoft.Web/serverfarms/restartSites/Action | Reiniciar todos os aplicativos Web em um Plano do Serviço de Aplicativo |
> | Ação | microsoft.web/serverfarms/sites/read | Obter aplicativos Web dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/skus/read | Obter as SKUs dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/usages/read | Obter usos dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/read | Obter as conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Excluir rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter a rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualizar rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/workers/reboot/action | Reinicializar trabalhos dos Planos do Serviço de Aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Write | Criar um novo Plano do Serviço de Aplicativo ou atualizar um plano existente |
> | Ação | microsoft.web/sites/analyzecustomhostname/read | Analisar nome de host personalizado. |
> | Ação | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao aplicativo Web atual |
> | Ação | Microsoft.Web/sites/backup/Action | Criar um novo backup do aplicativo Web |
> | Ação | microsoft.web/sites/backup/read | Obter o backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backup/write | Atualizar o backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/action | Descobre um backup existente de aplicativo que pode ser restaurado de um blob no Armazenamento do Azure. |
> | Ação | microsoft.web/sites/backups/delete | Excluir backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/list/action | Listar backups de aplicativos de Web. |
> | Ação | Microsoft.Web/sites/backups/Read | Obter as propriedades do backup de um aplicativo Web |
> | Ação | microsoft.web/sites/backups/restore/action | Restaurar backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/write | Atualiza os backup de aplicativos Web. |
> | Ação | microsoft.web/sites/config/delete | Excluir configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/list/Action | Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/config/Read | Obter definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/Write | Atualizar definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/containerlogs/action | Obtém Logs de contêiner compactados para o aplicativo Web. |
> | Ação | microsoft.web/sites/containerlogs/download/action | Baixa os Logs do Contêiner dos Aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/read | Obter trabalhos de Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/start/action | Iniciar trabalhos de Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/stop/action | Interromper trabalhos de Web contínuos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Delete | Excluir um aplicativo Web existente |
> | Ação | microsoft.web/sites/deployments/delete | Excluir as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/log/read | Obter o log de implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/read | Obter as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/write | Atualizar as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/detectors/read | Obtém os detectores de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/execute/Action | Executar Análise de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/read | Obter Análise de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/aspnetcore/read | Obter Diagnóstico de Aplicativos Web para aplicativo ASP.NET Core. |
> | Ação | microsoft.web/sites/diagnostics/autoheal/read | Obter Diagnóstico de Aplicativos Web para Autoheal. |
> | Ação | microsoft.web/sites/diagnostics/deployment/read | Obter a implantação de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/deployments/read | Obter implantações de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/execute/Action | Executar o Detector de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/read | Obter o Detector de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter solicitações de falha de diagnóstico de Aplicativos Web por URI. |
> | Ação | microsoft.web/sites/diagnostics/frebanalysis/read | Obter a análise FREB do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/runtimeavailability/read | Obter a disponibilidade de runtime do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/servicehealth/read | Obter integridade do serviço de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obter análise de CPU do site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas do Site de Diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitelatency/read | Obter a latência do Site de Diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter reinicialização do site de diagnóstico de Aplicativos Web iniciado pelo usuário. |
> | Ação | microsoft.web/sites/diagnostics/siteswap/read | Obter troca de site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workeravailability/read | Obter Workeravailability de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obter a reciclagem do processo de trabalho do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/write | Atualizar de identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Delete | Excluir filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/ler | Obter filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Write | Colocar filtro de grade de eventos no aplicativo Web. |
> | Ação | microsoft.web/sites/extensions/delete | Excluir extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/extensions/read | Obter extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/extensions/write | Atualizar extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/functions/action | Aplicativos Web do Functions. |
> | Ação | microsoft.web/sites/functions/delete | Excluir funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/chaves/excluir | Excluir chaves de função. |
> | Ação | Microsoft. Web/sites/funções/chaves/gravação | Atualizar chaves de função. |
> | Ação | Microsoft. Web/sites/funções/listkeys/ação | Listar chaves de função. |
> | Ação | microsoft.web/sites/functions/listsecrets/action | Listar segredos de função. |
> | Ação | microsoft.web/sites/functions/masterkey/read | Obter a chave mestra de funções de Aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/leitura | Ler propriedades de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/gravação | Atualizar propriedades de funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/read | Obter funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/token/read | Obter token de funções de Aplicativos Web. |
> | Ação | microsoft.web/sites/functions/write | Atualizar funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/Delete | Excluir funções chaves de função de host. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/gravação | Funções de atualização hospedam chaves de função. |
> | Ação | Microsoft. Web/sites/host/listkeys/ação | Listar chaves de host do functions. |
> | Ação | Microsoft. Web/sites/host/listsyncstatus/ação | Listar status de gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/Propriedades/leitura | Ler propriedades do host de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/sincronização/ação | Gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/systemkeys/Delete | Exclua as funções do sistema host. |
> | Ação | Microsoft. Web/sites/host/systemkeys/gravação | Funções de atualização hospedam chaves do sistema. |
> | Ação | microsoft.web/sites/hostnamebindings/delete | Excluir associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/read | Obter associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/write | Atualizar associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostruntime/functions/keys/read | Obtém as teclas de função do host de runtime dos Aplicativos Web. |
> | Ação | Microsoft.Web/sites/hostruntime/host/_master/read | Obter chave mestra do Aplicativo de funções para operações de administrador |
> | Ação | Microsoft.Web/sites/hostruntime/host/action | Executar a ação de runtime do Aplicativo de funções como gatilhos de sincronização, adicionar funções, invocar funções, excluir funções, etc. |
> | Ação | microsoft.web/sites/hostruntime/host/read | Obtém o host do host de runtime dos Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/delete | Excluir a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/read | Obter a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/write | Atualizar a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Listar chaves de retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionrelays/read | Obter transmissões de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/delete | Excluir implantações de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/read | Obter as implantações de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/log/read | Obter log de extensões de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/processes/read | Obtém os Processos de Extensões de Instâncias dos Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/read | Obter extensões de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/delete | Excluir processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/modules/read | Obtém os Módulos de Processos de Instâncias dos Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/read | Obter os processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/threads/read | Obter threads de processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/read | Obter instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Listar status do gatilho de função de sincronização. |
> | Ação | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicativos Web. |
> | Ação | microsoft.web/sites/metrics/read | Métrica de aplicativos Web. |
> | Ação | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de Aplicativos Web. |
> | Ação | microsoft.web/sites/migratemysql/action | Migrar Aplicativos Web do MySql. |
> | Ação | microsoft.web/sites/migratemysql/read | Obter migração do MySql de Aplicativos Web. |
> | Ação | microsoft.web/sites/networktrace/action | Aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/networktraces/operationresults/read | Obter resultados da operação de rastreamento de rede de aplicativos Web. |
> | Ação | microsoft.web/sites/newpassword/action | Aplicativos Web Newpassword. |
> | Ação | microsoft.web/sites/operationresults/read | Obter resultados de operação de aplicativos Web. |
> | Ação | microsoft.web/sites/operations/read | Obter operações de Aplicativos Web. |
> | Ação | microsoft.web/sites/perfcounters/read | Obter contadores de desempenho de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/delete | Excluir complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/read | Obter complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/write | Atualizar complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/privateaccess/read | Obter dados sobre a habilitação de acesso a sites privados e Redes Virtuais autorizadas que podem acessar o site. |
> | Ação | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Ação | microsoft.web/sites/processes/modules/read | Obter módulos de processos de aplicativos Web. |
> | Ação | microsoft.web/sites/processes/read | Obter processos de Aplicativos Web. |
> | Ação | microsoft.web/sites/processes/threads/read | Obter threads de processos de aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/delete | Excluir certificados públicos de Aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/read | Obter certificados públicos de Aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/write | Atualizar certificados públicos de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/publish/Action | Publicar um aplicativo Web |
> | Ação | Microsoft.Web/sites/publishxml/Action | Obter XML do perfil de publicação para um aplicativo Web |
> | Ação | microsoft.web/sites/publishxml/read | Obter XML de publicação de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Read | Obter as propriedades de um aplicativo Web |
> | Ação | microsoft.web/sites/recommendationhistory/read | Obter o histórico de recomendação de aplicativos Web. |
> | Ação | microsoft.web/sites/recommendations/disable/action | Desabilitar as recomendações de aplicativos Web. |
> | Ação | Microsoft.Web/sites/recommendations/Read | Obter a lista de recomendações para o aplicativo Web. |
> | Ação | microsoft.web/sites/recover/action | Recuperar Aplicativos Web. |
> | Ação | Microsoft.Web/sites/resetSlotConfig/Action | Redefinir a configuração de aplicativo Web |
> | Ação | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/restart/Action | Reiniciar um aplicativo Web |
> | Ação | microsoft.web/sites/restore/read | Obter a restauração de aplicativos Web. |
> | Ação | microsoft.web/sites/restore/write | Restaurar Aplicativos Web. |
> | Ação | microsoft.web/sites/restorefrombackupblob/action | Restaurar o aplicativo Web a partir do blob de backup. |
> | Ação | Microsoft. Web/sites/restorefromdeletedapp/Action | Restaura os aplicativos Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/restoresnapshot/action | Restaura os Instantâneos de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/delete | Excluir extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/read | Obter extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/write | Atualizar extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/analyzecustomhostname/read | Obter slots de aplicativos Web Analisar nome de host personalizado. |
> | Ação | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao slot atual. |
> | Ação | Microsoft.Web/sites/slots/backup/Action | Criar o novo backup do slot do aplicativo Web. |
> | Ação | microsoft.web/sites/slots/backup/read | Obter backup de Slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backup/write | Atualizar o backup de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/action | Descobrir backups de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/delete | Excluir backups de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/list/action | Listar backups de slots de aplicativos de Web. |
> | Ação | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades do backup dos slots de um aplicativo Web |
> | Ação | microsoft.web/sites/slots/backups/restore/action | Restaurar backups de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/config/delete | Excluir configuração de slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/config/list/Action | Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/config/Write | Atualizar definições de configuração do slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/containerlogs/action | Obter logs de contêiner compactados para slot do aplicativo Web. |
> | Ação | microsoft.web/sites/slots/containerlogs/download/action | Baixa os Logs do Contêiner de Slots dos Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/read | Obter trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/start/action | Iniciar trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/stop/action | Interromper trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Delete | Excluir um slot de aplicativo Web existente |
> | Ação | microsoft.web/sites/slots/deployments/delete | Excluir as implantações de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/log/read | Obter o log de implantações dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/read | Obter as implantações dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/write | Atualizar as implantações de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/detectors/read | Obter detectores de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Executar análise de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/read | Obter análise de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obter o diagnóstico de slots de Aplicativos Web para o aplicativo ASP.NET Core. |
> | Ação | microsoft.web/sites/slots/diagnostics/autoheal/read | Obter o diagnóstico de slots de Aplicativos Web para Autoheal. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployment/read | Obter implantação de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implantações de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Executar o detector de diagnóstico de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/read | Obter detector de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análise FREB de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/read | Obter diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obter disponibilidade de runtime de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter Integridade do Serviço do Azure do diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obter análise de CPU de site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas nos sites de diagnóstico dos slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obter latência de sites de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obter diagnóstico de slots de Aplicativos Web iniciado pelo usuário. |
> | Ação | microsoft.web/sites/slots/diagnostics/siteswap/read | Obter troca de sites de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obter disponibilidade do trabalho de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obter reciclagem de processo de trabalho de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/functions/listsecrets/action | Lista as Funções de Slots de Segredos dos Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/functions/read | Obter funções de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/delete | Excluir vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/read | Obter vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/write | Atualizar vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/delete | Excluir a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/read | Obter a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/write | Atualizar a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/deployments/read | Obter as implantações de instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/delete | Excluir processos de instâncias de slots de plicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/read | Obter os processos de instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/read | Obter instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métrica de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metrics/read | Obter métrica dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/migratemysql/read | Obter migração do MySql de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/networktrace/action | Slots de Aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/slots/networktraces/operationresults/read | Obter resultados de operação de rastreamento de rede de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/newpassword/action | Slots de aplicativos Web Newpassword. |
> | Ação | microsoft.web/sites/slots/operationresults/read | Obter resultados de operação de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/operations/read | Obter operações de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/perfcounters/read | Obter contadores de desempenho de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/phplogging/read | Obter Phplogging dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/delete | Excluir complementos de Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/read | Obter complementos Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/write | Atualizar complementos Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/processes/read | Obtém os Processos de Slots dos Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/delete | Excluir certificados públicos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/publish/Action | Publicar um slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/publishxml/Action | Obter XML do perfil de publicação para um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/Read | Obter as propriedades de um slot de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/recover/action | Recupera Slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/resetSlotConfig/Action | Redefinir a configuração de slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/restart/Action | Reiniciar um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/restore/read | Obter a restauração dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restore/write | Restaurar slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restorefrombackupblob/action | Restaurar o slot de aplicativos Web a partir do blob de backup. |
> | Ação | Microsoft. Web/sites/Slots/restorefromdeletedapp/Action | Restaura os slots de aplicativo Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/slots/restoresnapshot/action | Restaura os Instantâneos de Slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/delete | Excluir extensões de site de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/read | Obter extensões de site de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/write | Atualizar extensões de site de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Ação | Microsoft.Web/sites/slots/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/start/Action | Iniciar um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/stop/Action | Interromper um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/sync/action | Sincronizar slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/delete | Excluir trabalhos da Web disparados de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/read | Obter trabalhos da Web disparados de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/run/action | Executar trabalhos da Web dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/usages/read | Obter usos dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/delete | Excluir conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/read | Obter conexões de rede virtual dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualizar conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/webjobs/read | Obter trabalhos da Web dos slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Write | Criar um novo slot do aplicativo Web ou atualizar um existente |
> | Ação | Microsoft.Web/sites/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Ação | Microsoft.Web/sites/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Read | Obter configurações de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/start/Action | Iniciar um aplicativo Web |
> | Ação | Microsoft.Web/sites/stop/Action | Interromper um aplicativo Web |
> | Ação | microsoft.web/sites/sync/action | Sincronizar pplicativos Web. |
> | Ação | microsoft.web/sites/syncfunctiontriggers/action | Gatilhos de função de sincronização. |
> | Ação | microsoft.web/sites/triggeredwebjobs/delete | Excluir trabalhos da Web disparados para aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/history/read | Obter o histórico de WebJobs disparados de Aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/read | Obter trabalhos da Web disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/run/action | Executar trabalhos da Web disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/usages/read | Obter usos de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/delete | Excluir as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/read | Obter as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/write | Atualizar as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/webjobs/read | Obter trabalhos da Web de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Write | Criar um novo aplicativo Web ou atualizar um existente |
> | Ação | microsoft.web/skus/read | Obter SKUs. |
> | Ação | microsoft.web/sourcecontrols/read | Obter os controles de origem. |
> | Ação | microsoft.web/sourcecontrols/write | Atualizar controles de origem. |
> | Ação | microsoft.web/unregister/action | Cancelar o registro do provedor de recursos Microsoft.Web para a assinatura. |
> | Ação | microsoft.web/validate/action | Validar. |
> | Ação | microsoft.web/verifyhostingenvironmentvnet/action | Verificar VNET no ambiente de hospedagem. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.WorkloadMonitor/components/read | Obter componentes para o recurso |
> | Ação | Microsoft.WorkloadMonitor/componentsSummary/read | Obter resumo dos componentes |
> | Ação | Microsoft.WorkloadMonitor/monitorInstances/read | Obter instâncias de monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obter resumo das instâncias do monitor |
> | Ação | Microsoft.WorkloadMonitor/monitors/read | Obter monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitors/write | Configurar monitor para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/read | Obter as configurações de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/write | Configurar configurações de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/operations/read | Obter as operações com suporte |

## <a name="next-steps"></a>Próximas etapas

- [Corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md)
- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Funções internas para recursos do Azure](built-in-roles.md)
