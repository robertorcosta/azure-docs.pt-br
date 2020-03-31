---
title: Criptografia em repouso usando chaves gerenciadas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a criptografia do lado do servidor sobre índices e mapas de sinônimos no Azure Cognitive Search usando chaves que você cria e gerencia no Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899938"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia em repouso de conteúdo no Azure Cognitive Search usando chaves gerenciadas pelo cliente no Azure Key Vault

Por padrão, o Azure Cognitive Search criptografa o conteúdo indexado em repouso com [chaves gerenciadas por serviço](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando chaves que você cria e gerencia no Azure Key Vault. Este artigo te orienta através dos passos.

A criptografia do lado do servidor é suportada por meio da integração com [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com o Azure Key Vault, você também pode auditar o uso de chaves. 

A criptografia com chaves gerenciadas pelo cliente é configurada no nível de índice ou de mapa de sinônimo quando esses objetos são criados e não no nível de serviço de pesquisa. Você não pode criptografar o conteúdo que já existe. 

As chaves não precisam estar no mesmo Cofre. Um único serviço de pesquisa pode hospedar vários índices criptografados ou mapas de sinônimos criptografados cada um criptografado com suas próprias chaves de criptografia gerenciadas pelo cliente armazenadas em diferentes Cofres-chave.  Você também pode ter índices e mapas de sinônimos no mesmo serviço que não são criptografados usando chaves gerenciadas pelo cliente. 

> [!IMPORTANT] 
> Este recurso está disponível na [versão REST API 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) e [.NET SDK versão 8.0-preview](search-dotnet-sdk-migration-version-9.md). No momento, não há suporte para configurar chaves de criptografia gerenciadas pelo cliente no portal Azure. O serviço de busca deve ser criado após janeiro de 2019 e não pode ser um serviço gratuito (compartilhado).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços são usados neste exemplo. 

+ [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. 

+ [Crie um recurso do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou encontre um cofre existente sua assinatura.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) é usado para tarefas de configuração.

+ [Carteiro](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) podem ser usados para chamar a API REST. Não há suporte de portal para criptografia gerenciada pelo cliente no momento.

>[!Note]
> Devido à natureza da criptografia com o recurso de chaves gerenciadas pelo cliente, o Azure Cognitive Search não será capaz de recuperar seus dados se a chave do cofre do Azure Key for excluída. Para evitar a perda de dados causada por exclusões acidentais da chave do Key Vault, você **deve** ativar a Proteção de Exclusão e Purga Suave no Cofre de Chaves antes que ele possa ser usado. Para obter mais informações, consulte [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Habilitar a recuperação de chaves

Depois de criar o recurso Do Cofre de Chaves do Azure, habilite a **Proteção de Exclusão** e **Expurgo** Suave no cofre chave selecionado executando os seguintes comandos PowerShell ou Azure CLI:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Criar uma nova chave

Se você estiver usando uma chave existente para criptografar o conteúdo da Pesquisa Cognitiva do Azure, pule essa etapa.

1. [Faça login no portal Azure](https://portal.azure.com) e navegue até o painel do cofre da chave.

1. Selecione a configuração **Chaves** no painel de navegação à esquerda e clique **em Gerar/Importar**.

1. No Criar um painel **de chaves,** na lista de **Opções,** escolha o método que deseja usar para criar uma chave. Você pode **gerar** uma nova chave, **carregar** uma chave existente ou usar **restaurar backup** para selecionar um backup de uma chave.

1. Digite um **Nome** para sua chave e selecione opcionalmente outras propriedades-chave.

1. Clique no botão **Criar** para iniciar a implantação.

Anote o identificador de chave – este é composto a partir do **valor-chave Uri**, o **nome da chave**e a **versão-chave**. Você precisará disso para definir um índice criptografado na Pesquisa Cognitiva do Azure.
 
![Crie uma nova chave do cofre](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Crie uma nova chave do cofre")

## <a name="3---create-a-service-identity"></a>3 - Criar uma identidade de serviço

Atribuir uma identidade ao seu serviço de pesquisa permite que você conceda permissões de acesso do Key Vault ao seu serviço de pesquisa. Seu serviço de pesquisa usará sua identidade para autenticar com o cofre Azure Key.

O Azure Cognitive Search suporta duas maneiras de atribuir identidade: uma identidade gerenciada ou um aplicativo de diretório ativo do Azure gerenciado externamente. 

Se possível, use uma identidade gerenciada. É a maneira mais simples de atribuir uma identidade ao seu serviço de pesquisa e deve funcionar na maioria dos cenários. Se você estiver usando várias chaves para índices e mapas de sinônimos, ou se sua solução estiver em uma arquitetura distribuída que desqualifica a autenticação baseada em identidade, use a abordagem avançada [do Azure Active Directory gerenciada externamente](#aad-app) descrita no final deste artigo.

 Em geral, uma identidade gerenciada permite que seu serviço de pesquisa seja autenticado no Azure Key Vault sem armazenar credenciais em código. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerenciada. [Saiba mais sobre identidades gerenciadas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Para criar uma identidade gerenciada, [entre no portal Azure](https://portal.azure.com) e abra seu painel de serviço de pesquisa. 

1. Clique em **Identidade** no painel de navegação à esquerda, altere seu status para On e clique **em** **Salvar**.

![Habilitar uma identidade gerenciada](./media/search-enable-msi/enable-identity-portal.png "Habilite uma identidade manged")

## <a name="4---grant-key-access-permissions"></a>4 - Conceder permissões de acesso chave

Para permitir que seu serviço de pesquisa use sua chave Key Vault, você precisará conceder ao seu serviço de pesquisa certas permissões de acesso.

As permissões de acesso podem ser revogadas a qualquer momento. Uma vez revogado, qualquer índice de serviço de pesquisa ou mapa de sinônimo que use esse cofre de chaves se tornará inutilizável. Restaurar as permissões de acesso do cofre key posteriormente restaurará o acesso ao mapa index\synonym. Para obter mais informações, consulte [O acesso seguro a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Faça login no portal Azure](https://portal.azure.com) e abra a página de visão geral do cofre principal. 

1. Selecione a configuração **de políticas** de acesso no painel de navegação à esquerda e clique **em +Adicionar novo**.

   ![Adicionar nova política de acesso ao cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicionar nova política de acesso ao cofre de chaves")

1. Clique **em Selecionar o principal** e selecione o serviço de pesquisa cognitiva do Azure. Você pode procurá-lo pelo nome ou pelo ID do objeto que foi exibido após ativar a identidade gerenciada.

   ![Selecione o principal da política de acesso do cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecione o principal da política de acesso do cofre de chaves")

1. Clique em **Tecelar permissões** e *selecione Obter,* *Desembrulhar tecla* e *envoltório .* Você pode usar o modelo *Azure Data Lake Storage ou Azure Storage* para selecionar rapidamente as permissões necessárias.

   A Pesquisa Cognitiva do Azure deve ser concedida com as [seguintes permissões de acesso:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Obter* - permite que seu serviço de pesquisa recupere as partes públicas de sua chave em um Cofre de Chaves
   * *Wrap Key* - permite que seu serviço de pesquisa use sua chave para proteger a chave de criptografia interna
   * *Desembrulhe a chave* - permite que seu serviço de pesquisa use sua chave para desembrulhar a chave de criptografia interna

   ![Selecione as permissões-chave da política de acesso do cofre chave](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecione as permissões-chave da política de acesso do cofre chave")

1. Clique em **OK** e **Salve as** alterações da diretiva de acesso.

> [!Important]
> O conteúdo criptografado no Azure Cognitive Search é configurado para usar uma chave específica do Azure Key Vault com uma **versão**específica . Se você alterar a chave ou a versão, o mapa de índice ou sinônimo deve ser atualizado para usar a nova versão **key\antes** de excluir a versão anterior da chave\. Se não o fizer, o índice ou o mapa do sinônimo não poderão descriptografar o conteúdo quando o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5 - Criptografar conteúdo

A criação de um mapa de índice ou sinônimo criptografado com a chave gerenciada pelo cliente ainda não é possível usando o portal Azure. Use a Azure Cognitive Search REST API para criar tal índice ou mapa de sinônimo.

Tanto o mapa de índice quanto o mapa de sinônimos suportam uma nova criptografia de nível **superiorPropriedade key** usada para especificar a chave. 

Usando o **cofre-chave Uri**, **nome-chave** e a **versão-chave** da chave do cofre chave, podemos criar uma definição **de criptografiaChave:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Nenhum desses detalhes do cofre chave é considerado secreto e pode ser facilmente recuperado navegando para a página-chave do Azure Key Vault no portal Azure.

Se você estiver usando um aplicativo AAD para autenticação do Key Vault em vez de usar uma identidade gerenciada, adicione as **credenciais** de acesso ao aplicativo AAD à sua chave de criptografia: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Exemplo: Criptografia de índice
Os detalhes da criação de um novo índice através da API REST podem ser encontrados no [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), onde a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do índice: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Agora você pode enviar a solicitação de criação do índice e, em seguida, começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: Criptografia de mapa de sinônimo

Os detalhes da criação de um novo mapa de sinônimos através da API REST podem ser encontrados no [Create Synonym Map (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), onde a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do mapa do sinônimo: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Agora você pode enviar a solicitação de criação do mapa de sinônimo e, em seguida, começar a usá-lo normalmente.

>[!Important] 
> Embora **a criptografiaKey** não possa ser adicionada aos índices de pesquisa cognitiva do Azure existentes ou mapas de sinônimos, ele pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes do cofre-chave (por exemplo, atualizar a versão-chave). Ao mudar para uma nova chave Key Vault ou uma nova versão-chave, qualquer índice de pesquisa cognitiva do Azure ou um mapa de sinônimo que use a chave deve primeiro ser atualizado para usar a nova versão **key\antes** de excluir a versão anterior da chave\. Se não o fizer, o índice ou o mapa do sinônimo não poderão descriptografia o conteúdo quando o acesso à chave for perdido.   
> Restaurar as permissões de acesso do cofre Key posteriormente restaurará o acesso ao conteúdo.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avançado: Use um aplicativo de diretório ativo do Azure gerenciado externamente

Quando uma identidade gerenciada não é possível, você pode criar um aplicativo do Azure Active Directory com um diretor de segurança para o seu serviço de pesquisa cognitiva Do Azure. Especificamente, uma identidade gerenciada não é viável nessas condições:

* Não é possível conceder diretamente permissões de acesso ao seu serviço de pesquisa ao cofre Key (por exemplo, se o serviço de pesquisa estiver em um inquilino ativo diferente do Azure Key Vault).

* Um único serviço de pesquisa é necessário para hospedar vários índices criptografados\mapas de sinônimo, cada um usando uma chave diferente de um cofre de chaves diferente, onde cada cofre de chaves deve usar **uma identidade diferente** para autenticação. Se usar uma identidade diferente para gerenciar diferentes cofres Key não é um requisito, considere usar a opção de identidade gerenciada acima.  

Para acomodar tais topologias, o Azure Cognitive Search suporta o uso de aplicativos Azure Active Directory (AAD) para autenticação entre seu serviço de pesquisa e o Key Vault.    
Para criar um aplicativo AAD no portal:

1. [Crie um aplicativo de diretório ativo do Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenha a id do aplicativo e a chave de autenticação,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) pois elas serão necessárias para criar um índice criptografado. Os valores que você precisará fornecer incluem **id de aplicativo** e **chave de autenticação**.

>[!Important]
> Ao decidir usar um aplicativo AAD de autenticação em vez de uma identidade gerenciada, considere o fato de que o Azure Cognitive Search não está autorizado a gerenciar seu aplicativo AAD em seu nome, e cabe a você gerenciar seu aplicativo AAD, como periódico rotação da chave de autenticação do aplicativo.
> Ao alterar um aplicativo AAD ou sua chave de autenticação, qualquer índice de pesquisa cognitiva do Azure ou um mapa de sinônimo que use esse aplicativo deve primeiro ser atualizado para usar a nova chave ID\key do aplicativo **antes** de excluir o aplicativo anterior ou sua chave de autorização e antes de revogar o acesso do Key Vault a ele.
> Se não o fizer, o índice ou o mapa do sinônimo não poderão descriptografia o conteúdo quando o acesso à chave for perdido.   

## <a name="next-steps"></a>Próximas etapas

Se você não está familiarizado com a arquitetura de segurança do Azure, revise a documentação de segurança do [Azure](https://docs.microsoft.com/azure/security/)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
