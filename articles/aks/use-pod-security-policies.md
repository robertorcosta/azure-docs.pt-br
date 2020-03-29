---
title: Use políticas de segurança de pod no Azure Kubernetes Service (AKS)
description: Saiba como controlar as admissões de podusando podSecurityPolicy no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 74177136a7a61186ab1d273b57dbfce550a18ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914527"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Visualização - Proteja seu cluster usando políticas de segurança de pod no Azure Kubernetes Service (AKS)

Para melhorar a segurança do seu cluster AKS, você pode limitar quais pods podem ser agendados. Pods que solicitam recursos que você não permite não podem ser executados no cluster AKS. Você define esse acesso usando políticas de segurança de pod. Este artigo mostra como usar as políticas de segurança do pod para limitar a implantação de pods em AKS.

> [!IMPORTANT]
> Os recursos de visualização do AKS são opt-in de autoatendimento. As visualizações são fornecidas "como está" e "conforme disponível" e são excluídas dos contratos de nível de serviço e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente na melhor base de esforço. Como tal, esses recursos não são destinados ao uso da produção. Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar as políticas de segurança do pod, você precisa da versão de extensão CLI de *visualização aks* 0.4.1 ou superior. Instale a extensão *aks-preview* Azure CLI usando o comando [az extension add][az-extension-add] e verifique se há atualizações disponíveis usando o comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registre o provedor de recursos de política de segurança do pod

Para criar ou atualizar um cluster AKS para usar políticas de segurança do pod, primeiro habilite um sinalizador de recurso em sua assinatura. Para registrar o sinalizador de recurso *PodSecurityPolicyPreview,* use o comando [az feature register][az-feature-register] como mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, você não pode atualmente desregistrar esse recurso. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS criados na assinatura. Não habilite recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar feedback.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft.ContainerService* usando o comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Visão geral das políticas de segurança de pod

Em um cluster Kubernetes, um controlador de admissão é usado para interceptar solicitações para o servidor de API quando um recurso deve ser criado. O controlador de admissão pode então *validar* a solicitação de recurso em um conjunto de regras ou *alterar* o recurso para alterar parâmetros de implantação.

*PodSecurityPolicy* é um controlador de admissão que valida uma especificação de pod atende aos requisitos definidos. Esses requisitos podem limitar o uso de contêineres privilegiados, o acesso a certos tipos de armazenamento ou o usuário ou grupo que o contêiner pode executar como. Quando você tenta implantar um recurso onde as especificações do pod não atendam aos requisitos descritos na política de segurança do pod, a solicitação é negada. Essa capacidade de controlar quais pods podem ser agendados no cluster AKS evita algumas possíveis vulnerabilidades de segurança ou escalações de privilégios.

Quando você habilita a política de segurança de pod em um cluster AKS, algumas políticas padrão são aplicadas. Essas políticas padrão fornecem uma experiência fora da caixa para definir quais pods podem ser agendados. No entanto, os usuários de cluster podem ter problemas na implantação de pods até que você defina suas próprias políticas. A abordagem recomendada é:

* Criar um cluster AKS
* Defina suas próprias políticas de segurança de pod
* Habilite o recurso de política de segurança do pod

Para mostrar como as políticas padrão limitam as implantações do pod, neste artigo primeiro habilitamos o recurso de políticas de segurança do pod e, em seguida, criamos uma diretiva personalizada.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Habilite a política de segurança do pod em um cluster AKS

Você pode ativar ou desativar a política de segurança do pod usando o comando [az aks update.][az-aks-update] O exemplo a seguir permite a política de segurança do pod no nome de cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*.

> [!NOTE]
> Para uso no mundo real, não habilite a política de segurança do pod até que você tenha definido suas próprias políticas personalizadas. Neste artigo, você habilita a política de segurança do pod como o primeiro passo para ver como as políticas padrão limitam as implantações de pods.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas AKS padrão

Quando você habilita a política de segurança de pod, a AKS cria uma política padrão chamada *privilegiada*. Não edite ou remova a diretiva padrão. Em vez disso, crie suas próprias políticas que definem as configurações que você deseja controlar. Vamos primeiro ver quais são essas políticas padrão como elas afetam as implantações de pods.

Para visualizar as políticas disponíveis, use o comando [kubectl get psp,][kubectl-get] como mostrado no exemplo a seguir

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A política de segurança de pod *privilegiada* é aplicada a qualquer usuário autenticado no cluster AKS. Esta atribuição é controlada por ClusterRoles e ClusterRoleBindings. Use o comando [kubectl get clusterrolebindings][kubectl-get] e procure o *padrão:privilegiado:* binding:

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

