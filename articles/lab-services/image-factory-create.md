---
title: Criar uma fábrica de imagens no Azure DevTest Labs | Microsoft Docs
description: Este artigo mostra como configurar uma fábrica de imagens personalizada usando scripts de exemplo disponíveis no repositório Git (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759441"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Crie uma fábrica de imagens personalizada no Azure DevTest Labs
Este artigo mostra como configurar uma fábrica de imagens personalizada usando scripts de exemplo disponíveis no [repositório Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagens?
Uma fábrica de imagens é uma solução de configuração como código que constrói e distribui imagens automaticamente regularmente com todas as configurações desejadas. As imagens na fábrica de imagens estão sempre atualizadas, e a manutenção contínua é quase zero quando todo o processo é automatizado. E, como todas as configurações necessárias já estão na imagem, ela economiza o tempo de configurar manualmente o sistema depois que uma VM foi criada com o sistema operacional base.

O acelerador significativo para obter um desktop de desenvolvedor para um estado pronto no DevTest Labs está usando imagens personalizadas. A desvantagem das imagens personalizadas é que há algo extra para manter no laboratório. Por exemplo, as versões de teste dos produtos expiram ao longo do tempo (ou) atualizações de segurança recém-lançadas não são aplicadas, o que nos força a atualizar a imagem personalizada periodicamente. Com uma fábrica de imagens, você tem uma definição da imagem verificada para o controle de código fonte e tem um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, eliminando custos adicionais de manutenção contínua. Com esta solução, você pode criar automaticamente imagens personalizadas, distribuí-las para outros Laboratórios DevTest e retirar as imagens antigas. No vídeo a seguir, você aprende sobre a fábrica de imagens e como ela é implementada com o DevTest Labs.  Todos os scripts do Azure Powershell estão [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)disponíveis gratuitamente e localizados aqui: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Visão de alto nível da solução
A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, eliminando custos adicionais de manutenção contínua. Com esta solução, você pode criar automaticamente imagens personalizadas e distribuí-las para outros Laboratórios DevTest. Você usa o Azure DevOps (anteriormente Visual Studio Team Services) como o motor de orquestração para automatizar todas as operações no DevTest Labs.

![Visão de alto nível da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há uma [extensão VSTS para DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite executar essas etapas individuais:

- Criar imagem personalizada
- Criar VM
- Excluir VM
- Criar ambiente
- Excluir ambiente
- Ambiente de povoar

O uso da extensão DevTest Labs é uma maneira fácil de começar com a criação automática de imagens personalizadas no DevTest Labs.

Há uma implementação alternativa usando o script PowerShell para um cenário mais complexo. Usando o PowerShell, você pode automatizar totalmente uma fábrica de imagens baseada em DevTest Labs que pode ser usada em sua cadeia de ferramentas de Integração Contínua e Entrega Contínua (CI/CD). Os princípios seguidos nesta solução alternativa são:

- Atualizações comuns não devem exigir alterações na fábrica de imagens. (por exemplo, adicionar um novo tipo de imagem personalizada, retirar automaticamente imagens antigas, adicionar um novo 'endpoint' DevTest Labs para receber imagens personalizadas, e assim por diante.)
- As alterações comuns são apoiadas pelo controle de código-fonte (infra-estrutura como código)
- O DevTest Labs que recebe imagens personalizadas pode não estar na mesma assinatura do Azure (assinaturas de laboratórios abrangem)
- Os scripts PowerShell devem ser reutilizáveis para que possamos girar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Próximas etapas
Passe para o próximo artigo nesta seção: [Execute uma fábrica de imagens do Azure DevOps](image-factory-set-up-devops-lab.md)
