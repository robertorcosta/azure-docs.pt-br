---
title: Integração do Azure DevTest Labs e DevOps | Microsoft Docs
description: Aprenda a usar laboratórios do Azure DevTest Labs em um pipeline de integração contínua (CI)/ entrega contínua (CD) em um ambiente corporativo.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078918"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integração dos Laboratórios Azure DevTest e DevOps do Azure
DevOps é uma metodologia de desenvolvimento de software que integra o desenvolvimento de software (Dev) com operações (Ops) para um sistema. Esse sistema pode fornecer novos recursos, atualizações e correções em alinhamento com as metas de negócios. Essa metodologia abrange desde a concepção de novos recursos com base em metas, padrões de uso e feedback do cliente; para corrigir, recuperar e endurecer o sistema quando ocorrem problemas. Um componente facilmente identificado dessa metodologia é o pipeline de integração contínua (CI)/ entrega contínua (CD). Um pipeline de CI/CD leva informações, códigos e recursos de um compromisso através de uma série de etapas que incluem construção, teste e implantação, para produzir o sistema. Este artigo se concentra em diferentes maneiras de usar efetivamente laboratórios dentro de um pipeline em um ambiente corporativo. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Benefícios do uso de laboratórios no fluxo de trabalho DevOps 

### <a name="focused-access"></a>Acesso focado 
O uso de um laboratório como componente permite que um ecossistema específico se associe a um grupo limitado de pessoas. Normalmente, uma equipe ou grupo que está trabalhando em uma área comum ou um recurso específico terá um laboratório atribuído a eles.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicação de infra-estrutura na nuvem 
Um desenvolvedor pode configurar rapidamente um ecossistema de desenvolvimento que inclui uma caixa de desenvolvimento com código fonte e ferramentas. O desenvolvedor também pode criar um ambiente quase idêntico à configuração de produção. Ajuda no desenvolvimento mais rápido do loop interno. 

### <a name="pre-production"></a>Pré-produção 
Ter um laboratório no pipeline de CI/CD torna mais fácil ter vários ambientes ou máquinas de pré-produção diferentes funcionando ao mesmo tempo para testes assíncronos. Diferentes infra-estruturas de suporte, como agentes de construção, podem ser implantadas e gerenciadas dentro de um laboratório. 

## <a name="devops-with-devtest-labs"></a>DevOps com Laboratórios DevTest 

### <a name="development--operation"></a>Desenvolvimento / Operação 
Um laboratório deve ser focado em uma equipe que está trabalhando em uma área de recursos. Esse foco comum permite o compartilhamento de recursos específicos da área, como ferramentas, scripts ou modelos de Gerenciador de Recursos. Permite mudanças mais rápidas enquanto limita os efeitos negativos a um grupo menor. Esses recursos compartilhados permitem que o desenvolvedor crie VMs de desenvolvimento com todo o código, ferramentas e configuração necessários. Eles podem ser criados dinamicamente ou ter um sistema que cria imagens base com as personalizações. Não só o desenvolvedor pode criar VMs, mas também pode criar ambientes DevTest Labs com base nos modelos necessários para criar os recursos apropriados do Azure no laboratório. Qualquer mudança ou trabalho destrutivo pode ser feito contra o ambiente de laboratório sem afetar ninguém. Considere o cenário em que o produto é um sistema autônomo instalado na máquina do cliente. Neste cenário, o DevTest Labs melhorou a criação de VM, que inclui a instalação de software adicional usando artefatos e configurações de clientes pré-construção para testes mais rápidos de loop interno do código. 
  
## <a name="cicd-pipeline"></a>Gasoduto CI/CD 
O pipeline CI/CD é um dos componentes críticos no DevOps que moveo código a partir de uma solicitação de atração do desenvolvedor, integra-o ao código existente e o implanta no ecossistema de produção. Todos os recursos não precisam estar dentro de um laboratório. Por exemplo, um hospedeiro Jenkins poderia ser criado fora do laboratório como um recurso mais persistente. Aqui estão alguns exemplos específicos de integração de laboratórios no pipeline. 

### <a name="build"></a>Build 
O pipeline de construção está focado na criação de um pacote de componentes que serão testados juntos para serem entregues ao pipeline de liberação. Os laboratórios podem fazer parte do pipeline de construção como o local para agentes de construção e outros recursos de suporte. Ter a capacidade de construir dinamicamente a infra-estrutura permite um maior controle. Com a capacidade de ter vários ambientes em um laboratório, cada compilação pode ser executada de forma assíncrona enquanto usa o ID de compilação como parte das informações do ambiente para identificar exclusivamente os recursos para a compilação específica.   

Para os agentes de construção, a capacidade do laboratório de restringir o acesso aumenta a segurança e reduz a possibilidade de corrupção acidental.  

### <a name="test"></a>Teste 
O DevTest Labs permite que um pipeline de CI/CD automatize a criação de Recursos Azure (VMs, ambientes) que podem ser usados para testes automatizados e manuais. As VMs seriam criadas usando artefatos ou fórmulas que usam informações do processo de construção para criar as diferentes configurações personalizadas necessárias para testes.   

### <a name="release"></a>Versão 
O DevTest Labs é comumente usado para verificação na seção de versão antes que o código seja implantado. É semelhante aos testes na seção Build. Os recursos de produção não devem ser implantados dentro do DevTest Labs. 

### <a name="customization"></a>Personalização 
No Azure DevOps, existem tarefas existentes que permitem a manipulação de VMs e ambientes dentro de laboratórios específicos. Embora os Serviços Azure DevOps sejam uma maneira de gerenciar o pipeline de CI/CD, você pode integrar o laboratório em qualquer sistema que suporte a capacidade de chamar APIs REST, executar scripts PowerShell ou usar o Azure CLI. 

Enquanto alguns gerentes de pipeline de CI/CD possuem plugins de código aberto existentes que podem gerenciar recursos dentro do Azure e do DevTest Labs. Você pode precisar usar alguns scripts personalizados para atender às necessidades específicas do pipeline.  Com isso em mente, ao executar uma tarefa, um diretor de serviço é usado com o papel apropriado para obter acesso ao laboratório. O diretor do serviço geralmente precisa do acesso ao laboratório. Para obter mais informações, consulte [Integrar Azure DevTests Labs em seu azure DevOps de integração contínua e pipeline de entrega](devtest-lab-integrate-ci-cd-vsts.md). 
 