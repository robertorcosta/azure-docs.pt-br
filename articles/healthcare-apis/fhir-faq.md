---
title: Perguntas frequentes sobre os serviços FHIRs no Azure – API do Azure para FHIR
description: Obtenha respostas para perguntas frequentes sobre a API do Azure para FHIR, como o local de armazenamento de dados por trás de APIs do FHIR e suporte de versão.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870976"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a API do Azure para FHIR

## <a name="what-is-fhir"></a>O que é o FHIR?
Os recursos de interoperabilidade do Fast Healthcare (FHIR-pronunciado "incêndio") são um padrão de interoperabilidade destinado a habilitar a troca de dados de saúde entre diferentes sistemas de integridade. Esse padrão foi desenvolvido pela organização HL7 e está sendo adotado pelas organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (versão 4). A API do Azure para FHIR dá suporte a R4 e também dá suporte à versão anterior STU3 (Standard para avaliação, use 3). Para obter mais informações sobre o FHIR, visite [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados estão por trás das APIs do FHIR armazenadas no Azure?

Sim, os dados são armazenados em bancos de dados gerenciados no Azure. A API do Azure para FHIR não fornece acesso direto ao armazenamento de dados subjacente.

## <a name="what-identity-provider-do-you-support"></a>A qual provedor de identidade você dá suporte?

Atualmente, damos suporte a Microsoft Azure Active Directory como o provedor de identidade.

## <a name="what-fhir-version-do-you-support"></a>A qual versão do FHIR você dá suporte?

Damos suporte às versões 4.0.0 e 3.0.1 tanto na API do Azure para FHIR (PaaS) quanto no servidor FHIR para o Azure (código aberto).

Para obter detalhes, consulte [recursos com suporte](fhir-features-supported.md). Leia sobre o que mudou entre as versões no [histórico de versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre o Microsoft FHIR Server de software livre para o Azure e a API do Azure para FHIR?

A API do Azure para FHIR é uma versão hospedada e gerenciada do Microsoft FHIR Server de software livre para o Azure. No serviço gerenciado, a Microsoft fornece todas as atualizações e manutenção. 

Quando estiver executando o FHIR Server para Azure, você terá acesso direto aos serviços subjacentes. Mas você também é responsável por manter e atualizar o servidor e todo o trabalho de conformidade necessário se estiver armazenando PHI dados.

Do ponto de vista do desenvolvimento, todos os recursos são implantados no Microsoft FHIR Server de software livre para o Azure primeiro. Depois de ser validado em código-fonte aberto, ele será liberado para a solução de PaaS API do Azure para FHIR. O tempo entre a versão em software livre e PaaS depende da complexidade do recurso e de outras prioridades de roteiro. 

## <a name="what-is-smart-on-fhir"></a>O que é inteligente no FHIR?

SMART (aplicativos médicos substituíveis e tecnologia reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicativos de parceiros com servidores FHIR e outros sistemas de ti de saúde, como registros eletrônicos de integridade e trocas de informações de integridade. Ao criar um aplicativo inteligente no FHIR, você pode garantir que seu aplicativo possa ser acessado e utilizado por uma infinidade de sistemas diferentes.
Autenticação e API do Azure para FHIR. Para saber mais sobre o SMART, visite o [Smart Health IT](https://smarthealthit.org/).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu algumas das perguntas frequentes sobre a API do Azure para FHIR. Leia sobre os recursos com suporte no servidor FHIR para o Azure:
 
>[!div class="nextstepaction"]
>[Recursos de FHIR com suporte](fhir-features-supported.md)