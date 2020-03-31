---
title: Crie políticas de segurança personalizadas no Azure Security Center | Microsoft Docs
description: Definições de políticas personalizadas do Azure monitoradas pelo Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258253"
---
# <a name="using-custom-security-policies"></a>Usando políticas de segurança personalizadas

Para ajudar a proteger seus sistemas e ambiente, o Azure Security Center gera recomendações de segurança. Essas recomendações são baseadas nas melhores práticas do setor, que são incorporadas à política de segurança padrão e genérica fornecida a todos os clientes. Eles também podem vir do conhecimento do Security Center sobre a indústria e as normas regulamentares.

Com este recurso, você pode adicionar suas próprias iniciativas *personalizadas.* Em seguida, você receberá recomendações se seu ambiente não seguir as políticas criadas. Quaisquer iniciativas personalizadas criadas aparecerão ao lado das iniciativas incorporadas no painel de conformidade normativa descrito no tutorial [Melhore sua conformidade normativa](security-center-compliance-dashboard.md).

Como discutido [aqui](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) na documentação da Política do Azure, quando você especifica um local para sua iniciativa personalizada, deve ser um grupo de gerenciamento ou uma assinatura. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua assinatura 

1. Na barra lateral do Security Center, abra a página **de política de segurança.**

1. Selecione uma assinatura ou grupo de gerenciamento ao qual você gostaria de adicionar uma iniciativa personalizada.

    [![Selecionando uma assinatura para a qual você criará sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Você deve adicionar padrões personalizados no nível de assinatura (ou superior) para que eles sejam avaliados e exibidos no Security Center. 
    >
    > Quando você adiciona um padrão personalizado, ele atribui uma *iniciativa* a esse escopo. Por isso, recomendamos que você selecione o escopo mais amplo necessário para essa atribuição.

1. Na página de política de segurança, em Suas iniciativas personalizadas, clique **em Adicionar uma iniciativa personalizada**.

    [![Clique em **Adicionar uma iniciativa personalizada**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A seguinte página aparece:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página Adicionar iniciativas personalizadas, revise a lista de políticas personalizadas já criadas em sua organização. Se você ver um que deseja atribuir à sua assinatura, clique **em Adicionar**. Se não houver uma iniciativa na lista que atenda às suas necessidades, pule este passo.

1. Para criar uma nova iniciativa personalizada:

    1. Clique **em Criar novo**.
    1. Digite a localização e o nome da definição.
    1. Selecione as políticas para incluir e clique **em Adicionar**.
    1. Digite todos os parâmetros desejados.
    1. Clique em **Salvar**.
    1. Na página Adicionar iniciativas personalizadas, clique em atualizar e sua nova iniciativa será mostrada como disponível.
    1. Clique **em Adicionar** e atribua-o à sua assinatura.

    > [!NOTE]
    > Criar novas iniciativas requer credenciais do proprietário da assinatura. Para obter mais informações sobre as funções do Azure, consulte [Permissões no Azure Security Center](security-center-permissions.md).

    Sua nova iniciativa entra em vigor e você pode ver o impacto nas duas maneiras a seguir:

    * Na barra lateral do Security Center, sob conformidade de & de políticas, selecione **conformidade normativa**. O painel de conformidade é aberto para mostrar sua nova iniciativa personalizada ao lado das iniciativas incorporadas.
    
    * Você começará a receber recomendações se seu ambiente não seguir as políticas que você definiu.

1. Para ver as recomendações resultantes de sua política, clique em **Recomendações** da barra lateral para abrir a página de recomendações. As recomendações aparecerão com um rótulo "Personalizado" e estarão disponíveis em aproximadamente uma hora.

    [![Recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar políticas de segurança personalizadas. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [A visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança incorporadas](security-center-policy-definitions.md)