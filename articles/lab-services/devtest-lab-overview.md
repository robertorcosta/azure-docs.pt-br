---
title: Sobre os Laboratórios de Desenvolvimento/Teste | Microsoft Azure
description: Saiba como os Laboratórios de Desenvolvimento/Teste podem facilitar criar, gerenciar e monitorar as máquinas virtuais do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561448"
---
# <a name="about-azure-devtest-labs"></a>Sobre os Laboratórios de Desenvolvimento/Teste do Azure
O Azure DevTest Labs permite que desenvolvedores em equipes gerenciem eficientemente os recursos de máquinas virtuais (VMs) e PaaS sem esperar por aprovações.

O DevTest Labs cria laboratórios que consistem em bases pré-configuradas ou modelos do Azure Resource Manager. Estes têm todas as ferramentas e softwares necessários que você pode usar para criar ambientes. Você pode criar ambientes em poucos minutos, ao contrário de horas ou dias.

Usando o DevTest Labs, você pode testar as versões mais recentes de seus aplicativos fazendo as seguintes tarefas:

- Provisão rapidamente os ambientes Windows e Linux usando modelos e artefatos reutilizáveis.
- Integre facilmente seu pipeline de implantação dos Laboratórios de Teste/Desenvolvimento para provisionar ambientes sob demanda.
- Dimensione seu teste de carga provisionando vários agentes de teste e crie ambientes pré-provisionados para treinamento e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funcionalidades
O DevTest Labs fornece os seguintes recursos para desenvolvedores que trabalham com VMs:

- Crie VMs rapidamente seguindo menos de cinco passos simples.
- Escolha entre uma lista com curadoria de bases VM que são configuradas, aprovadas e autorizadas pelo líder da equipe ou pela TI central.
- Crie VMs a partir de imagens personalizadas pré-criadas que tenham todos os softwares e ferramentas já instalados. 
- Crie VMs a partir de fórmulas que são essencialmente imagens personalizadas combinadas com as compilações mais recentes do software que é instalado quando as VMs são criadas. 
- Instale artefatos que são extensões implantadas em VMs depois de provisionarem.
- Defina os horários de desligamento automático e de partida automática em VMs.
- Reivindique uma VM pré-criada sem passar pelo processo de criação.

O DevTest Labs fornece os seguintes recursos para desenvolvedores que trabalham com ambientes PaaS:

- Use o Resource Manager para criar rapidamente ambientes PaaS seguindo menos de três passos simples.
- Escolha entre uma lista com curadoria de modelos do Gerenciador de Recursos, que são configurados e autorizados pelo líder da equipe ou pela TI central.
- Gire um grupo de recursos vazio (sandbox) usando um modelo de Gerenciador de recursos para explorar o Azure no contexto de um laboratório.

O DevTest Labs também permite que a CENTRAL DE TI controle os desperdícios, otimize os custos com recursos e fique dentro dos orçamentos fazendo as seguintes tarefas:  

- Definindo os horários de desligamento automático e de partida automática em VMs.
- Definindo políticas sobre o número de VMs que os usuários podem criar.
- Definir políticas nos tamanhos das VMs e nas imagens da galeria que os usuários escolhem.
- Rastreando custos e estabelecendo metas em laboratórios.
- Ser notificado sobre os altos custos projetados para os laboratórios para que você possa tomar as ações necessárias.

O DevTest Labs oferece os seguintes benefícios na criação, configuração e gerenciamento de ambientes na nuvem.

## <a name="cost-control-and-governance"></a>Controle e governança de custos
O DevTest Labs facilita o controle de custos, permitindo que você faça as seguintes tarefas:

- [Defina políticas em seus laboratórios,](devtest-lab-get-started-with-lab-policies.md)como número de VMs por usuário ou por laboratório. 
- Crie [políticas para desligar automaticamente](devtest-lab-set-lab-policy.md) e iniciar VMs.
- Acompanhe os custos com vms e recursos paas espalhados dentro de laboratórios para ficar dentro do [seu orçamento.](devtest-lab-configure-cost-management.md)
- Fique dentro do contexto de seus laboratórios para que você não gire recursos fora deles.

## <a name="quickly-get-to-ready-to-test"></a>Chegue rapidamente ao estado Pronto para teste
O DevTest Labs permite que você crie ambientes pré-provisionados equipados com tudo o que sua equipe precisa para desenvolver e testar aplicativos. Basta [reivindicar os ambientes](devtest-lab-add-claimable-vm.md) onde a última boa compilação do seu aplicativo está instalada e começar a funcionar. Ou use recipientes para uma criação de ambiente ainda mais rápida e enxuta.

## <a name="create-once-use-everywhere"></a>Crie uma vez, use em qualquer lugar
Capture e compartilhe [modelos](devtest-lab-create-environment-from-arm.md) e [artefatos](add-artifact-repository.md) do ambiente PaaS dentro de sua equipe ou organização — tudo no controle de origem — para criar facilmente ambientes de desenvolvedor esnobados e testes.

## <a name="worry-free-self-service"></a>Autoatendimento sem preocupações
O DevTest Labs permite que seus desenvolvedores e testadores criem rapidamente e facilmente recursos de VMs e [PaaS](devtest-lab-create-environment-from-arm.md) do [IaaS](devtest-lab-add-vm.md) usando um conjunto de recursos pré-configurados.

## <a name="use-iaas-and-paas-resources"></a>Use recursos IaaS e PaaS 
Os desenvolvedores também podem girar os recursos do PaaS, como os clusters azure Service Fabric, o recurso Web Apps do Azure App Service e as fazendas SharePoint, usando modelos do Gerenciador de Recursos. Para começar no PaaS em laboratórios, use os modelos do [repositório](devtest-lab-configure-use-public-environments.md) de ambiente público ou [conecte o laboratório ao seu próprio repositório Git.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) Você também pode acompanhar os custos desses recursos para ficar dentro do seu orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integre-se à sua cadeia de ferramentas existente
Use plug-ins pré-fabricados ou a API para prover ambientes de desenvolvimento/teste diretamente da sua ferramenta de [integração contínua (CI)](devtest-lab-integrate-ci-cd-vsts.md)preferida, do ambiente de desenvolvimento integrado (IDE) ou do pipeline de liberação automatizada. Você também pode usar a ferramenta abrangente de linha de comando.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- Para saber mais sobre o DevTest Labs, consulte [os conceitos do DevTest Labs](devtest-lab-concepts.md).
- Para um passo a passo com instruções passo a passo, consulte [Tutorial: Configure um laboratório usando o Azure DevTest Labs](tutorial-create-custom-lab.md).