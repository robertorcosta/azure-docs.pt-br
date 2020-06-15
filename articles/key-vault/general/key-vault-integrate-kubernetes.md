---
title: Integrar o Azure Key Vault ao Kubernetes
description: Neste tutorial, você acessará o Azure Key Vault e vai recuperar segredos dele usando o Driver do Secrets Store CSI (Container Storage Interface, Interface de armazenamento de contêiner) para, em seguida, montar nos pods do Kubernetes.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636929"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Tutorial: Configurar e executar o provedor do Azure Key Vault para o driver do Secrets Store CSI no Kubernetes

Neste tutorial, você acessará o Azure Key Vault e vai recuperar segredos dele usando o driver do Secrets Store CSI (Container Storage Interface, Interface de armazenamento de contêiner) para, em seguida, montar nos pods do Kubernetes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma entidade de serviço
> * Implantar um cluster do Serviço de Kubernetes do Azure
> * Instalar o Helm e driver do Secrets Store CSI
> * Criar um Azure Key Vault e definir os segredos
> * Criar seu próprio objeto SecretProviderClass
> * Implantar seu pod com segredos montados do Key Vault

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Antes de iniciar este tutorial, instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Criar uma entidade de serviço ou usar identidades gerenciadas

Se você planeja usar identidades gerenciadas, pode passar para a próxima seção.

Crie uma entidade de serviço para controlar quais recursos podem ser acessados de seu Azure Key Vault. O acesso da entidade de serviço é restrito pelas funções atribuídas a ela. Esse recurso lhe dá controle sobre como a entidade de serviço pode gerenciar seus segredos. No exemplo a seguir, o nome da entidade de serviço é **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Essa operação retorna uma série de pares de chave/valor:

![Imagem](../media/kubernetes-key-vault-1.png)

Copie a **appID** e a **senha**. Você precisará dessas credenciais mais tarde.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Implantar um cluster do Serviço de Kubernetes do Azure usando a CLI do Azure

Você não precisa usar o Azure Cloud Shell, seu prompt de comando (terminal) com a CLI do Azure instalada será suficiente. 

Siga este [guia](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) e conclua as seguintes seções: **Crie um grupo de recursos**, **Criar cluster do AKS** e **Conectar-se ao cluster**.

