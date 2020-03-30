---
title: Recomendações de segurança para armazenamento em fila
titleSuffix: Azure Storage
description: Saiba mais sobre as recomendações de segurança para armazenamento em fila. A implementação dessa orientação ajudará você a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060891"
---
# <a name="security-recommendations-for-queue-storage"></a>Recomendações de segurança para armazenamento em fila

Este artigo contém recomendações de segurança para armazenamento em fila. A implementação dessas recomendações ajudará você a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do provedor de serviços, leia [responsabilidades compartilhadas para computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção de seus recursos no Azure. Para obter informações sobre o Azure Security Center, consulte [o Centro de Segurança do Azure?](../../security-center/security-center-intro.md).

O Azure Security Center analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, fornece recomendações sobre como lidar com eles. Para obter mais informações sobre as recomendações do Azure Security Center, consulte [recomendações de segurança no Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Use o modelo de implantação do Azure Resource Manager | Crie novas contas de armazenamento usando o modelo de implantação do Azure Resource Manager para aprimoramentos importantes de segurança, incluindo controle de acesso superior (RBAC) e auditoria, implantação e controle baseados no Gerenciador de Recursos, acesso a identidades gerenciadas, acesso ao Azure Key Vault para segredos e a autenticação e autorização baseadas no Azure AD para acesso aos dados e recursos do Azure Storage. Se possível, migre as contas de armazenamento existentes que usam o modelo clássico de implantação para usar o Azure Resource Manager. Para obter mais informações sobre o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview). | - |
| Habilite a opção **de transferência Segura necessária** em todas as suas contas de armazenamento | Quando você habilita rativar a opção **de transferência Segura necessária,** todas as solicitações feitas contra a conta de armazenamento devem ocorrer por conexões seguras. Quaisquer solicitações feitas sobre HTTP falharão. Para obter mais informações, consulte [Exigir transferência segura no Armazenamento Azure](../common/storage-require-secure-transfer.md). | [Sim](../../security-center/security-center-sql-service-recommendations.md) |
| Habilite proteção avançada contra ameaças para todas as suas contas de armazenamento | A proteção avançada contra ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de segurança são acionados no Azure Security Center quando ocorrem anomalias na atividade e também são enviados por e-mail para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. Para obter mais informações, consulte [Proteção avançada contra ameaças para armazenamento Azure](../common/storage-advanced-threat-protection.md). | [Sim](../../security-center/security-center-sql-service-recommendations.md) |
| Limitar tokens de assinatura de acesso compartilhado (SAS) apenas para conexões HTTPS | Exigir HTTPS quando um cliente usa um token SAS para acessar dados de fila ajuda a minimizar o risco de escutas. Para obter mais informações, consulte [o acesso limitado ao Grant aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS).](../common/storage-sas-overview.md) | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Use o Azure Active Directory (Azure AD) para autorizar o acesso aos dados da fila | O Azure AD oferece segurança superior e facilidade de uso sobre a Chave Compartilhada para autorizar solicitações ao armazenamento na fila. Para obter mais informações, consulte [Autorizar o acesso a blobs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md). | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um diretor de segurança Ad do Azure via RBAC | Ao atribuir uma função a um usuário, grupo ou aplicativo, conceda que o principal de segurança apenas as permissões necessárias para que eles realizem suas tarefas. Limitar o acesso aos recursos ajuda a evitar o uso indevido e malicioso de seus dados. | - |
| Proteja as chaves de acesso da sua conta com o Azure Key Vault | A Microsoft recomenda o uso do Azure AD para autorizar solicitações ao Azure Storage. No entanto, se você deve usar a autorização de chave compartilhada, então proteja as chaves da sua conta com o Azure Key Vault. Você pode recuperar as chaves do cofre de chaves em tempo de execução, em vez de salvá-las com sua aplicação. | - |
| Regenerar as chaves da sua conta periodicamente | Girar as chaves da conta reduz periodicamente o risco de expor seus dados a atores mal-intencionados. | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um SAS | Ao criar um SAS, especifique apenas as permissões exigidas pelo cliente para executar sua função. Limitar o acesso aos recursos ajuda a evitar o uso indevido e malicioso de seus dados. | - |
| Tenha um plano de revogação em vigor para qualquer SAS que você emitir aos clientes | Se um SAS for comprometido, você vai querer revogar esse SAS o mais rápido possível. Para revogar uma delegação de usuários SAS, revogue a chave da delegação do usuário para invalidar rapidamente todas as assinaturas associadas a essa chave. Para revogar um SAS de serviço associado a uma política de acesso armazenado, você pode excluir a diretiva de acesso armazenado, renomear a diretiva ou alterar seu tempo de validade para um tempo que está no passado. Para obter mais informações, consulte [o acesso limitado ao Grant aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS).](../common/storage-sas-overview.md)  | - |
| Se um SAS de serviço não estiver associado a uma política de acesso armazenada, defina o tempo de validade para uma hora ou menos | Um SAS de serviço que não esteja associado a uma política de acesso armazenado não pode ser revogado. Por essa razão, recomenda-se limitar o tempo de validade para que o SAS seja válido por uma hora ou menos. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Habilite regras de firewall | Configure regras de firewall para limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços ou intervalos IP especificados ou de uma lista de sub-redes em uma Rede Virtual Azure (VNet). Para obter mais informações sobre a configuração de regras de firewall, consulte [as configurações de proxy e firewall do Azure File Sync](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Permita que serviços confiáveis da Microsoft acessem a conta de armazenamento | A apelação de regras de firewall para sua conta de armazenamento bloqueia solicitações recebidas de dados por padrão, a menos que as solicitações tenham origem em um serviço operando dentro de uma Rede Virtual (VNet) do Azure ou de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante. Você pode permitir solicitações de outros serviços do Azure adicionando uma exceção para permitir que serviços confiáveis da Microsoft acessem a conta de armazenamento. Para obter mais informações sobre como adicionar uma exceção para serviços confiáveis da Microsoft, consulte [as configurações de proxy e firewall do Azure File Sync](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Use pontos finais privados | Um ponto final privado atribui um endereço IP privado da sua Rede Virtual (VNet) ao armazenamento. Ele protege todo o tráfego entre seu VNet e a conta de armazenamento por um link privado. Para obter mais informações sobre pontos finais privados, consulte [Conecte-se privadamente a uma conta de armazenamento usando o Azure Private Endpoint](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Limitar o acesso da rede a redes específicas | Limitar o acesso da rede a redes que hospedam clientes que necessitam de acesso reduz a exposição de seus recursos a ataques de rede. | [Sim](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Registro/monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Acompanhe como as solicitações são autorizadas | Habilite o registro de armazenamento do Azure para rastrear como cada solicitação feita contra o Azure Storage foi autorizada. Os registros indicam se uma solicitação foi feita anonimamente, usando um token OAuth 2.0, usando chave compartilhada ou usando uma assinatura de acesso compartilhado (SAS). Para obter mais informações, consulte [o registro de análise saqueadores do Azure Storage](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Próximas etapas

- [Documentação de segurança do Azure](https://docs.microsoft.com//azure/security/)
- [Documentação de desenvolvimento segura](https://docs.microsoft.com/azure/security/develop/).
