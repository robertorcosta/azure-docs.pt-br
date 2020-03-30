---
title: Módulos do Azure IoT Edge
description: A oferta Módulo do IoT Edge no Azure Marketplace para editores de aplicativos e serviços.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: dsindona
ms.openlocfilehash: aadbf33914f919e393a5ec88cf6fc0a6103911b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286142"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

A plataforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é apoiada pela nuvem do Azure.  Essa plataforma permite que os usuários implantem cargas de trabalho de nuvem a serem executadas diretamente em dispositivos IoT.  Um módulo do IoT Edge pode executar cargas de trabalho offline e fazer análise de dados localmente. Esse tipo de oferta ajuda a economizar largura de banda e proteger dados confidenciais e locais, e ainda oferece um tempo de resposta de baixa latência.  Agora você tem as opções necessárias para aproveitar essas cargas de trabalho pré-criadas. Até agora, havia apenas algumas soluções internas da Microsoft disponíveis.  Era necessário investir tempo e recursos para criar suas próprias soluções de IoT personalizadas.

Com a introdução dos [módulos do IoT Edge no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), agora oferecemos um único destino para que os editores exponham e vendam suas soluções à audiência de IoT. Assim, os desenvolvedores de IoT podem encontrar e comprar recursos para acelerar o desenvolvimento de suas soluções.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos do IoT Edge no Azure Marketplace:

| **Para editores**    | **Para clientes (desenvolvedores de IoT)**  |
| :------------------- | :-------------------|
| Alcançar milhões de desenvolvedores que desejam criar e implantar soluções do IoT Edge.  | Criar uma solução do IoT Edge com a confiança de usar componentes seguros e testados. |
| Publicar uma única vez e executar em qualquer hardware do IoT Edge com suporte para contêineres. | Reduzir o tempo de colocação no mercado encontrando e implantando módulos do IoT Edge de terceiros e internos de acordo com necessidades específicas. |
| Monetizar com opções flexíveis de cobrança <ul> <li> Gratuito e BYOL (traga sua própria licença). </li> </ul> | Fazer compras escolhendo entre as opções de modelos de cobrança. <ul> <li> Gratuito e BYOL (traga sua própria licença). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é um módulo do IoT Edge?

O Azure IoT Edge permite implantar e gerenciar a lógica de negócios na borda em forma de módulos. Os módulos do Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o código específico da sua própria solução. Para saber mais sobre os módulos do IoT Edge, confira [Entender os módulos do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Qual é a diferença entre um tipo de oferta de contêiner e um tipo de oferta de módulo do IoT Edge?**

O tipo de oferta de módulo do IoT Edge é um tipo específico de contêiner em execução em um dispositivo do IoT Edge. Ele vem com definições de configuração padrão para ser executado no contexto do IoT Edge e, opcionalmente, usa o SDK do módulo do IoT Edge para ser integrado ao runtime do IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicando o módulo do IoT Edge

**Selecionando a vitrine à direita**

Os módulos do IoT Edge só são publicados no Azure Marketplace. O AppSource não se aplica.  Para obter mais informações sobre as diferenças e a audiência das vitrines, confira [Determinando a opção de publicação para sua solução](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opções de faturamento**

No momento, o Marketplace dá suporte às opções de cobrança **Gratuito** e **BYOL (traga sua própria licença)** para os módulos do IoT Edge.
 
**Opções de publicação**

Em todos os casos, é necessário selecionar a opção de publicação **Transação** para os módulos do IoT Edge.  Confira [Escolher uma opção de publicação](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de qualificação

Todos os termos dos contratos e das políticas do Microsoft Azure Marketplace aplicam-se a ofertas de módulo do IoT Edge.  Além disso, há pré-requisitos e requisitos técnicos para os módulos do IoT Edge.  

**Pré-requisitos**

Para publicar um módulo do IoT Edge no Azure Marketplace, você precisa atender aos seguintes pré-requisitos:

- Acesso ao CPP (Portal do Cloud Partner). Para obter mais informações, confira [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hospedar seu módulo do IoT Edge em um Registro de Contêiner do Azure. 
- Preparar os metadados do módulo do IoT Edge como (lista parcial): 
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40 x 40 px, 90 x 90 px, 115 x 115 px, 255 x 115 px)
    - Um termo de uso e uma política de privacidade
    - Configuração de módulo padrão (rota, propriedades desejadas do gêmeo, createOptions, variáveis de ambiente)
    - Documentação
    - Contatos de suporte

**Requisitos Técnicos**

Os principais requisitos técnicos de um IoT Edge Module, para que ele seja certificado e publicado no Azure Marketplace, estão detalhados no [Prepare os recursos técnicos do módulo IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Documentação e recursos

[Crie uma oferta de módulo IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) -- As etapas para publicar uma nova oferta de módulo IoT Edge com o Portal de Publicação em Nuvem.

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso,

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- Entre no [Portal do Cloud Partner](https://cloudpartner.azure.com/) para criar ou concluir sua oferta.
- Consulte a [Visão geral da publicação de ofertas do módulo do IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) para obter informações sobre como publicar uma oferta de módulo do IoT Edge.
