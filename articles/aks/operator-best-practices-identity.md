---
title: Melhores práticas do operador - Identidade nos Serviços do Kubernetes do Azure (AKS)
description: Aprenda as práticas recomendadas do operador de cluster sobre como gerenciar autenticação e autorização para clusters no Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 06d15d66df0b2ec0049d4b2fffae6a9909b05dca
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549131"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para autenticação e autorização no Azure Kubernetes Service (AKS)

À medida que você implanta e mantém clusters no Azure Kubernetes Service (AKS), é preciso implementar maneiras de gerenciar o acesso a recursos e serviços. Sem esses controles, as contas podem ter acesso a recursos e serviços de que não precisam. Também pode ser difícil rastrear qual conjunto de credenciais foi usado para fazer alterações.

Este artigo de práticas recomendadas se concentra em como um operador de cluster pode gerenciar o acesso e a identidade dos clusters do AKS. Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Autenticar usuários de cluster do AKS com o Azure Active Directory Domain Services
> * Controlar o acesso a recursos com controles de acesso baseados em função (RBAC)
> * Use uma identidade gerenciada para se autenticar com outros serviços

## <a name="use-azure-active-directory"></a>Use o Azure Active Directory Domain Services

**Orientação sobre práticas recomendadas**: implante clusters do AKS com a integração do Microsoft Azure Active Directory. Usando o Microsoft Azure Active Directory centraliza o componente de gerenciamento de identidade. Qualquer alteração na conta do usuário ou no status do grupo é atualizada automaticamente no acesso ao cluster do AKS. Use Roles ou ClusterRoles e Bindings, conforme discutido na próxima seção, para direcionar usuários ou grupos para a menor quantidade de permissões necessárias.

Os desenvolvedores e proprietários de aplicativos do cluster do Kubernetes precisam ter acesso a recursos diferentes. O Kubernetes não fornece uma solução de gerenciamento de identidades para controlar quais usuários podem interagir com quais recursos. Em vez disso, você normalmente integra seu cluster a uma solução de identidade existente. O Azure Active Directory Domais Services (AD) fornece uma solução de gerenciamento de identidades pronta para a empresa e pode se integrar aos clusters do AKS.

Com os clusters integrados do Microsoft Azure Active Directory no AKS, você cria *Funções* ou *ClusterRoles* que definem as permissões de acesso aos recursos. Em seguida, você *vincula* as funções a usuários ou grupos do Microsoft Azure Active Directory. Esses controles de acesso baseados em função do Kubernetes (RBAC) são discutidos na próxima seção. A integração do Microsoft Azure Active Directory e como você controla o acesso a recursos pode ser vista no diagrama a seguir:

