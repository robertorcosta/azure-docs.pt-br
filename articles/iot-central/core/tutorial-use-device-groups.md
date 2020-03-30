---
title: Use grupos de dispositivos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Como operador, aprenda a usar grupos de dispositivos para analisar a telemetria a partir de dispositivos em seu aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167299"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Use grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como, como operador, usar grupos de dispositivos para analisar a telemetria do dispositivo em seu aplicativo Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos que são agrupados porque correspondem a alguns critérios especificados. Grupos de dispositivos ajudam você a gerenciar, visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos menores e lógicos. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar condicionado em Seattle para permitir que um técnico encontre os dispositivos pelos quais eles são responsáveis.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Use um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os inícios rápidos [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-pnp-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo de dispositivo **MXChip IoT DevKit** para usar neste tutorial:

![Cinco dispositivos de sensores simulados](./media/tutorial-use-device-groups/simulated-devices.png)

Para quatro dos dispositivos de sensores simulados, use a exibição **gerenciar** dispositivo para definir o nome do cliente como *Contoso*:

![Defina o nome do cliente para Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivo** no painel esquerdo.

1. Selecione: **+**

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups/image1.png)

1. Dê ao seu grupo de dispositivos o nome *Contoso devices*. Também é possível adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo **MXChip IoT DevKit** para usar para este grupo.

1. Para personalizar o grupo de dispositivos para incluir apenas os dispositivos pertencentes a **Contoso,** selecione **+ Filtro**. Selecione a propriedade Nome do **Cliente,** o operador de comparação **Equals** e **Contoso** como valor. Você pode adicionar vários filtros e dispositivos que atendam a **todos os** critérios de filtro são colocados no grupo do dispositivo. O grupo de dispositivos criado é acessível a qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa visualizar, modificar ou excluir o grupo do dispositivo:

    ![Consulta de grupo de dispositivos](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Cada vez que você visualizar a lista de dispositivos, poderá haver diferentes dispositivos na lista. A lista depende de quais dispositivos atualmente atendem aos critérios da consulta.

1. Escolha **Salvar**.

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Você pode usar **o Analytics** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, você pode traçar a temperatura média relatada por todos os sensores ambientais contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **Analytics** no painel esquerdo.

1. Selecione o grupo de **dispositivos Contoso** que você criou. Em seguida, adicione os tipos de telemetria **de temperatura** e **umidade:**

    ![Criar análises](./media/tutorial-use-device-groups/create-analysis.png)

    Use os ícones da roda de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Médio**. Use **Split para** alterar a forma como os dados agregados são mostrados. Por exemplo, se você dividir por ID do dispositivo, verá um gráfico para cada dispositivo quando selecionar **Analisar**.

1. Selecione **Analisar** para visualizar os valores médios de telemetria:

    ![Ver análise](./media/tutorial-use-device-groups/view-analysis.png)

    Você pode personalizar a exibição, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar grupos de dispositivos em seu aplicativo Azure IoT Central, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)
