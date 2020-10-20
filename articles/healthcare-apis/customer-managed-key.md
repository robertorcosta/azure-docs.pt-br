---
title: Configurar as chaves gerenciadas pelo cliente para a API do Azure para FHIR
description: Recurso Bring Your Own Key com suporte na API do Azure para FHIR por meio do Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92051328"
---
# <a name="configure-customer-managed-keys"></a>Configurar chaves gerenciadas pelo cliente

Quando você criar uma conta da API do Azure para FHIR, os seus dados serão criptografados usando as chaves gerenciadas pela Microsoft por padrão. Agora, você pode adicionar uma segunda camada de criptografia nos dados usando a própria chave escolhida e gerenciada por conta própria.

No Azure, isso normalmente é feito usando uma chave de criptografia no AKV (Azure Key Vault) do cliente. O SQL do Azure, o Armazenamento do Azure e o Cosmos DB são alguns exemplos que oferecem essa funcionalidade hoje em dia. A API do Azure para FHIR aproveita esse suporte do Cosmos DB. Ao criar uma conta, você terá a opção de especificar um URI de chave do AKV. Passaremos essa chave para o Cosmos DB quando a conta do BD for provisionada. Quando uma solicitação FHIR é feita, o Cosmos DB busca a sua chave e a usa para criptografar/descriptografar os dados. Para começar, você pode ver os seguintes links:

- [Registrar o provedor de recursos do Azure Cosmos DB para a sua assinatura do Azure](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [Configurar a sua instância do AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [Adicionar uma política de acesso à sua instância do AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [Gerar uma chave no AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

Depois de criar a conta da API do Azure para FHIR no portal do Azure, você poderá ver uma opção de configuração "Criptografia de Dados" em "Configurações do Banco de Dados" na guia "Configurações Adicionais". Por padrão, a opção de chave gerenciada pelo serviço será escolhida. Você pode especificar a sua chave do AKV aqui selecionando a opção "Chave gerenciada pelo cliente". Você pode inserir o URI de chave copiado aqui.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar a API do Azure para FHIR":::

Ou você pode escolher a chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Criar a API do Azure para FHIR":::

Para contas do FHIR existentes, você pode ver a opção de criptografia de chave (chave gerenciada pelo cliente ou pelo serviço) na folha "Banco de Dados", conforme descrito abaixo. A opção de configuração não poderá ser modificada depois de ser escolhida. No entanto, você pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Criar a API do Azure para FHIR":::

Além disso, você pode criar uma versão da chave especificada, após a qual os seus dados serão criptografados com a nova versão sem nenhuma interrupção do serviço. Você também pode remover o acesso à chave para remover o acesso aos dados.
