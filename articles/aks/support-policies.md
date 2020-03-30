---
title: Políticas de suporte para o Azure Kubernetes Service (AKS)
description: Saiba mais sobre as políticas de suporte do Azure Kubernetes Service (AKS), responsabilidade compartilhada e recursos que estão em pré-visualização (ou alfa ou beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593573"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte para o Serviço Azure Kubernetes

Este artigo fornece detalhes sobre políticas de suporte técnico e limitações para o Azure Kubernetes Service (AKS). O artigo também detalha o gerenciamento de nó do trabalhador, componentes gerenciados do plano de controle, componentes de código aberto de terceiros e gerenciamento de segurança ou patches.

## <a name="service-updates-and-releases"></a>Atualizações e lançamentos de serviços

* Para obter informações sobre a versão, consulte [notas de versão da AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre recursos na pré-visualização, consulte [recursos de pré-visualização aks e projetos relacionados](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Recursos gerenciados em AKS

A infra-estrutura base como um serviço (IaaS) componentes em nuvem, como computação ou componentes de rede, dão aos usuários acesso a controles de baixo nível e opções de personalização. Em contraste, o AKS fornece uma implantação de Kubernetes que dá aos clientes o conjunto comum de configurações e recursos de que precisam. Os clientes da AKS têm personalização limitada, implantação e outras opções. Esses clientes não precisam se preocupar ou gerenciar diretamente os clusters Kubernetes.

Com a AKS, o cliente recebe um plano de *controle*totalmente gerenciado. O plano de controle contém todos os componentes e serviços que o cliente precisa para operar e fornecer clusters Kubernetes para usuários finais. Todos os componentes do Kubernetes são mantidos e operados pela Microsoft.

A Microsoft gerencia e monitora os seguintes componentes através do painel de controle:

* Servidores API kubelet ou kubernetes
* Etcd ou uma loja de valor-chave compatível, fornecendo Qualidade de Serviço (QoS), escalabilidade e tempo de execução
* Serviços De DNS (por exemplo, kube-dns ou CoreDNS)
* Proxy kubernetes ou rede

AKS não é uma solução de cluster completamente gerenciada. Alguns componentes, como nós do trabalhador, têm *responsabilidade compartilhada,* onde os usuários devem ajudar a manter o cluster AKS. A entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança do sistema operacional do nó do trabalhador (OS).

Os serviços são *gerenciados* no sentido de que a Microsoft e a equipe da AKS implantam, operam e são responsáveis pela disponibilidade e funcionalidade do serviço. Os clientes não podem alterar esses componentes gerenciados. A Microsoft limita a personalização para garantir uma experiência de usuário consistente e escalável. Para obter uma solução totalmente personalizável, consulte [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Os nódulos de trabalhadores da AKS aparecem no portal Azure como recursos regulares do Azure IaaS. Mas essas máquinas virtuais são implantadas em um grupo\\de recursos personalizado do Azure (prefixado com MC *). É possível mudar os nódulos dos trabalhadores da AKS. Por exemplo, você pode usar o Secure Shell (SSH) para alterar os nós dos trabalhadores da AKS da maneira como você altera máquinas virtuais normais (você não pode, no entanto, alterar a imagem do sistema operacional base, e as alterações podem não persistir através de uma atualização ou reinicialização), e você pode anexar outros recursos do Azure ao AKS nódulos operários. Mas quando você faz alterações *no gerenciamento e na personalização da banda,* o cluster AKS pode se tornar insustentável. Evite alterar os nós dos trabalhadores, a menos que o Microsoft Support o direcione a fazer alterações.

A emissão de operações sem suporte, conforme definido acima, como a desalocação fora da banda de todos os nós de agente, torna o cluster sem suporte. A AKS reserva-se o direito de arquivar aviões de controle que tenham sido configurados fora das diretrizes de suporte por períodos prolongados iguais e além de 30 dias. O AKS mantém backups de metadados de cluster etcd e pode prontamente realocar o cluster. Essa realocação pode ser iniciada por qualquer operação PUT trazendo o cluster de volta ao suporte, como uma atualização ou escala para os nós de agente ativo.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

Quando um cluster é criado, o cliente define os nós de trabalhador do Kubernetes que o AKS cria. As cargas de trabalho dos clientes são executadas nesses nós. Os clientes possuem e podem visualizar ou modificar os nós do trabalhador.

Como os nós de cluster de clientes executam códigos privados e armazenam dados confidenciais, o Microsoft Support pode acessá-los apenas de forma limitada. O Suporte da Microsoft não pode fazer login, executar comandos ou exibir logs para esses nomes sem permissão ou assistência expressa do cliente.

Como os nós dos trabalhadores são sensíveis, a Microsoft tem muito cuidado para limitar seu gerenciamento de antecedentes. Em muitos casos, sua carga de trabalho continuará a ser executada mesmo se os nós mestres do Kubernetes, etcd, e outros componentes gerenciados pela Microsoft falharem. Nós detrabalhadors descuidadamente modificados podem causar perdas de dados e cargas de trabalho e podem tornar o cluster insustentável.

## <a name="aks-support-coverage"></a>Cobertura de suporte aKS

A Microsoft fornece suporte técnico para o seguinte:

* Conectividade com todos os componentes do Kubernetes que o serviço Kubernetes fornece e suporta, como o servidor API.
* Gerenciamento, tempo de atividade, QoS e operações de serviços de plano de controle kubernetes (nós mestres kubernetes, servidor de API, etcd, e kube-dns, por exemplo).
* Etc. O suporte inclui backups automatizados e transparentes de todos os dados etcd a cada 30 minutos para planejamento de desastres e restauração de estado de cluster. Esses backups não estão diretamente disponíveis para clientes ou usuários. Eles garantem a confiabilidade e consistência dos dados.
* Quaisquer pontos de integração no driver de provedor de nuvem do Azure para Kubernetes. Estes incluem integrações em outros serviços do Azure, como balanceadores de carga, volumes persistentes ou redes (Kubernetes e Azure CNI).
* Perguntas ou questões sobre personalização de componentes do plano de controle, como o servidor API kubernetes, etcd, e kube-dns.
* Questões sobre networking, como a CNI do Azure, kubenet ou outros problemas de acesso e funcionalidade da rede. Os problemas podem incluir resolução de DNS, perda de pacotes, roteamento e assim por diante. A Microsoft suporta vários cenários de rede:
  * Kubenet (rede básica) e avançada (Azure CNI) dentro do cluster e componentes associados
  * Conectividade com outros serviços e aplicativos do Azure
  * Configurações de controladores e intrizantes ou balanceadores de carga
  * Desempenho e latência da rede

A Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como usar Kubernetes. Por exemplo, o Microsoft Support não fornece conselhos sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicativos ou aplicar pacotes ou ferramentas de software de terceiros ou código aberto.
  > [!NOTE]
  > O Suporte microsoft pode aconselhar sobre a funcionalidade do cluster AKS, personalização e ajuste (por exemplo, problemas e procedimentos de operações do Kubernetes).
* Projetos de código aberto de terceiros que não são fornecidos como parte do plano de controle Kubernetes ou implantados com clusters AKS. Esses projetos podem incluir Istio, Helm, Envoy, ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer suporte de melhor esforço para projetos de código aberto de terceiros, como Helm e Kured. Quando a ferramenta de código aberto de terceiros se integra com o provedor de nuvem Kubernetes Azure ou outros bugs específicos da AKS, a Microsoft suporta exemplos e aplicativos da documentação da Microsoft.
* Software de fonte fechada de terceiros. Este software pode incluir ferramentas de varredura de segurança e dispositivos de rede ou software.
* Problemas sobre compilações de vários intervalos ou multifornecedores. Por exemplo, a Microsoft não suporta problemas relacionados à execução de uma solução de fornecedor de nuvem multipública federada.
* Personalizações de rede diferentes das listadas na [documentação AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft faz suporte a problemas e bugs relacionados a grupos de segurança de rede (NSGs). Por exemplo, o Suporte microsoft pode responder perguntas sobre uma falha do NSG para atualizar ou um comportamento inesperado de NSG ou balanceador de carga.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de suporte a AKS para nódulos de trabalhadores

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft para os nomes dos trabalhadores da AKS

A Microsoft e os clientes compartilham a responsabilidade pelos nós de trabalhadores da Kubernetes onde:

* A imagem do sistema operacional base tem adições necessárias (como agentes de monitoramento e rede).
* Os nós do trabalhador recebem patches do SO automaticamente.
* Os problemas com os componentes do plano de controle Kubernetes que funcionam nos nós do trabalhador são automaticamente remediados. Os componentes incluem o seguinte:
  * Kube-proxy
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres do Kubernetes
  * Kubelet
  * Docker ou Moby Daemon

> [!NOTE]
> Em um nó de trabalhador, se um componente do plano de controle não estiver operacional, a equipe aks pode precisar reiniciar componentes individuais ou todo o nó do trabalhador. Essas operações de reinicialização são automatizadas e fornecem remediação automática para problemas comuns. Essas reinicializações ocorrem apenas no nível do _nó_ e não no cluster, a menos que haja uma manutenção ou paralisação de emergência.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente para os nós de trabalhador da AKS

A Microsoft não reinicializa automaticamente os nós do trabalhador para aplicar patches no nível do SO. Embora os patches do SO sejam entregues aos nós do trabalhador, o *cliente* é responsável por reiniciar os nós do trabalhador para aplicar as alterações. Bibliotecas compartilhadas, daemons como unidade híbrida de estado sólido (SSHD) e outros componentes no nível do sistema ou sistema operacional são automaticamente corrigidos.

Os clientes são responsáveis pela execução de upgrades do Kubernetes. Eles podem executar upgrades através do painel de controle do Azure ou do Azure CLI. Isso se aplica a atualizações que contenham melhorias de segurança ou funcionalidade no Kubernetes.

> [!NOTE]
> Como o AKS é um *serviço gerenciado,* suas metas finais incluem remover a responsabilidade por patches, atualizações e coleta de registros para tornar o gerenciamento de serviços mais completo e prático. À medida que a capacidade do serviço para gerenciamento de ponta a ponta aumenta, versões futuras podem omitir algumas funções (por exemplo, reinicialização de nó e patches automáticos).

### <a name="security-issues-and-patching"></a>Problemas de segurança e patches

Se uma falha de segurança for encontrada em um ou mais componentes do AKS, a equipe aks irá corrigir todos os clusters afetados para mitigar o problema. Alternativamente, a equipe dará aos usuários orientações de atualização.

Para os nós do trabalhador que uma falha de segurança afeta, se um patch de tempo zero de inatividade estiver disponível, a equipe aks aplicará esse patch e notificará os usuários da alteração.

Quando um patch de segurança requer reinicialização do nó do trabalhador, a Microsoft notificará os clientes sobre essa exigência. O cliente é responsável por reiniciar ou atualizar para obter o patch de cluster. Se os usuários não aplicarem os patches de acordo com a orientação da AKS, seu cluster continuará vulnerável ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção e acesso ao nó

Os nós do trabalhador são uma responsabilidade compartilhada e são de propriedade dos clientes. Por causa disso, os clientes têm a capacidade de fazer login em seus nós de trabalhadores e fazer alterações potencialmente prejudiciais, como atualizações de kernel e instalação ou remoção de pacotes.

Se os clientes fizerem alterações destrutivas ou causarem que os componentes do plano de controle fiquem offline ou se tornem não funcionais, a AKS detectará essa falha e restaurará automaticamente o nó do trabalhador ao estado de trabalho anterior.

Embora os clientes possam entrar e alterar os nós do trabalhador, fazer isso é desencorajado porque as mudanças podem tornar um cluster insustentável.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerenciado, o AKS tem requisitos específicos de rede e conectividade. Esses requisitos são menos flexíveis do que os requisitos para componentes Normais de IaaS. No AKS, operações como personalizar regras do NSG, bloquear uma porta específica (por exemplo, usando regras de firewall que bloqueiam a porta de saída 443) e URLs de listagem branca podem tornar seu cluster incompatível.

> [!NOTE]
> Atualmente, a AKS não permite que você bloqueie completamente o tráfego de saída do seu cluster. Para controlar a lista de URLs e portas que o cluster pode usar para tráfego de saída, consulte [o tráfego de saída](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Recursos alpha e beta Kubernetes sem suporte

O AKS suporta apenas recursos estáveis dentro do projeto Upstream Kubernetes. Salvo documentação em contrário, o AKS não suporta recursos alfa e beta que estão disponíveis no projeto Upstream Kubernetes.

Em dois cenários, os recursos alfa ou beta podem ser implementados antes de estarem geralmente disponíveis:

* Os clientes se reuniram com as equipes de produto, suporte ou engenharia da AKS e foram convidados a experimentar esses novos recursos.
* Esses recursos foram [habilitados por um sinalizador de recursos](https://github.com/Azure/AKS/blob/master/previews.md). Os clientes devem optar explicitamente por usar esses recursos.

## <a name="preview-features-or-feature-flags"></a>Exibir recursos ou sinalizadores de recursos

Para recursos e funcionalidades que requerem testes estendidos e feedback do usuário, a Microsoft lança novos recursos de visualização ou recursos por trás de um sinalizador de recursos. Considere esses recursos como recursos de pré-lançamento ou beta.

Os recursos de visualização ou de destaque não foram feitos para a produção. Mudanças contínuas em APIs e comportamento, correções de bugs e outras alterações podem resultar em clusters instáveis e tempo de inatividade.

Os recursos na pré-visualização pública são suportados pelo "melhor esforço", pois esses recursos estão em pré-visualização e não são destinados à produção e são suportados pelas equipes de suporte técnico da AKS apenas durante o horário comercial. Para obter informações adicionais, consulte:

* [Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Os recursos de visualização fazem efeito no nível de *assinatura* do Azure. Não instale recursos de visualização em uma assinatura de produção. Em uma assinatura de produção, os recursos de visualização podem alterar o comportamento padrão da API e afetar as operações regulares.

## <a name="upstream-bugs-and-issues"></a>Bugs e problemas upstream

Dada a velocidade de desenvolvimento no projeto Kubernetes a montante, insetos invariavelmente surgem. Alguns desses bugs não podem ser corrigidos ou trabalhados dentro do sistema AKS. Em vez disso, as correções de bugs requerem patches maiores para projetos upstream (como Kubernetes, sistemas operacionais de nó ou trabalhadores e kernels). Para componentes que a Microsoft possui (como o provedor de nuvem do Azure), o pessoal da AKS e do Azure está comprometido em corrigir problemas a montante na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs upstream, as equipes de suporte e engenharia da AKS:

* Identifique e vincule os bugs upstream com quaisquer detalhes de suporte para ajudar a explicar por que esse problema afeta seu cluster ou carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam observar os problemas e ver quando uma nova versão fornecerá correções.
* Forneça soluções ou atenuações potenciais. Se o problema puder ser mitigado, um [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivado no repositório AKS. O arquivo de emissão conhecida explica:
  * O problema, incluindo links para bugs upstream.
  * A solução e os detalhes sobre um upgrade ou outra persistência da solução.
  * Cronogramas ásperos para a inclusão do problema, com base na cadência de liberação upstream.
