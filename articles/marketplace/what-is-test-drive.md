---
title: O que é um test drive? Marketplace comercial da Microsoft
description: Explicação do recurso de test drive do Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: abad72145b095b4da77ec499f936c6912fd970a6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229441"
---
# <a name="what-is-a-test-drive"></a>O que é um test drive?

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção de *experimentar antes de comprar*, resultando em uma maior conversão e geração de clientes potenciais altamente qualificados. Uma test drive dá vida ao seu produto em um cenário de implementação real, gerando leads altamente qualificados.

Unidades de teste são instâncias gerenciadas que implantam sua solução ou aplicativo sob demanda para clientes que a solicitam. Depois que uma instância de test drive for atribuída, ela estará disponível para uso por um determinado período de tempo e, em seguida, excluída para criar espaço para outro cliente.

Como Publicador, você gerencia e define as configurações de test drive no Partner Center. Os detalhes de configuração técnica variam dependendo do tipo de oferta com a qual você está trabalhando. Para obter diretrizes detalhadas, consulte o link na [próxima etapa](#next-step) no final deste tópico.

Os clientes potenciais detectam seu test drive no mercado comercial. Eles fornecem suas informações de contato e concordam com os termos e a política de privacidade da sua oferta e, em seguida, acessam seu ambiente pré-configurado para testá-lo por um período de tempo fixo. Os clientes recebem uma avaliação prática e Autoguiada dos principais recursos e benefícios do seu produto e você recebe um líder valioso.

## <a name="how-does-it-work"></a>Como ela funciona?

Como Publicador, você gerencia e define as configurações de test drive no Partner Center. Após a instalação, ele se torna uma instância gerenciada que será implantada sob demanda para o cliente que a solicita. Depois que uma instância de test drive for atribuída, ela estará disponível para uso por um determinado período de tempo e, em seguida, excluída para criar espaço para outro cliente.

## <a name="types-of-test-drives"></a>Tipos de unidades de teste

Há diferentes unidades de teste disponíveis no Marketplace comercial para Select offers, dependendo do tipo de produto, cenário e Marketplace em que você está:

- Azure Resource Manager
- test drive hospedado
    - Dynamics 365 para Central de Negócios
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplicativo lógico
- Power BI

Para obter detalhes sobre como configurar um desses drives de teste, consulte o link na [próxima etapa](#next-step) no final deste tópico.

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Este modelo de implantação contém todos os recursos do Azure que compõem sua solução. Os produtos adequados a esse cenário usam somente os recursos do Azure. Essa é a única opção test drive para a máquina virtual ou ofertas de aplicativo do Azure.

### <a name="hosted-test-drive"></a>test drive hospedado

Um test drive hospedado remove a complexidade da instalação, permitindo que a Microsoft hospede e mantenha o serviço que executa o provisionamento, a implantação e o desprovisionamento de usuários do test drive. Se você tiver uma oferta em Microsoft AppSource, crie seu test drive para se conectar a uma instância do Dynamics AX/CRM ou a qualquer outro recurso além do Azure. Use este tipo para ofertas do AppSource para se conectar com estas ofertas do Dynamics 365:

- Use o [Dynamics 365 for Business central](partner-center-portal/create-new-operations-offer.md) para um sistema de planejamento de recursos empresariais central de negócios, como finanças, operações, Cadeia de fornecedores e CRM.
- Use o [Dynamics 365 para o envolvimento do cliente](partner-center-portal/create-new-customer-engagement-offer.md) para um sistema de envolvimento do cliente, como vendas, serviço, serviço de projeto e serviço de campo.
- Use o [Dynamics 365 para operações](partner-center-portal/create-new-operations-offer.md) para um sistema de planejamento de recursos corporativos de finanças e operações, como finanças, operações e manufatura, Cadeia de suprimentos.

### <a name="logic-app-test-drive"></a>test drive de aplicativo lógico

Esse tipo de test drive não é hospedado pela Microsoft. Use-o para se conectar a uma oferta do Dynamics 365 ou a outro recurso personalizado.

### <a name="power-bi-test-drive"></a>Test Drive do Power BI

Trata-se simplesmente de um link inserido para um painel personalizado. Qualquer produto que demonstre apenas um Visual Power BI interativo deve usar esse tipo de test drive.

## <a name="transforming-examples"></a>Exemplos de transformação

O processo de transformar uma arquitetura de recursos em um test drive pode ser assustador. Confira estes exemplos de como transformar melhor as arquiteturas atuais.

[Transformar um modelo de site em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformar um modelo de máquina virtual em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transformar um modelo existente do Resource Manager em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Gerar leads de seu test drive

Uma test drive do Marketplace comercial é uma excelente ferramenta para os comerciantes. Recomendamos que você o incorpore em seus esforços de entrada no mercado ao iniciar para gerar mais clientes potenciais para sua empresa. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Se você fechar um negócio com um Lead test drive, não se esqueça de registrá-lo no [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Além disso, adoraríamos saber sobre seu cliente vence onde um test drive desempenhava uma função.

## <a name="other-resources"></a>Outros recursos

Recursos adicionais de test drive:

- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Verifique se o bloqueador de pop-ups está desativado)

## <a name="next-step"></a>Próxima etapa

- [Configuração técnica de test drive](test-drive-technical-configuration.md)
