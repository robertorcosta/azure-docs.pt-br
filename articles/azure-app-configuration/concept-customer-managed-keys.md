---
title: Use chaves gerenciadas pelo cliente para criptografar seus dados de configuração
description: Criptografe seus dados de configuração usando chaves gerenciadas pelo cliente
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 5749b2fc58c4e1c5c75142f85a5132946714e25b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472629"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Use chaves gerenciadas pelo cliente para criptografar os dados de configuração do aplicativo
A configuração do aplicativo Azure [criptografa informações confidenciais em repouso](../security/fundamentals/encryption-atrest.md). O uso de chaves gerenciadas pelo cliente fornece proteção de dados aprimorada, permitindo que você gerencie suas chaves de criptografia.  Quando a criptografia de chave gerenciada é usada, todas as informações confidenciais na configuração do aplicativo são criptografadas com uma chave Azure Key Vault fornecida pelo usuário.  Isso fornece a capacidade de girar a chave de criptografia sob demanda.  Ele também fornece a capacidade de revogar o acesso da configuração do aplicativo Azure a informações confidenciais, revogando o acesso da instância de configuração do aplicativo à chave.

## <a name="overview"></a>Visão geral 
A configuração do aplicativo Azure criptografa informações confidenciais em repouso usando uma chave de criptografia AES de 256 bits fornecida pela Microsoft. Cada instância de configuração de aplicativo tem sua própria chave de criptografia gerenciada pelo serviço e usada para criptografar informações confidenciais. Informações confidenciais incluem os valores encontrados em pares de valores-chave.  Quando o recurso de chave gerenciado pelo cliente é ativado, a Configuração do aplicativo usa uma identidade gerenciada atribuída à instância de configuração do aplicativo para autenticar com o Azure Active Directory. A identidade gerenciada então chama o Azure Key Vault e envolve a chave de criptografia da instância de configuração do aplicativo. A chave de criptografia embrulhada é então armazenada e a chave de criptografia desembrulhada é armazenada em cache na Configuração do Aplicativo por uma hora. A configuração do aplicativo atualiza a versão desembrulhada da chave de criptografia da instância de configuração do aplicativo de hora em hora. Isso garante a disponibilidade em condições normais de funcionamento. 

>[!IMPORTANT]
> Se a identidade atribuída à instância de configuração do aplicativo não estiver mais autorizada a desembrulhar a chave de criptografia da instância ou se a chave gerenciada for excluída permanentemente, não será mais possível descriptografar informações confidenciais armazenadas no Aplicativo Instância de configuração. O uso da função [de exclusão suave](../key-vault/key-vault-ovw-soft-delete.md) do Azure Key Vault reduz a chance de excluir acidentalmente sua chave de criptografia.

Quando os usuários habilitam o recurso de chave gerenciado pelo cliente em sua instância de configuração do aplicativo Azure, eles controlam a capacidade do serviço de acessar suas informações confidenciais. A chave gerenciada serve como uma chave de criptografia raiz. Um usuário pode revogar o acesso da instância de configuração do aplicativo à sua chave gerenciada alterando sua política de acesso ao cofre principal. Quando esse acesso for revogado, a Configuração do Aplicativo perderá a capacidade de descriptografar dados do usuário dentro de uma hora. Neste ponto, a instância de configuração do aplicativo proibirá todas as tentativas de acesso. Essa situação é recuperável, concedendo ao serviço acesso à chave gerenciada mais uma vez.  Dentro de uma hora, a Configuração do Aplicativo será capaz de descriptografar dados do usuário e operar em condições normais.

>[!NOTE]
>Todos os dados de configuração do aplicativo Azure são armazenados por até 24 horas em um backup isolado. Isso inclui a chave de criptografia desembrulhada. Esses dados não estão disponíveis imediatamente para a equipe de serviço ou serviço. No caso de uma restauração de emergência, a configuração do aplicativo Azure se rerevogará dos dados-chave gerenciados.

## <a name="requirements"></a>Requisitos
Os seguintes componentes são necessários para habilitar com sucesso o recurso de chave gerenciado pelo cliente para a configuração do aplicativo Azure:
- Exemplo de configuração do aplicativo Azure de nível padrão
- Azure Key Vault com recursos de exclusão suave e proteção contra purga ativados
- Uma chave RSA ou RSA-HSM dentro do cofre de chaves
    - A chave não deve ser expirada, deve ser habilitada, e deve ter recursos de embrulho e desembrulho habilitados

