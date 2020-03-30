---
title: Gerencie os modelos IoT Plug and Play Preview no repositório| Microsoft Docs
description: Como gerenciar modelos de capacidade do dispositivo no repositório usando o porta Azure Certified for IoT, o código Azure CLI e Visual Studio.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159142"
---
# <a name="manage-models-in-the-repository"></a>Gerenciar modelos no repositório

O repositório do modelo IoT Plug and Play Preview armazena modelos e interfaces de capacidade do dispositivo. O repositório torna os modelos e interfaces descobertos e consumíveis pelos desenvolvedores de soluções.

Existem três ferramentas que você pode usar para gerenciar o repositório:

- O portal Azure Certified for IoT
- A CLI do Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repositórios de modelos

Existem dois tipos de repositório de modelos para armazenar modelos e interfaces de capacidade do dispositivo:

- Existe um único _repositório público_ que armazena os modelos e interfaces de capacidade do dispositivo para dispositivos no [catálogo de dispositivos Azure Certified for IoT](https://aka.ms/iotdevcat). Este repositório também armazena [interfaces comuns](./concepts-common-interfaces.md) e [DCMs e interfaces publicadas pela Microsoft Partners](./howto-onboard-portal.md). Para saber como certificar um dispositivo e adicionar seu modelo de capacidade do dispositivo ao repositório público, consulte o tutorial [Certificar seu dispositivo IoT Plug and Play](./tutorial-certification-test.md).
- Existem vários _repositórios da Empresa._ Um repositório da empresa é criado automaticamente para sua organização quando você [a bordo do portal Azure Certified for IoT](./howto-onboard-portal.md). Você pode usar o repositório da sua empresa para armazenar os modelos e interfaces de capacidade do dispositivo durante o desenvolvimento e o teste.

## <a name="azure-certified-for-iot-portal"></a>Certificado Azure para Portal IoT

No [portal Azure Certified for IoT,](https://preview.catalog.azureiotsolutions.com)você pode concluir as seguintes tarefas:

- [Complete o processo de certificação do seu dispositivo IoT](./tutorial-certification-test.md).
- Encontre modelos de capacidade de dispositivo IoT Plug and Play. Você pode usar esses modelos para [criar rapidamente dispositivos prontos para IoT e integrá-los com soluções.](./quickstart-connect-pnp-device-solution-node.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI fornece comandos para gerenciar modelos e interfaces de capacidade de dispositivos nos repositórios de modelos públicos e de modelos ioT Plug and Play. Para obter mais informações, consulte a [instalação e use a extensão Azure IoT para guia de](./howto-install-pnp-cli.md) como fazer o Azure CLI.

## <a name="visual-studio-code"></a>Visual Studio Code

Para abrir a visualização **do Relomeu modelo** no Visual Studio Code.

1. Abra o Visual Studio Code, use **Ctrl+Shift+P,** digite e selecione **IoT Plug and Play: Open Model Repository**.

1. Você pode optar por **abrir repositório de modelos públicos** ou **repositório de modelo organizacional aberto.** Para o repositório de modelos da empresa, você precisa inserir sua seqüência de conexão de repositório de modelo. Você pode encontrar essa cadeia de conexão no [portal do Azure Certified para IoT** na guia **Cadeias de conexão** do seu **Repositório corporativo](https://preview.catalog.azureiotsolutions.com).

1. Uma nova guia abre a **exibição do Model Repository.**

    Use esta exibição para adicionar, baixar e excluir modelos e interfaces de capacidade do dispositivo. Você pode usar um filtro para encontrar itens específicos na lista.

1. Para alternar entre o repositório do modelo da empresa e o repositório de modelos públicos, use **Ctrl+Shift+P,** digite e selecione **IoT Plug and Play: Faça o Repositório do Modelo**. Em seguida, use o comando **IoT Plug and Play: Open Model Repository** novamente.

> [!NOTE]
> Em VS Code, o repositório de modelos públicos é somente leitura. Os Parceiros Microsoft podem atualizar o repositório público no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Próximas etapas

O próximo passo sugerido é aprender a [enviar um dispositivo IoT Plug and Play para certificação](tutorial-certification-test.md).
