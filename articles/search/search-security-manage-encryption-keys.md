---
title: Criptografia em repouso usando chaves gerenciadas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a criptografia do lado do servidor sobre índices e mapas de sinônimos no Azure Pesquisa Cognitiva usando chaves que você cria e gerencia no Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: ed5d1f5b35bc9b6dee234678fa82af95e1d53bc7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553982"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurar chaves gerenciadas pelo cliente para criptografia de dados no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva criptografa automaticamente o conteúdo indexado em repouso com [chaves gerenciadas pelo serviço](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Se for necessária mais proteção, você poderá complementar a criptografia padrão com uma camada de criptografia adicional usando as chaves que você cria e gerencia no Azure Key Vault. Este artigo orienta você pelas etapas de configuração da criptografia CMK.

A criptografia CMK depende [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com Azure Key Vault, você também pode auditar o uso da chave se [habilitar o registro em log](../key-vault/general/logging.md).  

A criptografia com chaves gerenciadas pelo cliente é aplicada a índices individuais ou mapas de sinônimos quando esses objetos são criados e não é especificado no próprio nível de serviço de pesquisa. Somente novos objetos podem ser criptografados. Você não pode criptografar o conteúdo que já existe.

As chaves nem todos precisam estar no mesmo cofre de chaves. Um único serviço de pesquisa pode hospedar vários índices criptografados ou mapas de sinônimos, cada um criptografado com suas próprias chaves de criptografia gerenciadas pelo cliente, armazenadas em diferentes cofres de chaves. Você também pode ter índices e mapas de sinônimos no mesmo serviço que não são criptografados usando chaves gerenciadas pelo cliente. 

## <a name="double-encryption"></a>Criptografia dupla

Para serviços criados após 1º de agosto de 2020 e em regiões específicas, o escopo da criptografia CMK inclui discos temporários, obtendo [criptografia dupla completa](search-security-overview.md#double-encryption), disponível atualmente nestas regiões: 

+ Oeste dos EUA 2
+ Leste dos EUA
+ Centro-Sul dos Estados Unidos
+ Gov. dos EUA – Virgínia
+ Governo dos EUA do Arizona

Se você estiver usando uma região diferente ou um serviço criado antes de 1º de agosto, a criptografia do CMK será limitada apenas ao disco de dados, excluindo os discos temporários usados pelo serviço.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e serviços a seguir são usados neste exemplo. 

+ [Crie um serviço do Azure Cognitive Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Crie um recurso Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou localize um cofre existente na mesma assinatura que o pesquisa cognitiva do Azure. Esse recurso tem um requisito da mesma assinatura.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) é usado para tarefas de configuração.

+ O [postmaster](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [visualização do SDK do .net](https://aka.ms/search-sdk-preview) podem ser usados para chamar a API REST que cria índices e mapas de sinônimos que incluem um parâmetro de chave de criptografia. Não há suporte do portal para adicionar uma chave a índices ou mapas de sinônimos no momento.

>[!Note]
> Devido à natureza da criptografia com chaves gerenciadas pelo cliente, o Azure Pesquisa Cognitiva não poderá recuperar seus dados se a chave do cofre de chaves do Azure for excluída. Para evitar a perda de dados causada por exclusões de chave Key Vault acidental, a exclusão reversível e a proteção de limpeza devem ser habilitadas no cofre de chaves. A exclusão reversível é habilitada por padrão, de modo que você só encontrará problemas se o tiver desabilitado propositadamente. A proteção de limpeza não é habilitada por padrão, mas é necessária para a criptografia do Azure Pesquisa Cognitiva CMK. Para obter mais informações, consulte visões gerais de exclusão e [limpeza](../key-vault/general/soft-delete-overview.md#purge-protection) [reversível](../key-vault/key-vault-ovw-soft-delete.md) .

## <a name="1---enable-key-recovery"></a>1-Habilitar recuperação de chave

O cofre de chaves deve ter a proteção de exclusão e **limpeza** **reversível** habilitada. Você pode definir esses recursos usando o portal ou os comandos do PowerShell ou do CLI do Azure a seguir.

### <a name="using-powershell"></a>Usando o PowerShell

1. Execute `Connect-AzAccount` para configurar suas credenciais do Azure.

1. Execute o seguinte comando para se conectar ao cofre de chaves, substituindo `<vault_name>` por um nome válido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault é criado com a exclusão reversível habilitada. Se ele estiver desabilitado em seu cofre, execute o seguinte comando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Habilitar a proteção de limpeza:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Salve suas atualizações:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-criar uma nova chave

Se você estiver usando uma chave existente para criptografar o conteúdo de Pesquisa Cognitiva do Azure, ignore esta etapa.

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do cofre de chaves.

1. Selecione a configuração **chaves** no painel de navegação à esquerda e clique em **+ gerar/importar**.

1. No painel **criar uma chave** , na lista de **Opções**, escolha o método que você deseja usar para criar uma chave. Você pode **gerar** uma nova chave, **carregar** uma chave existente ou usar **restore backup** para selecionar um backup de uma chave.

1. Insira um **nome** para a chave e, opcionalmente, selecione outras propriedades de chave.

1. Clique no botão **criar** para iniciar a implantação.

Anote o identificador de chave – isso é composto do **URI do valor da chave**, do nome da **chave**e da **versão da chave**. Você precisará delas para definir um índice criptografado no Azure Pesquisa Cognitiva.
 
![Criar uma nova chave do Key Vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Criar uma nova chave do Key Vault")

## <a name="3---create-a-service-identity"></a>3-criar uma identidade de serviço

Atribuir uma identidade ao serviço de pesquisa permite que você conceda permissões de acesso de Key Vault ao serviço de pesquisa. Seu serviço de pesquisa usará sua identidade para autenticar com o cofre de chaves do Azure.

O Azure Pesquisa Cognitiva dá suporte a duas maneiras de atribuir a identidade: uma identidade gerenciada ou um aplicativo de Azure Active Directory gerenciado externamente. 

Se possível, use uma identidade gerenciada. É a maneira mais simples de atribuir uma identidade ao serviço de pesquisa e deve funcionar na maioria dos cenários. Se você estiver usando várias chaves para índices e mapas de sinônimos, ou se sua solução estiver em uma arquitetura distribuída que desqualifica a autenticação baseada em identidade, use a abordagem avançada de [Azure Active Directory gerenciada externamente](#aad-app) descrita no final deste artigo.

 Em geral, uma identidade gerenciada permite que o serviço de pesquisa se autentique em Azure Key Vault sem armazenar credenciais no código. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerenciada. [Saiba mais sobre identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do serviço de pesquisa. 

1. Clique em **identidade** no painel de navegação esquerdo, altere seu status para **ativado**e clique em **salvar**.

![Habilitar uma identidade gerenciada](./media/search-enable-msi/enable-identity-portal.png "Habilitar uma identidade gerenciada")

## <a name="4---grant-key-access-permissions"></a>4-conceder permissões de acesso à chave

Para permitir que o serviço de pesquisa Use sua chave de Key Vault, você precisará conceder permissões de acesso específicas a seu serviço de pesquisa.

As permissões de acesso podem ser revogadas em um determinado momento. Depois de revogado, qualquer índice de serviço de pesquisa ou mapa de sinônimos que usa esse cofre de chaves ficará inutilizável. Restaurar as permissões de acesso do cofre de chaves posteriormente irá restaurar o acesso ao mapa do index\synonym. Para obter mais informações, consulte [proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do cofre de chaves. 

1. Selecione a configuração **políticas de acesso** no painel de navegação à esquerda e clique em **+ Adicionar nova**.

   ![Adicionar nova política de acesso do cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicionar nova política de acesso do cofre de chaves")

1. Clique em **selecionar entidade de segurança** e selecione o serviço de pesquisa cognitiva do Azure. Você pode procurá-lo pelo nome ou pela ID de objeto que foi exibida depois de habilitar a identidade gerenciada.

   ![Selecionar entidade de segurança de acesso do cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecionar entidade de segurança de acesso do cofre de chaves")

1. Clique em **permissões de chave** e *selecione obter*, *desencapsular chave* e *encapsular chave*. Você pode usar o *Azure data Lake Storage ou o modelo de armazenamento do Azure* para selecionar rapidamente as permissões necessárias.

   O Azure Pesquisa Cognitiva deve ser concedido com as seguintes [permissões de acesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* – permite que o serviço de pesquisa recupere as partes públicas da sua chave em um Key Vault
   * *Chave de encapsulamento* – permite que o serviço de pesquisa Use sua chave para proteger a chave de criptografia interna
   * *Desencapsular chave* – permite que o serviço de pesquisa Use sua chave para desencapsular a chave de criptografia interna

   ![Selecionar permissões de chave de política de acesso do cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecionar permissões de chave de política de acesso do cofre de chaves")

1. Para **permissões de segredo**, selecione *obter*.

1. Para **permissões de certificado**, selecione *obter*.

1. Clique em **OK** e **salve** as alterações da política de acesso.

> [!Important]
> O conteúdo criptografado no Azure Pesquisa Cognitiva está configurado para usar uma chave de Azure Key Vault específica com uma **versão**específica. Se você alterar a chave ou a versão, o mapa de índice ou sinônimo deverá ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, em você não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5-criptografar conteúdo

Para adicionar uma chave gerenciada pelo cliente em um índice ou mapa de sinônimos, você deve usar a [API REST de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) ou um SDK. O portal não expõe mapas de sinônimos ou propriedades de criptografia. Quando você usa uma API válida, os índices e os mapas de sinônimos dão suporte a uma propriedade **encryptionKey** de nível superior. 

Usando o **URI do Key Vault**, o **nome da chave** e a **versão** da chave da chave do cofre de chaves, crie uma definição de **encryptionKey** da seguinte maneira:

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
> Nenhum desses detalhes do cofre de chaves são considerados secretos e podem ser recuperados facilmente navegando até a página de chave Azure Key Vault relevante em portal do Azure.

Se você estiver usando um aplicativo do AAD para Key Vault autenticação em vez de usar uma identidade gerenciada, adicione as **credenciais de acesso** do aplicativo do AAD à sua chave de criptografia: 
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

## <a name="example-index-encryption"></a>Exemplo: index Encryption
Os detalhes da criação de um novo índice por meio da API REST podem ser encontrados em [criar índice (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-index), em que a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do índice: 

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
Agora você pode enviar a solicitação de criação de índice e começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: criptografia de mapa de sinônimos

Os detalhes da criação de um novo mapa de sinônimos por meio da API REST podem ser encontrados em [criar mapa de sinônimos (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), em que a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do mapa de sinônimos: 

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
Agora você pode enviar a solicitação de criação de mapa de sinônimo e começar a usá-la normalmente.

>[!Important] 
> Embora **encryptionKey** não possa ser adicionado a índices de pesquisa cognitiva do Azure existentes ou mapas de sinônimos, ele pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes do Key Vault (por exemplo, atualizar a versão da chave). Ao mudar para uma nova chave de Key Vault ou uma nova versão de chave, qualquer índice de Pesquisa Cognitiva do Azure ou mapa de sinônimos que usa a chave deve primeiro ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, pois ele não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.   
> Restaurar as permissões de acesso do cofre de chaves posteriormente irá restaurar o acesso ao conteúdo.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avançado: usar um aplicativo de Azure Active Directory gerenciado externamente

Quando uma identidade gerenciada não é possível, você pode criar um aplicativo Azure Active Directory com uma entidade de segurança para o serviço de Pesquisa Cognitiva do Azure. Especificamente, uma identidade gerenciada não é viável sob estas condições:

* Você não pode conceder permissões de acesso de serviço de pesquisa diretamente para o cofre de chaves (por exemplo, se o serviço de pesquisa estiver em um locatário de Active Directory diferente do Azure Key Vault).

* Um único serviço de pesquisa é necessário para hospedar vários mapas indexes\synonym criptografados, cada um usando uma chave diferente de um cofre de chaves diferente, em que cada cofre de chaves deve usar **uma identidade diferente** para autenticação. Se usar uma identidade diferente para gerenciar diferentes cofres de chaves não for um requisito, considere o uso da opção de identidade gerenciada acima.  

Para acomodar essas topologias, o Azure Pesquisa Cognitiva dá suporte ao uso de aplicativos Azure Active Directory (AAD) para autenticação entre o serviço de pesquisa e o Key Vault.    
Para criar um aplicativo do AAD no Portal:

1. [Crie um aplicativo Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenha a ID do aplicativo e a chave de autenticação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , pois elas serão necessárias para criar um índice criptografado. Os valores que você precisará fornecer incluem a **ID do aplicativo** e a **chave de autenticação**.

>[!Important]
> Ao decidir usar um aplicativo AAD de autenticação em vez de uma identidade gerenciada, considere o fato de que o Azure Pesquisa Cognitiva não está autorizado a gerenciar seu aplicativo AAD em seu nome, e cabe a você gerenciar seu aplicativo AAD, como a rotação periódica da chave de autenticação do aplicativo.
> Ao alterar um aplicativo do AAD ou sua chave de autenticação, qualquer índice de Pesquisa Cognitiva do Azure ou mapa de sinônimos que usa esse aplicativo deve primeiro ser atualizado para usar o novo aplicativo ID\key **antes** de excluir o aplicativo anterior ou sua chave de autorização e antes de revogar o acesso Key Vault a ele.
> A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, pois ele não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.

## <a name="work-with-encrypted-content"></a>Trabalhar com conteúdo criptografado

Com a criptografia CMK, você perceberá a latência para indexação e consultas devido ao trabalho extra de criptografia/descriptografia. O Azure Pesquisa Cognitiva não registra a atividade de criptografia, mas você pode monitorar o acesso à chave por meio do registro em log do Key Vault. Recomendamos que você [habilite o registro em log](../key-vault/general/logging.md) como parte da configuração do cofre de chaves.

Espera-se que a rotação de chaves ocorra ao longo do tempo. Sempre que você gira as chaves, é importante seguir esta sequência:

1. [Determine a chave usada por um índice ou mapa de sinônimos](search-security-get-encryption-keys.md).
1. [Crie uma nova chave no Key Vault](../key-vault/keys/quick-create-portal.md), mas deixe a chave original disponível.
1. [Atualize as propriedades encryptionKey](https://docs.microsoft.com/rest/api/searchservice/update-index) em um mapa de índice ou sinônimo para usar os novos valores. Somente os objetos que foram criados originalmente com essa propriedade podem ser atualizados para usar um valor diferente.
1. Desabilite ou exclua a chave anterior no cofre de chaves. Monitore o acesso à chave para verificar se a nova chave está sendo usada.

Por motivos de desempenho, o serviço de pesquisa armazena em cache a chave por até várias horas. Se você desabilitar ou excluir a chave sem fornecer uma nova, as consultas continuarão a funcionar de forma temporária até que o cache expire. No entanto, quando o serviço de pesquisa não puder descriptografar o conteúdo, você receberá esta mensagem: "acesso proibido. A chave de consulta usada pode ter sido revogada-tente novamente. " 

## <a name="next-steps"></a>Próximas etapas

Se você não estiver familiarizado com a arquitetura de segurança do Azure, examine a [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
