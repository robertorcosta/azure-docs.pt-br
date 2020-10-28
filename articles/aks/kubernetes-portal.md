---
title: Acessar recursos do kubernetes da portal do Azure (versão prévia)
description: Saiba como interagir com recursos do kubernetes para gerenciar um cluster do AKS (serviço kubernetes do Azure) do portal do Azure.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ae617615a8ba83e311a416581fb41d3cb6ca1b05
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635602"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Acessar recursos do kubernetes da portal do Azure (versão prévia)

O portal do Azure inclui um visualizador de recursos kubernetes (versão prévia) para facilitar o acesso aos recursos do kubernetes em seu cluster AKS (Azure kubernetes Service). A exibição de recursos de kubernetes da portal do Azure reduz a alternância de contexto entre o portal do Azure e a `kubectl` ferramenta de linha de comando, simplificando a experiência de exibição e edição dos recursos do kubernetes. O Visualizador de recursos atualmente inclui vários tipos de recursos, como implantações, pods e conjuntos de réplicas.

A exibição de recurso kubernetes da portal do Azure substitui o [complemento do painel do AKS][kubernetes-dashboard], que é definido para substituição.

>[!NOTE]
>Atualmente, o capabilty não tem suporte em [clusters privados do serviço kubernetes do Azure](./private-clusters.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para exibir os recursos de kubernetes no portal do Azure, você precisa de um cluster AKS. Há suporte para qualquer cluster, mas se estiver usando a integração do Azure Active Directory (Azure AD), o cluster deverá usar a [integração do Azure ad gerenciada pelo AKs][aks-managed-aad]. Se o cluster usar o Azure AD herdado, você poderá atualizar o cluster no portal ou com o [CLI do Azure][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Exibir recursos do kubernetes

Para ver os recursos do kubernetes, navegue até o cluster do AKS no portal do Azure. O painel de navegação à esquerda é usado para acessar seus recursos. Os recursos incluem:

- **Namespaces** exibe os namespaces do cluster. O filtro na parte superior da lista de namespaces fornece uma maneira rápida de filtrar e exibir seus recursos de namespace.
- As **cargas de trabalho** mostram informações sobre implantações, pods, conjuntos de réplicas e conjuntos de daemon implementados no cluster. A captura de tela abaixo mostra o pods padrão do sistema em um cluster AKS de exemplo.
- **Serviços e insere** mostra todos os recursos de serviço e entrada do cluster.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Informações de Pod kubernetes exibidas no portal do Azure." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Implantar um aplicativo

Neste exemplo, usaremos nosso cluster AKS de exemplo para implantar o aplicativo Azure vote no [início rápido do AKS][portal-quickstart].

1. Selecione **Adicionar** de qualquer um dos modos de exibição de recursos (namespace, cargas de trabalho ou serviços e insere).
1. Cole o YAML para o aplicativo de voto do Azure do [início rápido do AKS][portal-quickstart].
1. Selecione **Adicionar** na parte inferior do editor de YAML para implantar o aplicativo. 

Depois que o arquivo YAML for adicionado, o Visualizador de recursos mostrará os dois serviços do kubernetes que foram criados: o serviço interno (Azure-vote-back) e o serviço externo (Azure-vote-Front) para acessar o aplicativo Azure vote. O serviço externo inclui um endereço IP externo vinculado para que você possa exibir facilmente o aplicativo em seu navegador.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informações de Pod kubernetes exibidas no portal do Azure." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Monitorar informações de implantação

Os clusters AKS com [Azure monitor para contêineres][enable-monitor] habilitados podem exibir rapidamente as informações de implantação. Na exibição de recursos do kubernetes, os usuários podem ver o status ao vivo de implantações individuais, incluindo o uso de CPU e memória, bem como a transição para o Azure monitor para obter informações mais detalhadas. Aqui está um exemplo de informações de implantação de um cluster AKS de exemplo:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Informações de Pod kubernetes exibidas no portal do Azure." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Editar YAML

A exibição de recursos kubernetes também inclui um editor de YAML. Um editor YAML interno significa que você pode atualizar ou criar serviços e implantações de dentro do portal e aplicar as alterações imediatamente.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Informações de Pod kubernetes exibidas no portal do Azure.":::

Depois de editar o YAML, as alterações são aplicadas selecionando **revisar + salvar** , confirmando as alterações e salvando novamente.

>[!WARNING]
> A execução de alterações de produção direta via interface do usuário ou CLI não é recomendada, você deve aproveitar [as práticas recomendadas de CI (integração contínua) e de implantação contínua (CD)](kubernetes-action.md). Os recursos de gerenciamento de kubernetes do portal do Azure e o editor YAML são criados para aprender e comprovar novas implantações em uma configuração de desenvolvimento e teste.

## <a name="troubleshooting"></a>Solução de problemas

Esta seção aborda problemas comuns e etapas de solução de problemas.

### <a name="unauthorized-access"></a>Acesso não autorizado

Para acessar os recursos do kubernetes, você deve ter acesso ao cluster do AKS, à API do kubernetes e aos objetos do kubernetes. Certifique-se de que você seja um administrador de cluster ou um usuário com as permissões apropriadas para acessar o cluster AKS. Para obter mais informações sobre a segurança do cluster, consulte [Opções de acesso e identidade para AKs][concepts-identity].

>[!NOTE]
> O modo de exibição de recursos kubernetes no portal do Azure só tem suporte de [clusters habilitados para AAD gerenciados](managed-aad.md) ou clusters não habilitados para AAD. Se você estiver usando um cluster habilitado para AAD gerenciado, seu usuário ou identidade do AAD precisará ter as respectivas associações de funções/função para acessar a API kubernetes, além da permissão para efetuar pull [do `kubeconfig` usuário ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Habilitar exibição de recursos

Para os clusters existentes, talvez seja necessário habilitar a exibição de recursos kubernetes. Para habilitar o modo de exibição de recursos, siga os prompts no portal para o cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Informações de Pod kubernetes exibidas no portal do Azure." lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> O recurso AKS para [**intervalos de IP autorizados do servidor de API**](api-server-authorized-ip-ranges.md) pode ser adicionado para limitar o acesso do servidor de API somente ao ponto de extremidade público do firewall. Outra opção para esses clusters é atualizar `--api-server-authorized-ip-ranges` para incluir o acesso a um computador cliente local ou intervalo de endereços IP (do qual o portal está sendo procurado). Para permitir esse acesso, você precisa do endereço IPv4 público do computador. Você pode encontrar esse endereço com o comando abaixo ou pesquisando "o que é meu endereço IP" em um navegador da Internet.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como acessar recursos do kubernetes para o cluster do AKS. Consulte [implantações e manifestos YAML][deployments] para obter uma compreensão mais profunda dos recursos de cluster e dos arquivos YAML que são acessados com o Visualizador de recursos do kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md