Uma vez configurados esses recursos, restam duas etapas para permitir que a configuração do aplicativo Azure use a chave Key Vault:
1. Atribuir uma identidade gerenciada à instância de configuração do aplicativo Azure
2. Conceda `GET`a `WRAP`identidade `UNWRAP` e as permissões na política de acesso do Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Habilite a criptografia de chave gerenciada pelo cliente para a instância de configuração do aplicativo Do Zure
Para começar, você precisará de uma instância de configuração do aplicativo Azure configurada corretamente. Se você ainda não tiver uma instância de configuração do aplicativo disponível, siga uma dessas partidas rápidas para configurar uma:
- [Crie um aplicativo ASP.NET Core com a configuração do aplicativo Azure](quickstart-aspnet-core-app.md)
- [Crie um aplicativo .NET Core com a configuração do aplicativo Azure](quickstart-dotnet-core-app.md)
- [Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure](quickstart-dotnet-app.md)
- [Criar um aplicativo Java Spring com a Configuração de Aplicativos do Azure](quickstart-java-spring-app.md)

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as instruções de linha de comando contidas neste artigo.  Ele tem ferramentas comuns do Azure pré-instaladas, incluindo o SDK do .NET Core. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com.  Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Criar e configurar um Cofre de Chaves Azure
1. Crie um Cofre de Chaves Azure usando o Azure CLI.  Observe que `vault-name` `resource-group-name` ambos são fornecidos pelo usuário e devem ser únicos.  Usamos `contoso-vault` e `contoso-resource-group` nestes exemplos.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Habilite a proteção de exclusão suave e purga para o Cofre de Chaves. Substitua os nomes do`contoso-vault`Key Vault`contoso-resource-group`( ) e do Grupo de Recursos ( ) criadona etapa 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Crie uma chave key vault. Forneça um `key-name` exclusivo para esta chave e substitua`contoso-vault`os nomes do Key Vault ( ) criado na etapa 1. Especifique `RSA` `RSA-HSM` se você prefere ou criptografia.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A saída deste comando mostra o ID de chave ("criança") para a chave gerada.  Anote o ID chave para usar mais tarde neste exercício.  A id-chave tem `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`a forma: .  O ID-chave tem três componentes importantes:
    1. KEY Vault URI: 'https://{my key vault}.vault.azure.net
    1. Nome da chave do cofre: {Nome da chave}
    1. Versão chave do Key Vault: {Versão chave}

1. Crie uma identidade gerenciada atribuída ao sistema usando o Azure CLI, substituindo o nome da instância de configuração do aplicativo e do grupo de recursos usado nas etapas anteriores. A identidade gerenciada será usada para acessar a chave gerenciada. Usamos `contoso-app-config` para ilustrar o nome de uma instância de configuração do aplicativo:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    A saída deste comando inclui o ID principal ("principalId") e o ID do inquilino ("tenandId") do sistema atribuído identidade.  Isso será usado para conceder acesso à identidade da chave gerenciada.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. A identidade gerenciada da instância de configuração do aplicativo Azure precisa acessar a chave para executar a validação de chaves, criptografia e descriptografia. O conjunto específico de ações a que `GET` `WRAP`precisa `UNWRAP` de acesso inclui: , e para chaves.  A concessão do acesso requer o ID principal da identidade gerenciada da instância de configuração do aplicativo. Esse valor foi obtido na etapa anterior. É mostrado abaixo `contoso-principalId`como . Conceder permissão à chave gerenciada usando a linha de comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Uma vez que a instância de configuração do aplicativo Azure possa acessar a chave gerenciada, podemos habilitar o recurso de chave gerenciado pelo cliente no serviço usando o Cli do Azure. Relembre as seguintes propriedades registradas `key name` `key vault URI`durante as etapas de criação da chave: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

A instância de configuração do aplicativo Azure está agora configurada para usar uma chave gerenciada pelo cliente armazenada no Azure Key Vault.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você configurou a instância de configuração do aplicativo Azure para usar uma chave gerenciada pelo cliente para criptografia.  Saiba como [integrar seu serviço com o Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).