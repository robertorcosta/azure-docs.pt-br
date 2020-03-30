---
title: Aprenda a política do Azure para o motor AKS
description: Saiba como a Política do Azure usa CustomResourceDefinitions e Open Policy Agent do Gatekeeper v3 para gerenciar clusters com o AKS Engine.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436437"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Entenda a política do Azure para o motor AKS

A Azure Policy integra-se [ao AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), um sistema que fornece ferramentas convenientes para inicializar rapidamente um cluster Kubernetes auto-gerenciado no Azure. Essa integração permite a aplicação em escala e salvaguardas em seus clusters autogerenciados do AKS Engine de forma centralizada e consistente. Ao estender o uso do [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta) do [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), um webhook controlador de _admissão_ para Kubernetes, a Azure Policy permite gerenciar e relatar o estado de conformidade dos seus recursos do Azure e dos clusters autogerenciados do AKS Engine a partir de um só lugar.

> [!NOTE]
> A Política do Azure para AKS Engine está na Visualização Pública e não tem SLA. Gatekeeper v3 está em Beta e é suportado pela comunidade de código aberto. O serviço só suporta definições de diretiva incorporadas e um único cluster aks engine para cada grupo de recursos configurado com um Diretor de Serviço.

> [!IMPORTANT]
> Para obter suporte para a Política Azure para AKS Engine, AKS Engine ou Gatekeeper v3, crie um [novo problema](https://github.com/Azure/aks-engine/issues/new/choose) no repositório AKS Engine GitHub.

## <a name="overview"></a>Visão geral

Para habilitar e usar a Política Azure para AKS Engine com seu cluster Kubernetes autogerenciado no Azure, tome as seguintes ações:

- [Pré-requisitos](#prerequisites)
- [Instale o complemento da política do Azure](#installing-the-add-on)
- [Atribuir uma definição de política para AKS Engine](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o Complemento de Diretiva do Azure ou habilitar qualquer um dos recursos do serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de função para o principal do serviço de cluster. 

1. Para habilitar o provedor de recursos, siga as etapas em [provedores e tipos de recursos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando Azure CLI ou Azure PowerShell:

   - CLI do Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Criar uma atribuição de função para o principal de serviço de cluster

   - Se você não conhece o ID principal do aplicativo de serviço de cluster, procure-o com o seguinte comando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribuir 'Policy Insights Data Writer (Preview)' atribuição de função ao ID principal do aplicativo de serviço de cluster (valor _aadClientID_ da etapa anterior) com o Azure CLI. Substitua pelo `<subscriptionId>` seu `<aks engine cluster resource group>` ID de assinatura e pelo grupo de recursos em que o cluster Kubernetes autogerenciado do AKS Engine está dentro.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Complemento de política do Azure

O _complemento de política do Azure_ para Kubernetes conecta o serviço de política do Azure ao controlador de admissão Gatekeeper. O complemento, que é instalado no namespace do _sistema kube,_ decreta as seguintes funções:

- Verifica com a Política do Azure as atribuições para o cluster do Motor AKS
- Downloads e instala detalhes de diretiva, modelos de restrição e restrições
- Executa uma verificação completa de conformidade de varredura no cluster AKS Engine
- Relatórios de auditoria e detalhes de conformidade de volta à Política do Azure

### <a name="installing-the-add-on"></a>Instalando o complemento

Uma vez que os pré-requisitos sejam concluídos, o Complemento de Política do Azure pode ser instalado. A instalação pode ser durante o ciclo de criação ou atualização de um Motor AKS ou como uma ação independente em um cluster existente.

- Instalar durante a criação ou ciclo de atualização

  Para habilitar o Complemento de Diretiva do Azure durante a criação de um novo cluster autogerenciado ou como uma atualização para um cluster existente, inclua a definição de cluster de propriedade **addons** para AKS Engine.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Para obter mais informações, consulte a definição de [cluster do aks engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)do guia externo .

- Instale em cluster existente com Gráficos helm

  Use as seguintes etapas para preparar o cluster e instalar o complemento:

  1. Instale o Gatekeeper no namespace do _sistema gatekeeper._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adicione a etiqueta _do plano de controle_ ao sistema _kube_. Este rótulo exclui a auditoria de pods e serviços do _sistema kube_ pelo Gatekeeper e pelo Azure Policy Add-on.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sincronizar dados Kubernetes (Namespace, Pod, Ingress, Service) com OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Para obter mais informações, consulte [OPA - Replicando dados](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adicione o repo de política do Azure ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para obter mais informações, consulte [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um gráfico de leme. Substitua pelo `<subscriptionId>` seu `<aks engine cluster resource group>` ID de assinatura e pelo grupo de recursos em que o cluster Kubernetes autogerenciado do AKS Engine está dentro.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o Gráfico de Helm adicionais instala, consulte a definição do [Gráfico de Complemento de Política do Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) no GitHub.

     > [!NOTE]
     > Devido ao relacionamento entre o Azure Policy Add-on e o id do grupo de recursos, o Azure Policy suporta apenas um cluster AKS Engine para cada grupo de recursos.

Para validar que a instalação de complemento foi bem sucedida e que o pod _de diretiva azure_ está sendo executado, execute o seguinte comando:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e emissão de relatórios

O complemento faz check-on com a Política Azure para alterações nas atribuições de políticas a cada 5 minutos. Durante este ciclo de atualização, o complemento verifica se há alterações. Essas alterações disparam criações, atualizações ou exclusões dos modelos e restrições de restrição.

> [!NOTE]
> Embora um administrador _de cluster_ possa ter permissão para fazer alterações em modelos e restrições de restrição, não é recomendado ou suportado fazer alterações em modelos de restrição ou restrições criadas pela Azure Policy. Quaisquer alterações manuais feitas são perdidas durante o ciclo de atualização.

A cada 5 minutos, o complemento pede uma varredura completa do cluster. Depois de coletar detalhes da varredura completa e quaisquer avaliações em tempo real pelo Gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta à Política Azure para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer atribuição de Política do Azure. Apenas os resultados das atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como violações listadas no campo de status da restrição falha.

## <a name="policy-language"></a>Linguagem política

A estrutura de linguagem da Política Azure para o gerenciamento do AKS Engine segue a das políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerenciar seus clusters do Motor AKS e leva _informações específicas_ para trabalhar com [o OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o Gatekeeper v3. Para obter detalhes e exemplos, consulte o efeito [EnforceOPAConstraint.](effects.md#enforceopaconstraint)

Como parte dos _detalhes.constraintTemplate_ e _details.constraint_ properties in the policy definition, o Azure Policy passa as URIs desses CRD [(CustomResourceDefinitions)](https://github.com/open-policy-agent/gatekeeper#constraint-templates) para o complemento. Rego é a linguagem que a OPA e o Gatekeeper suportam para validar uma solicitação ao cluster Kubernetes. Ao apoiar um padrão existente para o gerenciamento do Kubernetes, o Azure Policy permite reutilizar as regras existentes e emparelhá-las com a Azure Policy para uma experiência unificada de relatórios de conformidade em nuvem. Para mais informações, consulte [O que é Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Políticas internas

Para encontrar as políticas incorporadas para gerenciar seu cluster AKS Engine usando o portal Azure, siga estas etapas:

1. Inicie o serviço de Política Do Azure no portal Azure. Selecione **Todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política do Azure, selecione **Definições**.

1. Na caixa de lista de estiagem de categoria, use **Selecione tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de diretiva e selecione o botão **Atribuir.**

> [!NOTE]
> Ao atribuir a diretiva Azure para definição do aks engine, o **Escopo** deve ser o grupo de recursos do cluster AKS Engine.

Alternativamente, use a [Política Atribuir - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política de mecanismo AKS. Procure uma definição de política do aks engine em vez da amostra 'audit vms'.

> [!IMPORTANT]
> Políticas incorporadas na categoria **Kubernetes** são apenas para uso com AKS Engine.

## <a name="logging"></a>Registrando em log

### <a name="azure-policy-add-on-logs"></a>Logs de complementos de política do Azure

Como um controlador/contêiner Kubernetes, o Azure Policy Add-on mantém logs no cluster AKS Engine.

Para exibir os logs de complementos `kubectl`de diretiva do Azure, use:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Registros do gatekeeper

O pod Gatekeeper, _gatekeeper-controller-manager-0,_ `gatekeeper-system` geralmente `kube-system` está no ou namespace, mas pode estar em um namespace diferente, dependendo de como ele é implantado.

Para visualizar os registros do `kubectl`Gatekeeper, use:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Para obter mais informações, consulte [Depurando gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação opa.

## <a name="remove-the-add-on"></a>Remova o complemento

Para remover o complemento de diretiva do Azure e o gatekeeper do cluster do AKS Engine, use o método que se alinha com a forma como o complemento foi instalado:

- Se instalado definindo a propriedade **addons** na definição de cluster para AKS Engine:

  Reimplante a definição de cluster no AKS Engine depois de alterar a propriedade **addons** para _a diretiva azure_ para falsa:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se instalado com gráficos de leme:

  1. Remover restrições antigas

     Atualmente, o mecanismo de desinstalação apenas remove o sistema Gatekeeper, não remove nenhum recurso _ConstraintTemplate,_ _Constraint_ou _Config_ que tenha sido criado pelo usuário, nem remove seus _CRDs_que acompanham.

     Quando o Gatekeeper está em execução, é possível remover restrições indesejadas por:

     - Excluindo todas as instâncias do recurso de restrição
     - Excluindo o recurso _ConstraintTemplate,_ que deve limpar automaticamente o _CRD_
     - A exclusão do recurso _Config_ remove finalizadores em recursos sincronizados

  1. Desinstalar o complemento da política do Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Desinstalar gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo Azure Policy Add-on

O complemento de política do Azure para Kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados diagnósticos são dados técnicos vitais relacionados ao software e ao desempenho. Ele é usada das seguintes maneiras:

- Mantenha o adesão à política do Azure atualizado
- Mantenha o Complemento de Política do Azure seguro, confiável e executante
- Melhorar o Complemento da Política do Azure - através da análise agregada do uso do complemento

As informações coletadas pelo complemento não são dados pessoais. Os seguintes detalhes são coletados atualmente:

- Versão do agente add-on da diretiva do Azure
- Tipo de cluster
- Região do cluster
- Grupo de recursos de cluster
- ID de recurso de cluster
- ID de assinatura de cluster
- Cluster OS (Exemplo: Linux)
- Cluster city (Exemplo: Seattle)
- Estado ou província do cluster (Exemplo: Washington)
- Cluster país ou região (Exemplo: Estados Unidos)
- Exceções/erros encontrados pelo Azure Policy Add-on durante a instalação do agente na avaliação de políticas
- Número de políticas gatekeeper não instaladas pelo Azure Policy Add-on

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revisar a [Estrutura de definição de política](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)