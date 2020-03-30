---
title: Aprenda a política do Azure para o serviço Azure Kubernetes
description: Saiba como a Azure Policy usa o Rego e o Open Policy Agent para gerenciar clusters no Azure Kubernetes Service.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372652"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Entender a política do Azure para o Serviço de Kubernetes do Azure

A Azure Policy integra-se ao [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) para aplicar em escala as aplicação e salvaguardas em seus clusters de forma centralizada e consistente.
Ao estender o uso do [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, um _webhook controlador de admissão_ para [OA (Open Policy Agent),](https://www.openpolicyagent.org/) o Azure Policy permite gerenciar e relatar o estado de conformidade de seus recursos Azure e clusters AKS a partir de um só lugar.

> [!IMPORTANT]
> A Azure Policy for AKS está no Preview e só suporta definições de diretiva incorporadas. As políticas incorporadas estão na categoria **Kubernetes.** O efeito **EnforceRegoPolicy** e as políticas relacionadas da categoria **kubernetes service** estão sendo _preteridos_. Em vez disso, use o efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) atualizado.

> [!WARNING]
> Este recurso ainda não está disponível em todas as regiões. Para obter um status no rollout, consulte [Problemas aks - Alterações de quebra para complemento de diretiva](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Visão geral

Para habilitar e usar a Política Do Azure para AKS com seu cluster AKS, tome as seguintes ações:

- [Aceitar versão prévia dos recursos](#opt-in-for-preview)
- [Instale o complemento da política do Azure](#installation-steps)
- [Atribuir uma definição de política para AKS](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Opt-in para visualização

Antes de instalar o Complemento de Política do Azure ou habilitar qualquer um dos recursos do serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft.ContainerService** e o provedor de recursos **Microsoft.PolicyInsights** e, em seguida, ser aprovado para participar da pré-visualização. Para participar da pré-visualização, siga estas etapas no portal Azure ou com o Azure CLI:

- Portal do Azure:

  1. Registre os provedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para etapas, consulte [Provedores de recursos e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

     ![Pesquisar Política em Todos os Serviços](../media/rego-for-aks/search-policy.png)

  1. Selecione **''''''''''''''''''''''''''''''''''''Inserindo',** no lado esquerdo

     ![Participe da pré-visualização da Policy for AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selecione a linha de assinatura que deseja adicionada à pré-visualização.

  1. Selecione o botão **Opt-in** no topo da lista de assinaturas.

- CLI do Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Complemento de política do Azure

O _complemento de política do Azure_ para Kubernetes conecta o serviço de política do Azure ao controlador de admissão Gatekeeper. O complemento, que é instalado no namespace do _sistema kube,_ decreta as seguintes funções:

- Verifica com o serviço de política do Azure as atribuições para o cluster.
- Implanta políticas no cluster como [modelo de restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e recursos personalizados de [restrição.](https://github.com/open-policy-agent/gatekeeper#constraints)
- Reporta auditoria e detalhes de conformidade de volta ao serviço de política do Azure.

### <a name="installing-the-add-on"></a>Instalando o complemento

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento no cluster AKS, a extensão de visualização deve ser instalada. Esta etapa é feita com o Azure CLI:

1. Se as políticas gatekeeper v2 forem instaladas, remova o complemento com o botão **Desativar** no cluster AKS na página **Políticas (visualização).**

1. Você precisa da versão 2.0.62 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. O cluster AKS deve ser a versão _1.14_ ou superior. Use o seguinte script para validar sua versão de cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _0.4.0_ da extensão de `aks-preview`visualização Cli do Azure para AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se você já instalou anteriormente a extensão _aks-preview,_ instale quaisquer atualizações usando o `az extension update --name aks-preview` comando.

#### <a name="installation-steps"></a>Etapas de instalação

Uma vez que os pré-requisitos sejam concluídos, instale o Complemento de Diretiva do Azure no cluster AKS que você deseja gerenciar.

- Portal do Azure

  1. Inicie o serviço AKS no portal Azure clicando em **Todos os serviços,** em seguida, procurando e selecionando **os serviços kubernetes**.

  1. Selecione um de seus clusters AKS.

  1. Selecione **Políticas (visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **Ativar o complemento.**

     ![Habilite a política do Azure para complemento AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se o botão **de adesão Habilitar** estiver acinzentado, a assinatura ainda não foi adicionada à visualização. Consulte [Opt-in para ver-se](#opt-in-for-preview) para ver as etapas necessárias. Se houver um botão **Desativar,** o Gatekeeper v2 ainda está instalado e deve ser removido.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e emissão de relatórios

O complemento faz check-on com o serviço de política do Azure para alterações nas atribuições de políticas a cada 15 minutos.
Durante este ciclo de atualização, o complemento verifica se há alterações. Essas alterações disparam criações, atualizações ou exclusões dos modelos e restrições de restrição.

> [!NOTE]
> Embora um administrador de cluster possa ter permissão para criar e atualizar modelos de restrição e recursos de restrições, esses cenários não são suportados, pois as atualizações manuais serão sobregravadas.

A cada 15 minutos, o complemento pede uma varredura completa do cluster. Depois de coletar detalhes da varredura completa e quaisquer avaliações em tempo real pelo Gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta ao serviço de política do Azure para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md#portal) como qualquer atribuição de Política do Azure. Apenas os resultados das atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações listadas](https://github.com/open-policy-agent/gatekeeper#audit) no campo de status da restrição falha.

## <a name="policy-language"></a>Linguagem política

A estrutura de linguagem da Política Do Azure para o gerenciamento dos Kubernetes segue a das políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerenciar seus clusters Kubernetes e leva informações específicas para trabalhar com [o OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o Gatekeeper v3. Para obter detalhes e exemplos, consulte o efeito [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
  
Como parte dos _detalhes.constraintTemplate_ e _details.constraint_ properties in the policy definition, o Azure Policy passa as URIs desses CRD [(CustomResourceDefinitions)](https://github.com/open-policy-agent/gatekeeper#constraint-templates) para o complemento. Rego é a linguagem que a OPA e o Gatekeeper suportam para validar uma solicitação ao cluster Kubernetes. Ao apoiar um padrão existente para o gerenciamento do Kubernetes, o Azure Policy permite reutilizar as regras existentes e emparelhá-las com a Azure Policy para uma experiência unificada de relatórios de conformidade em nuvem. Para mais informações, consulte [O que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Políticas internas

Para encontrar as políticas incorporadas para gerenciar seu cluster usando o portal Azure, siga estas etapas:

1. Inicie o serviço de Política Do Azure no portal Azure. Selecione Todos os serviços no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política do Azure, selecione **Definições**.

1. Na caixa de lista de estiagem de categoria, use Selecione tudo para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de diretiva e selecione o botão **Atribuir.**

1. Defina o **Escopo** para o grupo de gerenciamento, assinatura ou grupo de recursos do cluster Kubernetes onde a atribuição de diretiva será aplicada.

   > [!NOTE]
   > Ao atribuir a diretiva Azure para definição AKS, o **Escopo** deve incluir o recurso de cluster AKS.

1. Dê à atribuição de diretiva um **nome** e **descrição** que você pode usar para identificá-la facilmente.

1. Defina a [aplicação da Política](./assignment-structure.md#enforcement-mode) como um dos valores abaixo.

   - **Ativado** - Impor a política no cluster. Os pedidos de admissão de Kubernetes com violações são negados.
   
   - **Desativado** - Não aplique a política no cluster. Os pedidos de admissão de Kubernetes com violações não são negados. Os resultados da avaliação de conformidade ainda estão disponíveis. Ao implementar novas políticas para executar clusters, a opção _Disabled_ é útil para testar as políticas, pois as solicitações de admissão com violações não são negadas.

1. Selecione **Avançar**.

1. Definir **valores de parâmetros**
   
   - Para excluir os namespaces do Kubernetes da avaliação de políticas, especifique a lista de namespaces no parâmetro **Namespace exclusões**. Recomenda-se excluir: _kube-system_

1. Selecione **Revisão + criar**.

Alternativamente, use a [Política Atribuir - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política AKS. Procure uma definição de política do Kubernetes em vez da amostra 'audit vms'.

> [!IMPORTANT]
> Políticas incorporadas na categoria **Kubernetes** são apenas para uso com AKS. Para obter uma lista de políticas incorporadas, consulte [amostras de Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>Registrando em log

### <a name="azure-policy-add-on-logs"></a>Logs de complementos de política do Azure

Como um controlador/contêiner Kubernetes, tanto o Azure Policy Add-on quanto o Gatekeeper mantêm logs no cluster AKS. Os registros são expostos na página **Insights** do cluster AKS. Para obter mais informações, consulte [Entenda o desempenho do cluster AKS com o Monitor Azure para contêineres](../../../azure-monitor/insights/container-insights-analyze.md).

## <a name="remove-the-add-on"></a>Remova o complemento

Para remover o complemento da diretiva do Azure do seu cluster AKS, use o portal Azure ou o Azure CLI:

- Portal do Azure

  1. Inicie o serviço AKS no portal Azure clicando em **Todos os serviços,** em seguida, procurando e selecionando **os serviços kubernetes**.

  1. Selecione o cluster AKS onde deseja desativar o complemento da diretiva do Azure.

  1. Selecione **Políticas (visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **Desativar.**

     ![Desativar a política do Azure para acessação aks](../media/rego-for-aks/disable-policy-add-on.png)

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo Azure Policy Add-on

O complemento de política do Azure para Kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados diagnósticos são dados técnicos vitais relacionados ao software e ao desempenho. Ele é usada das seguintes maneiras:

- Mantenha o adesão da política do Azure atualizado
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
