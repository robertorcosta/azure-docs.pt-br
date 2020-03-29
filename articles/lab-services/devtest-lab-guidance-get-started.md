---
title: Cenários populares para usar o Azure DevTest Labs
description: Este artigo fornece os principais cenários para o uso do Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço em sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773801"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Cenários populares para usar o Azure DevTest Labs
Dependendo das necessidades de uma empresa, o DevTest Labs pode ser configurado para atender a diferentes requisitos.  Este artigo discute os cenários populares. Cada cenário cobre benefícios trazidos usando DevTest Labs e recursos para usar para implementar esses cenários.  

- Áreas de trabalho de desenvolvedor
- Ambientes de teste
- Sessões de treinamento, laboratórios práticos e hackathons
- Investigações sandboxed
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Áreas de trabalho de desenvolvedor
Os desenvolvedores geralmente têm requisitos diferentes para os computadores de desenvolvimento de projetos diferentes. Com o DevTest Labs, os desenvolvedores podem ter acesso a máquinas virtuais sob demanda que são configuradas para se adequarem aos seus cenários mais comuns. O DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer máquinas de desenvolvimento comuns garantindo consistência entre as equipes.
- Os desenvolvedores podem provisionar rapidamente suas máquinas de desenvolvimento demanda ou [reivindicar uma máquina pré-configurada existente.](devtest-lab-add-claimable-vm.md)
- Os desenvolvedores podem provisionar recursos de forma auto-serviço sem precisar de permissões de nível de assinatura.
- Os anúncios ou de TI podem [pré-definir a topologia de rede](devtest-lab-configure-vnet.md) e os desenvolvedores podem usá-la diretamente de forma simples e intuitiva sem exigir qualquer acesso especial.
- Os desenvolvedores podem facilmente [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) suas máquinas de desenvolvimento conforme necessário.
- Os administradores podem controlar os custos garantindo que:
    - Desenvolvedores [não podem obter mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que precisam para o desenvolvimento
    - [As VMs são desligadas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão em uso
    - Permitindo apenas [um subconjunto de tamanhos de instância vm](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - [Gerenciamento de metas de custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para obter mais leituras, consulte [Use Azure DevTest Labs para desenvolvedores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerenciar ambientes de teste em toda a empresa pode exigir um esforço significativo. Com o DevTest Labs, os ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Permite que as equipes acessem um ambiente totalmente configurado quando necessário. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer ambientes de teste comuns garantindo consistência entre as equipes.
- Os testadores podem testar a versão mais recente de seu aplicativo provisionando rapidamente ambientes Windows e Linux usando modelos reutilizáveis.
- Os administradores podem conectar o laboratório ao Azure DevOps para habilitar cenários DevOps
- Os proprietários de laboratórios podem controlar os custos garantindo que:
    - [VMs em ambientes são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão em uso
    - Permitindo apenas [um subconjunto de tamanhos de instância vm para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - [Gerenciamento de metas de custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para obter mais informações, consulte [Use Azure DevTest Labs para ambientes de teste VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações sandboxed
Os desenvolvedores geralmente investigam diferentes tecnologias ou design de infra-estrutura. Por padrão, todos os ambientes criados com o DevTest Labs são criados em seu próprio grupo de recursos. O usuário do DevTest Labs só tem acesso à leitura desses recursos. No entanto, para desenvolvedores que precisam de mais controle, uma configuração em laboratório pode ser atualizada para dar [direitos de contribuinte](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) ao usuário originado do DevTest Labs para cada ambiente que eles criam.  Com o DevTest Labs, os desenvolvedores podem receber permissão do contribuinte automaticamente para ambientes que eles criam no laboratório.  Esse cenário permite que os desenvolvedores adicionem e/ou alterem os recursos do Azure conforme precisarem para seus ambientes de desenvolvimento ou teste. O custo por página [de recursos](devtest-lab-configure-cost-management.md#view-cost-by-resource) permite que os proprietários do laboratório rastreiem o custo de cada ambiente usado para investigações.

Para obter mais informações, consulte [Definir direitos de acesso a um grupo de recursos ambientais](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinamentos, laboratórios práticos e hackathons 
Um laboratório no Azure DevTest Labs atua como um grande contêiner para atividades transitórias, como workshops, laboratórios práticos, treinamentos ou hackathons.  O serviço permite que você crie um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinamento. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- [As políticas](devtest-lab-set-lab-policy.md) garantem que os trainees só obtenham o número de recursos, como máquinas virtuais, de que precisam.
- Máquinas pré-configuradas e criadas são [reivindicadas](devtest-lab-add-claimable-vm.md) com ação única do estagiário.
- Os laboratórios são compartilhados com os estagiários acessando [url para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [As datas de validade](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) nas máquinas virtuais garantem que as máquinas sejam excluídas depois que elas não forem mais necessárias.
- É fácil [excluir um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os recursos [relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o treinamento acaba.

Para obter mais informações, consulte [Use Azure DevTest Labs para treinamento](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito versus implantação dimensionada
Uma vez que você decide explorar o DevTest Labs, existem dois caminhos gerais adiante: Prova de conceito vs Implantação em escala.  

Uma **implantação dimensionada** consiste em semanas/meses de revisão e planejamento com a intenção de implantar o DevTest Labs em toda a empresa que possui centenas ou milhares de desenvolvedores.

Uma prova de implantação **de conceito** se concentra em um esforço concentrado de uma única equipe para estabelecer valor organizacional. Embora possa ser tentador pensar em uma implantação dimensionada, a abordagem tende a falhar com mais frequência do que a opção de prova de conceito. Portanto, recomendamos que você comece pelo mais simples, aprenda com a primeira equipe, repita a mesma abordagem com outras duas ou três equipes e planeje uma implantação dimensionada com base no conhecimento adquirido. Para que sua prova de conceito seja bem-sucedida, recomendamos escolher uma ou duas equipes e identificar seus cenários (ambiente de desenvolvimento versus ambientes de teste), documentar seus casos de uso atuais e implantar o DevTest Labs.

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [Conceitos dos Laboratórios de Desenvolvimento/Teste](devtest-lab-concepts.md)
- [Perguntas frequentes do DevTest Labs](devtest-lab-faq.md)

