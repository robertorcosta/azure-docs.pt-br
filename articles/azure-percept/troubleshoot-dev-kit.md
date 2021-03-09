---
title: Solucionar problemas gerais com o Azure Percept DK e IoT Edge
description: Obtenha dicas de solução para alguns dos problemas mais comuns encontrados durante a experiência de integração
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a6d099e8d267c9fe03e0bb676276e7a4ab8157ab
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521519"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Solução de problemas do Azure Percept DK (Kit de desenvolvimento)

Consulte as diretrizes abaixo para obter dicas de solução de problemas gerais para o Azure Percept DK.

## <a name="general-troubleshooting-commands"></a>Comandos gerais de solução de problemas

Para executar esses comandos, 
1. Conectar-se ao [Wi-Fi AP do kit de desenvolvimento](./quickstart-percept-dk-set-up.md)
1. [SSH no kit de desenvolvimento](./how-to-ssh-into-percept-dk.md)
1. Insira os comandos no terminal SSH

Para redirecionar qualquer saída para um arquivo. txt para análise adicional, use a seguinte sintaxe:

```console
[command] > [file name].txt
```

Depois de redirecionar a saída para um arquivo. txt, copie o arquivo para o computador host por meio do SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` refere-se ao local no computador host para o qual você gostaria de copiar o arquivo. txt. ```[remote username]``` é o nome de usuário SSH escolhido durante a [experiência de instalação](./quickstart-percept-dk-set-up.md). Se você não configurou um logon SSH durante o OOBE, seu nome de usuário remoto é ```root``` .

Para obter informações adicionais sobre os comandos de Azure IoT Edge, consulte a [documentação de solução de problemas de dispositivo Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

|Categoria:         |Comando:                    |Função:                  |
|------------------|----------------------------|---------------------------|
|Sistema operacional                |```cat /etc/os-release```         |verificar a versão da imagem Mariner |
|Sistema operacional                |```cat /etc/os-subrelease```      |verificar versão da imagem derivada |
|Sistema operacional                |```cat /etc/adu-version```        |verificar a versão do ADU |
|Temperatura       |```cat /sys/class/thermal/thermal_zone0/temp``` |verificar temperatura de devkit |
|Wi-Fi             |```journalctl -u hostapd.service``` |verificar logs do SoftAP|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |verificar logs de serviços Wi-Fis |
|Wi-Fi             |```journalctl -u ztpd.service```  |verificar Wi-Fi os logs do serviço de provisionamento de toque zero |
|Wi-Fi             |```journalctl -u systemd-networkd``` |verificar logs da pilha de rede Mariner |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |verificar detalhes de configuração do ponto de acesso WiFi |
|OOBE              |```journalctl -u oobe -b```       |verificar logs do OOBE |
|Telemetria         |```azure-device-health-id```      |Localizar HW_ID de telemetria exclusiva |
|Azure IoT Edge          |```sudo iotedge check```          |executar verificações de configuração e conectividade para problemas comuns |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |verificar logs de contêiner, como módulos de fala e de visão |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |coletar logs de módulo, Azure IoT Edge logs do Security Manager, logs do mecanismo de contêiner, ```iotedge check``` saída JSON e outras informações de depuração úteis da última hora |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |exibir os logs do Gerenciador de segurança do Azure IoT Edge |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |reiniciar o daemon de segurança do Azure IoT Edge |
|Azure IoT Edge          |```sudo iotedge list```           |listar os módulos de Azure IoT Edge implantados |
|Outro             |```df [option] [file]```          |exibir informações sobre o espaço disponível/total em sistema (es) de arquivos especificado (s) |
|Outro             |```ip route get 1.1.1.1```        |exibir informações de IP e de interface do dispositivo |
|Outro             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |exibir somente endereço IP do dispositivo |


Os ```journalctl``` comandos Wi-Fi podem ser combinados no seguinte comando único:

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Comandos de solução de problemas do Docker

|Comando:                        |Função:                  |
|--------------------------------|---------------------------|
|```docker ps``` |[mostra quais contêineres estão em execução](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[mostra quais imagens estão no dispositivo](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[exclui uma imagem do dispositivo](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[usa logs de contêiner do módulo especificado](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[Remove todas as imagens pendente](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |verificar o status de download do contêiner do Docker |

## <a name="usb-updating"></a>Atualização de USB

|Erro:                                    |Solução:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX durante o flash USB via UUU |Esse erro é o resultado de uma falha de conexão USB durante a atualização do UUU. Se o cabo USB não estiver conectado corretamente às portas USB no PC ou no PE-10X, ocorrerá um erro desse formulário. Tente desconectar e reconectar ambas as extremidades do cabo USB e jiggling o cabo para garantir uma conexão segura. Isso quase sempre resolve o problema. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Estados do LED da placa da operadora do Azure Percept DK

Há três LEDs pequenos na parte superior do invólucro da placa da operadora. Um ícone de nuvem é impresso ao lado do LED 1, um ícone de Wi-Fi é impresso ao lado do LED 2, e um ícone de ponto de exclamação é impresso ao lado do LED 3. Consulte a tabela abaixo para obter informações sobre cada Estado de LED.

|LED             |Estado      |Descrição                      |
|----------------|-----------|---------------------------------|
|LED 1 (Hub IoT) |Ligado (sólido) |O dispositivo está conectado a um hub IoT. |
|LED 2 (Wi-Fi)   |Piscar lentamente |O dispositivo está pronto para ser configurado pelo Wi-Fi Easy Connect e está anunciando sua presença para um configurador. |
|LED 2 (Wi-Fi)   |Piscar rapidamente |A autenticação foi bem-sucedida, a associação de dispositivo está em andamento. |
|LED 2 (Wi-Fi)   |Ligado (sólido) |A autenticação e a Associação foram bem-sucedidas; o dispositivo está conectado a uma rede Wi-Fi. |
|LED 3           |NA         |O LED não está em uso. |


