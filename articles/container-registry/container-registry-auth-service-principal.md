---
title: Autenticação com a entidade de serviço
description: Fornecer acesso a imagens em seu registro de contêiner particular usando uma entidade de serviço do Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455415"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do Registro de Contêiner do Azure com entidades de serviço

Use uma entidade de serviço do Azure AD (Azure Active Directory) para fornecer à imagem de contêiner o acesso `docker push` e `pull` ao seu registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a serviços e aplicativos “sem periféricos”.

## <a name="what-is-a-service-principal"></a>O que é uma entidade de serviço?

As *entidades de serviço* do Azure AD fornecem acesso aos recursos do Azure em sua assinatura. Você pode pensar em um diretor de serviço como uma identidade de usuário para um serviço, onde "serviço" é qualquer aplicativo, serviço ou plataforma que precisa acessar os recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas nos recursos especificados. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do Registro de Contêiner do Azure, você pode criar uma entidade de serviço do Microsoft Azure Active Directory com permissões de pull, push e pull ou proprietário em seu Registro particular no Azure. Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner particular. Crie diferentes diretores de serviço para cada um de seus aplicativos ou serviços, cada um com direitos de acesso personalizados ao seu registro. Já que você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar as credenciais ou revogar o acesso somente à entidade de serviço (e, portanto, ao aplicativo) de sua escolha.

Por exemplo, configure seu aplicativo web para usar `pull` um diretor de serviço que o forneça apenas `push` acesso `pull` à imagem, enquanto seu sistema de compilação usa um diretor de serviço que fornece tanto com acesso quanto com acesso. Se o desenvolvimento do seu aplicativo mudar de mãos, você pode girar suas credenciais principais de serviço sem afetar o sistema de compilação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer o acesso de registro em **cenários “sem periféricos”**. Em outras palavras, qualquer aplicativo, serviço ou script que precisa enviar por push ou efetuar pull de imagens de contêiner de maneira automatizada ou autônoma. Por exemplo: 

  * *Puxar*: Implantar contêineres de um registro para sistemas de orquestração, incluindo Kubernetes, DC/OS e Docker Swarm. Você também pode retirar de registros de contêineres para serviços relacionados do Azure [Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Azure Container Instances,](container-registry-auth-aci.md) [App Service,](../app-service/index.yml) [Batch,](../batch/index.yml) [Service, Service Fabric,](/azure/service-fabric/)entre outros.

  * *Push*: Construa imagens de contêineres e empurre-as para um registro usando soluções de integração e implantação contínuas como Azure Pipelines ou Jenkins.

Para acesso individual a um registro, como quando você puxa manualmente uma imagem de contêiner para sua estação de trabalho de desenvolvimento, recomendamos usar sua própria [identidade Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) em vez de acesso ao registro (por exemplo, com [login az acr][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para Azure CLI no GitHub, bem como versões para Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autenticar com o diretor de serviço

Uma vez que você tenha um diretor de serviço que você concedeu acesso ao seu registro de contêiner, você `docker login` pode configurar suas credenciais para acesso a serviços e aplicativos "sem cabeça" ou inseri-los usando o comando. Use os seguintes valores:

* **Nome de usuário** - iD do aplicativo principal do serviço (também chamado *de ID do cliente*)
* **Senha** - senha principal do serviço (também chamada *de cliente secreto*)

Cada valor é um `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID do formulário . 

> [!TIP]
> Você pode regenerar a senha de uma entidade de serviço executando o comando [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset).
>

### <a name="use-credentials-with-azure-services"></a>Use credenciais com serviços do Azure

Você pode usar as principais credenciais de serviço de qualquer serviço Do Azure que autentica com um registro de contêiner do Azure.  Use as principais credenciais de serviço no lugar das credenciais de admin do registro para uma variedade de cenários.

Por exemplo, use as credenciais para puxar uma imagem de um registro de contêiner do Azure para [instâncias de contêiner do Azure](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Use com login docker

Você pode `docker login` executar usando um diretor de serviço. No exemplo a seguir, o ID do aplicativo `$SP_APP_ID`principal do serviço `$SP_PASSWD`é passado na variável ambiente , e a senha na variável . Para obter as melhores práticas para gerenciar as credenciais do Docker, consulte a referência de comando [de login do Docker.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Uma vez logado, o Docker armazena as credenciais.

### <a name="use-with-certificate"></a>Use com certificado

Se você adicionou um certificado ao seu diretor de serviço, você pode entrar no Azure CLI com autenticação baseada em certificadoe e, em seguida, usar o comando [de login az acr][az-acr-login] para acessar um registro. Usar um certificado como segredo em vez de uma senha fornece segurança adicional quando você usa o CLI. 

Um certificado auto-assinado pode ser criado quando você [cria um diretor de serviço](/cli/azure/create-an-azure-service-principal-azure-cli). Ou, adicione um ou mais certificados a um diretor de serviço existente. Por exemplo, se você usar um dos scripts deste artigo para criar ou atualizar um diretor de serviço com direitos de puxar ou empurrar imagens de um registro, adicione um certificado usando o comando [de reset credencial az ad sp.][az-ad-sp-credential-reset]

Para usar o diretor de serviço com certificado para [entrar no Azure CLI,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)o certificado deve estar em formato PEM e incluir a chave privada. Se o certificado não estiver no formato necessário, `openssl` use uma ferramenta como convertê-lo. Quando você executa o [login az][az-login] para entrar na CLI usando o principal de serviço, também forneça o ID de aplicativo do diretor do serviço e o ID de inquilino do Active Directory. O exemplo a seguir mostra esses valores como variáveis de ambiente:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Em seguida, execute [o login az acr][az-acr-login] para autenticar com o registro:

```azurecli
az acr login --name myregistry
```

A CLI usa o token `az login` criado quando você correu para autenticar sua sessão com o registro.

## <a name="next-steps"></a>Próximas etapas

* Consulte a visão geral de [autenticação](container-registry-authentication.md) para outros cenários para autenticar com um registro de contêiner do Azure.

* Para um exemplo de usar um cofre de chaves do Azure para armazenar e recuperar as principais credenciais do serviço para um registro de contêiner, consulte o tutorial para construir e implantar uma imagem de [contêiner usando tarefas ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
