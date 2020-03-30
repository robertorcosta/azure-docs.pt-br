---
title: Usar o dimensionador automático de cluster no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o dimensionador automático de cluster para dimensionar automaticamente seu cluster e atender às demandas de aplicativo em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596242"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Dimensionar automaticamente um cluster para atender às demandas de aplicativo no AKS (Serviço de Kubernetes do Azure)

Para se manter atualizado com as demandas de aplicativo no AKS (Serviço de Kubernetes do Azure), talvez você precise ajustar o número de nós que executam as cargas de trabalho. O componente de dimensionador automático de cluster pode inspecionar os pods no cluster que não podem ser agendados devido a restrições de recursos. Quando os problemas são detectados, o número de nós em um pool de nós é aumentado para atender à demanda de aplicação. Os nós também são regularmente verificados quanto à falta de pods em execução, com o número de nós diminuído posteriormente conforme necessário. Essa capacidade de escalar ou reduzir verticalmente o número de nós no cluster do AKS permite a execução de um cluster eficiente e econômico.

Este artigo mostra como habilitar e gerenciar o dimensionador automático de cluster em um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo exige que você esteja executando a versão 2.0.76 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que usam o cluster autoscaler:

* O complemento de roteamento do aplicativo HTTP não pode ser usado.

## <a name="about-the-cluster-autoscaler"></a>Sobre o dimensionador automático de cluster

Para se ajustar às mudanças nas demandas de aplicativo, como entre a jornada de trabalho e a noite ou em um fim de semana, os clusters geralmente precisam de uma maneira de serem dimensionados automaticamente. Os clusters do AKS podem ser dimensionados de uma das duas maneiras:

* O **dimensionador automático de cluster** inspeciona os pods que não podem ser agendados em nós devido a restrições de recursos. O cluster então aumenta automaticamente o número de nódulos.
* O **dimensionador automático de pod horizontal** usa o Servidor de Métricas em um cluster do Kubernetes para monitorar a demanda de recursos dos pods. Se um aplicativo precisar de mais recursos, o número de pods é automaticamente aumentado para atender à demanda.

![O dimensionador automático de cluster e o dimensionador automático de pod horizontal geralmente trabalham juntos para dar suporte às demandas necessárias de aplicativo](media/autoscaler/cluster-autoscaler.png)

Tanto o autoscaler de pod horizontal quanto o autoscaler de cluster também podem diminuir o número de pods e nós conforme necessário. O dimensionador automático de cluster diminui o número de nós quando a capacidade não é utilizada por um período. Os pods em um nó a serem removidos pelo dimensionador automático de cluster são agendados com segurança em outro lugar no cluster. O dimensionador automático de cluster poderá não conseguir fazer a redução vertical se os pods não puderem ser movidos, como nas seguintes situações:

* Um pod é criado diretamente e não é apoiado por um objeto controlador, como uma implantação ou conjunto de réplicas.
* Um PDB (orçamento de interrupção de pod) é muito restritivo e não permite que o número de pods fique abaixo de determinado limite.
* Um pod usa seletores de nó ou uma antiafinidade que não pode ser cumprida se ele está agendado em outro nó.

Para obter mais informações sobre como o dimensionador automático de cluster pode não conseguir fazer a redução vertical, confira [Quais tipos de pods podem impedir que o dimensionador automático de cluster remova um nó?][autoscaler-scaledown]

O dimensionador automático de cluster usa parâmetros de inicialização para itens como intervalos de tempo entre eventos de escala e limites de recursos. Esses parâmetros são definidos pela plataforma Azure e atualmente não são expostos para seu ajuste. Para obter mais informações sobre quais parâmetros são usados pelo dimensionador automático de cluster, confira [Quais são os parâmetros do dimensionador automático de cluster?][autoscaler-parameters].

Os autoescaladores de cluster e de pod horizontal podem trabalhar juntos, e muitas vezes são implantados em um cluster. Quando combinados, o dimensionador automático de pod horizontal se concentra na execução do número de pods necessários para atender à demanda de aplicativo. O dimensionador automático de cluster se concentra na execução do número de nós necessários para dar suporte aos pods agendados.

