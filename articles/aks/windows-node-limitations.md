---
title: Limitações para pools de nó do Windows Server no Azure Kubernetes Service (AKS)
description: Conheça as limitações conhecidas ao executar pools de nó do Windows Server e cargas de trabalho de aplicativos no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366414"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para pools de nó do Windows Server e cargas de trabalho de aplicativos no Azure Kubernetes Service (AKS)

No Azure Kubernetes Service (AKS), você pode criar um pool de nós que executa o Windows Server como o sistema operacional convidado nos nós. Esses nós podem executar aplicativos nativos de contêiner do Windows, como aqueles construídos no .NET Framework. Como há grandes diferenças na forma como o Sistema Operacional Linux e Windows fornece suporte a contêineres, alguns Kubernetes comuns e recursos relacionados a pods não estão disponíveis atualmente para pools de nós do Windows.

Este artigo descreve algumas das limitações e conceitos do SISTEMA OPERACIONAL para os álos do Windows Server em AKS. Os pools de nó para o Windows Server estão atualmente em pré-visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são opt-in de autoatendimento. As visualizações são fornecidas "como está" e "conforme disponível" e são excluídas dos contratos de nível de serviço e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente na melhor base de esforço. Como tal, esses recursos não são destinados ao uso da produção. Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Quais sistemas operacionais Windows são suportados?

