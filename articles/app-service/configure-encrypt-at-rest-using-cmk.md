---
title: Criptografe a fonte do aplicativo em repouso
description: Criptografe os dados do aplicativo no Azure Storage e implante-os como um arquivo de pacote.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408736"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Criptografia em repouso usando chaves gerenciadas pelo cliente

Criptografar os dados do aplicativo do seu aplicativo web em repouso requer uma conta de armazenamento do Azure e um Cofre de Chaves Do Azure. Esses serviços são usados quando você executa seu aplicativo a partir de um pacote de implantação.

  - [O Azure Storage fornece criptografia em repouso](../storage/common/storage-service-encryption.md). Você pode usar chaves fornecidas pelo sistema ou suas próprias chaves gerenciadas pelo cliente. É aqui que os dados do aplicativo são armazenados quando não estão sendo executados em um aplicativo web no Azure.
  - [Ser executado a partir de um pacote de implantação](deploy-run-package.md) é um recurso de implantação do App Service. Ele permite que você implante o conteúdo do seu site a partir de uma conta de armazenamento do Azure usando uma URL sas (SAS) de assinatura de acesso compartilhado.
  - [As referências do Key Vault](app-service-key-vault-references.md) são um recurso de segurança do App Service. Ele permite importar segredos em tempo de execução como configurações do aplicativo. Use isso para criptografar a URL SAS da sua conta de armazenamento do Azure.

## <a name="set-up-encryption-at-rest"></a>Configurar criptografia em repouso

### <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Primeiro, [crie uma conta do Azure Storage](../storage/common/storage-account-create.md) e [criptografe-a com chaves gerenciadas pelo cliente.](../storage/common/encryption-customer-managed-keys.md) Uma vez que a conta de armazenamento seja criada, use o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote.

Em seguida, use o Storage Explorer para [gerar um SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Salve esta URL SAS, isso é usado posteriormente para permitir o acesso seguro do pacote de implantação em tempo de execução.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configure a execução de um pacote da sua conta de armazenamento
  
Depois de carregar seu arquivo no armazenamento Blob e ter `WEBSITE_RUN_FROM_PACKAGE` uma URL SAS para o arquivo, defina a configuração do aplicativo para a URL SAS. O exemplo a seguir faz isso usando o Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Adicionar esta configuração de aplicativo faz com que seu aplicativo web seja reiniciado. Depois que o aplicativo for reiniciado, navegue até ele e certifique-se de que o aplicativo tenha começado corretamente usando o pacote de implantação. Se o aplicativo não tiver começado corretamente, consulte o [guia executar a solução de problemas do pacote](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Criptografe a configuração do aplicativo usando referências do Key Vault

Agora você pode substituir `WEBSITE_RUN_FROM_PACKAGE` o valor da configuração do aplicativo por uma referência do Key Vault à URL codificada pelo SAS. Isso mantém a URL SAS criptografada no Key Vault, que fornece uma camada extra de segurança.

1. Use o [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) seguinte comando para criar uma instância do Cofre de Chaves.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Siga [estas instruções para conceder acesso](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) ao seu aplicativo ao seu cofre principal:

1. Use o [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) seguinte comando para adicionar sua URL externa como um segredo no cofre de chaves:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Use o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) seguinte comando `WEBSITE_RUN_FROM_PACKAGE` para criar a configuração do aplicativo com o valor como uma referência do Key Vault à URL externa:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` será na saída do `az keyvault secret set` comando anterior.

Atualizar esta configuração do aplicativo faz com que seu aplicativo web seja reiniciado. Depois que o aplicativo for reiniciado, navegue para que ele tenha começado corretamente usando a referência key vault.

## <a name="how-to-rotate-the-access-token"></a>Como girar o token de acesso

É melhor fazer a rotação periódica da chave SAS da sua conta de armazenamento. Para garantir que o aplicativo web não perca o acesso inadvertidamente, você também deve atualizar a URL SAS no Key Vault.

1. Gire a tecla SAS navegando até sua conta de armazenamento no portal Azure. Em **Configurações** > **Tecida**de acesso, clique no ícone para girar a tecla SAS.

1. Copie a nova URL sas e use o seguinte comando para definir a URL SAS atualizada no cofre principal:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Atualize a referência do cofre chave na configuração do aplicativo para a nova versão secreta:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` será na saída do `az keyvault secret set` comando anterior.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Como revogar o acesso a dados do aplicativo web

Existem dois métodos para revogar o acesso do aplicativo web à conta de armazenamento. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Gire a tecla SAS para a conta do Azure Storage

Se a chave SAS para a conta de armazenamento for girada, o aplicativo web não terá mais acesso à conta de armazenamento, mas continuará a ser executado com a última versão baixada do arquivo do pacote. Reinicie o aplicativo web para limpar a última versão baixada.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Remova o acesso do aplicativo web ao Key Vault

Você pode revogar o acesso do aplicativo web aos dados do site desativando o acesso do aplicativo web ao Key Vault. Para isso, remova a política de acesso à identidade do aplicativo web. Esta é a mesma identidade que você criou anteriormente ao configurar referências do cofre de chaves.

## <a name="summary"></a>Resumo

Seus arquivos de aplicativos estão agora criptografados em repouso em sua conta de armazenamento. Quando seu aplicativo web é iniciado, ele recupera a URL SAS do cofre de chaves. Finalmente, o aplicativo web carrega os arquivos do aplicativo da conta de armazenamento. 

Se você precisar revogar o acesso do aplicativo web à sua conta de armazenamento, você pode revogar o acesso ao cofre de chaves ou girar as chaves da conta de armazenamento, o que invalida a URL do SAS.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Existe alguma taxa adicional para executar o meu aplicativo web a partir do pacote de implantação?

Apenas o custo associado à Conta de Armazenamento Do Azure e quaisquer encargos de saída aplicáveis.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Como a execução do pacote de implantação afeta meu aplicativo web?

- Executar seu aplicativo a `wwwroot/` partir do pacote de implantação torna-se somente leitura. Seu aplicativo recebe um erro quando tenta escrever para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Este recurso não é compatível com cache local.

## <a name="next-steps"></a>Próximas etapas

- [Principais referências do Cofre para Serviço de Aplicativo](app-service-key-vault-references.md)
- [Criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)
