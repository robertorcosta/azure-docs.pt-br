---
title: Controles de segurança
description: Encontre uma lista de verificação de controles de segurança para avaliar o Azure App Service para sua organização.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671443"
---
# <a name="security-controls-for-azure-app-service"></a>Controles de segurança para o Serviço de Aplicativos Azure

Este artigo documenta os controles de segurança incorporados ao Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Suporte a ponto final de serviço| Sim | Disponível para App Service.| [Restrições de acesso ao serviço do aplicativo Azure](app-service-ip-restrictions.md)
| Suporte à injeção VNet| Sim | Os Ambientes de Serviço de Aplicativos são implementações privadas do App Service dedicadas a um único cliente injetado na rede virtual de um cliente. | [Introdução aos Ambientes de Serviço de Aplicativo](environment/intro.md)
| Suporte a isolamento de rede e firewall| Sim | Para a variação pública de vários locatários do App Service, os clientes podem configurar ACLs de rede (Restrições ip) para bloquear o tráfego de entrada permitido.  Os Ambientes de Serviço de Aplicativos são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com NSGs. | [Restrições de acesso ao serviço do aplicativo Azure](app-service-ip-restrictions.md)
| Suporte forçado de tunelamento| Sim | Os ambientes de serviço de aplicativos podem ser implantados na rede virtual de um cliente, onde o túnel forçado é configurado. | [Configurar seu Ambiente de Serviço de Aplicativo com tunelamento forçado](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | O App Service integra-se com insights de aplicativos para idiomas que suportam insights de aplicativos (Full .NET Framework, .NET Core, Java e Node.JS).  Consulte [o desempenho do Monitor Azure App Service](../azure-monitor/app/azure-web-apps.md). O App Service também envia métricas de aplicativos para o Azure Monitor. | [Monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
| Registro e auditoria de plano de controle e gerenciamento| Sim | Todas as operações de gerenciamento realizadas em objetos do App Service ocorrem via [Azure Resource Manager](../azure-resource-manager/index.yml). Os registros históricos dessas operações estão disponíveis tanto no portal quanto através da CLI. | [Operações do provedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az monitora o registro de atividades](/cli/azure/monitor/activity-log)
| Registro e auditoria de data plan | Não | O plano de dados para o App Service é um compartilhamento remoto de arquivos que contém o conteúdo do site implantado pelo cliente.  Não há auditoria do compartilhamento de arquivos remotos. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações |  Documentação
|---|---|--|
| Autenticação| Sim | Os clientes podem criar aplicativos no App Service que se integram automaticamente ao [Azure Active Directory (Azure AD),](../active-directory/index.yml) bem como a outros provedores de identidade compatíveis com OAuth Para acesso gerencial aos ativos do App Service, todo o acesso é controlado por uma combinação de funções de principal autenticado do Azure AD e do Azure Resource Manager RBAC. | [Autenticação e autorização no Serviço de Aplicativo do Azure](overview-authentication-authorization.md)
| Autorização| Sim | Para acesso gerencial aos ativos do App Service, todo o acesso é controlado por uma combinação de funções principal autenticada do Azure AD e RBAC do Azure Resource Manager.  | [Autenticação e autorização no Serviço de Aplicativo do Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O conteúdo do arquivo do site é armazenado no Azure Storage, que criptografa automaticamente o conteúdo em repouso. <br><br>Os segredos fornecidos pelo cliente são criptografados em repouso. Os segredos são criptografados em repouso enquanto armazenados em bancos de dados de configuração do App Service.<br><br>Discos conectados localmente podem ser usados opcionalmente como armazenamento temporário por sites (D:\local e %TMP%). Os discos conectados localmente não são criptografados em repouso. | [Criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Os clientes podem optar por armazenar segredos de aplicativos no Key Vault e recuperá-los em tempo de execução. | [Use as referências do Key Vault para o serviço de aplicativo e as funções do Azure (visualização)](app-service-key-vault-references.md)
| Criptografia de nível de coluna (Azure Data Services)| N/D | |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | Os clientes podem configurar sites para exigir e usar HTTPS para tráfego de entrada.  | [Como fazer um Azure App Service HTTPS somente](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (post no blog)
| Chamadas criptografadas à API| Sim | As chamadas de gerenciamento para configurar o Serviço de Aplicativos ocorrem através de chamadas [do Azure Resource Manager](../azure-resource-manager/index.yml) em HTTPS. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração do App Service pode ser exportado como um modelo de Gerenciador de Recursos do Azure e versão ao longo do tempo. Para operações em tempo de execução, os clientes podem manter várias versões ao vivo diferentes de um aplicativo usando o recurso de slots de implantação do App Service. | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
