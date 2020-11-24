---
title: Configurar o Atestado do Azure com a CLI do Azure
description: Como configurar um provedor de atestado usando a CLI do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663965"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Início Rápido: Configurar o Atestado do Azure com a CLI do Azure

Introdução ao Atestado do Azure usando a CLI do Azure para configurar o atestado.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Introdução

1. Use o seguinte comando para entrar no Azure:

   ```azurecli
   az login
   ```

1. Se necessário, alterne para a assinatura do Atestado do Azure:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registre o provedor de recursos Microsoft.Attestation na assinatura com o comando [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Para obter mais informações sobre os provedores de recursos do Azure e como configurá-los e gerenciá-los, confira [Tipos e provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Você só precisa registrar um provedor de recursos uma vez para uma assinatura.

1. Crie um grupo de recursos para o provedor de atestado. Coloque outros recursos do Azure no mesmo grupo de recursos, incluindo uma máquina virtual com uma instância do aplicativo cliente. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou use um grupo de recursos existente:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Criar e gerenciar um provedor de atestado

Siga este procedimento para criar e gerenciar um provedor de atestado.

1. Execute o comando [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) para criar um provedor de atestado:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   O parâmetro **--certs-input-path** especifica um conjunto de chaves de autenticação confiáveis. Se você especificar um nome de arquivo para esse parâmetro, o provedor de atestado precisará ser configurado somente com políticas no formato JWT assinado. Caso contrário, a política poderá ser configurada no formato de texto ou em um formato JWT não assinado. Para obter informações sobre o JWT, confira [Conceitos básicos](basic-concepts.md). Para obter exemplos de certificado, confira [Exemplos de um certificado de signatário de política de atestado](policy-signer-examples.md).

1. Execute o comando [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) para recuperar as propriedades do provedor de atestado, como status e AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   Esse comando exibe valores como a seguinte saída:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Exclua um provedor de atestado usando o comando [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Gerenciamento de política

Para gerenciar políticas, um usuário do Azure AD exige as seguintes permissões para `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Essas permissões podem ser atribuídas a um usuário do Azure AD por meio de uma função como `Owner` (permissões curinga), `Contributor` (permissões curinga) ou `Attestation Contributor` (apenas permissões específicas para o Atestado do Azure).  

Para ler políticas, um usuário do Azure AD exige a seguinte permissão para `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Essa permissão pode ser atribuída a um usuário do Azure AD por meio de uma função como `Reader` (permissões curinga) ou `Attestation Reader` (permissões específicas somente para o Atestado do Azure).

Use os comandos descritos aqui para fornecer o gerenciamento de política para um provedor de atestado, um TEE de cada vez.

O comando [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retorna a política atual para o TEE especificado:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> O comando exibe a política no formato de texto e JWT.

Estes são os tipos de TEE compatíveis:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Use o comando [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) para definir uma nova política para o TEE especificado.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

A política de atestado no formato JWT precisa conter uma declaração chamada `AttestationPolicy`. Uma política assinada precisa ser assinada com uma chave que corresponda a um dos certificados de signatário de política existentes.

Para obter exemplos de políticas, confira [Exemplos de uma política de atestado](policy-examples.md).

O comando [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) define uma nova política para o TEE especificado.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Gerenciamento de certificados de signatário de política

Use os seguintes comandos para gerenciar os certificados de signatário de política para um provedor de atestado:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Um certificado de signatário de política é um JWT assinado com a declaração chamada `maa-policyCertificate`. O valor da declaração é um JWK, que contém a chave de autenticação confiável a ser adicionada. O JWT precisa ser assinado com a chave privada correspondente a um dos certificados de signatário de política existentes. Para obter informações sobre o JWT e o JWK, confira [Conceitos básicos](basic-concepts.md).

Todo o processamento semântico do certificado de signatário de política precisa ser feito fora da CLI do Azure. Com relação à CLI do Azure, trata-se de uma cadeia de caracteres simples.

Para obter exemplos de certificado, confira [Exemplos de um certificado de signatário de política de atestado](policy-signer-examples.md).

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Implementar o atestado com um enclave do SGX usando exemplos de código](/samples/browse/?expanded=azure&terms=attestation)
