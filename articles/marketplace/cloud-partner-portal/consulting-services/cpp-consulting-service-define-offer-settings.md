---
title: Definir configurações de oferta para uma oferta de serviço de consultoria | Azure Marketplace
description: Defina as configurações de oferta em uma oferta de serviço de consultoria do Azure ou Dynamics 365 no Portal do Cloud Partner para o Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 406a37a1ef946b1c3ceb0d7b02ba318f423dcf53
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146688"
---
# <a name="offer-settings-tab"></a>Guia Configurações da Oferta

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de serviço de consultoria para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [visão geral da criação do serviço de consultoria](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer) para gerenciar suas ofertas migradas.

Na nova tela de **oferta** , a primeira etapa é criar a identidade da oferta. A identidade da oferta consiste em três partes: **ID da oferta**, **ID do publicador** e **Nome**. Cada um desses blocos é abordado nas seções a seguir.

![Criar uma nova oferta de serviço de consultoria – guia Configurações da oferta](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID da oferta *

Esse identificador é um nome exclusivo que você cria quando envia a oferta pela primeira vez. Deve conter apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. A **ID da oferta** será visível na URL e afeta os resultados do mecanismo de pesquisa. Um exemplo é *yourcompanyname_exampleservice*.

Conforme mostrado no exemplo, a **ID da oferta** é anexada à ID do editor para criar um identificador exclusivo. O identificador exclusivo é exposto como um link permanente que pode ser registrado e é indexado pelos mecanismos de pesquisa.

>[!Note]
>Depois que uma oferta estiver ativa, o identificador não poderá ser atualizado.


### <a name="publisher-id"></a>ID do Publicador *

Esse identificador está relacionado à sua conta. Depois de se conectar com sua conta organizacional, a **ID do editor** será exibida no menu suspenso.


### <a name="name"></a>Nomes

Essa cadeia de caracteres será exibida como nome da oferta no AppSource ou no Azure Marketplace. O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

O exemplo a seguir mostra como o nome da oferta é montado. 

![Criar uma nova oferta de serviços de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** Definido na guia **detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** Definido na guia **detalhes da vitrine** do editor. Os tipos de serviço são `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposição:** Inserido pelo revisor.
-   **Nome:** Definido na página de **configurações da oferta** .

>[!Note]
>O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

A lista a seguir fornece vários nomes de oferta adequados:

-   Conceitos básicos para serviços profissionais: resumo de 1 hora
-   Plataforma de migração na nuvem: resumo de 1 hora
-   PowerApps e Microsoft Flow: workshop de 1 dia
-   Azure Machine Learning: 3-WK de VDC
-   Solução de varejo brick-and-click: resumo de 1 hora
-   Traga seus próprios dados: Workshop 1-WK
-   Análise de nuvem: workshop de 3 dias
-   Treinamento do Power BI: workshop de 3 dias
-   Solução de gerenciamento de vendas: implementação de 1 semana
-   Início rápido do CRM: Workshop de 1 dia
-   Dynamics 365 for Sales: avaliação de 2 dias

Depois de preencher a guia **Configurações da oferta**, salve seu envio. Agora, o nome da oferta é exibido acima do editor e você pode encontrá-lo em **Todas as ofertas**.

## <a name="next-steps"></a>Próximas etapas

Agora, você pode inserir [detalhes da vitrine e determinar se deseja publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).