![Autenticação no nível de cluster para integração do Azure Active Directory Domain Services com o AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor autentica com o Microsoft Azure Active Directory.
1. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
1. O desenvolvedor executa uma ação usando o token do Microsoft Azure Active Directory, como `kubectl create pod`
1. O Kubernetes valida o token com o Azure Active Directory Domain Services e busca as associações de grupo do desenvolvedor.
1. O controle de acesso baseado em função do Kubernetes (RBAC) e as políticas de cluster são aplicados.
1. A solicitação do desenvolvedor foi bem-sucedida ou não com base na validação anterior da associação do grupo do Microsoft Azure Active Directory e do Kubernetes RBAC e políticas.

Para criar um cluster AKS que usa o Azure AD, consulte [integrar Azure Active Directory com AKs][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Use controles de acesso baseado em função (RBAC)

**Orientação sobre práticas recomendadas**: use o RBAC do Kubernetes para definir as permissões que os usuários ou grupos têm para os recursos no cluster. Crie funções e ligações que atribuam a menor quantidade de permissões necessárias. Integre-se ao Microsoft Azure Active Directory para que qualquer alteração no status do usuário ou na associação ao grupo seja atualizada automaticamente e o acesso aos recursos do cluster seja atual.

No Kubernetes, você pode fornecer controle granular de acesso aos recursos no cluster. As permissões podem ser definidas no nível do cluster ou em namespaces específicos. Você pode definir quais recursos podem ser gerenciados e com quais permissões. Essas funções são então aplicadas a usuários ou grupos com uma associação. Para obter mais informações sobre *funções*, *ClusterRoles*e *associações*, consulte [Opções de acesso e identidade para o serviço kubernetes do Azure (AKs)][aks-concepts-identity].

Como exemplo, você pode criar uma Função que conceda acesso total aos recursos no namespace denominado *finance-app*, conforme mostrado no seguinte exemplo de manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Em seguida, é criada uma função que associa o usuário do Azure AD *developer1\@contoso.com* ao rolebinding, conforme mostrado no seguinte manifesto YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1\@contoso.com* é autenticado no cluster AKs, ele tem permissões completas para recursos no namespace *Finance-app* . Dessa forma, você separa e controla logicamente o acesso aos recursos. O Kubernetes RBAC deve ser usado em conjunto com a integração do Microsoft Azure Active Directory, conforme discutido na seção anterior.

Para saber como usar os grupos do Azure AD para controlar o acesso aos recursos do kubernetes usando o RBAC, consulte [controlar o acesso a recursos de cluster usando controles de acesso baseado em função e identidades de Azure Active Directory no AKs][azure-ad-rbac].

## <a name="use-pod-identities"></a>Usar identidades de pod

**Orientação sobre práticas recomendadas**: não use credenciais fixas em conjuntos ou imagens de contêiner, pois elas correm risco de exposição ou abuso. Em vez disso, use as identidades do conjunto para solicitar acesso automaticamente usando uma solução central de identidade do Microsoft Azure Active Directory. As identidades de Pod se destinam ao uso apenas com as imagens de contêiner e pods do Linux.

Quando os pods precisam de acesso a outros serviços do Azure, como o Cosmos DB, Key Vault ou Blob Storage, o pod precisa de credenciais de acesso. Essas credenciais de acesso podem ser definidas com a imagem do contêiner ou injetadas como um segredo do Kubernetes, mas precisam ser criadas e atribuídas manualmente. Geralmente, as credenciais são reutilizadas nos pods e não são rotacionadas regularmente.

Identidades gerenciadas para recursos do Azure (atualmente implementados como um projeto de código-fonte aberto AKS associado) permitem que você solicite automaticamente o acesso a serviços por meio do Azure AD. Você não define manualmente credenciais para os pods, em vez disso, eles solicitam um token de acesso em tempo real e podem usá-lo para acessar apenas os serviços atribuídos. No AKS, dois componentes são implantados pelo operador de cluster para permitir que os pods usem identidades gerenciadas:

* **O servidor NMI (Node Management Identity)** é um pod executado como um DaemonSet em cada nó no cluster AKS. O servidor NMI ouve solicitações de pod a serviços do Azure.
* **O Controlador de Identidade Gerenciado (MIC)** é um pod central com permissões para consultar o servidor da API do Kubernetes e verifica se há um mapeamento de identidade do Azure que corresponda a um pod.

Quando pods solicitam acesso a um serviço do Azure, as regras de rede redirecionam o tráfego para o servidor NMI (Node Management Identity). O servidor NMI identifica pods que solicitam acesso aos serviços do Azure com base em seu endereço remoto e consulta o Controlador de identidade gerenciada (MIC). O MIC verifica os mapeamentos de identidade do Azure no cluster do AKS e o servidor NMI solicita um token de acesso do Azure Active Directory Domain Services (AD) com base no mapeamento de identidade do grupo. O Microsoft Azure Active Directory fornece acesso ao servidor NMI, que é retornado ao pod. Esse token de acesso pode ser usado pelo pod para solicitar acesso aos serviços no Azure.

No exemplo a seguir, um desenvolvedor cria um pod que usa uma identidade gerenciada para solicitar acesso a uma instância do Azure SQL Server:

![As identidades do pod permitem que um pod solicite automaticamente o acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador de cluster primeiro cria uma conta de serviço que pode ser usada para mapear identidades quando os pods solicitam acesso aos serviços.
1. O servidor NMI e o MIC são implantados para retransmitir quaisquer solicitações de pod de tokens de acesso ao Microsoft Azure Active Directory.
1. Um desenvolvedor implanta um pod com uma identidade gerenciada que solicita um token de acesso por meio do servidor NMI.
1. O token é retornado ao pod e usado para acessar uma instância do Azure SQL Server.

> [!NOTE]
> As identidades de Pod gerenciadas são um projeto de software livre e não tem suporte do suporte técnico do Azure.

Para usar identidades de Pod, consulte [identidades de Azure Active Directory para aplicativos kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Próximos passos

Este artigo de práticas recomendadas enfocou a autenticação e a autorização de seu cluster e recursos. Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Integrar o Azure Active Directory com o AKS][aks-aad]
* [Usar identidades gerenciadas para recursos do Azure com AKS][aad-pod-identity]

Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-scheduler]
* [Recursos básicos do Agendador do kubernetes][aks-best-practices-scheduler]
* [Recursos do Agendador do kubernetes avançado][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
