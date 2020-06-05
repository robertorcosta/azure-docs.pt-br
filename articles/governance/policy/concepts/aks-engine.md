---
title: Conheça o Azure Policy para o Mecanismo AKS
description: Saiba como o Azure Policy usa o CustomResourceDefinitions e o Open Policy Agent do Gatekeeper v3 para gerenciar clusters com o Mecanismo AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1d6f08f07e015b1e7fe2886746bf9e8b43fdce0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654973"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Entender o Azure Policy para o Mecanismo AKS

O Azure Policy integra-se com o [Mecanismo AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md), um sistema que fornece ferramentas convenientes para inicializar rapidamente um cluster do Kubernetes autogerenciado no Azure. Essa integração permite imposições e garantias em escala em seus clusters autogerenciados do Mecanismo AKS de maneira centralizada e consistente. Estendendo o uso d [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), um _webhook do controlador de admissão_ do Kubernetes, o Azure Policy possibilita o gerenciamento e o relatório do estado de conformidade dos seus recursos do Azure e clusters autogerenciados do Mecanismo AKS de um único lugar.

> [!NOTE]
> O Azure Policy para o Mecanismo AKS está em visualização pública e não tem SLA. O Gatekeeper v3 está em versão beta e tem suporte da comunidade de software livre. O serviço só dá suporte a definições de políticas internas, pools de nós do Linux e um único cluster de Mecanismo AKS para cada grupo de recursos configurado com uma entidade de serviço.

