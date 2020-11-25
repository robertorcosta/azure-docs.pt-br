---
title: incluir arquivo
description: incluir arquivo
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e3eed0e7378f513e45e0eb9ac719a92e87ec1d53
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025584"
---
## <a name="access-the-virtual-machine"></a>Acessar a máquina virtual

As etapas a seguir usam o CLI do Azure no Azure Cloud Shell. Se preferir, você pode [instalar o CLI do Azure](/cli/azure/install-azure-cli) em seu computador de desenvolvimento e executar os comandos localmente.

As etapas a seguir mostram como configurar a máquina virtual do Azure para permitir o acesso de **SSH**. As etapas mostradas levam em conta que o nome escolhido para o acelerador de solução é **contoso-simulation** -- substitua esse valor pelo nome da sua implantação:

1. Liste o conteúdo do grupo de recursos que contém os recursos do acelerador de solução:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anote o nome da máquina virtual, o endereço IP público e o grupo de segurança de rede - você precisará desses valores mais tarde.

1. Atualize o grupo de segurança de rede para permitir o acesso de SSH. O comando a seguir pressupõe que o nome do grupo de segurança de rede seja **contoso-simulation-nsg** - substitua esse valor pelo nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Habilite somente o acesso de SSH durante o desenvolvimento e teste. Se você habilitar o SSH, [deverá desabilitá-lo novamente](../articles/security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines)assim que possível.

1. Atualize a senha para a conta **azureuser** na máquina virtual para uma senha de seu conhecimento. Escolha sua própria senha quando executar o comando a seguir:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Localize o endereço IP público de sua máquina virtual. O comando a seguir pressupõe que o nome da máquina virtual seja **vikxv vm** -- substitua esse valor pelo nome da máquina virtual que você anotou anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anote endereço IP público de sua máquina virtual.