Como mostrado na seguinte saída condensada, o *clusterRole psp:restrito* é atribuído a qualquer *sistema:usuários autenticados.* Essa habilidade fornece um nível básico de restrições sem que suas próprias políticas sejam definidas.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

É importante entender como essas políticas padrão interagem com as solicitações do usuário para agendar pods antes de começar a criar suas próprias políticas de segurança de pod. Nas próximas seções, vamos agendar alguns pods para ver essas políticas padrão em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um usuário de teste em um cluster AKS

Por padrão, quando você usa o comando [az aks get-credentials,][az-aks-get-credentials] as credenciais de `kubectl` *administrador* para o cluster AKS são adicionadas à sua configuração. O usuário de admin ignora a aplicação das políticas de segurança do pod. Se você usar a integração do Azure Active Directory para seus clusters AKS, você poderá fazer login com as credenciais de um usuário não administrador para ver a aplicação de políticas em ação. Neste artigo, vamos criar uma conta de usuário de teste no cluster AKS que você pode usar.

Crie um namespace de exemplo chamado *psp-aks* para recursos de teste usando o comando [kubectl create namespace.][kubectl-create] Em seguida, crie uma conta de serviço chamada *nonadmin-user* usando o comando [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie um RoleBinding para o *usuário de nonadmin* para executar ações básicas no namespace usando o comando [kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de alias para usuário de admin e não-admin

Para destacar a diferença entre o usuário `kubectl` de admin regular ao usar e o usuário não-admin criado nas etapas anteriores, crie dois aliases de linha de comando:

* O **alias kubectl-admin** é para o usuário de admin regular, e é escopo para o espaço de nome *psp-aks.*
* O **alias kubectl-nonadminuser** é para o *usuário de nonadmin* criado na etapa anterior, e é escopo para o namespace *psp-aks.*

Crie estes dois aliases como mostrado nos seguintes comandos:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Teste a criação de um pod privilegiado

Vamos primeiro testar o que acontece quando você agenda `privileged: true`um pod com o contexto de segurança de . Este contexto de segurança aumenta os privilégios do pod. Na seção anterior que mostrou as políticas de segurança padrão do pod AKS, a diretiva *restrita* deve negar essa solicitação.

Crie um `nginx-privileged.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Crie o pod usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

O pod não é agendado, como mostrado na saída de exemplo a seguir:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

O pod não chega ao estágio de agendamento, então não há recursos para excluir antes de seguir em frente.

## <a name="test-creation-of-an-unprivileged-pod"></a>Criação de teste de um pod desprivilegiado

No exemplo anterior, a especificação do pod solicitava escalonamento privilegiado. Essa solicitação é negada pela política de segurança de pod *restrita* padrão, de modo que o pod não é agendado. Vamos tentar agora executar o mesmo pod NGINX sem a solicitação de escalonamento de privilégios.

Crie um `nginx-unprivileged.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Crie o pod usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O agendador Kubernetes aceita a solicitação de pod. No entanto, se você olhar `kubectl get pods`para o status do pod usando, há um erro:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Use o comando [kubectl describe pod][kubectl-describe] para olhar os eventos para o pod. O exemplo condensado a seguir mostra que o contêiner e a imagem exigem permissões de raiz, mesmo que não as tenhamos solicitado:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Mesmo que não tenhamos solicitado nenhum acesso privilegiado, a imagem do contêiner para NGINX precisa criar uma vinculação para a porta *80*. Para ligar as portas *1024* e abaixo, o usuário *raiz* é necessário. Quando o pod tenta iniciar, a política de segurança do pod *restrito* nega esse pedido.

Este exemplo mostra que as políticas de segurança de pod padrão criadas pela AKS estão em vigor e restringem as ações que um usuário pode executar. É importante entender o comportamento dessas políticas padrão, pois você pode não esperar que um pod BÁSICO NGINX seja negado.

Antes de passar para a próxima etapa, exclua este pod de teste usando o comando [kubectl delete pod:][kubectl-delete]

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Criação de teste de um pod com um contexto de usuário específico

No exemplo anterior, a imagem do contêiner tentou automaticamente usar a raiz para ligar o NGINX à porta 80. Essa solicitação foi negada pela política de segurança de pod *restrita* padrão, de modo que o pod não é inicial. Vamos tentar agora executar o mesmo pod NGINX com `runAsUser: 2000`um contexto de usuário específico, como .

Crie um `nginx-unprivileged-nonroot.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Crie o pod usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

O agendador Kubernetes aceita a solicitação de pod. No entanto, se você olhar `kubectl get pods`para o status do pod usando, há um erro diferente do exemplo anterior:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Use o comando [kubectl describe pod][kubectl-describe] para olhar os eventos para o pod. O exemplo condensado a seguir mostra os eventos do pod:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Os eventos indicam que o contêiner foi criado e iniciado. Não há nada imediatamente óbvio sobre por que a cápsula está em um estado de falha. Vejando os registros de pod usando o comando [kubectl logs:][kubectl-logs]

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

O exemplo a seguir de saída de log dá uma indicação de que dentro da própria configuração NGINX, há um erro de permissões quando o serviço tenta iniciar. Este erro é novamente causado pela necessidade de vincular-se à porta 80. Embora a especificação do pod tenha definido uma conta de usuário regular, essa conta de usuário não é suficiente no nível do SO para que o serviço NGINX inicie e se vincule à porta restrita.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Novamente, é importante entender o comportamento das políticas de segurança padrão do pod. Este erro foi um pouco mais difícil de rastrear, e novamente, você pode não esperar que um pod BÁSICO NGINX seja negado.

Antes de passar para a próxima etapa, exclua este pod de teste usando o comando [kubectl delete pod:][kubectl-delete]

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Crie uma política de segurança de pod personalizada

Agora que você viu o comportamento das políticas de segurança padrão do pod, vamos fornecer uma maneira para o *usuário de nonadmin* agendar com sucesso os pods.

Vamos criar uma política para rejeitar pods que solicitam acesso privilegiado. Outras opções, como *runAsUser* ou *volumes permitidos,* não são explicitamente restritas. Esse tipo de política nega um pedido de acesso privilegiado, mas permite que o cluster execute os pods solicitados.

Crie um `psp-deny-privileged.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Crie a diretiva usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para visualizar as políticas disponíveis, use o comando [kubectl get psp,][kubectl-get] como mostrado no exemplo a seguir. Compare a diretiva *psp-deny-privileged* com a política *restrita* padrão que foi aplicada nos exemplos anteriores para criar um pod. Apenas o uso de escalada *PRIV* é negado por sua política. Não há restrições ao usuário ou grupo para a política *psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta de usuário use a política de segurança do pod personalizado

Na etapa anterior, você criou uma política de segurança de pod para rejeitar pods que solicitam acesso privilegiado. Para permitir que a diretiva seja usada, você cria uma *Função* ou um *ClusterRole*. Em seguida, você associa uma dessas funções usando um *RoleBinding* ou *ClusterRoleBinding*.

Para este exemplo, crie um ClusterRole que permita que você *use* a diretiva *psp-deny-privileged* criada na etapa anterior. Crie um `psp-deny-privileged-clusterrole.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Crie o ClusterRole usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora crie um ClusterRoleBinding para usar o ClusterRole criado na etapa anterior. Crie um `psp-deny-privileged-clusterrolebinding.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Crie um ClusterRoleBinding usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Na primeira etapa deste artigo, o recurso de política de segurança do pod foi habilitado no cluster AKS. A prática recomendada era apenas ativar o recurso de política de segurança do pod depois de definir suas próprias políticas. Este é o estágio em que você habilitaria o recurso de política de segurança do pod. Uma ou mais políticas personalizadas foram definidas e as contas de usuário foram associadas a essas políticas. Agora você pode ativar com segurança o recurso de diretiva de segurança do pod e minimizar os problemas causados pelas políticas padrão.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Teste a criação de um pod desprivilegiado novamente

Com sua política de segurança de pod personalizada aplicada e uma vinculação para que a conta de usuário use a diretiva, vamos tentar criar um pod desprivilegiado novamente. Use o `nginx-privileged.yaml` mesmo manifesto para criar o pod usando o comando [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A cápsula está programada com sucesso. Quando você verifica o status do pod usando o comando [kubectl get pods,][kubectl-get] o pod está *em execução:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como você pode criar políticas de segurança de pod personalizadas para definir o acesso ao cluster AKS para diferentes usuários ou grupos. As políticas aks padrão fornecem controles rígidos sobre quais pods podem ser executados, então crie suas próprias políticas personalizadas para definir corretamente as restrições necessárias.

Exclua o pod não privilegiado nginx usando o comando [kubectl delete][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar recursos

Para desativar a política de segurança do pod, use novamente o comando [az aks update.][az-aks-update] O exemplo a seguir desativa a política de segurança do pod no nome de cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Em seguida, exclua o ClusterRole e o ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Exclua a política de segurança usando o comando [kubectl delete][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Finalmente, exclua o espaço de nome *psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como criar uma política de segurança de pod para evitar o uso de acesso privilegiado. Existem muitos recursos que uma política pode impor, como tipo de volume ou usuário RunAs. Para obter mais informações sobre as opções disponíveis, consulte os [docs de referência de política de segurança do pod do Kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre como limitar o tráfego de rede de [pods, consulte Tráfego seguro entre pods usando políticas de rede em AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
