---
title: Usar grupos de dispositivos em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador, saiba como usar grupos de dispositivos para analisar a telemetria de dispositivos em seu aplicativo de IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167299"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: usar grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como, como um operador, usar grupos de dispositivos para analisar a telemetria do dispositivo em seu aplicativo de IoT Central do Azure.

Um grupo de dispositivos é uma lista de dispositivos agrupados, pois correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam a gerenciar, Visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar-condicionado em Seattle para permitir que um técnico encontre os dispositivos para os quais eles são responsáveis.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Usar um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Prerequisites

Antes de começar, você deve concluir a [criação de um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado aos seus](./quick-create-pnp-device.md) guias de início rápido do aplicativo IOT central para criar o modelo de dispositivo **MXChip IOT devkit** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo de dispositivo **MXChip IOT devkit** para usar neste tutorial:

![Cinco dispositivos de sensor simulados](./media/tutorial-use-device-groups/simulated-devices.png)

Para quatro dos dispositivos de sensor simulados, use a exibição **gerenciar dispositivo** para definir o nome do cliente como *contoso*:

![Definir o nome do cliente para contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivos** no painel esquerdo.

1. Selecione **+** :

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups/image1.png)

1. Dê ao seu grupo de dispositivos o nome *dispositivos contoso*. Também é possível adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo **MXChip IOT devkit** a ser usado para este grupo.

1. Para personalizar o grupo de dispositivos para incluir somente os dispositivos pertencentes à **contoso**, selecione **+ filtro**. Selecione a propriedade **nome do cliente** , o operador de comparação **Equals** e **contoso** como o valor. Você pode adicionar vários filtros e dispositivos que atendem a **todos** os critérios de filtro são colocados no grupo de dispositivos. O grupo de dispositivos que você cria pode ser acessado por qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa exibir, modificar ou excluir o grupo de dispositivos:

    ![Consulta do grupo de dispositivos](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Cada vez que você visualizar a lista de dispositivos, poderá haver diferentes dispositivos na lista. A lista depende de quais dispositivos atualmente atendem aos critérios da consulta.

1. Escolha **Salvar**.

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos de Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Você pode usar a **análise** com um grupo de dispositivos para analisar a telemetria dos dispositivos no grupo. Por exemplo, você pode plotar a temperatura média relatada por todos os sensores ambientais da contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **análise** no painel esquerdo.

1. Selecione o grupo de dispositivos da **contoso** que você criou. Em seguida, adicione os tipos de telemetria **temperatura** e **umidade** :

    ![Criar análise](./media/tutorial-use-device-groups/create-analysis.png)

    Use os ícones de roda de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Average**. Use **dividir por** para alterar a forma como os dados agregados são mostrados. Por exemplo, se você dividir por ID do dispositivo, verá uma plotagem para cada dispositivo quando selecionar **analisar**.

1. Selecione **analisar** para exibir os valores médios de telemetria:

    ![Exibir análise](./media/tutorial-use-device-groups/view-analysis.png)

    Você pode personalizar a exibição, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar grupos de dispositivos em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)
