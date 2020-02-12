---
title: Opções de autenticação do registro
description: Opções de autenticação para um registro de contêiner do Azure privado, incluindo a entrada com uma identidade de Azure Active Directory, o uso de entidades de serviço e o uso de credenciais de administrador opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152936"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com um Registro de contêiner do Azure

Há várias maneiras de autenticar com um Registro de Contêiner do Azure, cada uma das quais é aplicável a um ou mais cenários de uso do registro.

As maneiras recomendadas incluem a autenticação em um registro diretamente por meio de [logon individual](#individual-login-with-azure-ad)ou seus aplicativos e orquestradores de contêiner podem executar a autenticação autônoma ou "sem periféricos" usando uma entidade de [serviço](#service-principal)Azure Active Directory (AD do Azure).

## <a name="authentication-options"></a>Opções de autenticação

A tabela a seguir lista os métodos de autenticação disponíveis e os cenários recomendados. Consulte conteúdo vinculado para obter detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
|   de [identidade individual do AD](#individual-login-with-azure-ad)               | `az acr login` em CLI do Azure                             | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | O token do AD deve ser renovado a cada 3 horas     |
|   de [entidade de serviço do AD](#service-principal)                 | `docker login`<br/><br/>`az acr login` em CLI do Azure<br/><br/> Configurações de logon do registro em APIs ou ferramentas<br/><br/>      de [segredo de pull do kubernetes](container-registry-auth-kubernetes.md)                                       | Envio autônomo por push do pipeline de CI/CD<br/><br/> Pull autônoma para o Azure ou serviços externos  | Sim                              | A expiração padrão da senha do SP é de 1 ano       |                                                           
| [Integrar com o AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar registro quando o cluster AKS foi criado ou atualizado  | Pull autônomo para o cluster AKS                                                  | Não, somente acesso de pull             | Disponível somente com o cluster AKS            |
| [Identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` no CLI do Azure                                       | Push autônomo do pipeline do Azure CI/CD<br/><br/> Pull autônoma para os serviços do Azure<br/><br/>   | Sim                              | Use somente de serviços do Azure que [dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
|   de [usuário administrador](#admin-account)                           | `docker login`                                          | Push interativo/pull por desenvolvedor individual ou testador                           | Não, sempre acesso de pull e envio por push  | Conta única por registro, não recomendada para vários usuários         |
| [Token de acesso no escopo do repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` em CLI do Azure   | Envio por push/pull interativo para o repositório por desenvolvedor individual ou testador<br/><br/> Envio por push/pull autônomo para o repositório por sistema individual ou dispositivo externo                  | Sim                              | Não integrado no momento com a identidade do AD  |

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar com o registro diretamente, assim como ao extrair imagens para sua estação de trabalho de desenvolvimento e ao enviar imagens por push para ela, autentique-se usando o comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) da [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Ao fazer logon com `az acr login`, a CLI usa o token criado quando você executou [az login](/cli/azure/reference-index#az-login) para, sem interrupções, autenticar a sessão com o registro. Para concluir o fluxo de autenticação, o Docker deve estar instalado e em execução em seu ambiente. `az acr login` usa o cliente do Docker para definir um token de Azure Active Directory no arquivo de `docker.config`. Depois de você fazer logon dessa maneira, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes na sua sessão não requerem um nome de usuário ou senha.

> [!TIP]
> Além disso, use `az acr login` para autenticar uma identidade individual quando desejar enviar por Push ou extrair artefatos diferentes de imagens do Docker para o registro, como [artefatos de OCI](container-registry-oci-artifacts.md).  


Para acesso ao registro, o token usado pelo `az acr login` é válido por **3 horas**, portanto, é recomendável que você sempre faça logon no registro antes de executar um comando `docker`. Se o token expirar, você poderá atualizá-lo usando o comando `az acr login` novamente para se reautenticar. 

Usar `az acr login` com identidades do Azure fornece [acesso baseado em função](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, talvez você queira fazer logon em um registro com sua própria identidade individual no Azure AD. Para cenários de serviço cruzado ou para lidar com as necessidades de um grupo de trabalho ou de um Workflow de desenvolvimento em que você não deseja gerenciar o acesso individual, também é possível fazer logon com uma [identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entidade de serviço

Se você atribuir uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao registro, seu aplicativo ou serviço poderá usá-lo na autenticação remota. As entidades de serviço permitem [acesso baseado em função](../role-based-access-control/role-assignments-portal.md) a um registro ao qual você pode atribuir várias entidades de serviço. Várias entidades de serviço permitem que você defina acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: efetuar pull

* **AcrPush**: efetuar pull e push

* **Proprietário**: pull, push e atribuir funções a outros usuários

Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

Para que os scripts da CLI criem uma entidade de serviço para autenticação com um registro de contêiner do Azure e mais diretrizes, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que fica desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar as credenciais dele no portal do Azure ou usando a CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não recomendamos o compartilhamento das credenciais da conta de administrador entre vários usuários. Todos os usuários que se autenticam com a conta do administrador aparecem como um único usuário com acesso de push e de pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais dela. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço.
>

A conta do administrador é fornecida com duas senhas, que podem ser regeneradas. As duas senhas permitem manter conexões com o registro usando uma senha enquanto a outra é regenerada. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e a senha para o comando `docker login` quando solicitado a informar a autenticação Básica no Registro. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as práticas recomendadas para gerenciar as credenciais de logon, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Para habilitar o usuário administrador para um registro existente, você pode usar o parâmetro `--admin-enabled` do comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Você pode habilitar o usuário administrador no Portal do Azure navegando em seu registro, selecionando **Chaves de acesso** em **CONFIGURAÇÕES** e, em seguida, **Habilitar** em **Usuário administrador**.

![Habilitar a interface do usuário administrador no Portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Próximas etapas

* [Enviar sua primeira imagem por push usando a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
