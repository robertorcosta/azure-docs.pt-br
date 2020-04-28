---
title: Gerenciamento de computação do Azure Data Box Edge | Microsoft Docs
description: Descreve como gerenciar as configurações de computação de borda, como gatilho, módulos, exibir a configuração de computação, remover a configuração por meio do portal do Azure no Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "65953130"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Gerenciar computação no Azure Data Box Edge

Este artigo descreve como gerenciar a computação em seu Azure Data Box Edge. É possível gerenciar a computação pelo portal do Azure ou pela IU da Web local. Use o portal do Azure para gerenciar módulos, gatilhos e configuração de computação e a IU da Web local para gerenciar configurações de computação.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar disparadores
> * Gerenciar configuração da computação


## <a name="manage-triggers"></a>Gerenciar disparadores

Os eventos são coisas que acontecem dentro de seu ambiente de nuvem ou em seu dispositivo no qual você pode executar uma ação. Por exemplo, quando um arquivo é criado em um compartilhamento, ele é um evento. Gatilhos acionam os eventos. Para o Data Box Edge, os gatilhos podem ser em resposta a eventos de arquivo ou a um agendamento.

- **Arquivo**: esses gatilhos estão em resposta a eventos de arquivo, como a criação de um arquivo, a modificação de um arquivo.
- **Agendado**: esses gatilhos estão em resposta a uma agenda que você pode definir com uma data de início, uma hora de início e o intervalo de repetição.


### <a name="add-a-trigger"></a>Adicionar um gatilho

Siga estas etapas no portal do Azure para criar um gatilho.

1. No portal do Azure, acesse seu recurso do Data Box Edge e acesse **Computação de borda > Gatilho**. Selecione **+ Adicionar gatilho** na barra de comandos.

    ![Selecione adicionar gatilho](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Na folha **Adicionar gatilho**, forneça um nome exclusivo para o gatilho.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **Tipo** para o gatilho. Escolha **Arquivo** quando o gatilho for em resposta a um evento de arquivo. Selecione **Agendado** quando desejar que o gatilho inicie em um horário definido e seja executado em um intervalo de repetição especificado. Dependendo da sua seleção, um conjunto de opções diferente é apresentado.

    - **Gatilho de arquivo** – escolha na lista suspensa de um compartilhamento montado. Quando um evento de arquivo é acionado nesse compartilhamento, o gatilho invocaria uma função do Azure.

        ![Adicionar compartilhamento SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Gatilho agendado** – especifique a data/hora de início e o intervalo de repetição em horas, minutos ou segundos. Além disso, insira o nome de um tópico. Um tópico oferecerá a você a flexibilidade de rotear o gatilho para um módulo implantado no dispositivo.

        Um exemplo de cadeia de caracteres de rota é: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Adicionar compartilhamento NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Selecione **Adicionar** para criar o gatilho. Uma notificação mostra que a criação do gatilho está em andamento. Depois que o gatilho é criado, a folha é atualizada para refletir o novo gatilho.
 
    ![Lista de gatilhos atualizada](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Excluir um gatilho

Siga estas etapas no portal do Azure para excluir um gatilho.

1. Na lista de gatilhos, selecione o gatilho que você deseja excluir.

    ![Selecionar gatilho](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Clique com o botão direito do mouse e selecione **Excluir**.

    ![Selecione excluir](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Quando solicitada a confirmação, clique em **Sim**.

    ![Confirmar exclusão](media/data-box-edge-manage-compute/add-trigger-1.png)

A lista de gatilhos também é atualizada para refletir a exclusão.

## <a name="manage-compute-configuration"></a>Gerenciar configuração da computação

Use o portal do Azure para exibir a configuração de computação, para remover a configuração de computação existente ou para atualizar a configuração de computação para sincronizar as chaves de acesso do dispositivo IoT e do IoT Edge para seu Data Box Edge.

### <a name="view-compute-configuration"></a>Exibir configuração de computação

Siga estas etapas no portal do Azure para exibir a configuração de computação para seu dispositivo.

1. No portal do Azure, acesse o recurso do Data Box Edge e **Computação de borda > Módulos**. Selecione **Exibir computação** na barra de comandos.

    ![Selecione Exibir computação](media/data-box-edge-manage-compute/view-compute-1.png)

2. Anote a configuração de computação em seu dispositivo. Quando você tiver configurado a computação, você terá criado um recurso de hub IoT. Nesse recurso de hub IoT, um dispositivo IoT e um dispositivo IoT Edge são configurados. Há suporte apenas para a execução dos módulos do Linux no dispositivo IoT Edge.

    ![Exibir configuração](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Remover configuração da computação

Siga estas etapas no portal do Azure para remover a configuração de computação de borda existente para seu dispositivo.

1. No portal do Azure, acesse seu recurso do Data Box Edge e acesse **Computação de borda > Introdução**. Selecione **Remover computação** na barra de comandos.

    ![Selecione Remover computação](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Se você remover a configuração de computação, será necessário reconfigurar o dispositivo caso seja necessário usar a computação novamente. Quando solicitado a confirmar, selecione **Sim**.

    ![Selecione Remover computação](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sincronizar as chaves de acesso do dispositivo IoT e IoT Edge

Quando você configura a computação no Data Box Edge, um dispositivo IoT e um dispositivo IoT Edge são criados. Esses dispositivos recebem chaves de acesso simétricas automaticamente. Como uma melhor prática de segurança, essas chaves são trocadas regularmente por meio do serviço de Hub IoT.

Para trocar essas chaves, é possível acessar o serviço de Hub IoT que você criou e selecione o dispositivo IoT ou IoT Edge. Cada dispositivo tem uma chave de acesso primária e uma secundária. Atribua a chave de acesso primária à chave de acesso secundária e gere novamente a chave de acesso primária.

Se as chaves do seu dispositivo IoT e IoT Edge tiverem sido trocadas, será necessário atualizar a configuração no Data Box Edge para obter as chaves de acesso mais recentes. A sincronização ajuda o dispositivo obter as chaves mais recentes para seu dispositivo IoT e IoT Edge. O Data Box Edge usa apenas as chaves de acesso primárias.

Siga estas etapas no portal do Azure para sincronizar as chaves de acesso para seu dispositivo.

1. No portal do Azure, acesse seu recurso do Data Box Edge e acesse **Computação de borda > Introdução**. Selecione **Atualizar configuração** na barra de comandos.

    ![Selecione Atualizar configuração](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando solicitada a confirmação.

     ![Selecione Sim quando solicitado](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois que a sincronização tiver sido concluída.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar a rede de computação de borda por meio de portal do Azure](data-box-edge-extend-compute-access-modules.md).
