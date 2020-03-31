---
title: Desenvolvimento colaborativo distribuído dos recursos do Azure DevTest Labs
description: Fornece práticas recomendadas para a criação de um ambiente de desenvolvimento distribuído e colaborativo para desenvolver os recursos do DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170116"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Melhores práticas para desenvolvimento distribuído e colaborativo dos recursos do Azure DevTest Labs
O desenvolvimento colaborativo distribuído permite que diferentes equipes ou pessoas desenvolvam e mantenham uma base de código. Para ter sucesso, o processo de desenvolvimento depende da capacidade de criar, compartilhar e integrar informações. Este princípio de desenvolvimento chave pode ser usado dentro do Azure DevTest Labs. Existem vários tipos de recursos dentro de um laboratório que são comumente distribuídos entre diferentes laboratórios dentro de uma empresa. Os diferentes tipos de recursos estão focados em duas áreas:

- Recursos que são armazenados internamente dentro do laboratório (baseado em laboratório)
- Recursos armazenados em [repositórios externos que estão conectados ao laboratório](devtest-lab-add-artifact-repo.md) (baseado em código baseado em repositório). 

Este documento descreve algumas práticas recomendadas que permitem a colaboração e distribuição entre várias equipes, garantindo personalização e qualidade em todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens personalizadas de máquinas virtuais
Você pode ter uma fonte comum de imagens personalizadas que são implantadas em laboratórios todas as noites. Para obter informações detalhadas, consulte [A fábrica image](image-factory-create.md).    

### <a name="formulas"></a>Fórmulas
[As fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não têm um mecanismo de distribuição. Os membros do laboratório fazem todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos baseados em repositório de código
Existem duas características diferentes que são baseadas em repositórios de código, artefatos e ambientes. Este artigo analisa os recursos e como configurar mais eficazmente repositórios e fluxo de trabalho para permitir a capacidade de personalizar os artefatos e ambientes disponíveis no nível da organização ou da equipe.  Este fluxo de trabalho é baseado na estratégia padrão [de ramificação de ramificação do código-fonte](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Principais conceitos
As informações de origem dos artefatos incluem metadados, scripts. As informações de origem para ambientes incluem modelos de metadados e gerenciadores de recursos com quaisquer arquivos de suporte, como scripts PowerShell, scripts DSC, arquivos Zip e assim por diante.  

### <a name="repository-structure"></a>Estrutura do repositório  
A configuração mais comum para o controle de código-fonte (SCC) é configurar uma estrutura de vários níveis para armazenar arquivos de código (modelos, metadados e scripts do Resource Manager) que são usados nos laboratórios. Especificamente, crie diferentes repositórios para armazenar recursos que são gerenciados pelos diferentes níveis do negócio:   

- Recursos em toda a empresa.
- Recursos de unidade de negócios/divisão
- Recursos específicos da equipe.

Cada um desses níveis se liga a um repositório diferente onde o ramo mestre é necessário para ser da qualidade de produção. As [filiais](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) em cada repositório seriam para o desenvolvimento desses recursos específicos (artefatos ou modelos). Essa estrutura se alinha bem com o DevTest Labs, pois você pode facilmente conectar vários repositórios e vários ramos ao mesmo tempo aos laboratórios da organização. O nome do repositório está incluído na interface do usuário (UI) para evitar confusão quando há nomes, descrição e editor idênticos.
     
O diagrama a seguir mostra dois repositórios: um repositório da empresa que é mantido pela Divisão de TI, e um repositório de divisão mantido pela divisão R&D.

![Um ambiente de desenvolvimento distributivo e colaborativo de amostras](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Esta estrutura em camadas permite o desenvolvimento, mantendo um nível mais alto de qualidade no ramo principal, enquanto ter vários repositórios conectados a um laboratório permite maior flexibilidade.

## <a name="next-steps"></a>Próximas etapas    
Veja os artigos a seguir:

- Adicione um repositório a um laboratório usando o [portal Azure](devtest-lab-add-artifact-repo.md) ou através [do modelo de gerenciamento de recursos do Azure](add-artifact-repository.md)
- [Artefatos do DevTest Labs](devtest-lab-artifact-author.md)
- [Ambientes DevTest Labs](devtest-lab-create-environment-from-arm.md).
