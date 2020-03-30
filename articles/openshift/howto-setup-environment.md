---
title: Configure seu ambiente de desenvolvimento Azure Red Hat OpenShift
description: Aqui estão os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
keywords: red hat setup openshift configuração
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477027"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar seu ambiente de desenvolvimento do Red Hat OpenShift no Azure

Para criar e executar os aplicativos Microsoft Azure Red Hat OpenShift, você precisará:

* Instale a versão 2.0.65 (ou superior) do Azure CLI (ou use o Azure Cloud Shell).
* Registre-se `AROGA` para o recurso e provedores de recursos associados.
* Crie um inquilino do Azure Active Directory (Azure AD).
* Crie um objeto de aplicativo Azure AD.
* Crie um usuário ad do Azure.

As seguintes instruções irão levá-lo através de todos esses pré-requisitos.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

O Azure Red Hat OpenShift requer a versão 2.0.65 ou superior do Azure CLI. Se você já instalou o Azure CLI, você pode verificar qual versão você tem executando:

```azurecli
az --version
```

A primeira linha de saída terá a `azure-cli (2.0.65)`versão CLI, por exemplo.

Aqui estão as instruções para [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você precisar de uma nova instalação ou de um upgrade.

Alternativamente, você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ao usar o Azure Cloud Shell, certifique-se de selecionar o ambiente **Bash** se você planeja seguir junto com a [série de tutoriais do Cluster Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Registre provedores e recursos

O `Microsoft.ContainerService AROGA` `Microsoft.Solutions`recurso, `Microsoft.Compute` `Microsoft.Storage`e `Microsoft.KeyVault` `Microsoft.Network` os provedores devem ser registrados manualmente na sua assinatura antes de implantar seu primeiro cluster Azure Red Hat OpenShift.

Para registrar esses provedores e recursos manualmente, use as seguintes instruções de um shell Bash se você tiver instalado a CLI ou da sessão Azure Cloud Shell (Bash) no seu portal Azure:

1. Se você tiver várias assinaturas do Azure, especifique o ID de assinatura relevante:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre o recurso Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registre o provedor Microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registre o provedor Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registre o provedor Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registre o provedor Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registre o provedor Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualize o registro do provedor de recursos Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um inquilino do Azure Active Directory (Azure AD)

O serviço Azure Red Hat OpenShift requer um inquilino associado do Azure Active Directory (Azure AD) que representa sua organização e seu relacionamento com a Microsoft. O inquilino do Azure AD permite que você registre, crie e gerencie aplicativos, bem como use outros serviços do Azure.

Se você não tiver um Azure AD para usar como inquilino para o seu cluster Azure Red Hat OpenShift, ou desejar criar um inquilino para testes, siga as instruções em [Criar um inquilino Azure AD para o seu cluster Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com este guia.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Crie um usuário, grupo de segurança e objeto de aplicativo do Azure AD

O Azure Red Hat OpenShift requer permissões para executar tarefas em seu cluster, como configurar o armazenamento. Essas permissões são representadas através de um [diretor de serviço.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Você também vai querer criar um novo usuário do Active Directory para testar aplicativos em execução no cluster Azure Red Hat OpenShift.

Siga as instruções em [Criar um objeto de aplicativo AZure AD e usuário](howto-aad-app-configuration.md) para criar um principal de serviço, gerar um URL de retorno de chamada secreto e autenticação do cliente para o seu aplicativo e criar um novo grupo de segurança Azure AD e usuário para acessar o cluster.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
