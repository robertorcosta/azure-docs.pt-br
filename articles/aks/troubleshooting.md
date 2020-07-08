---
title: Solucionar problemas comuns do Serviço do Azure Kubernetes
description: Aprenda a solucionar problemas comuns ao usar o Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 08668289faa2341389a80b00cba11a33021da608
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054382"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Quando cria ou gerencia clusters do AKS (Serviço de Kubernetes do Azure), você pode ocasionalmente se deparar com problemas. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre como depurar problemas do Kubernetes?

Experimente o [guia oficial para solucionar problemas de clusters de Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também um [guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publicado por um engenheiro da Microsoft sobre solução de problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de “cota excedida” durante a criação ou atualização. O que devo fazer? 

 [Solicitar mais núcleos](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para o AKS?

A configuração máxima de pods por nó é 30 por padrão, se você implantar um cluster AKS no portal do Azure.
A configuração máxima de pods por nó é 110 por padrão, se você implantar um cluster AKS na CLI do Azure. (Verifique se está usando a versão mais recente da CLI do Azure). Essa configuração pode ser alterada usando o sinalizador `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo o erro “insuficienteSubnetSize” ao implantar um cluster AKS com a rede avançada. O que devo fazer?

Esse erro indica que uma sub-rede em uso para um cluster não tem mais IPs disponíveis em seu CIDR para atribuição de recursos bem-sucedida. Para clusters Kubenet, o requisito é espaço IP suficiente para cada nó no cluster. Para clusters CNI do Azure, o requisito é espaço IP suficiente para cada nó e Pod no cluster.
Leia mais sobre o [design do Azure CNI para atribuir IPS a pods](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Esses erros também são exibidos no [diagnóstico de AKs](https://docs.microsoft.com/azure/aks/concepts-diagnostics) , que mostra de forma proativa problemas como um tamanho de sub-rede insuficiente.

Os três (3) casos a seguir causam um erro de tamanho de sub-rede insuficiente:

1. Escala de AKS ou escala de Nodepool AKS
   1. Se estiver usando Kubenet, isso ocorrerá quando o `number of free IPs in the subnet` for **menor que** o `number of new nodes requested` .
   1. Se estiver usando o Azure CNI, isso ocorrerá quando o `number of free IPs in the subnet` for **menor que** o `number of nodes requested times (*) the node pool's --max-pod value` .

1. Atualização do AKS ou atualização do AKS Nodepool
   1. Se estiver usando Kubenet, isso ocorrerá quando o `number of free IPs in the subnet` for **menor** que o `number of buffer nodes needed to upgrade` .
   1. Se estiver usando o Azure CNI, isso ocorrerá quando o `number of free IPs in the subnet` for **menor que** o `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Por padrão, os clusters AKS definem um valor de surto máximo (buffer de atualização) de um (1), mas esse comportamento de atualização pode ser personalizado definindo o [valor máximo de surto de um pool de nós](upgrade-cluster.md#customize-node-surge-upgrade-preview) que aumentará o número de IPS disponíveis necessários para concluir uma atualização.

1. AKS Create ou AKS Nodepool Add
   1. Se estiver usando Kubenet, isso ocorrerá quando o `number of free IPs in the subnet` for **menor** que o `number of nodes requested for the node pool` .
   1. Se estiver usando o Azure CNI, isso ocorrerá quando o `number of free IPs in the subnet` for **menor que** o `number of nodes requested times (*) the node pool's --max-pod value` .

A atenuação a seguir pode ser executada com a criação de novas sub-redes. A permissão para criar uma nova sub-rede é necessária para atenuação devido à incapacidade de atualizar o intervalo CIDR de uma sub-rede existente.

1. Recrie uma nova sub-rede com um intervalo CIDR maior suficiente para os objetivos da operação:
   1. Crie uma nova sub-rede com um novo intervalo de não sobreposição desejado.
   1. Crie um novo nodepool na nova sub-rede.
   1. Dissipe pods do nodepool antigo que reside na sub-rede antiga a ser substituída.
   1. Exclua a sub-rede antiga e a antiga nodepool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o pod trave nesse modo. Você pode examinar:

* O próprio pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl logs <pod-name>`.

Para obter mais informações sobre como solucionar problemas de pod, consulte [Depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Estou recebendo `TCP timeouts` ao usar `kubectl` ou outras ferramentas de terceiros conectando-se ao servidor de API
AKS tem planos de controle de alta disponibilidade que são dimensionados verticalmente de acordo com o número de núcleos para garantir seus objetivos de nível de serviço (SLOs) e SLAs (contratos de nível de serviço). Se você estiver tendo conexões expirando, verifique o seguinte:

- **Todos os seus comandos de API expiram consistentemente ou apenas alguns?** Se for apenas alguns, seu `tunnelfront` Pod ou `aks-link` Pod, responsável pela comunicação do plano de controle de > de nó, pode não estar em estado de execução. Certifique-se de que os nós que hospedam esse Pod não sejam superutilizados ou sob estresse. Considere movê-los para seu próprio [ `system` pool de nós](use-system-pools.md).
- **Você abriu todas as portas necessárias, os FQDNs e os IPs indicados nos [documentos de tráfego de egresso AKs restringir](limit-egress-traffic.md)?** Caso contrário, podem ocorrer falhas em várias chamadas de comandos.
- **O IP atual é coberto por [intervalos autorizados de IP de API](api-server-authorized-ip-ranges.md)?** Se você estiver usando esse recurso e o IP não estiver incluído nos intervalos, suas chamadas serão bloqueadas. 
- **Você tem um cliente ou aplicativo que está vazando chamadas para o servidor de API?** Certifique-se de usar inspeções em vez de chamadas get frequentes e de que seus aplicativos de terceiros não estão vazando essas chamadas. Por exemplo, um bug no mixer İSTİO faz com que uma nova conexão de inspeção do servidor de API seja criada toda vez que um segredo é lido internamente. Como esse comportamento ocorre em intervalos regulares, observe que as conexões se acumulam rapidamente e, eventualmente, fazem com que o servidor de API se torne sobrecarregado, independentemente do padrão de dimensionamento. https://github.com/istio/istio/issues/19481
- **Você tem muitas versões em suas implantações do Helm?** Esse cenário pode fazer com que o gaveta use muita memória nos nós, bem como uma grande quantidade de `configmaps` , o que pode causar picos desnecessários no servidor de API. Considere configurar `--history-max` em `helm init` e aproveitar o novo Helm 3. Mais detalhes sobre os seguintes problemas: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543


## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar o RBAC (controle de acesso baseado em função) em um cluster existente. Como posso fazer isso?

No momento, não há suporte para habilitar o RBAC (controle de acesso baseado em função) em clusters existentes, ele deve ser definido no momento da criação dos clusters. O RBAC é habilitado por padrão ao usar a CLI, o portal ou uma versão de API posterior à `2020-03-01`.

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com o RBAC habilitado e agora vejo muitos avisos no painel do Kubernetes. O painel costumava trabalhar sem nenhum aviso. O que devo fazer?

O motivo para os avisos é que o cluster tem o RBAC habilitado e o acesso ao painel agora está restrito por padrão. Em geral, essa abordagem é uma boa prática, já que a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser ativar o painel, siga os passos nesta [postagem de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter os logs, usando kubectl logs ou não é possível conectar-se ao servidor de API. Estou recebendo o "Erro do servidor: erro de discagem back-end: discar tcp...". O que devo fazer?

Verifique se as portas 22, 9000 e 1194 estão abertas para se conectar ao servidor da API. Verifique se o pod `tunnelfront` ou `aks-link` está em execução no namespace *kube-system* usando o comando `kubectl get pods --namespace kube-system`. Se não estiver, force a exclusão do pod e ele será reiniciado.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Eu estou tentando atualizar ou dimensionar e estou recebendo um erro `"Changing property 'imageReference' is not allowed"`. Como faço para corrigir esse problema?

Você pode estar recebendo este erro porque modificou as tags nos nós do agente dentro do cluster do AKS. Modificar ou excluir marcas e outras propriedades dos recursos no grupo de recursos MC_* pode levar a resultados inesperados. Alterar os recursos no grupo MC_* no cluster do AKS quebra o SLO (objetivo de nível de serviço).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou recebendo erros indicando que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que ele seja corrigido

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-cluster-failed*

Esse erro ocorre quando os clusters entram em estado de falha por vários motivos. Siga as etapas abaixo para resolver o estado de falha do cluster antes de repetir a operação com falha anterior:

1. Até que o cluster esteja fora do estado `failed`, as operações `upgrade` e `scale` não terão sucesso. Os problemas raiz e as soluções comuns incluem:
    * Dimensionamento com **cota de computação (CRP) insuficiente**. Para resolver, primeiro dimensione o cluster de volta para uma meta de estado estável dentro da cota. Depois, siga estas [etapas para solicitar um aumento da cota de computação](../azure-portal/supportability/resource-manager-core-quotas-request.md) antes de voltar a tentar escalar verticalmente além dos limites de cota iniciais.
    * Dimensionar um cluster com rede avançada e **recursos de sub-rede (rede) insuficientes**. Para resolver, primeiro dimensione o cluster de volta para uma meta de estado estável dentro da cota. Depois, siga estas [etapas para solicitar um aumento dos recursos de computação](../azure-resource-manager/templates/error-resource-quota.md#solution) antes de voltar a tentar escalar verticalmente além dos limites de cota iniciais.
2. Após a causa subjacente da falha de atualização ser resolvida, o cluster deverá ter um estado bem-sucedido. Quando o estado bem-sucedido for verificado, repita a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Estou recebendo erros ao tentar atualizar ou dimensionar indicando que meu cluster está sendo atualizado ou que a atualização falhou

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-pending-upgrade*

 Você não pode fazer com que um cluster ou pool de nós seja atualizado e dimensionado simultaneamente. Em vez disso, cada tipo de operação deve ser concluída no recurso de destino antes da próxima solicitação no mesmo recurso. Como resultado, as operações ficam limitadas quando operações ativas de atualização ou dimensionamento estão ocorrendo ou sendo tentadas. 

Para ajudar a diagnosticar o problema, execute `az aks show -g myResourceGroup -n myAKSCluster -o table` para recuperar o status detalhado do cluster. Com base no resultado:

* Se o cluster estiver sendo atualizado ativamente, aguarde até que a operação seja concluída. Se ela foi bem-sucedida, repita a operação que falhou anteriormente.
* Se a atualização do cluster falhou, siga as etapas descritas na seção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mover meu cluster para outra assinatura ou mover minha assinatura com meu cluster para outro locatário?

Se você mover seu cluster do AKS para outra assinatura ou a assinatura do cluster para outro locatário, o cluster não funcionará devido à ausência de permissões de identidade do cluster. **O AKS não dá suporte à movimentação de clusters entre assinaturas ou locatários** devido a essa restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou recebendo erros ao tentar usar recursos que exigem conjuntos de dimensionamento de máquinas virtuais

*Esta assistência para solução de problemas é direcionada de aka.ms/aks-vmss-enablement*

Você pode receber erros indicando que seu cluster do AKS não está em um conjunto de dimensionamento de máquinas virtuais, como o seguinte exemplo:

**O AgentPool `<agentpoolname>` definiu o dimensionamento automático como habilitado, mas não está nos Conjuntos de Dimensionamento de Máquinas Virtuais**

Recursos como o autodimensionador de cluster ou pools com vários nós exigem conjuntos de dimensionamento de máquinas virtuais como o `vm-set-type`.

Siga as etapas *Antes de começar* indicadas no documento apropriado para criar corretamente um cluster do AKS:

* [Usar o autodimensionador de cluster](cluster-autoscaler.md)
* [Criar e usar pools com vários nós](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quais restrições de nomenclatura são impostas para recursos e parâmetros do AKS?

*Esta assistência para solução de problemas é direcionada de aka.ms/aks-naming-rules*

As restrições de nomenclatura são implementadas pela plataforma do Azure e pelo AKS. Se o nome de um recurso ou parâmetro não atender a alguma dessas restrições, será retornado um erro solicitando que você forneça uma entrada diferente. As seguintes diretrizes de nomenclatura comuns se aplicam:

* Os nomes de cluster devem ter entre 1 e 63 caracteres. Letras, números, traços e sublinhados são os únicos caracteres permitidos. O primeiro e o último caractere devem ser uma letra ou um número.
* O nome do grupo de recursos Nó/*MC_* do AKS combina o nome do grupo de recursos e o nome do recurso. A sintaxe gerada automaticamente de `MC_resourceGroupName_resourceName_AzureRegion` não pode ter mais de 80 caracteres. Se necessário, reduza o tamanho do nome do grupo de recursos ou do cluster do AKS. Você também pode [personalizar o nome do grupo de recursos do nó](cluster-configuration.md#custom-resource-group-name)
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos e deve ter entre 1 e 54 caracteres. Os caracteres válidos incluem valores alfanuméricos e hifens (-). O *dnsPrefix* não pode incluir caracteres especiais, como um ponto (.).
* Os nomes do Pool de nós do AKS devem estar em letras minúsculas e ter de 1 a 11 caracteres para pools de nós do Linux e de 1 a 6 caracteres para pools de nós do Windows. O nome deve começar com uma letra e os únicos caracteres permitidos são letras e números.
* O *admin-username*, que define o nome de usuário do administrador para nós do Linux, deve começar com uma letra, pode conter apenas letras, números, hifens e sublinhados e tem um comprimento máximo de 64 caracteres.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou recebendo erros ao tentar criar, atualizar, dimensionar, excluir ou atualizar o cluster, indicando que a operação não é permitida porque outra operação está em andamento.

*Esta assistência para solução de problemas é direcionada de aka.ms/aks-pending-operation*

As operações do cluster ficam limitadas quando uma operação anterior ainda está em andamento. Para recuperar um status detalhado do cluster, use o comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Use seu grupo de recursos e o nome do cluster do AKS conforme necessário.

Com base na saída do status do cluster:

* Se o cluster estiver em um estado de provisionamento diferente de *Êxito* ou *Falha* (*Atualizando/Criando/Dimensionando/Excluindo/Migrando*), aguarde até que a operação seja concluída. Quando a operação anterior for concluída, repita a operação mais recente do cluster.

* Se ocorrer uma falha no upgrade do cluster, siga as etapas descritas em [Estou recebendo erros indicando que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que ele seja corrigido](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Estou recebendo um erro indicando que minha entidade de serviço não foi encontrada ou é inválida quando tento criar um cluster.

Ao criar um cluster do AKS, ele precisa de uma entidade de serviço ou de uma identidade gerenciada para criar recursos em seu nome. O AKS pode criar automaticamente uma entidade de serviço no momento da criação do cluster ou pode receber uma existente. Ao usar uma entidade criada automaticamente, o Azure Active Directory precisa propagá-la para todas as regiões para que a criação seja realizada com sucesso. Quando a propagação demora muito, o cluster falha na validação da criação, pois não encontra uma entidade de serviço disponível para fazer isso. 

Use as seguintes soluções alternativas para esse problema:
* Use uma entidade de serviço existente, que já foi propagada pelas regiões e pode ser passada para o AKS no momento da criação do cluster.
* Se estiver usando scripts de automação, adicione atrasos entre a criação da entidade de serviço e a criação do cluster do AKS.
* Se estiver usando o portal do Azure, volte para as configurações do cluster durante a criação e tente novamente a página de validação após alguns minutos.





## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Estou recebendo erros depois de restringir o tráfego de saída

Ao restringir o tráfego de saída de um cluster do AKS, há portas de saída/regras de rede e regras de FQDN/aplicativo [obrigatórias e opcionais recomendadas](limit-egress-traffic.md) para o AKS. Se as suas configurações estiverem em conflito com alguma dessas regras, determinados comandos `kubectl` não funcionarão corretamente. Você também poderá ver erros ao criar um cluster do AKS.

Verifique se as suas configurações não estão em conflito com alguma das portas de saída/regras de rede e regras de FQDN/aplicativo obrigatórias ou opcionais recomendadas.

## <a name="azure-storage-and-aks-troubleshooting"></a>Solução de problemas do Armazenamento do Azure e do AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas do Kubernetes para o disco do Azure? 

| Versão do Kubernetes | Versão recomendada |
|--|:--:|
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Falha de WaitForAttach para o Disco do Azure: analisando "/dev/disk/Azure/scsi1/lun1": sintaxe inválida

No Kubernetes versão 1.10, MountVolume.WaitForAttach pode falhar com uma remontagem do Disco do Azure.

No Linux, você pode ver um erro de formato de DevicePath incorreto. Por exemplo:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

No Windows, você pode ver um erro de número de DevicePath (LUN) incorreto. Por exemplo:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Esse problema foi corrigido nas seguintes versões do Kubernetes:

| Versão do Kubernetes | Versão corrigida |
|--|:--:|
| 1,10 | 1.10.2 ou posterior |
| 1.11 | 1.11.0 ou posterior |
| 1.12 e posterior | N/D |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Falha ao definir uid e gid em mountOptions para o Disco do Azure

O Disco do Azure usa o sistema de arquivos ext4,xfs por padrão e mountOptions como uid=x,gid=x não pode ser definido no momento da montagem. Por exemplo, se você tentar definir mountOptions com uid=999,gid=999, verá um erro como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Você pode atenuar o problema seguindo uma destas opções:

* [Configurar o contexto de segurança para um pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) definindo uid em runAsUser e gid em fsGroup. Por exemplo, a seguinte configuração definirá a execução do pod na raiz, tornando-o acessível para qualquer arquivo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Como gid e uid são montados como raiz ou 0 por padrão, se gid ou uid forem definidos como não raiz, por exemplo, como 1000, o Kubernetes usará `chown` para alterar todos os diretórios e arquivos nesse disco. Essa operação pode ser demorada e pode tornar a montagem do disco muito lenta.

* Usar `chown` em initContainers para definir gid e uid. Por exemplo:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Falha ao desanexar Disco do Azure, que leva a possíveis problemas de condição de corrida e lista de discos de dados inválida

Quando a desanexação de um Disco do Azure falha, ele tenta desanexar o disco novamente até seis vezes usando a retirada exponencial. Ele também mantém um bloqueio no nível do nó na lista de discos de dados por aproximadamente três minutos. Se a lista de discos for atualizada manualmente durante esse período, isso fará com que a lista de discos mantida pelo bloqueio no nível do nó fique obsoleta e cause instabilidade no nó.

Esse problema foi corrigido nas seguintes versões do Kubernetes:

| Versão do Kubernetes | Versão corrigida |
|--|:--:|
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |
| 1.15 e posterior | N/D |

Se estiver usando uma versão do Kubernetes que não tem a correção para esse problema e seu nó tiver uma lista de discos obsoletos, você poderá atenuar o problema desanexando todos os discos inexistentes da VM como uma operação em massa. **A desanexação individual de discos inexistentes pode falhar.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Um grande número de Discos do Azure causa lentidão na anexação/desanexação

Quando o número de operações de anexação/desanexação de Disco do Azure visando uma só VM do nó é maior que 10 ou é maior que 3 visando um só pool de conjuntos de dimensionamento de máquinas virtuais, elas podem ser mais lentas do que o esperado, pois são feitas em sequência. Esse problema é uma limitação conhecida e não há soluções alternativas no momento. [Item do UserVoice para dar suporte à anexação/desanexação paralela além de um número.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for).

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Falha de desanexação de Disco do Azure que leva à VM do nó potencial a ter estado de falha

Em alguns casos de borda, uma desanexação de Disco do Azure pode falhar parcialmente e deixar a VM do nó em estado de falha.

Esse problema foi corrigido nas seguintes versões do Kubernetes:

| Versão do Kubernetes | Versão corrigida |
|--|:--:|
| 1.12 | 1.12.10 ou posterior |
| 1.13 | 1.13.8 ou posterior |
| 1,14 | 1.14.4 ou posterior |
| 1.15 e posterior | N/D |

Se estiver usando uma versão do Kubernetes que não tem a correção para esse problema e seu nó estiver em estado de falha, você poderá atenuar o problema atualizando manualmente o status da VM usando um dos seguintes:

* Para um cluster baseado em conjunto de disponibilidade:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Solução de problemas dos Arquivos do Azure e do AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quais são as versões estáveis recomendadas do Kubernetes para os Arquivos do Azure?
 
| Versão do Kubernetes | Versão recomendada |
|--|:--:|
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1,14 | 1.14.0 ou posterior |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quais são as mountOptions padrão ao usar os Arquivos do Azure?

Configurações recomendadas:

| Versão do Kubernetes | Valor de fileMode e dirMode|
|--|:--:|
| 1.12.0 – 1.12.1 | 0755 |
| 1.12.2 e posterior | 0777 |

As opções de montagem podem ser especificadas no objeto de classe de armazenamento. O exemplo a seguir define *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Algumas outras configurações úteis de *mountOptions*:

* *mfsymlinks* fará com que a montagem dos Arquivos do Azure (cifs) dê suporte a links simbólicos
* *nobrl* impedirá o envio de solicitações de bloqueio de intervalo de bytes para o servidor. Essa configuração é necessária para determinados aplicativos que interrompem com bloqueios de intervalo de bytes obrigatórios de estilo cifs. A maioria dos servidores cifs ainda não dá suporte à solicitação de bloqueios de intervalo de bytes por consultoria. Se não estiver usando *nobrl*, os aplicativos interrompidos com bloqueios de intervalo de bytes obrigatórios de estilo cifs poderão causar mensagens de erro semelhantes a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erro "não foi possível alterar as permissões" ao usar os Arquivos do Azure

Ao executar o PostgreSQL no plug-in dos Arquivos do Azure, você poderá ver um erro semelhante a:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Esse erro é causado porque o plug-in dos Arquivos do Azure está usando o protocolo cifs/SMB. Ao usar o protocolo cifs/SMB, as permissões de arquivo e diretório não puderam ser alteradas após a montagem.

Para resolver esse problema, use *subPath* com o plug-in de Disco do Azure. 

> [!NOTE] 
> Para o tipo de disco ext3/4, há um diretório lost+found depois que o disco é formatado.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Os Arquivos do Azure têm latência alta em comparação com o Disco do Azure ao lidar com muitos arquivos pequenos

Em alguns casos, como ao lidar com muitos arquivos pequenos, você pode ter latência alta ao usar os Arquivos do Azure em comparação com o Disco do Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erro ao habilitar a configuração "Permitir acesso da rede selecionada" na conta de armazenamento

Se habilitar *permitir acesso da rede selecionada* em uma conta de armazenamento usada para provisionamento dinâmico no AKS, você receberá um erro quando o AKS criar um compartilhamento de arquivo:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Esse erro ocorre quando o *persistentvolume-controller* do Kubernetes não está na rede escolhida ao definir *permitir acesso da rede selecionada*.

Você pode atenuar o problema usando o [provisionamento estático com os Arquivos do Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Os Arquivos do Azure falham ao remontar no pod do Windows

Se um pod do Windows com uma montagem dos Arquivos do Azure for excluído e agendado para ser recriado no mesmo nó, a montagem falhará. Essa falha ocorre devido à falha do comando `New-SmbGlobalMapping`, pois a montagem dos Arquivos do Azure já está montada no nó.

Por exemplo, você poderá ver um erro semelhante a:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Esse problema foi corrigido nas seguintes versões do Kubernetes:

| Versão do Kubernetes | Versão corrigida |
|--|:--:|
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1.14 e posterior | N/D |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>A montagem dos Arquivos do Azure falha porque a chave de conta de armazenamento foi alterada

Se a sua chave de conta de armazenamento tiver sido alterada, você poderá ver falhas na montagem dos Arquivos do Azure.

Você pode atenuar o problema atualizando manualmente o campo `azurestorageaccountkey` em um segredo de arquivo do Azure com sua chave de conta de armazenamento codificada em base64.

Para codificar a chave de conta de armazenamento em base64, você pode usar `base64`. Por exemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar seu arquivo secreto do Azure, use `kubectl edit secret`. Por exemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó do agente tentará novamente a montagem do Arquivo do Azure com a chave de armazenamento atualizada.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>O autodimensionador do cluster falha ao dimensionar, com o erro falha ao corrigir os tamanhos do grupo de nós

Se o autodimensionador de cluster não estiver sendo escalado/reduzido horizontalmente e você vir um erro como o mostrado abaixo nos [logs do autodimensionador de cluster][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Esse erro ocorre devido a uma condição de corrida upstream no autodimensionador de cluster. Nesse caso, o autodimensionador de cluster termina com um valor diferente daquele que realmente está no cluster. Para sair desse estado, desabilite e habilite novamente o [autodimensionador de cluster][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Anexação de disco lenta, GetAzureDiskLun leva de 10 a 15 minutos e você recebe um erro

Nas versões do Kubernetes **mais antigas que a 1.15.0**, você pode receber um erro como **Erro WaitForAttach não é capaz de encontrar o Lun para o disco**.  A solução alternativa para esse problema é aguardar aproximadamente 15 minutos e tentar novamente.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
