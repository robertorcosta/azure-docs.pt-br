---
title: IoT implanta dispositivos simulados personalizados - Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar dispositivos simulados personalizados no acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "66427575"
---
# <a name="deploy-a-new-simulated-device"></a>Implantar um novo dispositivo simulado

Ambos os aceleradores de solução de Simulação de Dispositivo e Monitoramento Remoto permitem definir seus próprios dispositivos simulados. Este artigo mostra como implantar um tipo de dispositivo de resfriador personalizado e um novo tipo de dispositivo de lâmpada no acelerador de solução de Monitoramento Remoto.

As etapas neste artigo assumem que você concluiu o guia de instruções [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) e tem os arquivos que definem o resfriador e novos tipos de dispositivos de lâmpada personalizados.

As etapas deste guia de instruções mostram como:

1. Use o SSH para acessar o sistema de arquivos da máquina virtual que hospeda o acelerador de solução de Monitoramento Remoto.

1. Configure o Docker para carregar os modelos de dispositivos de um local fora do contêiner do Docker.

1. Execute o acelerador de solução de Monitoramento Remoto usando arquivos de modelo de dispositivo personalizado.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- Uma instância implementada do [acelerador de solução de Monitoramento Remoto](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Um shell de **bash** para executar os comandos `ssh` e `scp`. No Windows, uma maneira fácil de instalar o **bash** é instalar o [git](https://git-scm.com/download/win).
- Os arquivos de modelo de dispositivo personalizado, como os descritos em [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurar o Docker

Nesta seção, você configura o Docker para carregar os arquivos de modelo de dispositivo da pasta **/tmp/devicemodels** na máquina virtual em vez de dentro do contêiner do Docker. Execute os comandos nesta seção em um shell de **bash** no computador local:

Nesta seção, você configura o Docker para carregar os arquivos de modelo de dispositivo da pasta **/tmp/devicemodels** na máquina virtual em vez de dentro do contêiner do Docker. Execute os comandos nesta seção em um shell de **bash** no computador local:

1. Use o SSH para conectar a máquina virtual no Azure a partir da máquina local. O comando a seguir assume que o endereço IP público da máquina virtual **vm-vikxv** é **104.41.128.108** -- substitua esse valor pelo endereço IP público da máquina virtual da seção anterior:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Siga as instruções para entrar na máquina virtual com a senha que você definiu na seção anterior.

1. Configure o serviço de simulação de dispositivo para carregar os modelos do dispositivo de fora do contêiner. Primeiro, abra o arquivo de configuração do Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localize as configurações do contêiner **devicesimulation** e edite a configuração de **volumes**, conforme mostrado no snippet a seguir:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Salve as alterações.

1. Copie os arquivos de modelo de dispositivo existentes do contêiner para o novo local. Primeiro, encontre a ID do contêiner para o contêiner de simulação de dispositivo:

    ```sh
    sudo docker ps
    ```

    Em seguida, copie os arquivos de modelo de dispositivo para a pasta **tmp** na máquina virtual. O comando a seguir supõe que a ID do contêiner é c378d6878407 -- substitua esse valor pela ID do contêiner de simulação do dispositivo:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Mantenha a janela de **bash** com a sessão SSH aberta.

1. Copie os arquivos de modelo de dispositivo personalizado na máquina virtual. Execute esse comando em outro shell de **bash** na máquina em que você criou os modelos de dispositivo personalizado. Primeiro, navegue até a pasta local que contém os arquivos JSON do modelo do dispositivo. Os seguintes comandos assumem que o endereço IP público da máquina virtual é **104.41.128.108** -- substitua esse valor pelo endereço IP público da máquina virtual. Quando solicitado, insira a senha da máquina virtual:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Reinicie o contêiner do Docker de simulação de dispositivo para usar os novos modelos de dispositivo. Execute os seguintes comandos no shell de **bash** com a sessão SSH aberta para a máquina virtual:

    ```sh
    sudo /app/start.sh
    ```

    Se você quiser ver o status dos contêineres do Docker em execução e as IDs do contêiner, use o comando a seguir:

    ```sh
    sudo docker ps
    ```

    Se você quiser ver o log do contêiner de simulação de dispositivo, execute o seguinte comando. Substitua a ID do contêiner pela ID do contêiner de simulação de dispositivo:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Executar simulação

Agora, é possível usar os modelos de dispositivo personalizado na solução de Monitoramento Remoto:

1. Inicialize o painel de Monitoramento Remoto dos [Aceleradores de Solução de IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Use a página **Dispositivos** para adicionar dispositivos simulados. Quando você adicionar um novo dispositivo simulado, seus novos modelos de dispositivos estarão disponíveis para escolha.

1. É possível usar o painel para exibir a telemetria do dispositivo e chamar os métodos de dispositivo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja explorar mais, deixe o acelerador de solução de Monitoramento Remoto implantado.

Se você não precisar mais do Solution Accelerator, exclua-o da página [soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) , selecionando-o e, em seguida, clicando em **excluir solução**.

## <a name="next-steps"></a>Próximas etapas

Este guia mostrou como implantar modelos de dispositivos personalizados no acelerador de solução de Monitoramento Remoto. A próxima etapa sugerida é saber como [conectar um dispositivo real à solução de Monitoramento Remoto](iot-accelerators-connecting-devices-node.md).