**Observação:** se você planeja usar a identidade de pod em vez de uma entidade de serviço, habilite-a ao criar o cluster do Kubernetes, conforme mostrado abaixo:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Defina a variável de ambiente PATH](https://www.java.com/en/download/help/path.xml) como o arquivo "kubectl.exe" que foi baixado.
1. Verifique sua versão do Kubernetes usando o comando a seguir. Esse comando retornará a versão do cliente e do servidor. A versão do cliente é o "kubectl.exe" que você instalou, e a versão do servidor são os Serviços de Kubernetes do Azure em que o cluster está sendo executado.
    ```azurecli
    kubectl version
    ```
1. Sua versão do Kubernetes deve ser a **v1.16.0** ou superior. Esse comando atualizará o cluster do Kubernetes e o pool de nós. A execução pode levar alguns minutos. Neste exemplo, o grupo de recursos é **contosoResourceGroup** e o cluster do Kubernetes é **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Exiba os metadados do cluster do AKS que você criou usando o comando a seguir. Copie a **principalId**, a **clientId**, **a subscriptionId** e o **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Esta é a saída com os dois parâmetros realçados.
    
    ![Imagem](../media/kubernetes-key-vault-5.png) ![Imagem](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Instalar o Helm e driver do Secrets Store CSI

Você precisará instalar o [Helm](https://helm.sh/docs/intro/install/) para instalar o driver do Secrets Store CSI.

A interface do driver do [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) permite que você obtenha conteúdos secretos armazenados em uma instância do Azure Key Vault e use a interface do driver para montar esses conteúdos secretos em pods do Kubernetes.

1. Verifique a versão do Helm e certifique-se de que seja a v3 ou superior:
    ```azurecli
    helm version
    ```
1. Instale o driver do Secrets Store CSI e o provedor do Azure Key Vault para o driver:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Criar um Azure Key Vault e definir os segredos

Siga este [guia](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) para criar seu próprio Key Vault e definir seus segredos.

**Observação:** você não precisa usar o Azure Cloud Shell nem criar um grupo de recursos. Usar o grupo de recursos criado anteriormente para o cluster do Kubernetes é suficiente.

## <a name="create-your-own-secretproviderclass-object"></a>Criar seu próprio objeto SecretProviderClass

Use este [modelo](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) fornecido para criar seu próprio objeto SecretProviderClass personalizado para fornecer parâmetros específicos do provedor para o driver do Secrets Store CSI. Esse objeto fornecerá acesso de identidade ao seu Key Vault.

Usando o arquivo SecretProviderClass YAML de exemplo fornecido, você vai fornecer os parâmetros ausentes. Os seguintes parâmetros são necessários:

1.  **userAssignedIdentityID:** ID de cliente da entidade de serviço
1.  **KeyVaultName:** nome do Key Vault
1.  **objects:** esse objeto terá todo o conteúdo secreto que você deseja montar
    1.  **objectName:** nome do conteúdo secreto
    1.  **objectType:** tipo de objeto (segredo, chave, certificado)
1.  **resourceGroup:** nome do grupo de recursos
1.  **subscriptionId:** a ID da assinatura do Key Vault
1.  **tenantID:** a ID do locatário (ou seja, a ID do diretório) do Key Vault

O modelo atualizado é fornecido a seguir, baixe-o como um arquivo .yaml e preencha os campos obrigatórios correspondentes. Neste exemplo, o Key Vault é **contosoKeyVault5** e tem dois segredos, **secret1** e **secret2**.

**Observação:** se você estiver usando identidades gerenciadas, o campo **usePodIdentity** precisará ser **true**, e deixe o campo **userAssignedIdentityID** apenas com aspas **""** . 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
A seguir, é mostrada a saída de "az keyvault show --name contosoKeyVault5" com os metadados relevantes realçados:

![Imagem](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Atribuir sua entidade de serviço ou usar identidades gerenciadas

### <a name="using-service-principal"></a>Usando a entidade de serviço

Se for usar uma entidade de serviço, você precisará conceder permissão a ela para acessar seu Key Vault e recuperar segredos. Atribua a função de **"Leitor"** e conceda à entidade de serviço permissão para **"obter"** segredos do Key Vault concluindo as etapas abaixo:

1. Atribua a entidade de serviço ao Key Vault existente. O parâmetro **$AZURE_CLIENT_ID** é a **appId** você copiou depois de criar a entidade de serviço.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Veja abaixo a saída do comando: 

    ![Imagem](../media/kubernetes-key-vault-3.png)

1. Dê à entidade de serviço permissão para obter segredos:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Agora, você configurou a entidade de serviço para ter permissão para ler segredos do seu Key Vault. O **$AZURE_CLIENT_SECRET** é a **senha** da sua entidade de serviço. Adicione suas credenciais da entidade de serviço como um segredo do Kubernetes acessível pelo driver do Secrets Store CSI:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Observação:** se posteriormente, ao implantar o pod do Kubernetes, você receber um erro sobre uma ID de Segredo do Cliente inválida, talvez você tenha uma ID de Segredo do Cliente mais antiga que expirou ou foi redefinida. Para resolver o problema, exclua seus segredos “secrets-store-creds” e crie um com a ID de Segredo do Cliente atual. Execute o comando abaixo para excluir seu “secrets-store-creds”:
```azurecli
kubectl delete secrets secrets-store-creds
```

Caso tenha esquecido a ID de Segredo do Cliente da entidade de serviço, você poderá redefini-la usando o seguinte comando:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Usando identidades gerenciadas

Se estiver usando identidades gerenciadas, atribua funções específicas ao cluster do AKS que você criou. 
1. Para criar/listar/ler uma identidade gerenciada atribuída pelo usuário, seu cluster do AKS precisa ter a função de [Colaborador de Identidade Gerenciada](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor). Verifique se a **$clientId** é do cluster do Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Instale a identidade do Azure AD (Azure Active Directory) no AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Crie uma identidade do Azure AD. Copie a **clientId** e a **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Atribua a função de leitor à Identidade do Azure AD que você acabou de criar para o Key Vault. Em seguida, conceda permissão para a Identidade obter segredos do Key Vault. Você usará a **clientId** e a **principalId** da Identidade do Azure que acabou de criar.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Implantar seu pod com segredos montados do Key Vault

O comando a seguir vai configurar o objeto SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Usando a entidade de serviço

Se for usar uma entidade de serviço, O comando a seguir implantará seu pods do Kubernetes com a SecretProviderClass e os secrets-store-creds que você configurou. Este é o modelo para implantação no [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) e no [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml).
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Usando identidades gerenciadas

Se estiver usando identidades gerenciadas, você criará uma **AzureIdentity** no cluster que fará referência à identidade criada anteriormente. Em seguida, crie uma **AzureIdentityBinding** que fará referência à **AzureIdentity** criada. Use o modelo a seguir, preencha os parâmetros correspondentes e salve-o como **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Execute o comando a seguir para executar a associação:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

A seguir, temos a implantação real do pod. Abaixo, temos o arquivo YAML da implantação que usará a associação de identidade do pod da última etapa. Salve este arquivo como **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Execute o seguinte comando para implantar o pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Verificar o status e o conteúdo do segredo 
Para exibir os pods que você implantou:
```azurecli
kubectl get pods
```

Para verificar o status do pod, use o comando a seguir:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Imagem](../media/kubernetes-key-vault-4.png)

O pod implantado deve estar no estado "Em execução". Na seção “Eventos” na parte inferior, todos os tipos de eventos à esquerda têm a classificação “Normal”.
Após verificar se o pod está em execução, você pode verificar se ele tem os segredos de seu Key Vault.

Para exibir todos os segredos que o pod tem:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Para obter conteúdo de um segredo específico:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifique o conteúdo do segredo exibido.

## <a name="next-steps"></a>Próximas etapas

Garantir que o Key Vault seja recuperável:
> [!div class="nextstepaction"]
> [Ligar a exclusão temporária](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
