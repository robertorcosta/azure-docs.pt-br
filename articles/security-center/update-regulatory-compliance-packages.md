---
title: Como atualizar para o monitoramento de conformidade regulatória dinâmica no painel de conformidade regulatória da central de segurança do Azure | Microsoft Docs
description: Atualizando seus pacotes de conformidade regulatória
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
ms.openlocfilehash: 7a017f9485ec847c0a2c045e4c511b6a68db0cb2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889941"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizando o conjunto de padrões em seu painel de conformidade regulatória

A central de segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e benchmarks do setor. O **painel de conformidade regulatória** fornece informações sobre sua postura de conformidade com base em como você está atendendo a requisitos e controles de conformidade específicos.


## <a name="overview-of-compliance-packages"></a>Visão geral dos pacotes de conformidade

Os padrões do setor, os padrões regulatórios e os parâmetros de comparação são representados na central de segurança como *pacotes de conformidade*.  Cada pacote é uma iniciativa definida em Azure Policy. Para ver os dados de conformidade mapeados como avaliações no seu painel, adicione um pacote de conformidade ao seu grupo de gerenciamento ou assinatura de dentro da página de **política de segurança** . (Saiba mais sobre Azure Policy e iniciativas em [trabalhando com políticas de segurança](tutorial-security-policy.md).)

Quando você tiver integrado um padrão ou um parâmetro de comparação ao escopo selecionado, o padrão aparecerá em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Você também pode baixar relatórios de resumo para qualquer um dos padrões que foram integrados.

A Microsoft também acompanha os padrões regulatórios e aprimora automaticamente sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lança novo conteúdo para a iniciativa (novas políticas que são mapeadas para mais controles no padrão), o conteúdo adicional é exibido automaticamente em seu painel.

> [!TIP]
> Um padrão que melhora ao longo do tempo à medida que a Microsoft lança um novo conteúdo é o **Azure cis 1.1.0 (novo)** (mais formalmente, o [cis Microsoft Azure Foundations benchmark versão 1.1.0](https://www.cisecurity.org/benchmark/azure/)). Você precisará adicioná-lo ao seu painel junto com "Azure CIS 1.1.0", a representação do CIS do Azure que está configurada por padrão em todos os ambientes da central de segurança. Esse pacote depende de um conjunto estático de regras. O pacote mais recente inclui mais políticas e será atualizado automaticamente com o passar do tempo. Atualize para o novo pacote dinâmico, conforme descrito abaixo.


## <a name="available-packages"></a>Pacotes disponíveis

Você pode adicionar padrões como NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official e UK NHS, Canadá Federal PBMM e Azure CIS 1.1.0 (novo) – uma representação mais completa do Azure CIS 1.1.0. 

Além disso, você pode adicionar o **benchmark de segurança do Azure**, as diretrizes de criação da Microsoft e específicas do Azure para práticas recomendadas de segurança e conformidade baseadas em estruturas de conformidade comuns. ([Saiba mais sobre o benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Padrões adicionais terão suporte no painel à medida que forem disponibilizados. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Adicionando um padrão normativo ao seu painel

As etapas a seguir explicam como adicionar um pacote para monitorar sua conformidade com um dos padrões regulatórios com suporte.

> [!NOTE]
> Somente os usuários que são proprietário ou colaborador de política têm as permissões necessárias para adicionar padrões de conformidade. 

1. Na barra lateral da central de segurança, selecione **conformidade regulatória** para abrir o painel conformidade regulatória. Aqui, você pode ver os padrões de conformidade atualmente atribuídos às assinaturas atualmente selecionadas.   

1. Na parte superior da página, selecione **gerenciar políticas de conformidade**. A página Gerenciamento de política é exibida.

1. Selecione a assinatura ou grupo de gerenciamento para o qual você deseja gerenciar a postura de conformidade regulatória. 

    > [!TIP]
    > É recomendável selecionar o escopo mais alto para o qual o padrão é aplicável para que os dados de conformidade sejam agregados e acompanhados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique em **adicionar mais padrões**. 

1. Na página **Adicionar padrões de conformidade regulatória** , você pode pesquisar pacotes para qualquer um dos padrões disponíveis. Alguns dos padrões disponíveis são:

    - **Azure Security Benchmark**
    - **NIST SP 800-53 R4**
    - **CSCF do CSP SWIFT-v2020**
    - **UKO e NHS do Reino Unido**
    - **PBMM do Canadá**
    
    ![Adicionando pacotes regulatórios ao painel de conformidade regulatória da central de segurança do Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Na barra lateral da central de segurança, selecione **conformidade regulatória** novamente para voltar ao painel de conformidade regulatória.
    * O novo padrão é exibido na sua lista de padrões de regulamentação do setor &. 
    * Se você adicionou o **Azure cis 1.1.0 (novo)**, a exibição *estática* original da sua conformidade do Azure cis 1.1.0 também permanecerá junto com ele. Ele pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    [![Painel de conformidade regulatória mostrando o Azure CIS antigo e novo](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a **adicionar pacotes de conformidade** para monitorar sua conformidade com padrões adicionais. 

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Painel de conformidade regulatória da central de segurança](security-center-compliance-dashboard.md)
- [Trabalhando com políticas de segurança](tutorial-security-policy.md)