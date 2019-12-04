---
title: Recomendações da central de segurança do Azure para computadores & aplicativos
description: Recomendações de segurança da central de segurança do Azure que ajudam a proteger suas máquinas virtuais, computadores, aplicativos Web e ambientes de serviço de aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782158"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Recomendações de computação e aplicativo – guia de referência

Este artigo fornece a lista completa das recomendações que você pode ver na central de segurança do Azure em relação aos seguintes tipos de recursos:

* VMs e computadores
* Conjuntos de Dimensionamento de Máquina Virtual
* Serviços em Nuvem
* Serviços de aplicativos
* Contêineres
* Recursos de computação

Para obter uma explicação de como encontrá-las e como resolvê-las, consulte [aqui](security-center-virtual-machine-protection.md).

## Recomendações de computação e aplicativo<a name="compute-and-app-recs"></a>
|Tipo de recurso|Classificação de segurança|Recomendações|Descrição|
|----|----|----|----|
|serviço de aplicativo|20|Aplicativo Web deve ser acessível somente por HTTPS|Limitar o acesso de aplicativos da Web somente por HTTPS.|
|serviço de aplicativo|20|O aplicativo de funções deve ser acessível apenas por HTTPS|Limite o acesso de aplicativos de função somente por HTTPS.|
|serviço de aplicativo|5|Os logs de diagnóstico nos serviços de aplicativos devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|serviço de aplicativo|10|Depuração remota deve ser desativada para o aplicativo da Web|Desative a depuração para aplicativos da Web se você não precisar mais usá-lo. A depuração remota exige que as portas de entrada estejam abertas em um aplicativo de funções.|
|serviço de aplicativo|10|Depuração remota deve ser desativada para o aplicativo de função|Desative a depuração para o aplicativo de função, se você não precisar mais usá-lo. A depuração remota exige que as portas de entrada estejam abertas em um aplicativo de funções.|
|serviço de aplicativo|10|Não permita que todos os recursos ('*') acessem o aplicativo| Não permita o conjunto do parâmetro WEBSITE_LOAD_CERTIFICATES para "". Definir o parâmetro como "" significa que todos os certificados são carregados no armazenamento de certificados pessoais de aplicativos da web. Isso pode levar a abuso do princípio de privilégio mínimo porque é improvável que o site precise de acesso a todos os certificados em runtime.|
|serviço de aplicativo|20|O CORS não deve permitir que todos os recursos acessem seus aplicativos da Web|Permitir que apenas os domínios necessários interajam com seu aplicativo da web. O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo Web.|
|serviço de aplicativo|20|O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções| Permitir que apenas os domínios necessários interajam com seu aplicativo de função. O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo de funções.|
|Computar recursos (lote)|1|As regras de alerta de métrica devem ser configuradas em contas do lote|Configure as regras de alerta de métrica na conta do Lote e ative as métricas Pool Delete Complete Events e Pool Delete Start Events|
|Recursos de computação (estrutura de serviço)|10|Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente|Realize a autenticação do cliente somente através do Active Directory do Azure no Service Fabric.|
|Recursos de computação (conta de automação)|5|As variáveis da conta de automação devem ser criptografadas|Ativar a criptografia de ativos variáveis da conta de automação ao armazenar dados confidenciais.|
|Recursos de computação (pesquisa)|5|Habilitação de auditoria de logs de diagnóstico para serviços de pesquisa|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Recursos de computação (barramento de serviço)|5|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Recursos de computação (análise de fluxo)|5|Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Computar recursos (lote)|5|Ativar logs de diagnóstico em contas em lote|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Recursos de computação (hub de eventos)|5|Os logs de diagnóstico no Hub de eventos devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Recursos de computação (aplicativos lógicos)|5|Ativar logs de diagnósticos em aplicativos lógicos|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Recursos de computação (estrutura de serviço)|15|Defina a propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção (Nenhum, Sinal e EncryptAndSign) para comunicação de nó a nó usando um certificado de cluster principal. Defina o nível de proteção para garantir que todas as mensagens de nó a nó sejam criptografadas e assinadas digitalmente. |
|Recursos de computação (barramento de serviço)|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey, do namespace do Service Bus |Os clientes do Service Bus não devem usar uma diretiva de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica.|
|Recursos de computação (hub de eventos)|1|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos|Os clientes do Hub de Eventos não devem usar uma política de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica.|
|Recursos de computação (hub de eventos)|5|As regras de autorização na entidade do hub de eventos devem ser definidas|Auditar regras de autorização na entidade do Hub de Eventos para conceder acesso com privilégios mínimos.|
|Computador|50|Instale o agente de monitoramento em suas máquinas|Instale o agente de monitoramento para ativar a coleta de dados, a varredura de atualizações, a varredura da linha de base e a proteção do ponto de extremidade em cada máquina.|
|Computador|50|Habilitar o provisionamento automático e coleta de dados para suas assinaturas |Habilite o provisionamento automático e coleta de dados para computadores em suas assinaturas para habilitar a coleta de dados, as atualizações de varredura, linha de base e proteção de ponto de extremidade em cada computador adicionado às suas assinaturas.|
|Computador|40|Resolver problemas de integridade do agente de monitoramento em suas máquinas|Para obter a proteção completa da Central de Segurança, resolva os problemas do agente de monitoramento em suas máquinas seguindo as instruções no guia de Resolução de Problemas.| 
|Computador|40|Resolver problemas de integridade da proteção do endpoint em suas máquinas|Para obter a proteção completa da Central de Segurança, resolva os problemas do agente de monitoramento em suas máquinas seguindo as instruções no guia de solução de problemas.|
|Computador|40|Solucione problemas de dados de varredura ausentes em suas máquinas|Solucione problemas de dados de varredura ausentes em máquinas virtuais e computadores. A falta de dados de varredura em suas máquinas resulta em avaliações de segurança ausentes, como varredura de atualização, varredura de linha de base e varredura de solução de proteção de ponto de extremidade ausente.|
|Computador|40|As atualizações do sistema devem ser instaladas em suas máquinas|Instale a segurança do sistema ausente e atualizações críticas para proteger suas máquinas virtuais Windows e Linux e computadores
|Computador|15|Adicione um firewall do aplicativo Web| Implemente uma solução WAF (firewall de aplicativo da web) para proteger seus aplicativos da web. |
|Computador|40|Atualize a versão do sistema operacional para suas funções de serviço em nuvem|Atualize a versão do sistema operacional (OS) para suas funções de serviço em nuvem para a versão mais recente disponível para sua família de sistemas operacionais.|
|Computador|35|As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas|Corrigir vulnerabilidades na configuração de segurança em suas máquinas para protegê-las contra ataques.|
|Computador|35|Corrigir vulnerabilidades na configuração de segurança em seus contêineres|Corrigir vulnerabilidades na configuração de segurança em computadores com o Docker instalado para protegê-los contra ataques.|
|Computador|25|Ativar controles de aplicativos adaptativos|Ative o controle de aplicativos para controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Isso ajudará a proteger suas VMs contra malware. O Security Center usa aprendizado de máquina para analisar os aplicativos em execução em cada VM e ajuda você a aplicar regras de permissão usando essa inteligência. Esse recurso simplifica o processo de configuração e manutenção de regras de permissão do aplicativo.|
|Computador|20|Instale a solução de proteção de ponto de extremidade em suas máquinas|Instale uma solução de proteção de ponto de extremidade em suas máquinas virtuais para protegê-las contra ameaças e vulnerabilidades.|
|Computador|20|Reinicie suas máquinas para aplicar atualizações do sistema|Reinicie suas máquinas para aplicar as atualizações do sistema e proteger a máquina contra vulnerabilidades.|
|Computador|15|A criptografia de disco deve ser aplicada em máquinas virtuais|Criptografe seus discos de máquina virtual usando a Criptografia de Disco do Azure para máquinas virtuais Windows e Linux. O Azure Disk Encryption (ADE) aproveita o recurso BitLocker padrão do setor do Windows eo recurso DM-Crypt do Linux para fornecer criptografia de disco de dados e sistema operacional para ajudar a proteger e proteger seus dados e ajudar a cumprir seus compromissos de conformidade e segurança organizacional no cofre de chaves do Azure do cliente. Quando os requisitos de conformidade e segurança exigirem que você criptografe os dados de ponta a ponta usando suas chaves de criptografia, incluindo a criptografia do disco efêmero (localmente conectado temporariamente), use a criptografia de disco do Azure. Como alternativa, por padrão, os discos gerenciados são criptografados em repouso por padrão, usando a Criptografia do Serviço de Armazenamento do Azure, em que as chaves de criptografia são chaves gerenciadas pela Microsoft no Azure. Se isso atender aos seus requisitos de conformidade e segurança, você poderá aproveitar a criptografia de disco gerenciada padrão para atender aos seus requisitos.|
|Computador|30|Instalar uma solução de avaliação de vulnerabilidades em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidades em suas máquinas virtuais|
|Computador|15|Adicione um firewall do aplicativo Web| Implemente uma solução WAF (firewall de aplicativo da web) para proteger seus aplicativos da web. |
|Computador|30|As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades|As máquinas virtuais para as quais uma solução de terceiros de avaliação de vulnerabilidade é implantada estão sendo continuamente avaliadas em relação às vulnerabilidades do aplicativo e do sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para mais informações como parte da recomendação.|
|Computador|30|Instalar uma solução de avaliação de vulnerabilidades em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidades em suas máquinas virtuais|
|Computador|1|As máquinas virtuais devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para que suas máquinas virtuais forneçam aprimoramentos de segurança como: RBAC (controle de acesso mais forte), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao key Vault para segredos, Autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança. |
|Computador|30|As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades|As máquinas virtuais para as quais uma solução de terceiros de avaliação de vulnerabilidade é implantada estão sendo continuamente avaliadas em relação às vulnerabilidades do aplicativo e do sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para mais informações como parte da recomendação.|
|Conjunto de escala de máquina virtual |4|Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados|Habilite os logs e retenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação. Além disso, será útil quando ocorrer um incidente de segurança ou sua rede estiver comprometida.|
|Conjunto de escala de máquina virtual|35|As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Corrija as vulnerabilidades na configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |
|Conjunto de escala de máquina virtual|5|Corrigir falhas de integridade da proteção do ponto de extremidade nos conjuntos de dimensionamento de máquinas virtuais|Corrija as falhas de integridade da proteção do ponto de extremidade nos conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ameaças e vulnerabilidades. |
|Conjunto de escala de máquina virtual|10|O Endpoint Protection deve ser instalado em máquinas virtuais|Instale uma solução de proteção do ponto de extremidade nos conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ameaças e vulnerabilidades. |
|Conjunto de escala de máquina virtual|40|As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Instale a segurança do sistema ausente e as atualizações críticas para proteger os conjuntos de dimensionamento de máquinas virtuais do Windows e Linux. |
|


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)
