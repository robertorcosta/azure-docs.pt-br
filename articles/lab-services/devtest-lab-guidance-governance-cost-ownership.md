---
title: Gerenciar o custo e a propriedade no Azure DevTest Labs
description: Este artigo fornece informações que ajudam a otimizar o custo e a alinhar a propriedade em seu ambiente.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561670"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governança da infraestrutura do Azure DevTest Labs – Gerenciar o custo e a propriedade
O custo e a propriedade são preocupações primárias ao considerar a criação de ambientes de desenvolvimento e teste. Nesta seção, há informações que ajudam você a otimizar o custo e alinhar a propriedade em todo o ambiente.

## <a name="optimize-for-cost"></a>Otimizar para custos

### <a name="question"></a>Pergunta
Como posso otimizar para custos no meu ambiente de DevTest Labs?

### <a name="answer"></a>Resposta
Há vários recursos internos de DevTest Labs que ajudam você a otimizar o custo. Confira os [limites, gerenciamento de custos](devtest-lab-configure-cost-management.md) e os artigos [sobre políticas](devtest-lab-set-lab-policy.md) para limitar as atividades de seus usuários. 

À medida que utiliza o DevTest Labs para cargas de trabalho de desenvolvimento e teste, é possível considerar a utilização do [Benefício da Assinatura de Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) como parte de seu Enterprise Agreement. Como alternativa, se você for um cliente do tipo Pago Conforme o Uso, considere a [oferta de DevTest Pago Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0023p/).

Essa abordagem oferece diversas vantagens:

- Taxas especiais mais baixas para Desenvolvimento/Teste em máquinas virtuais, serviços de nuvem, HDInsight, Serviço de Aplicativo e Aplicativos Lógicos do Windows
- Taxas EA (Enterprise Agreement) excelentes de outros serviços do Azure
- Acesso a imagens exclusivas de Desenvolvimento/Teste na Galeria, incluindo Windows 8.1 e Windows 10
 
Somente os assinantes ativos do Visual Studio (assinaturas padrão, assinaturas de nuvem anuais e assinaturas de nuvem mensais) podem usar os recursos do Azure em execução em uma assinatura de Desenvolvimento/Teste do Enterprise. No entanto, os usuários finais podem acessar o aplicativo para fornecer comentários ou realizar testes de aceitação. O uso de recursos nessa assinatura é restrito ao desenvolvimento e teste de aplicativos e nenhuma garantia de tempo de ativação é oferecida.

Se decidir usar a oferta de DevTest, observe que este benefício é exclusivamente para desenvolvimento e teste de seus aplicativos. O uso dentro da assinatura não acarreta um SLA com suporte financeiro, exceto para uso do Azure DevOps e HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definir um acesso baseado em função em sua organização
### <a name="question"></a>Pergunta
Como faço para definir o controle de acesso baseado em função para meus ambientes de DevTest Labs e garantir que a TI possa ter controle enquanto os desenvolvedores/teste realizam seu trabalho? 

### <a name="answer"></a>Resposta
Há um padrão amplo, no entanto, o detalhe depende de sua organização.

A TI central deve controlar apenas o que é necessário e permitir que as equipes de projeto e aplicativo tenham o nível necessário de controle. Normalmente, isso significa que a TI central controla a assinatura e lida com as principais funções de TI, como configurações de rede. O conjunto de **proprietários** de uma assinatura deve ser pequeno. Esses proprietários podem nomear proprietários adicionais quando houver necessidade ou aplicar políticas no nível da assinatura, por exemplo, "Sem IP público".

Pode haver um subconjunto de usuários que exija acesso por meio de uma assinatura, como o suporte à Camada 1 ou à Camada 2. Nesse caso, recomendamos que você conceda a esses usuários o acesso de **colaborador** para que eles possam gerenciar os recursos sem fornecer acesso a usuários nem ajustar políticas.

O recurso DevTest Labs deve pertencer a proprietários próximos à equipe do projeto/aplicativo. Isso ocorre porque eles conhecem os requisitos em relação a máquinas e software necessários. Na maioria das organizações, o proprietário do recurso DevTest Labs geralmente é o líder do projeto/desenvolvimento. Esse proprietário pode gerenciar usuários e políticas no ambiente de laboratório e gerenciar todas as VMs no ambiente de DevTest Labs.

Os membros da equipe de projeto/aplicativo devem ser adicionados à função Usuários do DevTest Labs. Esses usuários podem criar máquinas virtuais (alinhadas com políticas do laboratório e do nível da assinatura). Eles também podem gerenciar suas próprias máquinas virtuais. Eles não podem gerenciar máquinas virtuais que pertencem a outros usuários.

Para saber mais, confira a documentação do [Andaime empresarial do Azure – governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Próximos passos
Confira [Política corporativa e conformidade](devtest-lab-guidance-governance-policy-compliance.md).
