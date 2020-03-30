---
title: Entregar uma prova de conceito - Azure DevTest Labs | Microsoft Docs
description: Aprenda a fornecer uma prova de conceito para que o Azure DevTest Labs possa ser incorporado com sucesso em um ambiente corporativo.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643280"
---
# <a name="deliver-a-proof-of-concept"></a>Entregue uma prova de conceito 

Um dos principais cenários do Azure DevTest Labs é permitir o desenvolvimento e testar ambientes na nuvem. Os exemplos incluem:

* Criando desktops de desenvolvedorna nuvem.
* Configuração de ambientes para testes.
* Permitindo o acesso a máquinas virtuais e outros recursos do Azure.
* Criação de uma área de sandbox para os desenvolvedores aprenderem e experimentarem.

O DevTest Labs também fornece os guardrails de políticas e controles de custos para capacitar a empresa a fornecer "Self-serve Azure" para desenvolvedores que aderem às políticas de segurança corporativa, normativa e conformidade. 

Cada empresa tem requisitos diferentes de como o Azure DevTest Labs pode ser incorporado com sucesso em seu ambiente. Este artigo descreve os passos mais comuns que as empresas precisam concluir para garantir uma prova de conceito bem-sucedida. Uma prova de conceito é o primeiro passo para uma implantação de ponta a ponta bem sucedida. 

## <a name="getting-started"></a>Introdução 

Para começar a entregar uma prova de conceito. É importante passar algum tempo para aprender sobre a Azure e DevTest Labs.  Aqui estão alguns recursos iniciais: 

