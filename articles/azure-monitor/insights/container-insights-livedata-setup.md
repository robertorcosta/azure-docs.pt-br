---
title: Configurar o Monitor Azure para contêineres Live Data (visualização) | Microsoft Docs
description: Este artigo descreve como configurar a visão em tempo real de troncos de contêineres (stdout/stderr) e eventos sem usar kubectl com o Monitor Azure para contêineres.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275367"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Como configurar o recurso Dados vivos (visualização)

Para visualizar dados ao vivo (visualização) com o Azure Monitor para contêineres de clusters Azure Kubernetes Service (AKS), você precisa configurar a autenticação para conceder permissão de acesso aos seus dados do Kubernetes. Essa configuração de segurança permite o acesso em tempo real aos seus dados através da API Kubernetes diretamente no portal Azure.

Esse recurso suporta os seguintes métodos para controlar o acesso aos logs, eventos e métricas:

- AKS sem autorização do RBAC do Kubernetes habilitada
- AKS habilitado com autorização do RBAC do Kubernetes
    - AKS configurado com o cluster's binding ** [binding clusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS habilitado com o azure Active Directory (AD) saml-based single-sign on

Essas instruções exigem tanto acesso administrativo ao seu cluster Kubernetes quanto se configuram para usar o Azure Active Directory (AD) para autenticação do usuário, acesso administrativo ao Azure AD.  

Este artigo explica como configurar a autenticação para controlar o acesso ao recurso Live Data (preview) do cluster:

- RBAC (Role-based Access Control, controle de acesso baseado em função) habilitado para cluster AKS
- O Azure Active Directory integrou o cluster AKS. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esse recurso. Esse recurso depende de acessar diretamente a API do Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança de rede para bloquear a API do Kubernetes a partir deste proxy bloqueará esse tráfego. 

>[!NOTE]
>Este recurso está disponível em todas as regiões do Azure, incluindo o Azure China. Atualmente, não está disponível no Governo dos EUA do Azure.

## <a name="authentication-model"></a>Modelo de autenticação

Os recursos live data (visualização) utilizam a API `kubectl` kubernetes, idêntica à ferramenta de linha de comando. Os pontos finais da API do Kubernetes utilizam um certificado auto-assinado, que seu navegador não poderá validar. Esse recurso utiliza um proxy interno para validar o certificado com o serviço AKS, garantindo que o tráfego seja confiável.

O portal Azure solicita que você valide suas credenciais de login para um cluster do Azure Active Directory e redirecione-o para a configuração de registro do cliente durante a criação de clusters (e reconfigurado neste artigo). Esse comportamento é semelhante ao processo `kubectl`de autenticação exigido por . 

>[!NOTE]
>A autorização para o cluster é gerenciada pela Kubernetes e o modelo de segurança com o que ele está configurado. Os usuários que acessam esse recurso exigem permissão para baixar `az aks get-credentials -n {your cluster name} -g {your resource group}`a configuração do Kubernetes *(kubeconfig),* semelhante à execução . Este arquivo de configuração contém o token de autorização e autenticação para a função de usuário do cluster de **serviço azure Kubernetes,** no caso de clusters Azure RBAC e AKS sem autorização RBAC ativada. Ele contém informações sobre o Azure AD e os detalhes de registro do cliente quando o AKS está habilitado com o adesão único baseado em SAML (Azure Active Directory) com base no SAML.

>[!IMPORTANT]
>Os usuários desses recursos exigem [o Azure Kubernetes Cluster User Role](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) para o cluster, a fim de baixar o `kubeconfig` recurso e usar esse recurso. Os usuários **não** exigem acesso do contribuinte ao cluster para utilizar esse recurso. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Usando clusterMonitoringUser com clusters habilitados para RBAC