> [!NOTE]
> O dimensionamento manual é desabilitado quando o dimensionador automático de cluster é usado. Deixe o dimensionador automático de cluster determinar o número de nós necessários. Caso deseje dimensionar o cluster manualmente, [desabilite o dimensionador automático de cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster do AKS e habilitar o dimensionador automático de cluster

Caso precise criar um cluster do AKS, use o comando [az aks create][az-aks-create]. Para ativar e configurar o cluster autoscaler no pool de nó para o cluster, use o parâmetro *--enable-cluster-autoscaler* e especifique um nó *--contagem mínima* e *--contagem máxima*.

> [!IMPORTANT]
> O dimensionamento automático do cluster é um componente de Kubernetes. Embora o cluster do AKS use um conjunto de dimensionamento de máquinas virtuais para os nós, não habilite ou edite manualmente as configurações de dimensionamento automático do conjunto de dimensionamento no portal do Azure ou usando a CLI do Azure. Permita que o dimensionador automático do cluster de Kubernetes gerencie as configurações de dimensionamento necessárias. Para obter mais [informações, consulte Posso modificar os recursos do AKS no grupo de recursos do nó?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

O exemplo a seguir cria um cluster AKS com um único pool de nó apoiado por um conjunto de escala de máquina virtual. Ele também habilita o cluster autoscaler no pool de nó para o cluster e define um mínimo de *1* e máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

São necessários alguns minutos para criar o cluster e definir as configurações do dimensionador automático de cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as configurações do dimensionador automático de cluster

> [!IMPORTANT]
> Se você tiver vários pools de nó no cluster AKS, pule para a [escala automática com a seção de pools de vários agentes](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Clusters com vários pools de `az aks nodepool` agentes exigem o uso do `az aks`conjunto de comandos para alterar propriedades específicas do pool de nó em vez de .

Na etapa anterior para criar um cluster AKS ou atualizar um pool de nó existente, a contagem de nó mínimo do cluster foi definida como *1*, e a contagem máxima de nó foi definida como *3*. Conforme as demandas de aplicativo mudam, você pode precisar ajustar a contagem de nós do dimensionador automático de cluster.

Para alterar a contagem de nós, use o comando [az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o cluster autoscaler no pool de nó único no *myAKSCluster* para um mínimo de *1* e máximo de *5* nós.

> [!NOTE]
> Você não pode definir uma contagem mínima de nó mais alta do que está atualmente definida para o pool de nó. Por exemplo, se atualmente você tiver uma contagem mínima definida como *1*, não poderá atualizar a contagem mínima para *3*.

Monitore o desempenho de seus aplicativos e serviços e ajuste as contagens de nós do dimensionador automático de cluster para que correspondam ao desempenho necessário.

## <a name="using-the-autoscaler-profile"></a>Usando o perfil do autoscaler

Você também pode configurar detalhes mais granulares do cluster autoscaler alterando os valores padrão no perfil de escalador automático em todo o cluster. Por exemplo, um evento de rebaixação de escala acontece depois que os nós são subutilizados após 10 minutos. Se você tiver cargas de trabalho que eram realizadas a cada 15 minutos, você pode querer alterar o perfil do escalador automático para reduzir a escala em nomes utilizados após 15 ou 20 minutos. Quando você habilita o cluster autoscaler, um perfil padrão é usado a menos que você especifique configurações diferentes. O perfil de escalador automático de cluster tem as seguintes configurações que você pode atualizar:

| Configuração                          | Descrição                                                                              | Valor padrão |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| intervalo de varredura                    | Quantas vezes o cluster é reavaliado para escalar para cima ou para baixo                                    | 10 segundos    |
| scale-down-delay-after-add       | Quanto tempo após a escala de escala para baixo avaliação recomeça                               | 10 minutos    |
| escala-down-delay-after-delete    | Quanto tempo após a exclusão do nó que reduz a avaliação de escala saem                          | intervalo de varredura |
| escala-down-delay-after-failure   | Quanto tempo após a recaída de escala que a avaliação reduz a escala é retomada                     | 3 minutos     |
| escala-down-de-tempo desnecessário         | Quanto tempo um nó deve ser desnecessário antes de ser elegível para reduzir a escala                  | 10 minutos    |
| scale-down-unready-time          | Quanto tempo um nó não-pronto deve ser desnecessário antes que ele seja elegível para reduzir a escala         | 20 minutos    |
| escala-down-utilization-limiar | Nível de utilização do nó, definido como soma dos recursos solicitados divididos por capacidade, abaixo do qual um nó pode ser considerado para rebaixar a escala | 0.5 |
| max-gracioso-termination-se     | Número máximo de segundos o cluster autoscaler espera pelo término do pod ao tentar reduzir um nó. | 600 segundos   |

> [!IMPORTANT]
> O perfil do cluster autoscaler afeta todos os pools de nós que usam o cluster autoscaler. Você não pode definir um perfil de escalador automático por pool de nó.

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para definir o perfil de configurações do cluster autoscaler, você precisa da versão DE extensão CLI *com visualização aks* 0.4.30 ou superior. Instale a extensão *aks-preview* Azure CLI usando o comando [az extension add][az-extension-add] e verifique se há atualizações disponíveis usando o comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Defina o perfil do cluster autoscaler em um cluster AKS existente

Use o comando [az aks update][az-aks-update] com o parâmetro *cluster-autoscaler-profile* para definir o perfil do cluster autoscaler em seu cluster. O exemplo a seguir configura a configuração do intervalo de varredura como 30s no perfil.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Quando você habilitar o cluster autoscaler em pools de nó no cluster, esses clusters também usarão o perfil do cluster autoscaler. Por exemplo: 

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Quando você definir o perfil do cluster autoscaler, quaisquer pools de nó existentes com o cluster autoscaler ativado começarão a usar o perfil imediatamente.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Defina o perfil do cluster autoscaler ao criar um cluster AKS

Você também pode usar o parâmetro *cluster-autoscaler-profile* ao criar seu cluster. Por exemplo: 

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

O comando acima cria um cluster AKS e define o intervalo de varredura como 30 segundos para o perfil de escalador automático em todo o cluster. O comando também habilita o cluster autoscaler no pool de nó inicial, define a contagem mínima de nó para 1 e a contagem máxima de nó para 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Redefinir o perfil do escalador automático de cluster para valores padrão

Use o comando [az aks update][az-aks-update] para redefinir o perfil do cluster autoscaler em seu cluster.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Desabilitar o dimensionador automático de cluster

Se você não quiser mais usar o cluster autoscaler, você pode desativá-lo usando o comando [az aks update,][az-aks-update] especificando o parâmetro *--desativar-cluster-autoscaler.* Os nós não são removidos quando o dimensionador automático de cluster é desabilitado.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Você pode dimensionar manualmente seu cluster depois de desativar o cluster autoscaler usando o comando [az aks scale.][az-aks-scale] Se você usar o autoscaler de pod horizontal, esse recurso continuará a ser executado com o cluster autoscaler desativado, mas os pods podem acabar não podendo ser agendados se todos os recursos do nó estiverem em uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Reativar um cluster autoscaler desativado

Se você deseja reativar o cluster autoscaler em um cluster existente, você pode reatifeí-lo usando o comando [az aks update,][az-aks-update] especificando os parâmetros *--enable-cluster-autoscaler*, *--min-count*e *--max-count.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperar logs de escalador de cluster e status

Para diagnosticar e depurar eventos de autoscaler, os logs e o status podem ser recuperados do complemento do autoscaler.

A AKS gerencia o cluster autoscaler em seu nome e executa-o no plano de controle gerenciado. Os logs de nó mestre devem ser configurados para serem visualizados como resultado.

Para configurar logs a serem empurrados do cluster autoscaler para o Log Analytics, siga essas etapas.

1. Configure uma regra para logs de diagnóstico para empurrar logs de cluster-autoscaler para Log Analytics. [As instruções são detalhadas aqui,](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)certifique-se de verificar a caixa para `cluster-autoscaler` selecionar opções para "Logs".
1. Clique na seção "Logs" em seu cluster através do portal Azure.
1. Insira a seguinte consulta de exemplo no Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Você deve ver registros semelhantes ao seguinte exemplo, desde que haja registros para recuperar.

![Logs de log analytics](media/autoscaler/autoscaler-logs.png)

O cluster autoscaler também irá escrever o estado `cluster-autoscaler-status`de saúde para um configmap chamado . Para recuperar esses logs, `kubectl` execute o seguinte comando. Um estado de saúde será informado para cada pool de nó configurado com o cluster autoscaler.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Para saber mais sobre o que está registrado no autoscaler, leia o FAQ no [projeto Kubernetes/autoscaler GitHub](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Use o cluster autoscaler com vários pools de nó ativados

O cluster autoscaler pode ser usado juntamente com [vários pools de nó ativados.](use-multiple-node-pools.md) Siga esse documento para saber como ativar vários pools de nó e adicionar pools de nó adicionais a um cluster existente. Ao usar ambos os recursos em conjunto, você habilita o cluster autoscaler em cada pool de nós individuais no cluster e pode passar regras exclusivas de autodimensionamento para cada um.

O comando abaixo pressupõe que você seguiu as [instruções iniciais](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) anteriormente neste documento e deseja atualizar a contagem máxima de um pool de nó existente de *3* a *5*. Use o comando [aks nodepool update][az-aks-nodepool-update] para atualizar as configurações de um pool de nó existentes.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O cluster autoscaler pode ser desativado com a atualização do `--disable-cluster-autoscaler` [aks nodepool][az-aks-nodepool-update] e passando o parâmetro.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se você deseja reativar o cluster autoscaler em um cluster existente, você pode reatifeí-lo usando o comando [az aks nodepool update,][az-aks-nodepool-update] especificando os parâmetros *--enable-cluster-autoscaler*- *--min-count*e *--max-count.*

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como dimensionar automaticamente o número de nós do AKS. Você também pode usar o dimensionador automático de pod horizontal para ajustar automaticamente o número de pods que executam o aplicativo. Para obter as etapas de como usar o dimensionador automático de pod horizontal, confira [Dimensionar aplicativos no AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
