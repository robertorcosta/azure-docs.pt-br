---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876596"
---
### <a name="add-relationships"></a>Adicionar relações

No modelo de dispositivo **Gateway de Borda de LVA**, em **Módulos/Módulo do Gateway de Borda de LVA**, selecione **Relações**. Selecione **+ Adicionar relação** e adicione as duas seguintes relações:

|Nome de exibição               |Nome          |Destino |
|-------------------------- |------------- |------ |
|Detector de Movimentos de Borda de LVA   |Usar padrão   |Dispositivo de Detecção de Movimentos de Borda de LVA |
|Detector de Objetos de Borda de LVA   |Usar padrão   |Dispositivo de Detecção de Objetos de Borda de LVA |

Em seguida, selecione **Salvar**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Adicionar relações":::

### <a name="add-views"></a>Adicionar exibições

O modelo de dispositivo **Gateway de Borda de LVA** não inclui nenhuma definição de exibição.

Para adicionar uma exibição ao modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA**, procure **Exibições** e selecione o bloco **Visualização do dispositivo**.

1. Insira *Dispositivo de Gateway de Borda de LVA* como o nome de exibição.

1. Adicione os seguintes blocos à exibição:

    * Um bloco com as propriedades **Informações do Dispositivo**: **Modelo do dispositivo**, **Fabricante**, **Sistema operacional**, **Arquitetura do processador**, **Versão do software**, **Memória total** e **Armazenamento total**.
    * Um bloco de gráfico de linhas com os valores de telemetria de **Memória Livre** e **Pulsação do Sistema**.
    * Um bloco de histórico de eventos com os seguintes eventos: **Criar Câmera**, **Excluir Câmera**, **Reinicialização do Módulo**, **Módulo Iniciado** e **Módulo Interrompido**.
    * Um bloco 2x1 do último valor conhecido mostrando a telemetria de **Estado do Cliente do IoT Central**.
    * Um bloco 2x1 do último valor conhecido mostrando a telemetria de **Estado do Módulo**.
    * Um bloco 1x1 do último valor conhecido mostrando a telemetria de **Pulsação do Sistema**.
    * Um bloco 1x1 do último valor conhecido mostrando a telemetria de **Câmeras Conectadas**.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Adicionar relações":::

1. Clique em **Salvar**.

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para adicionar um dispositivo ao aplicativo, publique o modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA**, selecione **Publicar**.

1. Na página **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

O **Gateway de Borda de LVA** agora está disponível como o tipo de dispositivo a ser usado na página **Dispositivos** no aplicativo.

## <a name="add-a-gateway-device"></a>Adicionar um dispositivo de gateway

Para adicionar um dispositivo **Gateway de Borda de LVA** ao aplicativo:

1. Procure a página **Dispositivos** e selecione o modelo de dispositivo **Gateway de Borda de LVA**.

1. Selecione **+ Novo**.

1. Na caixa de diálogo **Criar um dispositivo**, altere o nome do dispositivo para *Gateway de LVA 001* e a identificação do dispositivo para *lva-gateway-001*.

    > [!NOTE]
    > A identificação do dispositivo precisa ser exclusiva no aplicativo.

1. Selecione **Criar**.

O status do dispositivo é **Registrado**.

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Você precisará das credenciais que permitem que o dispositivo se conecte ao seu aplicativo do IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivos**, selecione o dispositivo **lva-gateway-001** criado.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote no arquivo *scratchpad.txt* o **Escopo da ID**, a **Identificação do Dispositivo** e a **Chave Primária** do dispositivo. Esses valores serão usados depois.

1. Verifique se o método de conexão está definido como **Assinatura de Acesso Compartilhado**.

1. Selecione **Fechar**.

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um aplicativo do IoT Central usando o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo** e um modelo de dispositivo para o dispositivo de gateway, além de adicionar um dispositivo de gateway ao aplicativo.

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam uma VM de nuvem com fluxos de vídeo simulados:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam um dispositivo real com uma câmera **ONVIF** real:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
