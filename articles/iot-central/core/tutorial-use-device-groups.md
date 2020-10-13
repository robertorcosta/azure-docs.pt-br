---
title: Tutorial – usar grupos de dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Tutorial – como um operador, saiba como usar grupos de dispositivos para analisar a telemetria de dispositivos no aplicativo do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967940"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Usar grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como um operador pode usar grupos de dispositivos para analisar a telemetria do dispositivo no aplicativo do Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos agrupados devido à correspondência com alguns critérios especificados. Os grupos de dispositivos ajudam a gerenciar, visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar-condicionado em Seattle e permitir que um técnico encontre os dispositivos pelos quais é responsável.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Usar um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os inícios rápidos [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo de dispositivo **MXChip IoT DevKit** a ser usado neste tutorial:

![Cinco dispositivos de sensor simulados](./media/tutorial-use-device-groups/simulated-devices.png)

Para quatro dos dispositivos de sensor simulados, use a exibição **Gerenciar dispositivo** para definir o nome do cliente como *Contoso*:

![Definir o nome do cliente como Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **Grupos de dispositivos** no painel esquerdo.

1. Selecione **+** :

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups/image1.png)

1. Dê o nome *Dispositivos Contoso* ao grupo de dispositivos. Também é possível adicionar uma descrição. Um grupo de dispositivos somente pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo **MXChip IoT DevKit** a ser usado para esse grupo.

1. Para personalizar o grupo de dispositivos e incluir somente os dispositivos pertencentes ao **Contoso**, selecione **+ Filtrar**. Selecione a propriedade **Nome do Cliente**, o operador de comparação **Igual** e **Contoso** como o valor. É possível adicionar vários filtros e dispositivos que atendam a **todos** os critérios de filtro colocados no grupo de dispositivos. O grupo de dispositivos que você criou fica acessível a qualquer pessoa que tenha acesso ao aplicativo, portanto qualquer pessoa pode exibir, modificar ou excluir o grupo de dispositivos:

    ![Consulta do grupo de dispositivos](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Cada vez que você visualizar a lista de dispositivos, poderá haver diferentes dispositivos na lista. A lista depende de quais dispositivos atualmente atendem aos critérios da consulta.

1. Selecione **Salvar**.

> [!NOTE]
> Para dispositivos do Azure IoT Edge, selecione modelos do Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Você pode usar a **Análise** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, você pode plotar a temperatura média relatada por todos os sensores ambientais da Contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **Análise** no painel esquerdo.

1. Selecione o grupo de dispositivos **Dispositivos Contoso** que você criou. Em seguida, adicione os tipos de telemetria **Temperatura** e **Umidade**:

    ![Criar análise](./media/tutorial-use-device-groups/create-analysis.png)

    Use os ícones de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Média**. Use **Dividir por** para alterar a forma como os dados agregados são mostrados. Por exemplo, se você dividir por ID do dispositivo, verá uma plotagem para cada dispositivo ao selecionar **Analisar**.

1. Selecione **Analisar** para exibir os valores médios da telemetria:

    ![Exibir análise](./media/tutorial-use-device-groups/view-analysis.png)

    Você pode personalizar a exibição, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar grupos de dispositivos no aplicativo do Azure IoT Central, veja a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)
