---
title: Gerencie a rede de computação no Azure Data Box Edge para acessar módulos| Microsoft Docs
description: Descreve como estender a rede de computação em sua Data Box Edge para acessar módulos através de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917229"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Habilite a rede de computação no azure Data Box Edge

Este artigo descreve como os módulos em execução no Azure Data Box Edge podem acessar a rede de computação habilitada no dispositivo.

Para configurar a rede, você tomará as seguintes etapas:

- Habilite uma interface de rede em seu dispositivo Data Box Edge para computação
- Adicione um módulo para acessar a rede de computação em sua Data Box Edge
- Verifique se o módulo pode acessar a interface de rede habilitada

Neste tutorial, você usará um módulo de aplicativo webserver para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você vai precisar:

- Um dispositivo Data Box Edge com a configuração do dispositivo concluída.
- Você completou **Configure a** etapa de computação de acordo com o [Tutorial: Transforme dados com o Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) em seu dispositivo. Seu dispositivo deve ter um recurso IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Habilite a interface de rede para computação

Para acessar os módulos em execução em seu dispositivo através de uma rede externa, você precisará atribuir um endereço IP a uma interface de rede em seu dispositivo. Você pode gerenciar essas configurações de computação por meio da IU da Web local.

Siga as seguintes etapas na IU da Web local para definir as configurações de computação.

1. Na IU da Web local, vá para **Configuração > Configurações de computação**.  

2. **Habilite** a interface de rede que você deseja usar para se conectar a um módulo de computação que você executará no dispositivo.

    - Se estiver usando endereços IP estáticos, insira um endereço IP para o adaptador de rede.
    - Se usar o DHCP, os endereços IP serão atribuídos automaticamente. Este exemplo usa DHCP.

    ![Habilitar configurações de computação 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecione **Aplicar** para aplicar as configurações. Anote o endereço IP atribuído à interface da rede se estiver usando DHCP.

    ![Habilitar configurações de computação](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo do servidor web

Tome as seguintes etapas para adicionar um módulo de aplicativo do servidor web no seu dispositivo Data Box Edge.

1. Vá para o recurso IoT Hub associado ao seu dispositivo Data Box Edge e, em seguida, selecione **dispositivo IoT Edge**.
2. Selecione o dispositivo IoT Edge associado ao dispositivo Data Box Edge. Nos **detalhes**do dispositivo, **selecione Definir módulos**. Em **Adicionar módulos,** selecione **+ Adicione** e, em seguida, selecione Módulo de Borda **IoT**.
3. Na lâmina de **módulos personalizados IoT Edge:**

    1. Especifique um **nome** para o módulo de aplicativo do servidor web que você deseja implantar.
    2. Forneça um **URI de imagem** para a imagem do módulo. Um módulo que corresponda ao nome e às tags fornecidos é recuperado. Neste caso, `nginx:stable` puxará uma imagem nginx estável (marcada como estável) do [repositório público do Docker](https://hub.docker.com/_/nginx/).
    3. Nas **opções de criação de contêiner,** cole o seguinte código de amostra:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Essa configuração permite que você acesse o módulo usando o IP da rede de computação através *do http* na porta TCP 8080 (com a porta padrão do servidor web sendo 80).

        ![Especificar informações da porta na lâmina do módulo personalizado IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selecione **Salvar**.

## <a name="verify-module-access"></a>Verificar o acesso ao módulo

1. Verifique se o módulo foi implantado com sucesso e está sendo executado. Na página Detalhes do **dispositivo,** na guia **Módulos,** o status de tempo de execução do módulo deve estar **em execução**.  
2. Conecte-se ao módulo do aplicativo do servidor web. Abra uma janela e digite um navegador:

    `http://<compute-network-IP-address>:8080`

    Você deve ver que o aplicativo do servidor web está sendo executado.

    ![Verifique a conexão com o módulo sobre a porta especificada](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](data-box-edge-manage-users.md).