> [!IMPORTANT]
> Para obter suporte para o Azure Policy para o Mecanismo AKS, o Mecanismo AKS ou o Gatekeeper v3, crie um [novo problema](https://github.com/Azure/aks-engine/issues/new/choose) no repositório do GitHub do Mecanismo AKS.

## <a name="overview"></a>Visão geral

Para habilitar e usar o Azure Policy para o Mecanismo AKS com seu cluster do Kubernetes autogerenciado no Azure, execute as seguintes ações:

- [Pré-requisitos](#prerequisites)
- [Instalar complemento do Azure Policy](#installing-the-add-on)
- [Atribuir uma definição de política para o Mecanismo AKS](#built-in-policies)
- [Aguardar a validação](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento do Azure Policy ou de habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de função para a entidade de serviço de cluster. 

1. Para habilitar o provedor de recursos, siga as etapas em [Provedores de recursos e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando do CLI do Azure ou do Azure PowerShell:

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

1. Criar uma atribuição de função para a entidade de serviço de cluster

   - Se você não souber a ID do aplicativo de entidade de serviço de cluster, procure o comando a seguir.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribua a atribuição de função “Gravador de dados de informações de política (visualização)” à ID do aplicativo de entidade de serviço de cluster (valor _aadClientID_ da etapa anterior) com a CLI do Azure. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` pelo grupo de recursos no qual o cluster do Kubernetes autogerenciado do Mecanismo AKS está.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Complemento do Azure Policy

O _complemento do Azure Policy_ para o Kubernetes conecta o serviço de Azure Policy ao controlador de admissão do Gatekeeper. O complemento, que é instalado no namespace _kube-system_, executa as seguintes funções:

- Verifica no Azure Policy se há atribuições para o cluster do Mecanismo AKS
- Baixa e instala detalhes da política, modelos de restrição e restrições
- Executa uma verificação de conformidade de exame completa no cluster do Mecanismo AKS
- Relata os detalhes de auditoria e de conformidade de volta ao Azure Policy

### <a name="installing-the-add-on"></a>Instalação do complemento

Quando os pré-requisitos forem concluídos, o complemento do Azure Policy poderá ser instalado. A instalação pode ser durante o ciclo de criação ou de atualização de um Mecanismo AKS ou como uma ação independente em um cluster existente.

- Instalar durante o ciclo de criação ou de atualização

  Para habilitar o complemento do Azure Policy durante a criação de um novo cluster autogerenciado ou como uma atualização de um cluster existente, inclua a definição de cluster de propriedade **addons** para o Mecanismo AKS.

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

  Para obter mais informações, consulte o guia externo [Definição de cluster do Mecanismo AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalar no cluster existente com Gráficos Helm

  Use as etapas a seguir para preparar o cluster e instalar o complemento:

  1. Instale o gatekeeper no namespace _gatekeeper-system_.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adicione o rótulo _control-plane_ a _kube-system_. Esse rótulo exclui a auditoria dos pods _kube-system_ e serviços pelo Gatekeeper e pelo complemento do Azure Policy.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sincronizar dados do Kubernetes (namespace, Pod, entrada, serviço) com o OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Para obter mais informações, consulte [OPA: replicação de dados](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adicione o repositório do Azure Policy ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para obter mais informações [Gráfico do Helm – Guia de Início Rápido](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um gráfico do Helm. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` pelo grupo de recursos no qual o cluster do Kubernetes autogerenciado do Mecanismo AKS está.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o gráfico do Helm do complemento instala, consulte a [Definição do gráfico do Helm do complemento do Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) no GitHub.

     > [!NOTE]
     > Devido à relação entre o complemento do Azure Policy e a ID do grupo de recursos, o Azure Policy dá suporte a apenas um cluster do Mecanismo AKS para cada grupo de recursos.

Para validar que a instalação do complemento foi bem-sucedida e que o pod _azure-policy_ está em execução, execute o seguinte comando:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e relatório

O complemento se comunica com o Azure Policy para ver se há alterações nas atribuições de política a cada 5 minutos. Durante esse ciclo de atualização, o complemento verifica se há alterações. Essas alterações disparam criações, atualizações ou exclusões dos modelos de restrição e das restrições.

> [!NOTE]
> Embora um _administrador de cluster_ possa ter permissão para fazer alterações em modelos de restrição e em restrições, não é recomendável ou não há suporte para fazer alterações em modelos de restrição ou em restrições criadas pelo Azure Policy. Todas as alterações manuais feitas são perdidas durante o ciclo de atualização.

A cada 5 minutos, o complemento chama um exame completo do cluster. Depois de coletar detalhes do exame completo e de quaisquer avaliações em tempo real pelo Gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta ao Azure Policy para inclusão nos [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer atribuição do Azure Policy. Apenas os resultados de atribuições de política ativas são retornados durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como violações listadas no campo de status da restrição com falha.

## <a name="policy-language"></a>Linguagem do Azure Policy

A estrutura de linguagem do Azure Policy para gerenciar o Mecanismo AKS segue a das políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerenciar seus clusters do Mecanismo AKS e usa propriedades _details_ específicas ao trabalho com a [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o Gatekeeper v3. Para obter detalhes e exemplos, consulte o efeito [EnforceOPAConstraint](effects.md#enforceopaconstraint).

Como parte das propriedades _details.constraintTemplate_ e _details.constraint_ na definição de política, o Azure Policy passa os URIs desses [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o complemento. Rego é a linguagem que o OPA e o Gatekeeper suportam para validar uma solicitação ao cluster do Kubernetes. Dando suporte a um padrão existente para o gerenciamento do Kubernetes, o Azure Policy torna possível reutilizar as regras existentes e as emparelhar com o Azure Policy para uma experiência unificada de relatórios de conformidade de nuvem. Para obter mais informações, consulte [O que é Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Políticas internas

Para localizar as políticas internas para o gerenciamento do seu cluster do Mecanismo AKS usando o portal do Azure, siga estas etapas:

1. Inicie o serviço do Azure Policy no portal do Azure. Selecione **Todos os serviços** no painel esquerdo e, em seguida, pesquise e selecione **Política**.

1. No painel esquerdo da página do Azure Policy, selecione **Definições**.

1. Na caixa de listagem suspensa Categoria, use **Selecionar tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, o botão **Atribuir**.

> [!NOTE]
> Ao atribuir a definição do Azure Policy para o Mecanismo AKS, o **Escopo** deve ser o grupo de recursos do cluster do Mecanismo AKS.

Como alternativa, use o início rápido [Atribuir uma política – Portal](../assign-policy-portal.md) para localizar e atribuir uma política do Mecanismo AKS. Procure uma definição de política do Mecanismo AKS em vez do exemplo 'audit vms'.

> [!IMPORTANT]
> As políticas internas na categoria **Kubernetes** são apenas para uso com o Mecanismo AKS.

## <a name="logging"></a>Registro em log

### <a name="azure-policy-add-on-logs"></a>Logs do complemento do Azure Policy

Como um controlador/contêiner do Kubernetes, o complemento do Azure Policy mantém os logs no cluster do Mecanismo AKS.

Para exibir os logs do complemento do Azure Policy, use `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Logs do Gatekeeper

O pod do Gatekeeper, _gatekeeper-controller-manager-0_, geralmente está no namespace `gatekeeper-system` ou `kube-system`, mas pode estar em um namespace diferente, dependendo de como é implantado.

Para exibir os logs do Gatekeeper, use `kubectl`:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Para obter mais informações, consulte [Depuração do Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação do OPA.

## <a name="remove-the-add-on"></a>Remover o complemento

Para remover o complemento do Azure Policy e o Gatekeeper do seu cluster do Mecanismo AKS, use o método que se alinha com o modo como o complemento foi instalado:

- Se instalado através da definição da propriedade **addons** na definição de cluster para o Mecanismo AKS:

  Reimplante a definição de cluster no Mecanismo AKS depois de alterar a propriedade **addons** de _azure-policy_ para falso:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se instalado com gráficos do Helm:

  1. Remover restrições antigas

     Atualmente, o mecanismo de desinstalação remove apenas o sistema do Gatekeeper; ele não remove nenhum recurso de _ConstraintTemplate_, _Constraint_ ou _Config_ que foram criados pelo usuário, nem remove os _CRDs_ que o acompanham.

     Quando o Gatekeeper está em execução, é possível remover restrições indesejadas da seguinte forma:

     - Excluindo todas as instâncias do recurso de restrição
     - Excluindo o recurso _ConstraintTemplate_, que deve limpar automaticamente o _CRD_
     - Excluindo o recurso _Config_, que remove os finalizadores em recursos sincronizados

  1. Desinstalar o complemento do Azure Policy
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Desinstalar o Gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo complemento do Azure Policy

O complemento do Azure Policy para o Kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados de diagnóstico são dados técnicos vitais relacionados ao software e ao desempenho. Eles podem ser usados das seguintes maneiras:

- Manter atualizado o complemento do Azure Policy
- Manter o complemento do Azure Policy seguro, confiável e com bom desempenho
- Melhorar o complemento do Azure Policy por meio da análise agregada do uso do complemento

As informações coletadas pelo complemento não são dados pessoais. Os detalhes a seguir são coletados no momento:

- Versão do agente do complemento do Azure Policy
- Tipo de cluster
- Região do cluster
- Grupo de recursos de cluster
- ID do recurso de cluster
- ID da assinatura de cluster
- Sistema operacional do cluster (exemplo: Linux)
- Cidade do cluster (exemplo: Seattle)
- Estado ou província do cluster (exemplo: Washington)
- País ou região do cluster (exemplo: Estados Unidos)
- Exceções/erros encontrados por complemento do Azure Policy durante a instalação do agente na avaliação da política
- Número de políticas do Gatekeeper não instaladas pelo complemento do Azure Policy

## <a name="next-steps"></a>Próximas etapas

- Veja os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar a [Estrutura de definição de política](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).