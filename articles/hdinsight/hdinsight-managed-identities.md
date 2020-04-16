---
title: Identidades gerenciadas no Azure HDInsight
description: Fornece uma visão geral da implementação de identidades gerenciadas no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408935"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades gerenciadas no Azure HDInsight

Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (Azure AD), cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar os diretores de serviço no Azure AD. Ou manter credenciais como certificados.

As identidades gerenciadas são usadas no Azure HDInsight para acessar serviços de domínio Azure AD ou arquivos de acesso no Azure Data Lake Storage Gen2 quando necessário.

Existem dois tipos de identidades gerenciadas: atribuídas pelo usuário e atribuídas ao sistema. O Azure HDInsight suporta apenas identidades gerenciadas pelo usuário. O HDInsight não suporta identidades gerenciadas atribuídas ao sistema. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure, que você pode atribuir a uma ou mais instâncias de serviço do Azure. Em contraste, uma identidade gerenciada atribuída ao sistema é criada no Azure AD e, em seguida, ativada diretamente em uma instância de serviço azure específica automaticamente. A vida dessa identidade gerenciada atribuída ao sistema está então ligada à vida da instância de serviço em que está habilitada.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerenciada pelo HDInsight

No Azure HDInsight, as identidades gerenciadas são provisionadas em cada nó do cluster. Esses componentes de identidade, no entanto, só podem ser utilizáveis pelo serviço HDInsight. Atualmente, não há nenhum método suportado para gerar tokens de acesso usando as identidades gerenciadas instaladas nos álos de cluster HDInsight. Para alguns serviços do Azure, as identidades gerenciadas são implementadas com um ponto final que você pode usar para adquirir tokens de acesso. Use os tokens para interagir com outros serviços do Azure por conta própria.

## <a name="create-a-managed-identity"></a>Crie uma identidade gerenciada

As identidades gerenciadas podem ser criadas com qualquer um dos seguintes métodos:

* [Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [PowerShell do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

As etapas restantes para a configuração da identidade gerenciada dependem do cenário em que ela será usada.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade gerenciados no Azure HDInsight

As identidades gerenciadas são usadas no Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções detalhadas de configuração e configuração:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pacote de segurança corporativa](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Criptografia de disco de chave gerenciada pelo cliente](disk-encryption.md)

## <a name="faq"></a>Perguntas frequentes

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontece se eu excluir a identidade gerenciada após a criação do cluster?

Seu cluster terá problemas quando a identidade gerenciada for necessária. No momento, não há nenhuma maneira de atualizar ou alterar uma identidade gerenciada após a criação do cluster. Portanto, nossa recomendação é garantir que a identidade gerenciada não seja excluída durante o tempo de execução do cluster. Ou você pode recriar o cluster e atribuir uma nova identidade gerenciada.

## <a name="next-steps"></a>Próximas etapas

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