* [Entendendo o portal Azure](https://azure.microsoft.com/features/azure-portal/)
* [Noções básicas do DevTest Labs](devtest-lab-overview.md)
* [DevTest Labs suportado cenários](devtest-lab-guidance-get-started.md)
* [Documentação corporativa do DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Introdução ao networking Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir com sucesso um piloto ou prova de conceito com o DevTest Labs, existem alguns pré-requisitos: 

* **Assinatura do Azure**: Muitas vezes as empresas têm um [Contrato Corporativo](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) existente que permite o acesso ao Azure, e eles podem usar uma assinatura existente ou nova para o DevTest Labs. Alternativamente, as empresas podem usar uma [assinatura do Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante o piloto (aproveitando os créditos gratuitos do Azure). Se nenhuma dessas opções estiver disponível, uma empresa pode criar e usar uma [conta gratuita do Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Se houver um Contrato Corporativo em vigor, usar uma [assinatura Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) é uma ótima opção para ter acesso aos sistemas operacionais do cliente Windows 10/Windows 8.1 e taxas com desconto para o desenvolvimento e testes de cargas de trabalho. 
* **Inquilino do Azure Active Directory**: Para permitir o gerenciamento de usuários (por exemplo, adicionar usuários ou adicionar proprietários de laboratório), esses usuários devem fazer parte do inquilino do [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usado na assinatura do Azure para o piloto. Muitas vezes, as empresas configurarão a [identidade híbrida](../active-directory/hybrid/whatis-hybrid-identity.md) para permitir que os usuários usem sua identidade no local na nuvem, mas isso não é necessário para o piloto do DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Escopo do piloto 

É importante planejar um piloto antes de começar a implementação. Saber com antecedência que os recursos não ficarão por perto indefinidamente estabelece expectativas adequadas para os usuários do piloto. 

> [!IMPORTANT]
> Não podemos enfatizar o suficiente a importância de escovar o piloto e criar expectativas na frente.

Responda a essas perguntas-chave antes de iniciar o piloto: 

* O que você quer aprender, e como é o sucesso para o piloto? 
* Quais cargas de trabalho ou cenários serão cobertos pelo piloto? É importante definir apenas um pequeno conjunto para garantir que o piloto possa ser escopo e completado rapidamente. 
* Que recursos devem estar disponíveis no laboratório? Por exemplo: imagens personalizadas, imagens de mercado, políticas, topologia de rede. 
* Quem são os usuários e equipes que estarão envolvidos no piloto para verificar a experiência?  
* Qual é a duração do piloto? Escolha um prazo que se alinhe bem ao escopo planejado, como duas semanas ou um mês. 
* Depois que o piloto estiver completo, o que acontecerá com os recursos alocados que foram usados durante o piloto? Você planeja descartar os recursos do piloto? Você pode pensar:
   
   "Se pudermos planejar jogar fora as máquinas virtuais e laboratórios no final do piloto, então podemos configurar uma única assinatura para o piloto e fazer todo o nosso trabalho lá enquanto resolvemos as questões de implantação de escala em paralelo." 

Há uma tendência geral de tornar o piloto "perfeito" para que represente de forma idêntica qual será o estado final após o serviço ser implantado na empresa. É uma falsa suposição. Quanto mais perto você chega de "perfeito", mais você tem que completar *antes* de começar no piloto. O objetivo do piloto é tomar as decisões certas sobre escalar e implementar o serviço final. 

O foco do piloto deve ser escolher as cargas de trabalho e dependências mínimas necessárias para responder à questão de se o Azure DevTest Labs é o serviço certo para sua empresa. Recomendamos que você escolha a carga de trabalho mais simples com menos dependências para ajudar a garantir um sucesso rápido e limpo. Se isso não for possível, escolha uma carga de trabalho mais representativa que exponha complexidades potenciais para que o sucesso na fase piloto possa ser replicado na fase de escoada. 

O exemplo a seguir demonstra uma prova de conceito bem escopo. 

## <a name="example-proof-of-concept-plan"></a>Exemplo: plano de prova de conceito 

Esta seção mostra uma amostra para usar para escopo de uma prova de conceito do piloto para DevTest Labs. 

> [!TIP]
> Para minimizar a possibilidade de configurar seu projeto para falha, recomendamos que você não pule o exemplo descrito nesta seção. 

### <a name="overview"></a>Visão geral 

Estamos planejando desenvolver um novo ambiente no Azure baseado no DevTest Labs para que os fornecedores usem como um ambiente isolado da rede corporativa. Para determinar se a solução atenderá aos requisitos, desenvolveremos uma prova de conceito para validar a solução completa. Incluímos vários fornecedores para testar e verificar a experiência. 

### <a name="outcomes"></a>Resultados 

Ao construir uma prova de conceito, focamos primeiro nos resultados (o que estamos tentando alcançar). Ao final da prova de conceito, esperamos: 

* Uma solução completa para que os fornecedores usem contas de hóspedes no Azure Active Directory (Azure AD) para acessar um ambiente isolado no Azure. O meio ambiente tem os recursos necessários para que sejam produtivos. 
* Quaisquer questões potenciais de bloqueio que afetam o uso e a adoção em escala mais ampla são enumeradas e compreendidas.
* Os indivíduos envolvidos no desenvolvimento da prova de conceito têm uma boa compreensão de todos os códigos. Eles também entendem as garantias envolvidas e estão confiantes em uma adoção mais ampla.

### <a name="open-questions-and-prerequisites"></a>Perguntas abertas e pré-requisitos 

* Temos uma assinatura criada que podemos usar para este projeto? 
* Temos um inquilino azure AD e um administrador global do Azure AD identificados que podem fornecer ajuda e orientação para perguntas relacionadas ao Azure AD? 
* Temos um lugar para colaborar para os indivíduos que trabalham no projeto? 

   * Código-fonte e scripts (como Azure Repos) 
   * Documentos (como Microsoft Teams ou SharePoint)  
   * Conversas (como Microsoft Teams) 
   * Itens de trabalho (como azure Boards) 
* Quais são os recursos necessários para os fornecedores? Isso inclui aplicativos disponíveis na rede, tanto localmente nas máquinas virtuais quanto em outros servidores necessários. 
* As máquinas virtuais serão unidas a um domínio no Azure? Se assim for, este será o Azure Active Directory Domain Services (Azure AD DS) ou outra coisa? 
* Identificamos a equipe ou os fornecedores que serão alvo da prova de conceito? Quem serão os clientes do meio ambiente?
* Qual região do Azure usaremos para a prova de conceito? 
* Temos uma lista de serviços que os fornecedores podem usar via DevTest Labs além de IaaS (VMs)? 
* Como planejamos treinar fornecedores/usuários sobre o uso do laboratório? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componentes da solução de prova de conceito 

Esperamos que a solução tenha os seguintes componentes: 

* Várias equipes de fornecedores usarão um conjunto de laboratórios no Azure.
* Os laboratórios estão conectados a uma infra-estrutura de rede que suporta os requisitos.
* Os fornecedores têm acesso aos laboratórios via Azure AD e atribuições de função.
* Os fornecedores têm uma maneira de se conectar com sucesso aos seus recursos. Especificamente, uma VPN site-to-site permite acessar máquinas virtuais diretamente sem endereços IP públicos.
* Um conjunto de artefatos cobre o software necessário que os fornecedores precisam nas máquinas virtuais.

## <a name="additional-planning-and-design-decisions"></a>Decisões adicionais de planejamento e design 

Antes de lançar uma solução completa do DevTest Labs, você tem que tomar algumas decisões importantes de planejamento e design. A experiência de trabalhar em uma prova de conceito pode ajudá-lo a tomar essas decisões. Outras considerações incluem: 

* **Topologia de assinatura**: Os requisitos de nível corporativo para recursos no Azure podem se estender além das [cotas disponíveis dentro de uma única assinatura](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Isso requer várias assinaturas do Azure e/ou solicitações de serviços para aumentar os limites iniciais de assinatura. É importante decidir antecipadamente como distribuir recursos entre assinaturas. Um recurso valioso é o guia de decisão de [assinatura](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) porque é difícil mover recursos para outra assinatura mais tarde. Por exemplo, um laboratório não pode ser movido para outra assinatura depois de criado.  
* **Topologia de rede**: A [infra-estrutura de rede padrão](../app-service/networking-features.md) que o DevTest Labs cria automaticamente pode não ser suficiente para atender aos requisitos e restrições para os usuários corporativos. É comum ver redes virtuais conectadas ao [Azure ExpressRoute,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para conectividade entre assinaturas e até mesmo [roteamento forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) para garantir apenas a conectividade no local. O DevTest Labs permite que as redes virtuais existentes sejam conectadas ao laboratório para permitir seu uso quando você está criando novas máquinas virtuais no laboratório. 
* **Acesso remoto de máquinas virtuais**: Existem muitas opções para acessar remotamente as máquinas virtuais localizadas no DevTest Labs. O mais fácil é usar IPs públicos ou IPs públicos compartilhados. Estas são [configurações disponíveis no laboratório.](devtest-lab-shared-ip.md) Se essas opções não forem suficientes, usar um gateway de acesso remoto também é uma opção. Essa opção é mostrada na arquitetura de referência corporativa do [DevTest Labs](devtest-lab-reference-architecture.md) e descrita ainda na documentação do [gateway de desktop remoto do DevTest Labs](configure-lab-remote-desktop-gateway.md). As empresas também podem usar o ExpressRoute ou uma VPN site-to-site para conectar seus laboratórios à sua rede local. Essa opção permite conexões diretas de desktop remoto ou SSH para as máquinas virtuais com base em seu endereço IP privado sem exposição à internet. 
* **Permissões de manuseio**: As duas permissões-chave comumente usadas em DevTest Labs são [Proprietário e Usuário de Laboratório](devtest-lab-add-devtest-user.md). É importante decidir antes de implantar os DevTest Labs amplamente quem será confiado a cada nível de acesso no laboratório. Um modelo comum é o proprietário do orçamento (líder da equipe, por exemplo) como o proprietário do laboratório e os membros da equipe como usuários de laboratório. Esse modelo permite que a pessoa (líder da equipe) responsável pelo orçamento ajuste as configurações da política e mantenha a equipe dentro do orçamento.  

## <a name="completing-the-proof-of-concept"></a>Completando a prova de conceito 

Depois que os aprendizados esperados foram cobertos, é hora de completar o piloto. Este é o momento de coletar feedback dos usuários, determinar se o piloto foi bem sucedido e decidir se a organização avançará em um lançamento em escala do DevTest Labs na empresa. Também é um ótimo momento para considerar a automatização da implantação de DevTest Labs e recursos associados para garantir a consistência durante toda a distribuição de escala. 

## <a name="next-steps"></a>Próximas etapas 

* [Documentação corporativa do DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Arquitetura de referência para uma empresa](devtest-lab-reference-architecture.md)
* [Dimensionamento da implantação do DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
