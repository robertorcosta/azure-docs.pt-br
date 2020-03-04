---
title: Gerenciar a rede de computação na borda de caixa de dados do Azure para módulos de acesso | Microsoft Docs
description: Descreve como estender a rede de computação na borda da caixa de dados para acessar os módulos por meio de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917229"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Habilitar a rede de computação na borda da caixa de dados do Azure

Este artigo descreve como os módulos em execução na borda da caixa de dados do Azure podem acessar a rede de computação habilitado no dispositivo.

Para configurar a rede, você seguirá as etapas a seguir:

- Habilitar uma interface de rede em seu dispositivo de borda da caixa de dados para a computação
- Adicionar um módulo a rede de computação de acesso na borda da caixa de dados
- Verifique se que o módulo pode acessar a interface de rede habilitada

Neste tutorial, você usará um módulo de aplicativo do servidor Web para demonstrar o cenário.

## <a name="prerequisites"></a>Prerequisites

Antes de começar, você precisará:

- Um dispositivo de borda da caixa de dados com a instalação de dispositivo foi concluída.
- Você concluiu **configurar computação** etapa de acordo o [Tutorial: Transformar dados com a caixa de dados do Azure Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) em seu dispositivo. O dispositivo deverá ter um recurso de IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Habilitar a interface de rede para computação

Para acessar os módulos em execução no seu dispositivo por meio de uma rede externa, você precisará atribuir um endereço IP a um adaptador de rede em seu dispositivo. Você pode gerenciar essas configurações de computação por meio da IU da Web local.

Siga as seguintes etapas na IU da Web local para definir as configurações de computação.

1. Na IU da Web local, vá para **Configuração > Configurações de computação**.  

2. **Habilitar** a interface de rede que você deseja usar para se conectar a um módulo de computação que serão executados no dispositivo.

    - Se estiver usando endereços IP estáticos, insira um endereço IP para o adaptador de rede.
    - Se usar o DHCP, os endereços IP são atribuídos automaticamente. Este exemplo usa o DHCP.

    ![Habilitar configurações de computação 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecione **Aplicar** para aplicar as configurações. Anote o endereço IP atribuído ao adaptador de rede se usar o DHCP.

    ![Habilitar configurações de computação](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar o módulo de aplicativo do servidor Web

Execute as seguintes etapas para adicionar um módulo de aplicativo do servidor Web em seu dispositivo de borda da caixa de dados.

1. Vá para o recurso de IoT Hub associado a seu dispositivo de borda da caixa de dados e, em seguida, selecione **dispositivo IoT Edge**.
2. Selecione o dispositivo do IoT Edge associado ao seu dispositivo de borda da caixa de dados. Sobre o **detalhes do dispositivo**, selecione **definir módulos**. Na **Adicionar módulos**, selecione **+ adicionar** e, em seguida, selecione **módulo do IoT Edge**.
3. No **módulos personalizados do IoT Edge** folha:

    1. Especifique um **nome** para o seu módulo de aplicativo do servidor Web que você deseja implantar.
    2. Fornecer um **URI da imagem** para sua imagem de módulo. Um módulo, o nome fornecido e marcas correspondentes é recuperado. Nesse caso, `nginx:stable` efetuará pull a uma imagem de nginx estável (marcada como estável) do público [repositório Docker](https://hub.docker.com/_/nginx/).
    3. No **opções de criar contêiner**, cole o código de exemplo a seguir:  

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

        Essa configuração permite que você acesse o módulo usando o IP de rede da computação pela *http* na porta TCP 8080 (com a porta do servidor Web padrão que está sendo 80).

        ![Especificar informações de porta na folha de módulo personalizado do IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Clique em **Salvar**.

## <a name="verify-module-access"></a>Verificar o acesso de módulo

1. Verifique se o módulo é implantado com êxito e está em execução. Sobre o **detalhes do dispositivo** página, na **módulos** guia, o status de tempo de execução do módulo deve ser **executando**.  
2. Conecte-se ao módulo de aplicativo de servidor web. Abra uma janela do navegador e digite:

    `http://<compute-network-IP-address>:8080`

    Você deve ver que o aplicativo de servidor Web está em execução.

    ![Verifique se a conexão ao módulo pela porta especificada](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](data-box-edge-manage-users.md).
