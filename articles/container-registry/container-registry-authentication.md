---
title: Opções de autenticação de registro
description: Opções de autenticação para um registro privado de contêineres do Azure, incluindo o login com uma identidade do Azure Active Directory, o uso de princípios de serviço e o uso de credenciais de administrador opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247040"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com um Registro de contêiner do Azure

Há várias maneiras de autenticar com um Registro de Contêiner do Azure, cada uma das quais é aplicável a um ou mais cenários de uso do registro.

As maneiras recomendadas incluem autenticação em um registro diretamente através de [login individual,](#individual-login-with-azure-ad)ou seus aplicativos e orquestradores de contêineres podem executar autenticação sem vigilância, ou "sem cabeça", usando um diretor de serviço do Azure Active Directory (Azure AD). [service principal](#service-principal)

## <a name="authentication-options"></a>Opções de autenticação

A tabela a seguir lista métodos de autenticação disponíveis e cenários recomendados. Consulte o conteúdo vinculado para obter detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade AD individual](#individual-login-with-azure-ad)                | `az acr login` no Azure CLI                             | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | O token AD deve ser renovado a cada 3 horas     |
| [Diretor de serviços de AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`no Azure CLI<br/><br/> Configurações de login de registro em APIs ou ferramentas<br/><br/> [Kubernetes puxam segredo](container-registry-auth-kubernetes.md)                                           | Empurrão autônomo do gasoduto CI/CD<br/><br/> Atração não atendida para o Azure ou serviços externos  | Sim                              | O vencimento padrão da senha de SP é de 1 ano       |                                                           
| [Integre-se com a AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar registro quando o cluster AKS criado ou atualizado  | Atração não atendida para cluster AKS                                                  | Não, apenas puxe o acesso             | Disponível apenas com cluster AKS            |
| [Identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` no Azure CLI                                       | Push autônomo do pipeline Azure CI/CD<br/><br/> Atração desacompanhada para os serviços do Azure<br/><br/>   | Sim                              | Use apenas a partir de serviços do Azure que [suportam identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Usuário de admin](#admin-account)                            | `docker login`                                          | Push/pull interativo por desenvolvedor ou testador individual                           | Não, sempre puxe e empurre o acesso  | Conta única por registro, não recomendada para vários usuários         |
| [Token de acesso com escopo de repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`no Azure CLI   | Empurrão/tração interativo para repositório por desenvolvedor ou testador individual<br/><br/> Push/pull autônomo para repositório por sistema individual ou dispositivo externo                  | Sim                              | Não está atualmente integrado com a identidade AD  |

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar com o registro diretamente, assim como ao extrair imagens para sua estação de trabalho de desenvolvimento e ao enviar imagens por push para ela, autentique-se usando o comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) da [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Ao fazer logon com `az acr login`, a CLI usa o token criado quando você executou [az login](/cli/azure/reference-index#az-login) para, sem interrupções, autenticar a sessão com o registro. Para completar o fluxo de autenticação, o Docker deve ser instalado e executado em seu ambiente. `az acr login`usa o cliente Docker para definir um token `docker.config` Azure Active Directory no arquivo. Depois de você fazer logon dessa maneira, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes na sua sessão não requerem um nome de usuário ou senha.

> [!TIP]
> Use `az acr login` também para autenticar uma identidade individual quando você quiser empurrar ou puxar artefatos diferentes de imagens docker para o seu registro, como [artefatos OCI](container-registry-oci-artifacts.md).  


Para acesso ao registro, `az acr login` o token usado por é válido por **3 horas,** por `docker` isso recomendamos que você sempre faça login no registro antes de executar um comando. Se o token expirar, você poderá atualizá-lo usando o comando `az acr login` novamente para se reautenticar. 

Usar `az acr login` com identidades do Azure fornece [acesso baseado em função](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, você pode querer fazer login em um registro com sua própria identidade individual no Azure AD. Para cenários de serviço saqueou para lidar com as necessidades de um grupo de trabalho ou de um fluxo de trabalho de desenvolvimento onde você não deseja gerenciar o acesso individual, você também pode fazer login com uma [identidade gerenciada para os recursos do Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entidade de serviço

Se você atribuir uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao registro, seu aplicativo ou serviço poderá usá-lo na autenticação remota. As entidades de serviço permitem [acesso baseado em função](../role-based-access-control/role-assignments-portal.md) a um registro ao qual você pode atribuir várias entidades de serviço. Várias entidades de serviço permitem que você defina acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: efetuar pull

* **AcrPush**: efetuar pull e push

* **Proprietário**: pull, push e atribuir funções a outros usuários

Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

Para que os scripts cli criem um princípio de serviço para autenticação com um registro de contêiner do Azure e mais orientação, consulte [a autenticação do Registro de Contêineres do Azure com os princípios do serviço](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que fica desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar as credenciais dele no portal do Azure ou usando a CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não recomendamos compartilhar as credenciais da conta de administração entre vários usuários. Todos os usuários que se autenticam com a conta do administrador aparecem como um único usuário com acesso de push e de pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais dela. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço.
>

A conta do administrador é fornecida com duas senhas, que podem ser regeneradas. As duas senhas permitem manter conexões com o registro usando uma senha enquanto a outra é regenerada. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e a senha para o comando `docker login` quando solicitado a informar a autenticação Básica no Registro. Por exemplo: 

```
docker login myregistry.azurecr.io 
```

Para obter as melhores práticas para gerenciar credenciais de login, consulte a referência de comando [de login do docker.](https://docs.docker.com/engine/reference/commandline/login/)

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
