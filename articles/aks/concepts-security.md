---
title: Conceitos – Segurança no AKS (Serviços do Kubernetes do Azure)
description: Saiba mais sobre segurança no AKS (Serviço de Kubernetes do Azure), incluindo segredos do Kubernetes, políticas de rede e comunicação mestre e de nó.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595936"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)

Para proteger seus dados do cliente conforme você executa cargas de trabalho de aplicativo no AKS (Serviço de Kubernetes do Azure), a segurança do seu cluster é uma consideração importante. O Kubernetes inclui componentes de segurança, como *políticas de rede* e *Segredos*. O Azure então adiciona componentes, como grupos de segurança de rede e atualizações de cluster orquestradas. Esses componentes de segurança são combinados para manter seu cluster do AKS executando as mais recentes atualizações de segurança do sistema operacional e versões do Kubernetes e com tráfego de pod seguro e acesso a credenciais confidenciais.

Este artigo apresenta os conceitos básicos que protegem seus aplicativos no AKS:

- [Segurança de componentes mestres](#master-security)
- [Segurança do nó](#node-security)
- [Atualizações de cluster](#cluster-upgrades)
- [Segurança da rede](#network-security)
- [Segredos kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Segurança mestre

No AKS, os componentes mestres de Kubernetes fazem parte do serviço gerenciado fornecido pela Microsoft. Cada cluster AKS tem seu próprio mestre Kubernetes dedicado e de um único inquilino para fornecer o Servidor de API, Agendador, etc. Este mestre é gerenciado e mantido pela Microsoft.

Por padrão, o servidor API do Kubernetes usa um endereço IP público e um nome de domínio totalmente qualificado (FQDN). Você pode controlar o acesso ao Servidor de API usando os controles de acesso baseados em função do Kubernetes e do Azure Active Directory. Para obter mais informações, confira [Integração do Azure AD com o AKS][aks-aad].

## <a name="node-security"></a>Segurança do nó

Nós do AKS são máquinas virtuais do Azure que você gerenciar e manter. Os nós Linux executam uma distribuição Ubuntu otimizada usando o tempo de execução do contêiner Moby. Os nódulos do Windows Server (atualmente em pré-visualização no AKS) executam uma versão otimizada do Windows Server 2019 e também usam o tempo de execução do contêiner Moby. Quando um cluster do AKS é criado ou dimensionado, os nós são implantados automaticamente com as configurações e atualizações de segurança do sistema operacional mais recentes.

A plataforma Azure aplica automaticamente patches de segurança do SO aos nós Linux durante a noite. Se uma atualização de segurança do Sistema Operacional Linux exigir uma reinicialização do host, essa reinicialização não será realizada automaticamente. Você pode reinicializar manualmente os nós linux, ou uma abordagem comum é usar [kured][kured], um daemon de reinicialização de código aberto para Kubernetes. O Kured é executado como um [DaemonSet][aks-daemonsets] e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. As reinicializações são gerenciadas no cluster usando o mesmo [o processo de cordon e drenagem](#cordon-and-drain) como uma atualização do cluster.

Para os números do Windows Server (atualmente em pré-visualização no AKS), o Windows Update não é executado automaticamente e aplica as últimas atualizações. Em um cronograma regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, você deve executar uma atualização no pool de nó do Windows Server no cluster AKS. Este processo de atualização cria números que executam a imagem e patches mais recentes do Windows Server e, em seguida, remove os álos mais antigos. Para obter mais informações sobre este processo, consulte [Atualizar um pool de nós no AKS][nodepool-upgrade].

Nós são implantados em uma sub-rede de rede virtual privada, sem nenhum endereço IP público atribuído. Para fins de solução de problemas e gerenciamento, o SSH é habilitado por padrão. Esse acesso SSH só está disponível usando o endereço IP interno.

Para fornecer armazenamento, os nós usam o Azure Managed Disks. Para a maioria dos tamanhos de nó VM, esses são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos gerenciados são criptografados automaticamente em repouso na plataforma Azure. Para melhorar a redundância, esses discos também são replicados com segurança no datacenter do Azure.

Os ambientes do Kubernetes, no AKS ou em outro lugar, não estão completamente seguros atualmente para uso de vários locatários hostis. Recursos de segurança adicionais, como *Políticas de Segurança Pod* ou RBAC (controles de acesso baseado em função) mais refinado para nós dificultam as explorações. No entanto, para ter uma segurança de verdade ao executar cargas de trabalho de vários locatários hostis, um hipervisor é o único nível de segurança no qual você deve confiar. O domínio de segurança para o Kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters fisicamente isolados. Para obter mais informações sobre formas de isolar as cargas de trabalho, consulte [Melhores práticas para o isolamento de cluster no AKS][cluster-isolation].

## <a name="cluster-upgrades"></a>Atualizações do cluster

Para segurança e conformidade, ou para usar os recursos mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster e componentes do AKS. Essa orquestração de atualização inclui os componentes de mestre e agente do Kubernetes. Você pode exibir uma [lista de versões do Kubernetes](supported-kubernetes-versions.md) disponíveis para seu cluster do AKS. Para iniciar o processo de atualização, você especificar uma dessas versões disponíveis. O Azure então realiza cordon e drenagem com segurança de cada nó do AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordon e drenagem

Durante o processo de atualização, os nós AKS são isolados individualmente do cluster para que novas cápsulas não sejam programadas neles. Os nós então são drenados e atualizados da seguinte maneira:

- Um novo nó é implantado na piscina de nó. Este nó executa a imagem e os patches mais recentes do sistema operacional.
- Um dos nódulos existentes é identificado para atualização. Os pods neste nó são graciosamente encerrados e agendados nos outros nós na piscina de nós.
- Este nó existente é excluído do cluster AKS.
- O próximo nó no cluster é isolado e drenado usando o mesmo processo até que todos os nós sejam substituídos com sucesso como parte do processo de atualização.

Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança de rede

Para conectividade e segurança com redes locais, você pode implantar um cluster do AKS em sub-redes de rede virtual do Azure existentes. Essas redes virtuais podem ter uma conexão de VPN Site a Site ou Express Route do Azure para sua rede local. Controladores de entrada de Kubernetes podem ser definidos com os endereços IP privados e internos para que os serviços estejam acessíveis somente por essa conexão de rede interna.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, o Azure usa regras de grupo de segurança de rede. Essas regras definem o código-fonte e intervalos de IP de destino, portas e protocolos que têm o acesso a recursos permitido ou negado. As regras padrão são criadas para permitir o tráfego DeTS para o servidor API do Kubernetes. Conforme você cria serviços com balanceadores de carga, mapeamentos de porta ou rotas de entrada, o AKS modifica automaticamente o grupo de segurança de rede para o tráfego fluir de modo adequado.

## <a name="kubernetes-secrets"></a>Segredos do Kubernetes

Um *Segredo* do Kubernetes é usado para injetar dados confidenciais em pods, como chaves ou credenciais de acesso. Você primeiro criae um segredo usando a API do Kubernetes. Quando você define seu pod ou implantação, um segredo específico pode ser solicitado. Segredos são fornecidos apenas para nós que têm um pod agendado que precisa dele, e o segredo é armazenado no *tmpfs*, não gravado no disco. Quando o último pod em um nó que exige um segredo for excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados dentro de um determinado namespace e só podem ser acessados pelo pods no mesmo namespace.

O uso de Segredos reduz as informações confidenciais definidas no manifesto YAML de pod ou serviço. Em vez disso, você pode solicitar o Segredo armazenado no servidor de API do Kubernetes como parte do seu manifesto YAML. Essa abordagem fornece apenas ao pod específico acesso ao Segredo. Nota: os arquivos do manifesto secreto bruto contém os dados secretos no formato base64 (veja a [documentação oficial][secret-risks] para obter mais detalhes). Portanto, este arquivo deve ser tratado como informação sensível, e nunca comprometido com o controle de origem.

## <a name="next-steps"></a>Próximas etapas

Para começar a proteger seus clusters do AKS, confira [Atualizar um cluster do AKS][aks-upgrade-cluster].

Para práticas recomendadas associadas, consulte [Práticas recomendadas para segurança de cluster e upgrades em AKS][operator-best-practices-cluster-security] e [práticas recomendadas para segurança de pods em AKS][developer-best-practices-pod-security].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Kubernetes / clusters AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / escala de AKS][aks-concepts-identity]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / escala de AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
