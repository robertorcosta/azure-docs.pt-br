---
title: Como atualizar para o monitoramento de conformidade regulatória dinâmica no painel de conformidade regulatória da Central de Segurança do Azure | Microsoft Docs
description: Como atualizar os pacotes da conformidade regulatória
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6ba0be3a6fba35e413270dd6770f5d3f47586b5e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873332"
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


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Como adicionar um padrão regulatório ao seu painel

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

1. Na barra lateral da Central de Segurança, selecione **Conformidade regulatória** novamente para voltar para o painel de conformidade regulatória.
    * O novo padrão é exibido na sua lista de padrões de regulamentação e do setor. 
    * Se você tiver adicionado o **Azure CIS 1.1.0 (novo)** , a exibição original *estática* da sua conformidade do Azure CIS 1.1.0 também permanecerá junto com ele. Ele poderá ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    [![Painel de conformidade regulatória mostrando o Azure CIS antigo e novo](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a **adicionar pacotes de conformidade** para monitorar sua conformidade com padrões adicionais. 

Para obter outros materiais relacionados, confira os seguintes artigos: 

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Painel de conformidade regulatória da Central de Segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)