Para eliminar a necessidade de aplicar alterações adicionais de **clusterUser** configuração para permitir que o cluster de vinculação de função do usuário KubernetesAcesse o usuário ao recurso De dados vivos (visualização) após a habilitação da autorização [do RBAC,](#configure-kubernetes-rbac-authorization) a AKS adicionou uma nova vinculação de função de cluster Kubernetes chamada **clusterMonitoringUser**. Essa vinculação de função de cluster tem todas as permissões necessárias para acessar a API do Kubernetes e os pontos finais para utilizar o recurso Dados vivos (visualização).

Para utilizar o recurso Dados vivos (visualização) com este novo usuário, você precisa ser um membro da função [Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) no recurso de cluster AKS. O Monitor Azure para contêineres, quando ativado, é configurado para autenticar usando este usuário por padrão. Se a vinculação de função clusterMonitoringUser não existir em um cluster, **clusterUser** será usado para autenticação.

A AKS lançou essa nova função em janeiro de 2020, de modo que os clusters criados antes de janeiro de 2020 não o têm. Se você tiver um cluster criado antes de janeiro de 2020, o novo **clusterMonitoringUser** pode ser adicionado a um cluster existente realizando uma operação PUT no cluster ou realizando qualquer outra operação no cluster tha executa uma operação PUT no cluster, como atualizar a versão do cluster.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster do Kubernetes sem RBAC habilitado

Se você tiver um cluster do Kubernetes que não está configurado com autorização do RBAC do Kubernetes ou integrado ao Azure AD com logon único, não precisará seguir estas etapas. Isso porque você tem permissões administrativas por padrão em uma configuração não RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Configurar a autorização kubernetes RBAC

Quando você habilita a autorização do Kubernetes RBAC, dois usuários são utilizados: **clusterUser** e **clusterAdmin** para acessar a API kubernetes. Isso é semelhante `az aks get-credentials -n {cluster_name} -g {rg_name}` ao correr sem a opção administrativa. Isso significa que o **clusterUser** precisa ter acesso aos pontos finais da API kubernetes.

As etapas de exemplo a seguir demonstram como configurar a associação de função de cluster com base no modelo de configuração yaml.

1. Copie e cole o arquivo yaml e salve-o como LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Para atualizar sua configuração, `kubectl apply -f LogReaderRBAC.yaml`execute o seguinte comando: .

>[!NOTE] 
> Se você aplicou uma versão `LogReaderRBAC.yaml` anterior do arquivo ao seu cluster, atualize-o copiando e colando o novo código mostrado na etapa 1 acima e, em seguida, execute o comando mostrado na etapa 2 para aplicá-lo ao seu cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurar autenticação integrada a AD 

Um cluster AKS configurado para usar o Azure Active Directory (AD) para autenticação do usuário utiliza as credenciais de login da pessoa que acessa esse recurso. Nesta configuração, você pode fazer login em um cluster AKS usando o token de autenticação Azure AD.

O registro do cliente Azure AD deve ser reconfigurado para permitir que o portal Azure redirecione páginas de autorização como uma URL de redirecionamento confiável. Os usuários do Azure AD têm acesso diretamente aos mesmos pontos finais da API do Kubernetes através **de ClusterRoles** e **ClusterRoleBindings**. 

Para obter mais informações sobre a configuração avançada de segurança em Kubernetes, revise a documentação do [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Se você estiver criando um novo cluster habilitado para RBAC, consulte [Integrar o Diretório Ativo do Azure com o Azure Kubernetes Service](../../aks/azure-ad-integration.md) e siga as etapas para configurar a autenticação Azure AD. Durante as etapas para criar o aplicativo cliente, uma nota nessa seção destaca os dois URLs de redirecionamento que você precisa criar para o Monitor Azure para recipientes correspondentes aos especificados na Etapa 3 abaixo.

### <a name="client-registration-reconfiguration"></a>Reconfiguração do registro de clientes

1. Localize o registro do cliente para o seu cluster Kubernetes no Azure AD em **azure Active Directory > registros de aplicativos** no portal Azure.

2. Selecione **Autenticação** no painel esquerdo. 

3. Adicione dois URLs redirecionados a esta lista como tipos de aplicativos **da Web.** O valor da URL `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de primeira base deve `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`ser e o valor da URL de segunda base deve ser .

    >[!NOTE]
    >Se você estiver usando esse recurso no Azure China, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` o valor da URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`de primeira base deve ser e o valor da URL de segunda base deve ser . 
    
4. Depois de registrar os URLs de redirecionamento, em **subvenção implícita,** selecione as opções **Tokens de acesso** e **tokens de ID** e, em seguida, salve suas alterações.

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para o single-sign on só pode ser realizada durante a implantação inicial de um novo cluster AKS. Não é possível configurar o logon único em um cluster do AKS já implantado.
  
>[!IMPORTANT]
>Se você reconfigurou o Azure AD para autenticação do usuário usando o URI atualizado, limpe o cache do seu navegador para garantir que o token de autenticação atualizado seja baixado e aplicado.

## <a name="grant-permission"></a>Conceder permissão

Cada conta Azure AD deve ser concedida permissão às APIs apropriadas em Kubernetes para acessar o recurso Dados vivos (visualização). As etapas para conceder a conta do Azure Active Directory são semelhantes às etapas descritas na seção [de autenticação RBAC do Kubernetes.](#configure-kubernetes-rbac-authorization) Antes de aplicar o modelo de configuração yaml ao cluster, substitua **clusterUser** em **ClusterRoleBinding** pelo usuário desejado. 

>[!IMPORTANT]
>Se o usuário para o que você concede a vinculação RBAC estiver no mesmo inquilino do Azure AD, atribua permissões com base no usuárioPrincipalName. Se o usuário estiver em um inquilino Azure AD diferente, consulte e use a propriedade objectId.

Para obter ajuda adicional para configurar o cluster AKS **ClusterRoleBinding,** consulte [Criar vinculação RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem autenticação de configuração, você pode visualizar [métricas,](container-insights-livedata-metrics.md) [implantações](container-insights-livedata-deployments.md)e [eventos e logs](container-insights-livedata-overview.md) em tempo real a partir do seu cluster.
