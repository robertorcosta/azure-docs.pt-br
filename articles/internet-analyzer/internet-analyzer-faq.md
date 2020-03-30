---
title: Faq analisador da Internet | Microsoft Docs
description: A FAQ para Analisador de Internet Do Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184264"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Faq (Visualização) do Azure Internet Analyzer

Este é o FAQ para Azure Internet Analyzer- se você tiver perguntas adicionais, vá para o [fórum de feedback](https://aka.ms/internetAnalyzerFeedbackForum) e poste sua pergunta. Quando uma pergunta é frequentemente feita, adicionamos-na a este artigo para que ela possa ser encontrada de forma rápida e fácil.

## <a name="how-do-i-participate-in-the-preview"></a>Como participo da pré-estreia?

A pré-visualização está disponível para clientes selecionados. Se você estiver interessado em participar da pré-visualização, faça o seguinte:

1. Faça login no [portal Azure](https://ms.portal.azure.com).
2. Navegue até a página **Assinaturas.**
3. Clique na assinatura do Azure com a qual você pretende usar o Analisador da Internet.
4. Vá para as configurações **dos provedores de recursos** para a assinatura.
5. Procure o **Microsoft.Network** e clique no botão **Registrar** (ou **Recadastrar).**
![solicitação de acesso](./media/ia-faq/request-preview-access.png)

6. [Solicite aprovação](https://aka.ms/internetAnalyzerContact) fornecendo-nos seu endereço de e-mail e o ID de assinatura do Azure que foi usado para fazer a solicitação de acesso.
7. Uma vez que sua solicitação tenha sido aprovada, você receberá uma confirmação de e-mail e poderá criar/atualizar/modificar os recursos do Analisador da Internet a partir da assinatura recém-permitida do Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Preciso incorporar o cliente para fazer um teste?

Sim, o cliente do Analisador de Internet deve ser instalado em seu aplicativo para coletar métricas específicas para seus usuários. [Saiba como instalar o cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Eu sou cobrado por participar da pré-estréia?
Não, o Azure Internet Analyzer é gratuito para usar na pré-visualização. Não há nenhum contrato de nível de serviço durante a pré-visualização.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Quais cenários o Analisador de Internet foi projetado para abordar?

O Internet Analyzer foi projetado para fornecer insights de desempenho de rede com base na sua população de usuários. Para ajudar a tomar as melhores decisões de desempenho para seus usuários, o Internet Analyzer compara o desempenho de dois pontos finais da Internet usando sua população de usuários distintas. Embora o Analisador de Internet possa responder a várias perguntas, algumas das mais comuns são:

* Qual é o impacto da migração para a nuvem no desempenho? 
    * *Teste sugerido: Personalizado (sua infra-estrutura local atual) vs. Azure (qualquer ponto final pré-configurado)*
* Qual é o valor de colocar meus dados na borda versus em um data center? 
    *  *Teste sugerido: Azure vs. Azure Front Door, Azure vs. Azure CDN da Microsoft*
* Qual é o benefício de desempenho do Azure Front Door?
    *  *Teste sugerido: Personalizado/ Azure/ CDN vs. Porta da Frente do Azure*
* Qual é o benefício de desempenho da CDN do Azure da Microsoft? 
    *  *Teste sugerido: Personalizado/ Azure/ AFD vs. CDN Azure da Microsoft*
* Como a CDN do Azure da Microsoft cresce? 
    *  *Teste sugerido: Personalizado (outro ponto final do CDN) vs. CDN do Azure da Microsoft*
* Qual é a melhor nuvem para sua população de usuários finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço de nuvem) vs. Azure (qualquer ponto final pré-configurado)*

## <a name="which-tests-can-i-run-in-preview"></a>Quais testes posso fazer na pré-visualização?

Cada teste criado no Analisador de Internet é composto por dois pontos finais — Endpoint A e Endpoint B. Qualquer uma das seguintes combinações pode ser executada como testes:  
* Dois pontos finais pré-configurados,
* Um ponto final personalizado e um pré-configurado, ou
* Dois [pontos finais personalizados](internet-analyzer-custom-endpoint.md) (um ponto final personalizado deve residir no Azure).

Os seguintes pontos finais pré-configurados estão disponíveis durante a visualização:
* **Regiões azure**
    * Sul do Brasil
    * Índia Central
    * Centro dos EUA
    * Leste da Ásia
    * Leste dos EUA
    * Oeste do Japão
    * Norte da Europa
    * Norte da África do Sul
    * Sudeste Asiático
    * Norte dos EAU
    * Oeste do Reino Unido  
    * Europa Ocidental
    * Oeste dos EUA
    * Oeste dos EUA 2
* **Várias combinações de regiões do Azure**
    * Leste dos EUA, Sul do Brasil
    * Leste dos EUA, Leste da Ásia
    * Oeste da Europa, Sul do Brasil
    * Oeste da Europa, Sudeste Asiático
    * Oeste da Europa, Norte dos EAU
    * Oeste dos EUA, Leste dos EUA
    * Oeste dos EUA, Oeste da Europa
    * Oeste dos EUA, Norte dos EAU
    * Oeste da Europa, Norte dos EAU, Sudeste Asiático
    * Oeste dos EUA, Oeste da Europa, Leste da Ásia
    * Oeste dos EUA, Norte da Europa, Sudeste Asiático, Norte dos EAU, Norte da África do Sul 
* **Azure + Azure Front Door**: implantação em qualquer combinação única ou múltipla de regiões do Azure listada acima
* **Azure + CDN do Azure do Microsoft**: implantação em qualquer combinação de regiões única do Azure listada acima
* **Azure + Gerenciador de Tráfego do Azure**: implantação em qualquer combinação de regiões múltiplas do Azure listada acima

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Como o Analisador de Internet é diferente de outros serviços de monitoramento fornecidos pelo Azure?

O Internet Analyzer ajuda você a entender o desempenho de seus usuários finais e ajuda a tomar decisões para melhorar seu desempenho. Enquanto outras ferramentas de monitoramento do Azure fornecem insights sobre seus serviços do Azure, o Internet Analyzer se concentra em medir o desempenho de ponta a ponta da Internet para seus usuários.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Como os dados de medição são tratados pelo Analisador de Internet?

O Azure tem [fortes processos de segurança e cumpre uma ampla gama de padrões de conformidade](https://azure.microsoft.com/support/trust-center/). Somente você e a sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a eles apenas em circunstâncias limitadas específicas e com o seu conhecimento. Está criptografado em trânsito e em repouso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte nossa [visão geral do Analisador da Internet](internet-analyzer-overview.md).
