---
title: Grupos secure Azure Data Explorer no Azure
description: Saiba como proteger clusters no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373360"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Grupos secure Azure Data Explorer no Azure

Este artigo fornece uma introdução à segurança no Azure Data Explorer para ajudá-lo a proteger seus dados e recursos na nuvem e atender às necessidades de segurança de seus negócios. É importante manter seus clusters seguros. Proteger seus clusters inclui um ou mais recursos do Azure que incluem acesso e armazenamento seguros. Este artigo fornece informações para ajudá-lo a manter seu cluster seguro.

## <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas dos recursos do Azure

Um desafio comum ao criar aplicativos em nuvem é o gerenciamento de credenciais em seu código para autenticação em serviços em nuvem. Proteger as credenciais é uma tarefa importante. As credenciais não devem ser armazenadas em estações de trabalho de desenvolvedores ou verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais, os segredos e outras chaves, mas seu código precisa se autenticar no Key Vault para recuperá-los.

O azure Active Directory (Azure AD) gerenciava identidades para recursos do Azure resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código. Para obter mais informações sobre esse serviço, consulte identidades gerenciadas para a página de visão geral dos [recursos do Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="data-encryption"></a>Criptografia de dados

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[A Criptografia de Disco Do Azure](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e proteger seus dados para atender aos seus compromissos de segurança organizacional e conformidade. Ele fornece criptografia de volume para o Sistema Operacional e discos de dados das máquinas virtuais do seu cluster. O Azure Disk Encryption também se integra ao [Azure Key Vault](/azure/key-vault/), que nos permite controlar e gerenciar as chaves e segredos de criptografia de disco e garantir que todos os dados nos discos VM sejam criptografados. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados. Você pode gerenciar a criptografia de seus dados no nível de armazenamento com suas próprias chaves. Uma chave gerenciada pelo cliente é usada para proteger e controlar o acesso à chave de criptografia raiz, que é usada para criptografar e descriptografar todos os dados. As chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Use o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar uma API do Azure Key Vault para gerar chaves. O cluster Azure Data Explorer e o Azure Key Vault devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](/azure/key-vault/key-vault-overview). Para obter uma explicação detalhada sobre as chaves gerenciadas pelo cliente, consulte [as chaves gerenciadas pelo cliente com o Azure Key Vault](/azure/storage/common/storage-service-encryption). Configure as chaves gerenciadas pelo cliente no cluster Do Azure Data Explorer usando [C#](/azure/data-explorer/customer-managed-keys-csharp) ou o [modelo do Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure Active Directory (Azure AD). Para configurar chaves gerenciadas pelo cliente no portal Azure, você precisa configurar uma identidade **gerenciada do SystemAssigned** para o seu cluster conforme detalhado em [Configurar identidades gerenciadas para o cluster Azure Data Explorer](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazene chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar as chaves gerenciadas pelo cliente em um cluster, use um Cofre de Chaves do Azure para armazenar suas chaves. Você deve habilitar as propriedades **Soft Delete** e **Não Expurguir** no cofre de chaves. O cofre-chave deve estar localizado na mesma assinatura do cluster. O Azure Data Explorer usa identidades gerenciadas para recursos do Azure para autenticar no cofre-chave para operações de criptografia e descriptografia. Identidades gerenciadas não suportam cenários de diretórios cruzados.

#### <a name="rotate-customer-managed-keys"></a>Gire as chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o cluster para usar a nova chave URI. Girar a chave não desencadeia a recriptografia de dados no cluster. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) ou [Azure Key Vault CLI](/cli/azure/keyvault). A revogação do acesso bloqueia o acesso a todos os dados no nível de armazenamento do cluster, uma vez que a chave de criptografia é, consequentemente, inacessível pelo Azure Data Explorer.

> [!Note]
> Quando o Azure Data Explorer identificar que o acesso a uma chave gerenciada pelo cliente é revogado, ele suspenderá automaticamente o cluster para excluir quaisquer dados armazenados em cache. Uma vez que o acesso à chave é devolvido, o cluster precisa ser retomado manualmente.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Usando [o RBAC (Role-based Access Control, controle de acesso baseado em função),](/azure/role-based-access-control/overview)você pode segregar funções dentro de sua equipe e conceder apenas o acesso necessário aos usuários de cluster. Em vez de dar permissões irrestritas a todos no cluster, você pode permitir apenas certas ações. Você pode configurar o controle de [acesso para os bancos de dados](/azure/data-explorer/manage-database-permissions) no portal [Azure,](/azure/role-based-access-control/role-assignments-portal)usando o [Azure CLI](/azure/role-based-access-control/role-assignments-cli)ou o [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Próximas etapas

* [Proteja seu cluster no Azure Data Explorer - Portal,](manage-cluster-security.md) ativando a criptografia em repouso.
* [Configure identidades gerenciadas para o cluster Azure Data Explorer](managed-identities.md)
* [Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configure as chaves gerenciadas pelo cliente usando C #](customer-managed-keys-csharp.md)

