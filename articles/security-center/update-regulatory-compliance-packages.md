---
title: Como atualizar para o monitoramento dinâmico de conformidade normativa em seu painel de conformidade normativa do Azure Security Center | Microsoft Docs
description: Atualizando seus pacotes de conformidade normativa (Preview)
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
ms.openlocfilehash: 4080825bbb1f6c274f5b5aafd28e8c672148b98f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159278"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Atualize para pacotes de conformidade dinâmica em seu painel de conformidade normativa (Preview)

O Azure Security Center compara continuamente a configuração de seus recursos com requisitos em padrões do setor, regulamentos e benchmarks. O **painel de conformidade normativa** fornece insights sobre sua postura de conformidade com base em como você está atendendo a controles e requisitos específicos de conformidade.

Um padrão para o qual você pode acompanhar sua postura de conformidade é [o Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (mais formalmente, a "CIS Microsoft Azure Foundations Benchmark versão 1.1.0"). 

A representação do Azure CIS que aparece inicialmente em seu painel de conformidade depende de um conjunto estático de regras que está incluída no Security Center.

Com o recurso **dynamic compliance packages (preview),** o Security Center melhora automaticamente sua cobertura dos padrões do setor ao longo do tempo. Os pacotes de conformidade são essencialmente iniciativas definidas na Política do Azure. Eles podem ser atribuídos ao seu escopo selecionado (assinatura, grupo de gerenciamento e assim por diante). Para ver os dados de conformidade mapeados como avaliações em seu painel, adicione um pacote de conformidade ao seu grupo de gerenciamento ou assinatura de dentro da Política de Segurança. A adição de um pacote de conformidade atribui efetivamente a iniciativa de conformidade normativa ao escopo selecionado. Desta forma, você pode acompanhar as iniciativas regulatórias recém-publicadas como padrões de conformidade em seu painel. Quando a Microsoft lança novos conteúdos para a iniciativa (novas políticas que mapeiam para mais controles no padrão), o conteúdo adicional aparece automaticamente em seu painel.

O pacote de conformidade dinâmica para o benchmark Azure CIS, **Azure CIS 1.1.0 (novo)**, melhora a versão *estática* original por:

* Incluindo mais políticas
* Atualização automática com nova cobertura à medida que é adicionada 

Atualize para o novo pacote dinâmico conforme descrito abaixo.

## <a name="adding-a-dynamic-compliance-package"></a>Adicionando um pacote de conformidade dinâmica

As etapas a seguir explicam como adicionar o pacote dinâmico para monitorar sua conformidade com o benchmark CIS do Azure v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Atualização para o pacote de conformidade dinâmica Azure CIS 1.1.0 (novo) 

1. Abra a página **da política de segurança.** Esta página mostra o número de grupos de gerenciamento, assinaturas, espaços de trabalho e sua estrutura de grupo de gerenciamento.

1. Selecione o grupo de assinatura ou gerenciamento para o qual deseja gerenciar a postura de conformidade normativa. Recomendamos selecionar o escopo mais alto para o qual o padrão é aplicável para que os dados de conformidade sejam agregados e rastreados para todos os recursos aninhados. 

1. Na seção de padrões regulatórios (preview) da &, você verá que o Azure CIS 1.1.0 pode ser atualizado para novos conteúdos. Clique **em Atualizar agora**. 

1. Opcionalmente, clique **em Adicionar mais padrões** para abrir a página **Adicionar padrões de conformidade normativa.** Lá, você pode pesquisar manualmente para **Azure CIS 1.1.0 (Novo)** e pacotes dinâmicos para outros padrões de conformidade, como **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020,** **UKO e UK NHS,** e **Canada PBMM**.
    
    > [!TIP]
    > Apenas os usuários que são proprietários ou contribuintes de políticas têm as permissões necessárias para adicionar padrões de conformidade. 

    ![Adicionando pacotes regulatórios ao painel de conformidade normativa do Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Na barra lateral do Security Center, selecione **conformidade normativa** para abrir o painel de conformidade normativa. 
    * A zure CIS 1.1.0 (Novo) agora aparece em sua lista de normas regulatórias do & da Indústria. 
    * A visão *estática* original da sua conformidade CIS 1.1.0 do Azure também permanecerá ao lado dela. Pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um novo padrão seja adicionado no painel de conformidade.


    [![Painel de conformidade normativa mostrando antigo e novo CIS do Azure](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu que:

* Como **atualizar os padrões** mostrados em seu painel de conformidade normativa para os novos pacotes *dinâmicos*
* Como **adicionar pacotes de conformidade** para monitorar sua conformidade com padrões adicionais. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Painel de conformidade normativa do centro de segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como usar as recomendações da Central de Segurança do Azure para ajudar a proteger seus recursos do Azure.