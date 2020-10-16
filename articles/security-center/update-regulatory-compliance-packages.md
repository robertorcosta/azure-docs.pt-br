---
title: Usando o painel de conformidade regulatória na central de segurança do Azure
description: Saiba como adicionar e remover padrões regulatórios do painel de conformidade regulatória na central de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: 5e32aa8491e18a66a3e8b90f065e6b785d1e3bd9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106256"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Como personalizar o conjunto de padrões em seu painel de conformidade regulatória

A Central de Segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e parâmetros de comparação do setor. O **painel de conformidade regulatória** fornece informações sobre sua postura de conformidade com base em como você está atendendo a requisitos e controles de conformidade específicos.


## <a name="overview-of-compliance-packages"></a>Visão geral de pacotes de conformidade

Os padrões do setor, os padrões regulatórios e os parâmetros de comparação são representados na Central de Segurança como *pacotes de conformidade*.  Cada pacote é uma iniciativa definida em Azure Policy. Para ver os dados de conformidade mapeados como avaliações no seu painel, adicione um pacote de conformidade ao seu grupo de gerenciamento ou assinatura de dentro da página **Política de segurança**. (Saiba mais sobre o Azure Policy e iniciativas em [Como trabalhar com políticas de segurança](tutorial-security-policy.md).)

Quando você tiver integrado um padrão ou um parâmetro de comparação ao escopo selecionado, ele atribuirá a iniciativa ao escopo e o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Você também pode baixar relatórios de resumo para qualquer um dos padrões que foram integrados.

A Microsoft também acompanha os padrões regulatórios e aprimora automaticamente sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lança novo conteúdo para a iniciativa (novas políticas que são mapeadas para mais controles no padrão), o conteúdo adicional é exibido automaticamente em seu painel.

> [!TIP]
> Um padrão que melhora ao longo do tempo à medida que a Microsoft lança novo conteúdo é o **Azure CIS 1.1.0 (novo)** (mais formalmente, o [CIS Microsoft Azure Foundations Benchmark versão 1.1.0](https://www.cisecurity.org/benchmark/azure/)). Você precisará adicioná-lo ao seu painel junto com "Azure CIS 1.1.0", a representação do Azure CIS que está configurada por padrão em todos os ambientes da Central de Segurança. Esse pacote depende de um conjunto estático de regras. O pacote mais recente inclui mais políticas e será atualizado automaticamente com o passar do tempo. Atualize para o novo pacote dinâmico, conforme descrito abaixo.


## <a name="available-packages"></a>Pacotes disponíveis

Você pode adicionar padrões como NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official e UK NHS, PBMM Federal do Canadá e Azure CIS 1.1.0 (novo) – uma representação mais completa do Azure CIS 1.1.0. 

Além disso, você pode adicionar o **Azure Security Benchmark**, as diretrizes específicas do Azure criadas pela Microsoft para melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. ([Saiba mais sobre o Parâmetro de Segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Outros padrões se tornarão compatíveis com o painel à medida que forem disponibilizados. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Adicionar um padrão normativo ao seu painel

As etapas a seguir explicam como adicionar um pacote para monitorar sua conformidade com um dos padrões regulatórios com suporte.

> [!NOTE]
> Somente os usuários que são proprietários ou colaboradores de política têm as permissões necessárias para adicionar padrões de conformidade. 

1. Na barra lateral da Central de Segurança, selecione **Conformidade regulatória** para abrir o painel de conformidade regulatória. Aqui você pode ver os padrões de conformidade atualmente atribuídos às assinaturas atualmente selecionadas.   

1. Na parte superior da página, selecione **Gerenciar políticas de conformidade**. A página Gerenciamento de políticas é exibida.

1. Selecione a assinatura ou grupo de gerenciamento para o qual você deseja gerenciar a postura de conformidade regulatória. 

    > [!TIP]
    > É recomendável selecionar o escopo mais alto para o qual o padrão é aplicável para que os dados de conformidade sejam agregados e acompanhados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique em **Adicionar mais padrões**. 

1. Na página **Adicionar padrões de conformidade regulatória**, você pode procurar pacotes para qualquer um dos padrões disponíveis. Alguns dos padrões disponíveis são:

    - **Azure Security Benchmark**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO e UK NHS**
    - **PBMM do Canadá**
    
    ![Como adicionar pacotes regulatórios ao painel de conformidade regulatória da Central de Segurança do Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selecione **Adicionar** e insira todos os detalhes necessários para a iniciativa específica, como escopo, parâmetros e correção.

1. Na barra lateral da Central de Segurança, selecione **Conformidade regulatória** novamente para voltar para o painel de conformidade regulatória.
    * O novo padrão é exibido na sua lista de padrões de regulamentação e do setor. 
    * Se você tiver adicionado o **Azure CIS 1.1.0 (novo)** , a exibição original *estática* da sua conformidade do Azure CIS 1.1.0 também permanecerá junto com ele. Ele poderá ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    [![Painel de conformidade regulatória mostrando o Azure CIS antigo e novo](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Removendo um padrão do seu painel

Se qualquer um dos padrões regulatórios fornecidos não for relevante para sua organização, será um processo simples simplesmente removê-los da interface do usuário. Isso permite personalizar ainda mais o painel de conformidade regulatória e se concentrar apenas nos padrões que se aplicam a você.

Para remover um padrão:

1. No menu da central de segurança, selecione **política de segurança**.

1. Selecione a assinatura relevante da qual você deseja remover um padrão.

    > [!NOTE]
    > Você pode remover um padrão de uma assinatura, mas não de um grupo de gerenciamento. 

    A página política de segurança é aberta. Para a assinatura selecionada, ela mostra a política padrão, os padrões do setor e regulamentares e todas as iniciativas personalizadas que você criou.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Removendo um padrão normativo do painel de conformidade regulatória na central de segurança do Azure":::

1. Para o padrão que você deseja remover, selecione **desabilitar**. Uma janela de confirmação é exibida.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Removendo um padrão normativo do painel de conformidade regulatória na central de segurança do Azure":::

1. Selecione **Sim** na barra superior. O padrão será removido. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a **adicionar pacotes de conformidade** para monitorar sua conformidade com padrões adicionais. 

Para obter outros materiais relacionados, confira os seguintes artigos: 

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Painel de conformidade regulatória da Central de Segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)