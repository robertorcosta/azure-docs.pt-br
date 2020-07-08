---
title: Ofertas do módulo IoT Edge no Azure Marketplace
description: Saiba mais sobre a publicação de ofertas do módulo IoT Edge no Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 9783eb1dbcbfc3ee01008dfa33ff2429a87cf62a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963369"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

A plataforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é apoiada pela nuvem do Microsoft Azure.  Essa plataforma permite que os usuários implantem cargas de trabalho de nuvem a serem executadas diretamente em dispositivos IoT.  Um módulo do IoT Edge pode executar cargas de trabalho offline e fazer análise de dados localmente. Esse tipo de oferta ajuda a economizar largura de banda e proteger dados confidenciais e locais, e ainda oferece um tempo de resposta de baixa latência.  Agora você tem as opções necessárias para aproveitar essas cargas de trabalho pré-criadas. Até agora, havia apenas algumas soluções internas da Microsoft disponíveis.  Era necessário investir tempo e recursos para criar suas próprias soluções de IoT personalizadas.

Com os [módulos do IoT Edge no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), agora oferecemos um único destino para que os editores exponham e vendam suas soluções à audiência de IoT. Assim, os desenvolvedores de IoT podem encontrar e comprar recursos para acelerar o desenvolvimento de suas soluções.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos do IoT Edge no Azure Marketplace

| **Para editores**    | **Para clientes (desenvolvedores de IoT)**  |
| :------------------- | :-------------------|
| Alcançar milhões de desenvolvedores que desejam criar e implantar soluções do IoT Edge.  | Criar uma solução do IoT Edge com a confiança de usar componentes seguros e testados. |
| Publicar uma única vez e executar em qualquer hardware do IoT Edge com suporte para contêineres. | Reduzir o tempo de colocação no mercado encontrando e implantando módulos do IoT Edge de terceiros e internos de acordo com necessidades específicas. |
| Monetizar com opções flexíveis de cobrança <ul> <li> Gratuito e BYOL (traga sua própria licença). </li> </ul> | Fazer compras escolhendo entre as opções de modelos de cobrança. <ul> <li> Gratuito e BYOL (traga sua própria licença). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é um módulo do IoT Edge?

O Azure IoT Edge permite implantar e gerenciar a lógica de negócios na borda em forma de módulos. Os módulos do Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o código específico da sua própria solução. Para saber mais sobre os módulos do IoT Edge, confira [Entender os módulos do Azure IoT Edge](../iot-edge/iot-edge-modules.md).

**Qual é a diferença entre um tipo de oferta de contêiner e um tipo de oferta de módulo do IoT Edge?**

O tipo de oferta de módulo do IoT Edge é um tipo específico de contêiner em execução em um dispositivo do IoT Edge. Ele vem com definições de configuração padrão para ser executado no contexto do IoT Edge e, opcionalmente, usa o SDK do módulo do IoT Edge para ser integrado ao runtime do IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicando o módulo do IoT Edge

**Selecionando a vitrine à direita**

Os módulos do IoT Edge só são publicados no Azure Marketplace. O AppSource não se aplica.  Para obter mais informações sobre as diferenças e a audiência das vitrines, confira [Determinando a opção de publicação para sua solução](determine-your-listing-type.md).
 
**Opções de cobrança**

No momento, o Marketplace dá suporte às opções de cobrança **Gratuito** e **BYOL (traga sua própria licença)** para os módulos do IoT Edge.
 
**Opções de publicação**

Em todos os casos, é necessário selecionar a opção de publicação **Transação** para os módulos do IoT Edge.  Confira [Escolher uma opção de publicação](determine-your-listing-type.md) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de qualificação

Todos os termos dos contratos e das políticas do Microsoft Azure Marketplace aplicam-se a ofertas de módulo do IoT Edge.  Além disso, há pré-requisitos e requisitos técnicos para os módulos do IoT Edge.  

**Pré-requisitos**

Para publicar um módulo do IoT Edge no Azure Marketplace, você precisa atender aos seguintes pré-requisitos:

- Acesso à Central de Parceiros. Para obter mais informações, confira [Guia de publicação do Azure Marketplace e do AppSource](marketplace-publishers-guide.md).
- Hospedar seu módulo do IoT Edge em um Registro de Contêiner do Azure. 
- Preparar os metadados do módulo do IoT Edge como (lista parcial): 
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40 x 40 px, 90 x 90 px, 115 x 115 px, 255 x 115 px)
    - Um termo de uso e uma política de privacidade
    - Configuração de módulo padrão (rota, propriedades desejadas do gêmeo, createOptions, variáveis de ambiente)
    - Documentação
    - Contatos de suporte

**Requisitos técnicos**

Os principais requisitos técnicos de um IoT Edge Module, para que ele seja certificado e publicado no Azure Marketplace, estão detalhados no [Prepare os recursos técnicos do módulo IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Documentação e recursos

[Criar uma oferta de módulo do IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) – as etapas para publicar uma nova oferta de módulo do IoT Edge na Central de Parceiros.

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso,

- [Saiba mais](https://azuremarketplace.microsoft.com/sell) sobre o marketplace.

Para se registrar na Central de Parceiros, crie uma nova oferta ou trabalhe em uma existente,

- Entre na [Central de Parceiros](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Confira como publicar uma oferta na [Visão geral da oferta do módulo do IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md).
