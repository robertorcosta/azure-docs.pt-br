---
title: Disponibilidade da região e residência de dados
titleSuffix: Azure AD B2C
description: Disponibilidade da região, residência de dados e informações sobre os inquilinos de pré-visualização do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188840"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Residência de dados e disponibilidade de região

Residência de dados e disponibilidade de região são dois conceitos muito diferentes que se aplicam a B2C do Azure AD diferentemente do restante do Azure. Este artigo explica as diferenças entre esses dois conceitos, e compara como eles se aplicam ao Azure versus Azure AD B2C.

O Azure AD B2C está **geralmente disponível em todo** o mundo com a opção de residência de **dados** nos **Estados Unidos, Europa ou Ásia-Pacífico.**

[Disponibilidade da região](#region-availability) refere-se onde um serviço está disponível para uso.

[Residência dados](#data-residency) refere-se ao armazenamento de dados do usuário.

## <a name="region-availability"></a>Disponibilidade de região

B2C do Azure AD está disponível em todo o mundo por meio da nuvem pública do Azure.

Isso difere do modelo seguido pela maioria dos outros serviços do Azure, que normalmente *acoplas da disponibilidade* com residência de *dados*. É possível visualizar exemplos disso na página [Produtos Disponíveis por Região](https://azure.microsoft.com/regions/services/) e [Calculadora de preços B2C do Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência de dadosResidência de dados

O Azure AD B2C armazena dados de usuários nos Estados Unidos, Europa ou na região Ásia-Pacífico.

A residência de dados é determinada pelo país/região que você seleciona quando [cria um inquilino Azure AD B2C](tutorial-create-tenant.md):

![Captura de tela de um inquilino de pré-visualização](./media/data-residency/data-residency-b2c-tenant.png)

Os dados residem **nos Estados Unidos** para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad e Tobago

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia, Áustria, Azerbaijão, Bahrein, Bielorrússia, Bélgica, Bulgária, Croácia, Chipre, República Tcheca, Dinamarca, Egito, Estônia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quênia, Kuwait, Letônia, Líbano, Liechtenstein, Lituânia, Luxemburgo, Macedônia do Norte, Malta, Montenegro, Marrocos, Holanda, Nigéria, Noruega, Omã, Paquistão, Polônia, Portugal, Catar, Romênia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovênia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Emirados Árabes Unidos e Reino Unido.

Os dados residem na **Ásia-Pacífico** para os seguintes países/regiões:

> Afeganistão, Hong Kong SAR, Índia, Indonésia, Japão, Coréia, Malásia, Filipinas, Cingapura, Sri Lanka, Taiwan e Tailândia.

Os seguintes países/regiões estão em processo de adição à lista. Por enquanto, ainda é possível utilizar B2C do Azure AD, escolhendo qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Locatário de visualização

Se você criou um inquilino B2C durante o período de pré-visualização do Azure AD B2C, é provável que seu tipo de **inquilino** diga **o inquilino preview**.

Se este for o caso, você deve usar seu inquilino SOMENTE para fins de desenvolvimento e teste. NÃO use um inquilino de pré-visualização para aplicações de produção.

**Não há caminho** de migração de um inquilino B2C de pré-visualização para um inquilino B2C em escala de produção. Você deve criar um novo inquilino B2C para suas aplicações de produção.

Existem problemas conhecidos quando você exclui um inquilino B2C de visualização e cria um inquilino B2C em escala de produção com o mesmo nome de domínio. *Você deve criar um inquilino B2C em escala de produção com um nome de domínio diferente*.

![Captura de tela de um inquilino de pré-visualização](./media/data-residency/preview-b2c-tenant.png)