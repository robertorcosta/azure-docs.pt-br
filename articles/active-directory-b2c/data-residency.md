---
title: Disponibilidade de região e residência de dados
titleSuffix: Azure AD B2C
description: Disponibilidade de região, residência de dados e informações sobre Azure Active Directory B2C locatários de visualização.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: f8c6f7daecd38babaa4f2961d04a6cd4c3b4dbed
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840550"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Residência de dados e disponibilidade de região

Residência de dados e disponibilidade de região são dois conceitos muito diferentes que se aplicam a B2C do Azure AD diferentemente do restante do Azure. Este artigo explica as diferenças entre esses dois conceitos e compara como eles se aplicam ao Azure versus Azure AD B2C.

O Azure AD B2C **geralmente está disponível em todo o mundo** com a opção de **residência de dados** no **Estados Unidos, na Europa ou no Pacífico Asiático**.

[Disponibilidade da região](#region-availability) refere-se onde um serviço está disponível para uso.

[Residência dados](#data-residency) refere-se ao armazenamento de dados do usuário.

## <a name="region-availability"></a>Disponibilidade de região

B2C do Azure AD está disponível em todo o mundo por meio da nuvem pública do Azure.

Isso difere do modelo seguido pela maioria dos outros serviços do Azure, que normalmente combinam a *disponibilidade* com a *residência de dados*. É possível visualizar exemplos disso na página [Produtos Disponíveis por Região](https://azure.microsoft.com/regions/services/) e [Calculadora de preços B2C do Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência de dadosResidência de dados

O Azure AD B2C armazena dados do usuário nas regiões Estados Unidos, Europa ou Pacífico Asiático.

A residência de dados é determinada pelo país/região que você selecionar ao [criar um locatário de Azure ad B2C](tutorial-create-tenant.md):

![Captura de tela de um formulário criar locatário, escolhendo país ou região.](./media/data-residency/data-residency-b2c-tenant.png)

Os dados residem no **Estados Unidos** para os seguintes países/regiões:

> Estados Unidos (EUA), Canadá (CA), Costa Rica (CR), República Dominicana (DO), El Salvador (VA), Guatemala (GT), México (MX), Panamá (PA), Porto Rico (PR) e Trinidad & Tobago (TT)

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia (DZ), Áustria (AT), Azerbaijão (AZ), Bahrein (BH), Belarus (por), Bélgica (is), Bulgária (BG), Croácia (HR), Chipre (CY), República Tcheca (CZ), Dinamarca (DK), Egito (por exemplo), Estônia (EE), Finlândia (FT), França (FR), Alemanha (DE), Grécia (GR), Hungria (HU), Islândia (IS), Irlanda (IE), Israel (IL), Itália (IT), Jordânia (JO), Cazaquistão (KZ), Quênia (KE), Kuwait (KW), Letônia (LV), Líbano (LB), Liechtenstein (LI), Lituânia (LT) , Luxemburgo (LU), norte da Macedônia (ML), Malta (MT), Montenegro (ME), Marrocos (MA), Holanda (NL), Nigéria (NG), Noruega (não), Omã (OM), Paquistão (CP), Polônia (PL), Portugal (PT), Catar (QA), Romênia (RO), Rússia (RU), Arábia Saudita (SA), Sérvia (RS), Eslováquia (SK), Eslovênia (ST), África do Sul (ZA), Espanha (ES), Suécia (SE), Suíça (CH), Tunísia (TN), Turquia (TR), Ucrânia (UA), Emirados Árabes Unidos (AE) e Reino Unido (GB)

Os dados residem em **Pacífico Asiático** para os seguintes países/regiões:

> Afeganistão (AF), Hong Kong SAR (HK), Índia (IN), Indonésia (ID), Japão (JP), Coreia (KR), Malásia (MY), Filipinas (PH), Cingapura (SG), Sri Lanka (LK), Taiwan (Taiwan) e Tailândia (TH).

Os seguintes países/regiões estão no processo de serem adicionados à lista. Por enquanto, ainda é possível utilizar B2C do Azure AD, escolhendo qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Locatário de visualização

Se você tiver criado um locatário B2C durante o período de visualização do Azure AD B2C, é provável que o **tipo de locatário** informe o **locatário de visualização**.

Se esse for o caso, você deve usar seu locatário somente para fins de desenvolvimento e teste. Não use um locatário de visualização para aplicativos de produção.

Não **há nenhum caminho de migração** de um locatário do B2C de visualização para um locatário B2C de escala de produção. Você deve criar um novo locatário B2C para seus aplicativos de produção.

Há problemas conhecidos quando você exclui um locatário do B2C de visualização e cria um locatário B2C de escala de produção com o mesmo nome de domínio. *Você deve criar um locatário B2C de escala de produção com um nome de domínio diferente*.

![Captura de tela de um tipo de locatário, como locatário de visualização.](./media/data-residency/preview-b2c-tenant.png)