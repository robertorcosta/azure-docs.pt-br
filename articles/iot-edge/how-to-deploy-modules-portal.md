---
title: Implantar módulos do portal Azure - Azure IoT Edge
description: Use seu IoT Hub no portal Azure para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271441"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implantar módulos do Azure IoT Edge do portal do Azure

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os.

Este artigo mostra como o portal do Azure guia você por meio da criação de um manifesto de implantação e de empurrar a implantação a um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que visa vários dispositivos com base em suas tags compartilhadas, consulte [Implantar e monitorar módulos ioT edge em escala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub de IoT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
* Um [Dispositivo do IoT Edge](how-to-register-device.md#register-in-the-azure-portal) com o runtime do IoT Edge instalado.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que ajuda você por meio da criação de manifesto de implantação, em vez de compilar manualmente o documento JSON. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="select-device-and-add-modules"></a>Selecione o dispositivo e adicione módulos

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu hub de IoT.
1. No painel esquerdo, selecione **IoT Edge** no menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Na barra superior, selecione **Definir Módulos**.
1. Na seção **Configurações** de Registro de Contêiner da página, forneça as credenciais para acessar quaisquer registros privados de contêineres que contenham imagens do módulo.
1. Na seção **Módulos de borda IoT** da página, **selecione Adicionar**.
1. Veja os tipos de módulos do menu suspenso:

   * **Módulo de borda IoT** - Você fornece o nome do módulo e a imagem do contêiner URI. Por exemplo, o URI de imagem para `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`a amostra SimulatedTemperatureSensor é . Se a imagem do módulo estiver armazenada em um registro de contêiner privado, adicione as credenciais nesta página para acessar a imagem.
   * **Módulo de Marketplace** - Módulos hospedados no Azure Marketplace. Alguns módulos de marketplace requerem configuração adicional, então revise os detalhes do módulo na lista [de Módulos de Borda ioT do Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Módulo Azure Stream Analytics** - Módulos gerados a partir de uma carga de trabalho do Azure Stream Analytics.

1. Depois de adicionar um módulo, selecione o nome do módulo na lista para abrir as configurações do módulo. Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](module-composition.md#define-or-update-desired-properties).
1. Se necessário, repita as etapas 5 a 8 para adicionar módulos adicionais à sua implantação.
1. Selecione **A seguir: Rotas** para continuar até a seção rotas.

### <a name="specify-routes"></a>Especificar Rotas

Na guia **Rotas**, defina como as mensagens são transmitidas entre os módulos e o Hub IoT. As mensagens são construídas com pares nome/valor. Por padrão, uma rota é chamada **de rota** e definida como **FROM /messages/\* INTO $upstream,** o que significa que qualquer saída de mensagens por quaisquer módulos são enviadas para o seu hub IoT.  

Adicione ou atualize as rotas com informações das [rotas Declare](module-composition.md#declare-routes)e selecione **Next: Review + create** para continuar até a próxima etapa do assistente.

### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Note que há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [runtime do IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Revise suas informações de implantação e selecione **Criar**.

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Depois de implantar módulos no seu dispositivo, você pode visualizá-los na página de detalhes do dispositivo do seu Hub IoT. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

[O Azure Marketplace](https://azuremarketplace.microsoft.com/) é um mercado online de aplicativos e serviços onde você pode navegar por uma ampla gama de aplicativos e soluções corporativas que são certificadas e otimizadas para serem executadas no Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

Você pode implantar um módulo IoT Edge do Azure Marketplace e do seu IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implantar do Azure Marketplace

Peruse os módulos IoT Edge no mercado e quando você encontrar o que você quer, você pode implantá-lo selecionando **Criar** ou **Obtê-lo agora**. Prossiga com as etapas do assistente de implantação que podem variar dependendo do módulo IoT Edge selecionado:

1. Confirme os termos de uso e a política de privacidade do provedor selecionando **Continuar**. Você pode primeiro ter que fornecer informações de contato.
1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.
1. Selecione **Implantar em um dispositivo**.
1. Insira o nome do dispositivo ou selecione **Localizar dispositivo** para navegar entre os dispositivos registrados com o hub.
1. Selecione **Criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos se desejado. Os detalhes para o novo módulo são predefinidos, como a imagem do URI, as opções de criação e as propriedades desejadas, mas podem ser alterados.

Verifique se o módulo está implantado em seu Hub IoT no portal Azure. Selecione seu dispositivo, **selecione Definir Módulos** e o módulo deve ser listado na seção **Módulos de Borda IoT.**

### <a name="deploy-from-azure-iot-hub"></a>Implantar a partir do Azure IoT Hub

Você pode implantar rapidamente um módulo do Azure Marketplace em seu dispositivo em seu IoT Hub no portal Azure.

1. No portal do Azure, navegue até o Hub IoT.
1. No painel esquerdo, em **Gerenciamento automático de dispositivos,** selecione **IoT Edge**.
1. Selecione o dispositivo IoT Edge que deve receber a implantação.
1. Na barra superior, selecione **Definir Módulos**.
1. Na seção **Módulos de borda IoT,** clique **em Adicionar**e selecione Módulo de **Mercado** no menu suspenso.

![Adicionar módulo no IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Escolha um módulo na página **IoT Edge Module Marketplace.** O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece em sua lista de módulos IoT Edge. Alguns módulos podem exigir configuração adicional.

> [!TIP]
> As informações sobre módulos IoT Edge do Azure IoT Hub são limitadas. Você pode primeiro aprender mais sobre os [módulos IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) no Azure Marketplace.

Selecione **Next: Rotas** e continue com a implantação conforme descrito por [Especificar rotas](#specify-routes) e [revisar a implantação](#review-deployment) no início deste artigo.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)
