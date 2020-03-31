---
title: Solucionar problemas comuns do Serviço do Azure Kubernetes
description: Aprenda a solucionar problemas comuns ao usar o Serviço de Kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368325"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Quando você cria ou gerencia clusters do Serviço de Kubernetes do Azure (AKS), você pode ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre como depurar problemas do Kubernetes?

Experimente o [guia oficial para solucionar problemas de clusters de Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também um [guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publicado por um engenheiro da Microsoft sobre solução de problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de “cota excedida” durante a criação ou atualização. O que devo fazer? 

Você precisa [solicitar núcleos](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para o AKS?

A configuração máxima de pods por nó é 30 por padrão, se você implantar um cluster AKS no portal do Azure.
A configuração máxima de pods por nó é 110 por padrão, se você implantar um cluster AKS na CLI do Azure. (Verifique se está usando a versão mais recente da CLI do Azure). Essa configuração padrão pode ser alterada usando o sinalizador `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo o erro “insuficienteSubnetSize” ao implantar um cluster AKS com a rede avançada. O que devo fazer?

Se o Azure CNI (rede avançada) for usado, o AKS alocará endereços IP com base nos "pods max" por nó configurado. Com base nos pods max configurados por nó, o tamanho da sub-rede deve ser maior do que o produto do número de nós e da configuração de pod max por nó. A seguinte equação descreve isso:

Tamanho da sub-rede > número de nós no cluster (levando em consideração os requisitos futuros de dimensionamento) * pods máximos por nó definido.

Para saber mais, confira [Planejar o endereçamento IP para o cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o pod trave nesse modo. Você pode examinar:

* O próprio pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl logs <pod-name>`.

Para obter mais informações sobre como solucionar problemas de pod, consulte [Depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar RBAC em um cluster existente. Como posso fazer isso?

Infelizmente, a ativação do controle de acesso baseado em função (RBAC) em clusters existentes não é suportada no momento. Você deve explicitamente criar novos clusters. Se você usar a CLI, o RBAC é habilitado por padrão. Se você usar o portal do AKS, um botão de alternância para habilitar o RBAC está disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Eu criei um cluster com o RBAC habilitado usando a CLI do Azure com padrões ou o portal do Azure, agora vejo vários avisos no painel do Kubernetes. O painel costumava trabalhar sem nenhum aviso. O que devo fazer?

O motivo para os avisos no painel é que agora o cluster está habilitado com o RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é uma boa prática, já que a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser ativar o painel, siga os passos nesta [postagem de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo conectar-me ao painel. O que devo fazer?

A maneira mais fácil de acessar seu serviço fora do cluster é executar `kubectl proxy`, que enviará solicitações de proxy para sua porta local 8001 ao servidor Kubernetes API. A partir daí, o servidor de API pode usar um proxy ao seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se você não vir o painel do Kubernetes, verifique se o `kube-proxy` pod está sendo executado no `kube-system` namespace. Se não estiver em estado de execução, exclua o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter os logs, usando kubectl logs ou não é possível conectar-se ao servidor de API. Estou recebendo "Erro do servidor: erro discando backend: disque tcp...". O que devo fazer?

Certifique-se de que o grupo de segurança de rede padrão não foi modificado e que ambas as portas 22 e 9000 estão abertas para conexão com o servidor de API. Verifique se `tunnelfront` o pod está sendo executado no `kubectl get pods --namespace kube-system` namespace do *sistema kube* usando o comando. Se não estiver, force a exclusão do pod e ele será reiniciado.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou tentando atualizar ou dimensionar e estou recebendo um erro "mensagem: Alterar a propriedade 'imageReference' não é permitido". Como faço para corrigir esse problema?

Você pode estar recebendo este erro porque modificou as tags nos nós do agente dentro do cluster do AKS. Modificar e excluir tags e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos sob o grupo MC_ * no cluster do AKS quebra o objetivo de nível de serviço (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou recebendo erros que meu cluster está em estado falho e atualizar ou dimensionar não funcionará até que seja corrigido

*Esta assistência de solução de problemas é direcionada a partir dehttps://aka.ms/aks-cluster-failed*

Esse erro ocorre quando os clusters entram em um estado de falha por várias razões. Siga as etapas abaixo para resolver o estado de falha do cluster antes de tentar novamente a operação anteriormente falhada:

1. Até que o `failed` grupo `upgrade` esteja `scale` fora do estado, e as operações não tenham sucesso. Problemas e resoluções de raiz comuns incluem:
    * Dimensionamento com **cota de cálculo insuficiente (CRP).** Para resolver, primeiro dimensione seu cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga [estas etapas para solicitar um aumento da cota de cálculo](../azure-portal/supportability/resource-manager-core-quotas-request.md) antes de tentar escalar novamente além dos limites iniciais de cotas.
    * Dimensionamento de um cluster com rede avançada e **recursos insuficientes de sub-rede (rede).** Para resolver, primeiro dimensione seu cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga [estas etapas para solicitar um aumento da cota de recursos](../azure-resource-manager/templates/error-resource-quota.md#solution) antes de tentar escalar novamente além dos limites iniciais de cotas.
2. Uma vez que a causa básica para falha de atualização seja resolvida, seu cluster deve estar em um estado bem sucedido. Uma vez verificado um estado bem sucedido, tente novamente a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou recebendo erros ao tentar atualizar ou dimensionar esse estado que meu cluster está sendo atualizado ou com falha na atualização

*Esta assistência de solução de problemas é direcionada a partir dehttps://aka.ms/aks-pending-upgrade*

As operações de upgrade e escala em um cluster com um único pool de nós ou um cluster com vários pools de nós são mutuamente [exclusivas.](use-multiple-node-pools.md) Você não pode ter um cluster ou um pool de nó simultaneamente atualizar e dimensionar. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação sobre esse mesmo recurso. Como resultado, as operações são limitadas quando as operações ativas de upgrade ou escala estão ocorrendo ou tentativas e, posteriormente, falharam. 

Para ajudar a `az aks show -g myResourceGroup -n myAKSCluster -o table` diagnosticar o problema, execute-se para recuperar o status detalhado em seu cluster. Com base no resultado:

* Se o cluster estiver atualizado ativamente, aguarde até que a operação termine. Se for bem sucedido, tente novamente a operação anteriormente fracassada.
* Se o cluster tiver falhado na atualização, siga as etapas descritas na seção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mover meu cluster para uma assinatura diferente ou minha assinatura com meu cluster para um novo inquilino?

Se você mudou seu cluster AKS para uma assinatura diferente ou a assinatura do cluster para um novo inquilino, o cluster perderá a funcionalidade devido à perda de atribuições de função e direitos de diretores de serviço. **A AKS não suporta clusters móveis entre assinaturas ou inquilinos** devido a essa restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou recebendo erros tentando usar recursos que requerem conjuntos de escala de máquinas virtuais

*Esta assistência de solução de problemas é dirigida a partir de aka.ms/aks-vmss-enablement*

Você pode receber erros que indicam que seu cluster AKS não está em um conjunto de escala de máquina virtual, como o seguinte exemplo:

**AgentPool 'agentpool' definiu o dimensionamento automático como ativado, mas não está nos conjuntos de escala de máquinavirtual**

Para usar recursos como o cluster autoscaler ou vários grupos de nó, devem ser criados clusters AKS que usam conjuntos de escala de máquinavirtual. Os erros são devolvidos se você tentar usar recursos que dependem de conjuntos de escala de máquinas virtuais e você mira um cluster AKS conjunto de escala de máquina não virtual regular.

Siga as *etapas antes* de iniciar as etapas no doc apropriado para criar corretamente um cluster AKS:

* [Use o autoscaler de cluster](cluster-autoscaler.md)
* [Crie e use vários pools de nó](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quais restrições de nomeação são aplicadas para recursos e parâmetros AKS?

*Esta assistência de solução de problemas é dirigida a partir de aka.ms/aks-naming-rules*

As restrições de nomeação são implementadas tanto pela plataforma Azure quanto pela AKS. Se um nome ou parâmetro de recurso quebrar uma dessas restrições, um erro é retornado que pede que você forneça uma entrada diferente. Aplicam-se as seguintes diretrizes comuns de nomeação:

* Os nomes de cluster devem ser de 1 a 63 caracteres. Os únicos caracteres permitidos são letras, números, traços e sublinhados. O primeiro e último caractere deve ser uma letra ou um número.
* O nome do grupo de recursos *aKS MC_* combina nome do grupo de recursos e nome do recurso. A sintaxe autogerada não `MC_resourceGroupName_resourceName_AzureRegion` deve ser superior a 80 chars. Se necessário, reduza o comprimento do nome do grupo de recursos ou do cluster AKS.
* O *dnsPrefix* deve iniciar e terminar com valores alfanuméricos e deve estar entre 1-54 caracteres. Caracteres válidos incluem valores alfanuméricos e hífens (-). O *dnsPrefix* não pode incluir caracteres especiais, como um período (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou recebendo erros ao tentar criar, atualizar, dimensionar, excluir ou atualizar cluster, essa operação não é permitida, pois outra operação está em andamento.

*Esta assistência de solução de problemas é dirigida a partir de aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em andamento. Para recuperar um status detalhado do `az aks show -g myResourceGroup -n myAKSCluster -o table` seu cluster, use o comando. Use seu próprio grupo de recursos e nome de cluster AKS conforme necessário.

Com base na saída do status do cluster:

* Se o cluster estiver em qualquer estado de provisionamento que não *seja bem sucedido* ou *falho,* aguarde até que a operação *(Atualização / Atualização / Criação / Dimensionamento / Exclusão / Migração*) termine. Quando a operação anterior estiver concluída, tente novamente sua última operação de cluster.

* Se o cluster tiver uma atualização com falha, siga as etapas descritas [Estou recebendo erros que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionarão até que seja corrigido](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Estou recebendo erros que meu diretor de serviço não foi encontrado quando tento criar um novo cluster sem passar em um já existente.

Ao criar um cluster AKS, é necessário um diretor de serviço para criar recursos em seu nome. O AKS oferece a capacidade de ter um novo criado no momento da criação de clusters, mas isso requer que o Azure Active Directory propague totalmente o novo diretor de serviço em um tempo razoável, a fim de que o cluster tenha sucesso na criação. Quando essa propagação demorar muito, o cluster falhará na validação para criar, pois não poderá encontrar um principal de serviço disponível para fazê-lo. 

Use as seguintes soluçãos para isso:
1. Use um princípio de serviço existente que já se propagou entre as regiões e existe para passar para aKS no tempo de criação de cluster.
2. Se usar scripts de automação, adicione atrasos de tempo entre a criação principal do serviço e a criação de clusterS AKS.
3. Se estiver usando o portal Azure, retorne às configurações do cluster durante a criação e tente novamente a página de validação após alguns minutos.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Estou recebendo erros depois de restringir meu tráfego de saída

Ao restringir o tráfego de saída de um cluster AKS, existem portas de saída [recomendadas e opcionais e](limit-egress-traffic.md) regras de fqdn / aplicativos para AKS. Se suas configurações estiverem em conflito com qualquer uma dessas `kubectl` regras, você pode não ser capaz de executar certos comandos. Você também pode ver erros ao criar um cluster AKS.

Verifique se suas configurações não estão conflitantes com qualquer uma das portas de saída recomendadas ou opcionais exigidas/ regras de rede e regras de fqdn / aplicativo.

## <a name="azure-storage-and-aks-troubleshooting"></a>Armazenamento azure e solução de problemas aks

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas do Kubernetes para disco Azure? 

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Quais versões do Kubernetes têm suporte ao Azure Disk na Nuvem Soberana?

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 | 1.14.0 ou posterior |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Falha no WaitForAttach para o Disco Azure: análise "/dev/disk/azure/scsi1/lun1": sintaxe inválida

Na versão 1.10 do Kubernetes, o MountVolume.WaitForAttach pode falhar com uma remontagem do Disco Azure.

No Linux, você pode ver um erro de formato do DevicePath incorreto. Por exemplo: 

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

No Windows, você pode ver um erro de número do DevicePath (LUN) errado. Por exemplo: 

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1,10 | 1.10.2 ou posterior |
| 1.11 | 1.11.0 ou posterior |
| 1.12 e posterior | N/D |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Falha ao definir uid e gid em mountOptions for Azure Disk

O Azure Disk usa o sistema de arquivos ext4.xfs por padrão e montagemOpções como uid=x,gid=x não podem ser definidas na hora da montagem. Por exemplo, se você tentasse definir mountOptions uid=999,gid=999, veria um erro como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Você pode mitigar o problema fazendo um o seguinte:

* [Configure o contexto de segurança de um pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) definindo uid em runAsUser e gid no fsGroup. Por exemplo, a configuração a seguir definirá a execução do pod como raiz, tornando-a acessível a qualquer arquivo:

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
  > Uma vez que gid e uid são montados como raiz ou 0 por padrão. Se o gid ou uid for definido como não raiz, por exemplo `chown` 1000, os Kubernetes usarão para alterar todos os diretórios e arquivos esse disco. Esta operação pode ser demorada e pode tornar a montagem do disco muito lenta.

* Use `chown` initContainers para definir gid e uid. Por exemplo: 

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Erro ao excluir o Azure Disk PersistentVolumeVolumeem uso por um pod

Se você tentar excluir um Azure Disk PersistentVolumeClaim que está sendo usado por um pod, você poderá ver um erro. Por exemplo: 

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Na versão 1.10 do Kubernetes e posterior, há um recurso de proteção PersistentVolumeClaim ativado por padrão para evitar esse erro. Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema, você pode mitigar esse problema excluindo o pod usando o PersistentVolumeClaim antes de excluir o PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Erro "Não é possível encontrar Lun para disco" ao anexar um disco a um nó

Ao anexar um disco a um nó, você pode ver o seguinte erro:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1,10 | 1.10.10 ou posterior |
| 1.11 | 1.11.5 ou posterior |
| 1.12 | 1.12.3 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1.14 e posterior | N/D |

Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema, você pode mitigar o problema esperando vários minutos e tentando novamente.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure Disk attach/desacoplado falha, problemas de montagem ou erros de I/O durante várias operações de conexão/desconexão

A partir da versão 1.9.2 do Kubernetes, ao executar várias operações de conexão/desacoplamento em paralelo, você pode ver os seguintes problemas de disco devido a um cache de VM sujo:

* Falhas de acoplação/desacoplação de disco
* Erros de I/O do disco
* Desprendimento de disco inesperado da VM
* VM em estado de falha devido à anexação de disco não existente

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1,10 | 1.10.12 ou posterior |
| 1.11 | 1.11.6 ou posterior |
| 1.12 | 1.12.4 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1.14 e posterior | N/D |

Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema, você pode mitigar o problema tentando o abaixo:

* Se houver um disco esperando para desapegar por um longo período de tempo, tente separar o disco manualmente

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco Azure esperando para desapegar indefinidamente

Em alguns casos, se uma operação de desacoplamento do Disco Azure falhar na primeira tentativa, ela não tentará novamente a operação de desacoplamento e permanecerá anexada à VM do nó original. Esse erro pode ocorrer ao mover um disco de um nó para outro. Por exemplo: 

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.11 | 1.11.9 ou posterior |
| 1.12 | 1.12.7 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1.14 e posterior | N/D |

Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema, você pode mitigar o problema desvinculando manualmente o disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure Disk desacoplado falha levando a problema potencial de condição de corrida e lista de disco de dados inválida

Quando um Disco Azure não se desprende, ele tentará novamente até seis vezes para desprender o disco usando um recuo exponencial. Ele também manterá um bloqueio de nível de nó na lista de discos de dados por cerca de 3 minutos. Se a lista de discos for atualizada manualmente durante esse período de tempo, como uma operação manual de fixação ou desacoplamento, isso fará com que a lista de discos mantida pelo bloqueio de nível de nó seja obsoleta e cause instabilidade na VM do nó.

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |
| 1.15 e posterior | N/D |

Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema e sua VM de nó tiver uma lista de discos obsoleto, você pode mitigar o problema desvinculando todos os discos não existentes da VM como uma única operação em massa. **A desvinculação individual de discos não existentes pode falhar.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Grande número de discos Azure causa conexão/desapego lento

Quando o número de discos Azure conectados a uma VM de nó for maior que 10, as operações de fixação e desvinculação podem ser lentas. Este problema é um problema conhecido e não há solução para o momento.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure Disk desacoplado falha levando a potencial vm de nó em estado falho

Em alguns casos de borda, um desacoplamento do Disco Azure pode falhar parcialmente e deixar a VM do nó em um estado de falha.

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.10 ou posterior |
| 1.13 | 1.13.8 ou posterior |
| 1,14 | 1.14.4 ou posterior |
| 1.15 e posterior | N/D |

Se você estiver usando uma versão do Kubernetes que não tem a correção para este problema e sua VM de nó estiver em um estado de falha, você pode mitigar o problema atualizando manualmente o status da VM usando um dos abaixo:

* Para um cluster baseado em conjunto de disponibilidade:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files e AKS Solução de Problemas

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quais são as versões estáveis recomendadas de Kubernetes para arquivos Azure?
 
| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1,14 | 1.14.0 ou posterior |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Quais versões do Kubernetes têm suporte a Arquivos Azure na Nuvem Soberana?

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 | 1.14.0 ou posterior |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quais são as opções de montagem padrãoao usar arquivos Do Zure?

Configurações recomendadas:

| Versão Kubernetes | fileMode e dirMode valor|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 e posterior | 0777 |

Se usar um cluster com a versão 1.8.5 ou superior do Kubernetes e criar dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777*:

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

Algumas configurações de *opções* de montagem úteis adicionais:

* *mfsymlinks* farão com que o Azure Files mount (cifs) suporte links simbólicos
* *nobrl* impedirá o envio de solicitações de bloqueio de faixa de byte para o servidor. Esta configuração é necessária para certos aplicativos que rompem com bloqueios de faixa de byte obrigatórios do estilo CIFS. A maioria dos servidores cifs ainda não suporta bloqueios de faixa de byte de solicitação de assessoria. Se não usar *nobrl,* aplicativos que rompam com bloqueios de faixa de byte obrigatórios do estilo cifs podem causar mensagens de erro semelhantes às:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erro "não foi possível alterar permissões" ao usar arquivos do Azure

Ao executar o PostgreSQL no plugin Azure Files, você pode ver um erro semelhante ao:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Esse erro é causado pelo plugin Azure Files usando o protocolo cifs/SMB. Ao usar o protocolo cifs/SMB, as permissões de arquivo e diretório não puderam ser alteradas após a montagem.

Para resolver esse problema, use *o subPath* juntamente com o plugin Azure Disk. 

> [!NOTE] 
> Para o tipo de disco ext3/4, há um diretório perdido+encontrado depois que o disco é formatado.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files tem alta latência em comparação com o Azure Disk ao lidar com muitos arquivos pequenos

Em alguns casos, como o manuseio de muitos arquivos pequenos, você pode experimentar alta latência ao usar arquivos Azure quando comparado ao Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erro ao ativar a configuração "Permitir acesso a partir da rede selecionada" na conta de armazenamento

Se você *habilitar permitir o acesso a partir de uma rede selecionada* em uma conta de armazenamento que é usada para provisionamento dinâmico no AKS, você terá um erro quando o AKS criar um compartilhamento de arquivos:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Este erro é devido ao *controlador de volume persistente* kubernetes não estar na rede escolhida quando a configuração *permite o acesso a partir da rede selecionada*.

Você pode mitigar o problema usando [provisionamento estático com arquivos Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Arquivos do Azure falham em remontar no pod do Windows

Se um pod do Windows com uma montagem de Arquivos Azure for excluído e, em seguida, programado para ser recriado no mesmo nó, a montagem falhará. Esta falha é `New-SmbGlobalMapping` devido à falha de comando, uma vez que a montagem de Arquivos Do Azure já está montada no nó.

Por exemplo, você pode ver um erro semelhante a:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Este problema foi corrigido nas seguintes versões do Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1.14 e posterior | N/D |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Falha no suporte do Azure Files devido à alteração da chave da conta de armazenamento

Se a chave da sua conta de armazenamento tiver sido alterada, você poderá ver falhas de montagem de arquivos do Azure.

Você pode mitigar o problema atualizando manualmente o campo *azurestorageaccountkey* manualmente no segredo de arquivo Azure com a chave da conta de armazenamento codificada base64.

Para codificar a chave da sua conta `base64`de armazenamento na base64, você pode usar . Por exemplo: 

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar seu arquivo secreto `kubectl edit secret`do Azure, use . Por exemplo: 

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó do agente tentará novamente a montagem do arquivo azure com a chave de armazenamento atualizada.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>O cluster autoscaler falha na escala com erro não foi corrigido os tamanhos do grupo de nó

Se o escalador automático do cluster não estiver escalando para cima/para baixo e você verá um erro como o abaixo nos [logs de escalador automático][view-master-logs]do cluster .

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Este erro é devido a uma condição de corrida de cluster de cluster upstream onde o cluster autoscaler termina com um valor diferente do que está realmente no cluster. Para sair deste estado, basta desativar e reativar o [cluster autoscaler][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Anexo de disco lento, GetAzureDiskLun leva de 10 a 15 minutos e você recebe um erro

Nas versões Kubernetes **com mais de 1.15.0** você pode receber um erro como **O Erro WaitForAttach Não é possível encontrar Lun para disco**.  A solução para isso é esperar aproximadamente 15 minutos e tentar novamente.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
