---
title: Políticas de suporte para o AKS (Serviço de Kubernetes do Azure)
description: Saiba mais sobre as políticas de suporte do AKS (Serviço de Kubernetes do Azure), a responsabilidade compartilhada e os recursos que estão na versão prévia (ou alfa ou beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: ec58f8df5507fd9c52950e880c062e6cad964b7a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106978"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte para o Serviço de Kubernetes do Azure

Este artigo fornece detalhes sobre as políticas de suporte técnico e as limitações do AKS (Serviço de Kubernetes do Azure). O artigo também detalha o gerenciamento de nó de trabalho, os componentes do plano de controle gerenciado, os componentes de software livre de terceiros e o gerenciamento de segurança ou de patch.

## <a name="service-updates-and-releases"></a>Atualizações de serviço e versões

* Para obter informações de versão, confira as [Notas sobre a versão do AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre os recursos na versão prévia, confira [Versões prévias dos recursos do AKS e os projetos relacionados](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Recursos gerenciados no AKS

Os componentes de nuvem de IaaS (infraestrutura como serviço) de base, como componentes de computação ou de rede, oferecem aos usuários acesso a controles de baixo nível e opções de personalização. Por outro lado, o AKS fornece uma implantação completa do Kubernetes que oferece aos clientes o conjunto comum de configurações e funcionalidades de que eles precisam. Os clientes do AKS têm personalização, implantação e outras opções limitadas. Esses clientes não precisam se preocupar nem gerenciar clusters do Kubernetes diretamente.

Com o AKS, o cliente recebe um *painel de controle* totalmente gerenciado. O painel de controle contém todos os componentes e serviços de que o cliente precisa para operar e fornecer clusters do Kubernetes aos usuários finais. Todos os componentes do Kubernetes são mantidos e operados pela Microsoft.

A Microsoft gerencia e monitora os seguintes componentes usando o painel de controle:

* Servidor de API do Kubelet ou do Kubernetes
* Etcd ou um repositório de valor-chave compatível, fornecendo QoS (Qualidade de Serviço), escalabilidade e runtime
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Proxy ou rede Kubernetes

O AKS não é uma solução de cluster completamente gerenciada. Alguns componentes, como nós de trabalho, têm *responsabilidade compartilhada*, na qual os usuários devem ajudar a manter o cluster do AKS. A entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança do SO (sistema operacional) do nó de trabalho.

Os serviços são *gerenciados* no sentido de que a Microsoft e a equipe do AKS implantam, operam e são responsáveis pela disponibilidade e pela funcionalidade do serviço. Os clientes não podem alterar esses componentes gerenciados. A Microsoft limita a personalização para garantir uma experiência de usuário consistente e escalonável. Para obter uma solução totalmente personalizável, confira o [mecanismo do AKS](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

Quando um cluster é criado, o cliente define os nós de trabalho do Kubernetes criados pelo AKS. As cargas de trabalho do cliente são executadas nesses nós. Os clientes detêm e podem ver ou modificar os nós de trabalho.

Como os nós de cluster do cliente executam um código particular e armazenam dados confidenciais, o Suporte da Microsoft podem acessá-los apenas de uma forma limitada. O Suporte da Microsoft não pode se conectar, executar comandos ou ver logs desses nós sem a permissão ou a assistência expressa do cliente.

Como os nós de trabalho são confidenciais, a Microsoft toma muito cuidado para limitar o gerenciamento em segundo plano. Em muitos casos, sua carga de trabalho continuará sendo executada, mesmo se os nós mestre do Kubernetes, o etcd e outros componentes gerenciados pela Microsoft falharem. Os nós de trabalho modificados de maneira descuidada podem causar perdas de dados e de cargas de trabalho e podem tornar o cluster incompatível.

## <a name="aks-support-coverage"></a>Cobertura de suporte do AKS

A Microsoft oferece suporte técnico para o seguinte:

> [!NOTE]
> Todas as ações de cluster tomadas pela Microsoft/AKS são feitas com consentimento do usuário em uma função interna kubernetes `aks-service` e Associação de função interna `aks-service-rolebinding` . Essa função permite que o AKS solucione problemas e diagnostique problemas de cluster, mas não pode modificar permissões nem criar funções ou associações de função nem outras ações de alto privilégio. O acesso à função só é habilitado em Tíquetes de suporte ativos com acesso JIT (just-in-time).

* Conectividade com todos os componentes do Kubernetes que o serviço de Kubernetes oferece e dá suporte, como o servidor de API.
* Gerenciamento, tempo de atividade, QoS e operações dos serviços do painel de controle do Kubernetes (nós mestre do Kubernetes, servidor de API, etcd e kube-dns, por exemplo).
* Etcd. O suporte inclui backups automatizados e transparentes de todos os dados do etcd a cada 30 minutos para planejamento de desastre e restauração de estado do cluster. Esses backups não estão disponíveis diretamente para clientes ou usuários. Eles garantem a confiabilidade e a consistência dos dados. Etcd. Não há suporte para a reversão ou restauração sob demanda como um recurso.
* Qualquer ponto de integração no driver do provedor de nuvem do Azure para Kubernetes. Isso inclui integrações em outros serviços do Azure, como balanceadores de carga, volumes persistentes ou rede (Kubernetes e CNI do Azure).
* Perguntas ou problemas sobre a personalização de componentes do painel de controle, como o servidor de API do Kubernetes, etcd e kube-dns.
* Problemas sobre redes, como CNI do Azure, kubenet ou outros problemas de acesso e funcionalidade de rede. Os problemas podem incluir resolução de DNS, perda de pacotes, roteamento e assim por diante. A Microsoft dá suporte a vários cenários de rede:
  * Kubenet (básico) e rede avançada (CNI do Azure) dentro do cluster e componentes associados
  * Conectividade com outros serviços e aplicativos do Azure
  * Controladores de entrada e configurações de entrada ou do balanceador de carga
  * Desempenho e latência de rede

A Microsoft não oferece suporte técnico para o seguinte:

* Perguntas sobre como usar o Kubernetes. Por exemplo, o Suporte da Microsoft não faz recomendações sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicativos ou aplicar pacotes ou ferramentas de software livre ou de terceiros.
  > [!NOTE]
  > O Suporte da Microsoft pode recomendar a funcionalidade, a personalização e o ajuste do cluster do AKS (por exemplo, problemas e procedimentos de operações do Kubernetes).
* Projetos de software livre de terceiros que não são fornecidos como parte do painel de controle do Kubernetes ou implantados com clusters do AKS. Esses projetos podem incluir Istio, Helm, Envoy ou outros.
  > [!NOTE]
  > A Microsoft pode oferecer suporte de melhor esforço para projetos de software livre de terceiros, como Helm e Kured. Nos casos em que a ferramenta de software livre de terceiros se integra ao provedor de nuvem do Azure no Kubernetes ou outros bugs específicos do AKS, a Microsoft dá suporte a exemplos e aplicativos da documentação da Microsoft.
* Software closed-source de terceiros. Esse software pode incluir ferramentas de verificação de segurança e software ou dispositivos de rede.
* Problemas sobre o desenvolvimento de multinuvem ou de vários fornecedores. Por exemplo, a Microsoft não dá suporte a problemas relacionados à execução de uma solução de fornecedor de nuvem multipública federada.
* Personalizações de rede diferentes daquelas listadas na [documentação do AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft dá suporte a problemas e bugs relacionados aos NSGs (grupos de segurança de rede). Por exemplo, o Suporte da Microsoft pode responder a perguntas sobre uma falha de NSG para atualização ou um comportamento inesperado de NSG ou de balanceador de carga.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de suporte do AKS para nós de trabalho

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft pelos nós de trabalho do AKS

A Microsoft e os clientes compartilham a responsabilidade pelos nós de trabalho do Kubernetes nos quais:

* A imagem base do sistema operacional tem adições necessárias (como agentes de rede e monitoramento).
* Os nós de trabalho recebem patches do sistema operacional automaticamente.
* Os problemas com os componentes do painel de controle do Kubernetes executados nos nós de trabalho são corrigidos automaticamente. Os componentes incluem o seguinte:
  * Kube-proxy
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres do Kubernetes
  * Kubelet
  * Docker ou daemon do Moby

> [!NOTE]
> Em um nó de trabalho, se um componente de painel de controle não estiver operacional, a equipe do AKS poderá precisar reinicializar componentes individuais ou o todo o nó de trabalho. Essas operações de reinicialização são automatizadas e fornecem correção automática para problemas comuns. Essas reinicializações ocorrem apenas no nível de _nó_ e não no cluster, a menos que haja uma interrupção ou manutenção de emergência.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente pelos nós de trabalho do AKS

A Microsoft não reinicia automaticamente os nós de trabalho para aplicar patches no nível do sistema operacional. Embora os patches do sistema operacional sejam entregues aos nós de trabalho, o *cliente* é responsável por reinicializar os nós de trabalho para aplicar as alterações. As bibliotecas compartilhadas, os daemons como SSHD (unidade híbrida de estado sólido) e outros componentes no nível do sistema ou do sistema operacional são corrigidos automaticamente.

Os clientes são responsáveis por executar as atualizações do Kubernetes. Eles podem executar atualizações por meio do painel de controle do Azure ou da CLI do Azure. Isso se aplica a atualizações que contêm aprimoramentos de segurança ou de funcionalidade no Kubernetes.

#### <a name="user-customization-of-worker-nodes"></a>Personalização de nós de trabalho do usuário
> [!NOTE]
> Os nós de trabalho do AKS aparecem no portal do Azure como recursos comuns de IaaS do Azure. Mas essas máquinas virtuais são implantadas em um grupo de recursos personalizado do Azure (prefixado com MC\\*). É possível aumentar os nós de trabalho do AKS usando as configurações básicas. Por exemplo, você pode usar o SSH (Secure Shell) para alterar os nós de trabalho do AKS da maneira como você altera as máquinas virtuais normais. No entanto, você não pode alterar a imagem base do sistema operacional. Qualquer alteração personalizada pode não ser mantida por meio de atualização, escala, atualização ou reinicialização. **No entanto**, as alterações realizadas *fora da banda e fora do escopo da API do AKS* levam a um cluster do AKS que se torna incompatível. Evite alterar os nós de trabalho, a menos que Suporte da Microsoft indique a realização das alterações.

A emissão de operações incompatíveis, conforme definido acima, como desalocação fora da banda de todos os nós de agente, torna o cluster incompatível. O AKS reserva-se o direito de arquivar painéis de controle que foram configurados fora das diretrizes de suporte por longos períodos iguais e superiores a 30 dias. O AKS mantém backups dos metadados do cluster etcd e pode realocar o cluster imediatamente. Essa realocação pode ser iniciada por qualquer operação PUT que retorne o cluster para suporte, como uma atualização ou escala para os nós de agente ativos.

O AKS gerencia o ciclo de vida e as operações de nós de trabalho em nome dos clientes. A modificação dos recursos de IaaS associados aos nós de trabalho **não é compatível**. Um exemplo de uma operação incompatível é a personalização de um Conjunto de Dimensionamento de VM do pool de nós por meio da alteração manual de configurações no VMSS usando o portal do VMSS ou a API do VMSS.
 
Para configurações ou pacotes específicos da carga de trabalho, o AKS recomenda o uso de [daemonsets do Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

O uso de contêineres daemonsets e init com privilégios de Kubernetes permite que os clientes ajustem/modifiquem ou instalem software de terceiros em nós de trabalho do cluster. Os exemplos dessas personalizações incluem adicionar software personalizado de verificação de segurança ou atualizar configurações de sysctl.

Embora esse seja um caminho recomendado se os requisitos acima forem aplicáveis, a engenharia e o suporte do AKS não poderão auxiliar na solução de problemas ou no diagnóstico de modificações desfeitas/não funcionais ou que tornam o nó não disponível por conta de um DaemonSet implantado pelo cliente.

> [!NOTE]
> O AKS como um *serviço gerenciado* tem metas finais, como a remoção de responsabilidade por patches, atualizações e coleção de logs para tornar o gerenciamento de serviços mais completo e sem intervenção. À medida que a capacidade do serviço de gerenciamento de ponta a ponta aumenta, as versões futuras podem omitir algumas funções (por exemplo, reinicialização de nó e aplicação automática de patches).

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patch

Se uma falha de segurança for encontrada em um ou mais componentes do AKS, a equipe do AKS aplicará patches a todos os clusters afetados para atenuar o problema. Como alternativa, a equipe dará diretrizes de atualização aos usuários.

Para nós de trabalho afetados por uma falha de segurança, se um patch com tempo de inatividade zero estiver disponível, a equipe do AKS aplicará esse patch e notificará os usuários sobre a alteração.

Quando um patch de segurança exigir reinicializações do nó de trabalho, a Microsoft notificará os clientes sobre esse requisito. O cliente é responsável pela reinicialização ou pela atualização para obter o patch do cluster. Se os usuários não aplicarem os patches de acordo com as diretrizes do AKS, o cluster continuará vulnerável ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção e acesso do nó

Os nós de trabalho são uma responsabilidade compartilhada e pertencem aos clientes. Por isso, os clientes têm a capacidade de entrar nos nós de trabalho e fazer alterações potencialmente prejudiciais, como atualizações de kernel e instalação ou remoção de pacotes.

Se os clientes fizerem alterações destrutivas ou fizerem com que os componentes do painel de controle fiquem offline ou se não sejam funcionais, o AKS detectará essa falha e restaurará automaticamente o nó de trabalho para o estado de funcionamento anterior.

Embora os clientes possam entrar e alterar os nós de trabalho, isso não é recomendado porque as alterações podem tornar o cluster incompatível.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerenciado, o AKS tem requisitos específicos de rede e conectividade. Esses requisitos são menos flexíveis do que os requisitos para componentes regulares de IaaS. No AKS, operações como a personalização de regras do NSG, o bloqueio de uma porta específica (por exemplo, o uso de regras de firewall que bloqueiam a porta de saída 443) e URLs de lista de permissões podem tornar o cluster incompatível.

> [!NOTE]
> Atualmente, o AKS não permite que você bloqueie completamente o tráfego de saída do cluster. Para controlar a lista de URLs e portas que o cluster pode usar para tráfego de saída, confira [limitar o tráfego de saída](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Recursos de Kubernetes alfa e beta sem suporte

O AKS dá suporte apenas a recursos estáveis no projeto upstream do Kubernetes. Salvo indicação em contrário, o AKS não dá suporte a recursos alfa e beta disponíveis no projeto upstream do Kubernetes.

Em dois cenários, os recursos alfa ou beta podem ser distribuídos antes de estarem em disponibilidade geral:

* Os clientes se reuniram com as equipes de produtos, suporte ou engenharia do AKS e precisarão experimentar esses novos recursos.
* Esses recursos foram [habilitados por um sinalizador de recurso](https://github.com/Azure/AKS/blob/master/previews.md). Os clientes devem aceitar explicitamente o uso desses recursos.

## <a name="preview-features-or-feature-flags"></a>Versões prévias dos recursos ou sinalizadores de recurso

Para recursos e funcionalidades que exigem testes estendidos e comentários do usuário, a Microsoft lança novas versões prévias dos recursos ou recursos por trás de um sinalizador de recurso. Considere esses recursos como recursos beta ou de pré-lançamento.

As versões prévias dos recursos ou os recursos do sinalizador de recurso não são destinados à produção. As alterações contínuas em APIs e comportamento, correções de bugs e outras alterações podem resultar em clusters e tempo de inatividade instáveis.

Os recursos na versão prévia pública estão sob o suporte de "melhor esforço", pois esses recursos estão em versão prévia e não são destinados à produção e recebem suporte das equipes de suporte técnico do AKS somente durante o horário comercial. Para obter informações adicionais, confira:

* [Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> As versões prévias dos recursos entram em vigor no nível de *assinatura* do Azure. Não instale as versões prévias dos recursos em uma assinatura de produção. Em uma assinatura de produção, as versões prévias dos recursos podem alterar o comportamento da API padrão e afetar as operações regulares.

## <a name="upstream-bugs-and-issues"></a>Problemas e bugs de upstream

Devido à velocidade de desenvolvimento no projeto upstream do Kubernetes, invariavelmente surgem bugs. Alguns desses bugs não podem ser corrigidos ou solucionados no sistema do AKS. Em vez disso, as correções de bugs exigem patches maiores para os projetos upstream (como Kubernetes, sistemas operacionais de nó ou de trabalho e kernels). Para os componentes detidos pela Microsoft (como o provedor de nuvem do Azure), as equipes do AKS e do Azure estão comprometidas em corrigir problemas de upstream na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs de upstream, as equipes de suporte e engenharia do AKS vão:

* Identificar e vincular os bugs anteriores com os detalhes de suporte para ajudar a explicar por que esse problema afeta o cluster ou a carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam observar os problemas e ver quando uma nova versão fornecerá correções.
* Fornecer possíveis soluções alternativas ou mitigações. Se o problema puder ser mitigado, um [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivado no repositório do AKS. O arquivamento de problemas conhecidos explica:
  * O problema, incluindo links para bugs de upstream.
  * A solução alternativa e os detalhes sobre uma atualização ou outra persistência da solução.
  * Linhas do tempo aproximadas para a inclusão do problema, com base na cadência da versão de upstream.
