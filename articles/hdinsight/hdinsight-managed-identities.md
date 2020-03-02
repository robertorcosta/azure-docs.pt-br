---
title: Identidades gerenciadas no Azure HDInsight
description: Fornece uma visão geral da implementação de identidades gerenciadas no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206003"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades gerenciadas no Azure HDInsight

Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (AD do Azure) cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar entidades de serviço no Azure AD ou manter credenciais como certificados.

Identidades gerenciadas são usadas no Azure HDInsight para acessar os serviços de domínio do Azure AD ou acessar arquivos no Azure Data Lake Storage Gen2 quando necessário.

Há dois tipos de identidades gerenciadas: atribuído pelo usuário e atribuído pelo sistema. O Azure HDInsight dá suporte apenas a identidades gerenciadas atribuídas pelo usuário. O HDInsight não oferece suporte a identidades gerenciadas atribuídas pelo sistema. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure, que você pode atribuir a uma ou mais instâncias de serviço do Azure. Por outro lado, uma identidade gerenciada atribuída pelo sistema é criada no Azure AD e, em seguida, habilitada diretamente em uma instância de serviço do Azure específica automaticamente. A vida dessa identidade gerenciada atribuída pelo sistema é então vinculada à vida útil da instância do serviço em que está habilitada.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerenciada do HDInsight

No Azure HDInsight, as identidades gerenciadas são provisionadas em cada nó do cluster. No entanto, esses componentes de identidade só podem ser usados pelo serviço HDInsight. Atualmente, não há um método com suporte para gerar tokens de acesso usando as identidades gerenciadas instaladas em nós de cluster HDInsight. Para alguns serviços do Azure, as identidades gerenciadas são implementadas com um ponto de extremidade que você pode usar para adquirir tokens de acesso para interagir com outros serviços do Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Identidades gerenciadas podem ser criadas com qualquer um dos seguintes métodos:

* [Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [PowerShell do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

As etapas restantes para configurar a identidade gerenciada dependem do cenário em que serão usadas.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade gerenciada no Azure HDInsight

Identidades gerenciadas são usadas no Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções de instalação e configuração detalhadas:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Criptografia de disco de chave gerenciada pelo cliente](disk-encryption.md)

## <a name="faq"></a>Perguntas frequentes
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontecerá se eu excluir a identidade gerenciada após a criação do cluster?
O cluster terá problemas quando a identidade gerenciada for necessária. Atualmente, não há nenhuma maneira de atualizar ou alterar a identidade de gerenciamento depois que o cluster é criado. Portanto, nossa recomendação é garantir que a identidade gerenciada não seja excluída durante o tempo de execução do cluster. Como alternativa, você pode recriar o cluster e atribuir uma nova identidade gerenciada.

## <a name="next-steps"></a>Próximas etapas

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