O AKS usa o Windows Server 2019 como versão do sistema operacional host e só suporta isolamento de processos. As imagens de contêiner construídas usando outras versões do Windows Server não são suportadas. [Compatibilidade com a versão do contêiner do Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>O Kubernetes é diferente no Windows e Linux?

O suporte ao pool de nós do Windows Server inclui algumas limitações que fazem parte do projeto Upstream Windows Server in Kubernetes. Essas limitações não são específicas para AKS. Para obter mais informações sobre esse suporte upstream para o Windows Server em Kubernetes, consulte a seção [Funcionalidade e Limitações suportadas][upstream-limitations] do suporte ao Intro to Windows no documento [Kubernetes,][intro-windows] a partir do projeto Kubernetes.

Kubernetes é historicamente focado em Linux. Muitos exemplos usados no site [upstream Kubernetes.io][kubernetes] destinam-se a ser usados em nós Linux. Quando você cria implantações que usam contêineres do Windows Server, as seguintes considerações no nível do sistema operacional se aplicam:

- **Identidade** - O Linux identifica um usuário por um identificador de usuário inteiro (UID). Um usuário também tem um nome de usuário alfanumérico para logon, que o Linux traduz para o UID do usuário. Da mesma forma, o Linux identifica um grupo de usuários por um identificador de grupo inteiro (GID) e traduz um nome de grupo para seu GID correspondente.
    - O Windows Server usa um identificador de segurança binário maior (SID) que é armazenado no banco de dados SAM (Windows Security Access Manager). Este banco de dados não é compartilhado entre o host e os contêineres, ou entre contêineres.
- **Permissões de arquivo** - O Windows Server usa uma lista de controle de acesso baseada em SIDs, em vez de uma máscara de bits de permissões e UID+GID
- **Caminhos de** arquivo - convenção no Windows Server é usar \ em vez de /.
    - Nas especificações do pod que montam volumes, especifique o caminho corretamente para os contêineres do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* em um contêiner Linux, especifique uma letra de unidade e um local como */K/Volume* para montar como a unidade *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Que tipo de discos são suportados para windows?

Discos Azure e Arquivos Azure são os tipos de volume suportados, acessados como volumes NTFS no contêiner do Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Posso executar apenas clusters do Windows em AKS?

Os nós mestres (o plano de controle) em um cluster AKS são hospedados pela AKS o serviço, você não será exposto ao sistema operacional dos nós que hospedam os componentes mestres. Todos os clusters AKS são criados com um pool padrão de primeiro nó, que é baseado em Linux. Este pool de nós contém serviços do sistema, que são necessários para que o cluster funcione. Recomenda-se executar pelo menos dois nódulos no primeiro pool de nó para garantir a confiabilidade do seu cluster e a capacidade de fazer operações de cluster. O primeiro pool de nós baseado em Linux não pode ser excluído a menos que o próprio cluster AKS seja excluído.

## <a name="what-network-plug-ins-are-supported"></a>Quais plug-ins de rede são suportados?

Os clusters AKS com pools de nó windows devem usar o modelo de rede Azure CNI (avançado). A rede Kubenet (básica) não é suportada. Para obter mais informações sobre as diferenças nos modelos de rede, consulte [conceitos de rede para aplicações em AKS][azure-network-models]. - O modelo de rede da CNI Azure requer planejamento adicional e considerações para o gerenciamento de endereços IP. Para obter mais informações sobre como planejar e implementar o Azure CNI, consulte [Configure Azure CNI networking in AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Posso mudar o máximo. # de pods por nó?

Sim. Para obter as implicações e opções disponíveis, consulte [Número máximo de pods][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Como corrigir meus nós do Windows?

Os números do Windows Server no AKS devem ser *atualizados* para obter as correções e atualizações de patches mais recentes. As atualizações do Windows não estão habilitadas em nós no AKS. A AKS libera novas imagens de pool de nós assim que os patches estiverem disponíveis, é responsabilidade dos clientes atualizar pools de nós para se manter em dia em patches e hotfix. Isso também é verdade para a versão Kubernetes que está sendo usada. As notas de versão do AKS indicarão quando novas versões estiverem disponíveis. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [Atualizar um pool de nós no AKS][nodepool-upgrade].

> [!NOTE]
> A imagem atualizada do Windows Server só será usada se uma atualização de cluster (atualização do plano de controle) tiver sido realizada antes de atualizar o pool de nó
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Como faço para girar o diretor de serviço para o meu pool de nó do Windows?

Durante a visualização, os pools de nó do Windows não suportam a rotação principal do serviço como uma limitação de visualização. Para atualizar o principal do serviço, crie um novo pool de nó do Windows e migre seus pods do pool mais antigo para o novo. Uma vez que isso esteja concluído, exclua o pool de nós mais antigos.

## <a name="how-many-node-pools-can-i-create"></a>Quantas piscinas de nós posso criar?

O cluster AKS pode ter um máximo de 10 piscinas de nó. Você pode ter um máximo de 1000 nodes nessas piscinas de nó. [Limitações do pool de nó][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>O que posso nomear minhas piscinas de nó do Windows?

Você tem que manter o nome em um máximo de 6 (seis) caracteres. Esta é uma limitação atual do AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Todos os recursos são suportados com nós do Windows?

As políticas de rede e o kubenet não são suportados atualmente com nós do Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Posso executar controladores de ingestão em nós do Windows?

Sim, um controlador de entrada que suporta contêineres do Windows Server pode ser executado em nomes windows em AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Posso usar o Azure Dev Spaces com nós do Windows?

Atualmente, o Azure Dev Spaces está disponível apenas para pools de nós baseados em Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Meus contêineres do Windows Server podem usar gMSA?

O suporte a contas de serviço gerenciadas em grupo (gMSA) não está disponível no AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Posso usar o Monitor Azure para contêineres com nós e recipientes do Windows?

Sim, você pode, no entanto, o Azure Monitor não coleta logs (stdout) de contêineres do Windows. Você ainda pode anexar à transmissão ao vivo de logs stdout de um contêiner do Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>E se eu precisar de um recurso que não seja suportado?

Trabalhamos duro para trazer todos os recursos necessários para o Windows em AKS, mas se você encontrar lacunas, o projeto [aks-engine de][aks-engine] código aberto fornece uma maneira fácil e totalmente personalizável de executar Kubernetes no Azure, incluindo suporte ao Windows. Por favor, certifique-se de verificar o nosso roteiro de recursos que estão chegando [ao roteiro da AKS][aks-roadmap].

## <a name="next-steps"></a>Próximas etapas

Para começar com os contêineres do Windows Server no AKS, [crie um pool de nós que execute o Windows Server em AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
