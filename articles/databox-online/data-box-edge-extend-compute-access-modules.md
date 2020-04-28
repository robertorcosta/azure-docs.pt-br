---
title: Gerenciar rede de computação em Azure Data Box Edge para acessar módulos | Microsoft Docs
description: Descreve como estender a rede de computação em seu Data Box Edge para acessar módulos por meio de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "65917229"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Habilite a rede de computação em seu Azure Data Box Edge

Este artigo descreve como os módulos em execução no seu Azure Data Box Edge podem acessar a rede de computação habilitada no dispositivo.

Para configurar a rede, você executará as seguintes etapas:

- Habilitar uma interface de rede em seu dispositivo Data Box Edge para computação
- Adicionar um módulo para acessar a rede de computação em seu Data Box Edge
- Verificar se o módulo pode acessar a interface de rede habilitada

Neste tutorial, você usará um módulo de aplicativo do WebServer para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

- Um dispositivo Data Box Edge com a instalação do dispositivo foi concluído.
- Você concluiu a etapa de **configuração de computação** de acordo [com o tutorial: transformar dados com Azure data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) em seu dispositivo. Seu dispositivo deve ter um recurso de Hub IoT associado, um dispositivo IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Habilitar interface de rede para computação

Para acessar os módulos em execução em seu dispositivo por meio de uma rede externa, você precisará atribuir um endereço IP a uma interface de rede em seu dispositivo. Você pode gerenciar essas configurações de computação por meio da IU da Web local.

Siga as seguintes etapas na IU da Web local para definir as configurações de computação.

1. Na IU da Web local, vá para **Configuração > Configurações de computação**.  

2. **Habilite** a interface de rede que você deseja usar para se conectar a um módulo de computação que será executado no dispositivo.

    - Se estiver usando endereços IP estáticos, insira um endereço IP para o adaptador de rede.
    - Se estiver usando DHCP, os endereços IP serão atribuídos automaticamente. Este exemplo usa DHCP.

    ![Habilitar configurações de computação 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecione **Aplicar** para aplicar as configurações. Anote o endereço IP atribuído à interface de rede se estiver usando DHCP.

    ![Habilitar configurações de computação](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo do webserver

Execute as etapas a seguir para adicionar um módulo de aplicativo do servidor WebServer em seu dispositivo Data Box Edge.

1. Vá para o recurso do Hub IoT associado ao dispositivo Data Box Edge e, em seguida, selecione **IOT Edge dispositivo**.
2. Selecione o dispositivo IoT Edge associado ao dispositivo Data Box Edge. Nos **detalhes do dispositivo**, selecione **definir módulos**. Em **Adicionar módulos**, selecione **+ Adicionar** e, em seguida, selecione **IOT Edge módulo**.
3. Na folha **módulos personalizados IOT Edge** :

    1. Especifique um **nome** para o módulo de aplicativo do servidor da WebServer que você deseja implantar.
    2. Forneça um **URI de imagem** para a imagem do módulo. Um módulo que corresponde ao nome e às marcas fornecidas é recuperado. Nesse caso, `nginx:stable` o receberá uma imagem Nginx estável (marcada como estável) do repositório público do [Docker](https://hub.docker.com/_/nginx/).
    3. Nas **Opções de criação do contêiner**, Cole o seguinte código de exemplo:  

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

        Essa configuração permite que você acesse o módulo usando o IP de rede de computação sobre *http* na porta TCP 8080 (com a porta padrão do webserver sendo 80).

        ![Especificar informações de porta na folha IoT Edge módulo personalizado](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Clique em **Salvar**.

## <a name="verify-module-access"></a>Verificar o acesso ao módulo

1. Verifique se o módulo foi implantado com êxito e se está em execução. Na página **detalhes do dispositivo** , na guia **módulos** , o status do tempo de execução do módulo deve estar **em execução**.  
2. Conecte-se ao módulo de aplicativo do servidor Web. Abra uma janela do navegador e digite:

    `http://<compute-network-IP-address>:8080`

    Você deve ver que o aplicativo WebServer está em execução.

    ![Verificar conexão com o módulo pela porta especificada](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](data-box-edge-manage-users.md).
