---
title: 'Início Rápido: Criar alertas personalizados para a Central de Segurança do Azure para IoT'
description: Neste início rápido, crie e atribua alertas de dispositivo personalizados para a Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904132"
---
# <a name="quickstart-create-custom-alerts"></a>Início Rápido: Criar alertas personalizados


Usando alertas e grupos de segurança personalizados, aproveite ao máximo as informações de segurança de ponta a ponta e o conhecimento categórico do dispositivo para garantir a melhor segurança em solução de IoT. 

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados? 

Você conhece melhor seus dispositivos de IoT.

Para clientes que compreendem totalmente o comportamento esperado do seu dispositivo, a Central de Segurança do Azure para IoT permite traduzir esse reconhecimento em uma política de comportamento do dispositivo e alertar sobre qualquer desvio do comportamento normal e esperado.

## <a name="security-groups"></a>Grupos de segurança

Grupos de segurança permitem que você defina grupos lógicos de dispositivos e gerencie seu estado de segurança de forma centralizada.

Esses grupos podem representar dispositivos com o hardware específico, implantados em um determinado local, ou qualquer outro grupo adequado às suas necessidades específicas de dispositivos.

Grupos de segurança são definidos por uma propriedade de marca do dispositivo gêmeo denominada **SecurityGroup**. Por padrão, cada solução de IoT no Hub IoT tem um grupo de segurança chamado **padrão**. Altere o valor da propriedade **SecurityGroup** para alterar o grupo de segurança de um dispositivo.
 
Por exemplo:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Use grupos de segurança para agrupar seus dispositivos em categorias lógicas. Depois de criar os grupos, atribua-os para os alertas personalizados de sua escolha, para a solução de segurança da IoT de ponta a ponta mais eficaz. 

## <a name="customize-an-alert"></a>Personalizar um alerta

1. Abra seu Hub IoT. 
2. Clique em **Alertas personalizados** na seção **Segurança**. 
3. Escolha um grupo de segurança que você deseja aplicar a personalização. 
4. Clique em **Adicionar um alerta personalizado**.
5. Selecione um alerta personalizado na lista suspensa. 
6. Edite as propriedades necessárias, clique em **OK**.
7. Certifique-se de clicar em **Salvar**. Sem salvar o novo alerta, o alerta é excluído na próxima vez que você fechar o Hub IoT.

 
## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

A tabela a seguir fornece um resumo de alertas disponíveis para personalização.


| Severity | NOME | Fonte de dados | DESCRIÇÃO | Correção sugerida|
|---|---|---|---|---|
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para o dispositivo no protocolo AMQP está fora do intervalo permitido          | Hub IoT     | O número de mensagens de nuvem para dispositivo (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para o dispositivo rejeitadas no protocolo AMQP está fora do intervalo permitido | Hub IoT     | O número de mensagens de nuvem para dispositivo (protocolo AMQP) rejeitadas pelo dispositivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixo      | Alerta personalizado – o número de mensagens do dispositivo para a nuvem no protocolo AMQP está fora do intervalo permitido      | Hub IoT     | O número de mensagens do dispositivo para a nuvem (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|   |
| Baixo      | Alerta personalizado – o número de invocações de método direto está fora do intervalo permitido | Hub IoT     | O número de invocações de método direto em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixo      | Alerta personalizado – o número de uploads de arquivos está fora do intervalo permitido | Hub IoT     | O número de uploads de arquivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.| |
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para o dispositivo no protocolo HTTP está fora do intervalo permitido | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta Personalizado – o número de mensagens da nuvem para o dispositivo rejeitadas no protocolo HTTP está fora do intervalo permitido | Hub IoT     | O número de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo      | Alerta personalizado – o número de mensagens do dispositivo para a nuvem no protocolo HTTP está fora do intervalo permitido | Hub IoT| O número de mensagens do dispositivo para a nuvem (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|    |
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para o dispositivo no protocolo MQTT está fora do intervalo permitido | Hub IoT     | O número de mensagens da nuvem para o dispositivo (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|   |
| Baixo      | Alerta personalizado – o número de mensagens rejeitadas da nuvem para o dispositivo no protocolo MQTT está fora do intervalo permitido | Hub IoT     | O número de mensagens de nuvem para dispositivo (protocolo MQTT) rejeitadas pelo dispositivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo      | Alerta personalizado – o número de mensagens do dispositivo para a nuvem no protocolo MQTT está fora do intervalo permitido          | Hub IoT     | O número de mensagens do dispositivo para a nuvem (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixo      | Alerta personalizado – o número de limpezas de filas de comando está fora do intervalo permitido                               | Hub IoT     | O número de limpezas de fila de comando em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixo      | Alerta personalizado – o número de atualizações de módulos gêmeos está fora do intervalo permitido                                       | Hub IoT     | O número de atualizações de módulo gêmeo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixo      | Alerta personalizado – o número de operações não autorizadas está fora do intervalo permitido  | Hub IoT     | O número de operações não autorizadas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixo      | Alerta poersonalizado – o número de conexões ativas está fora do intervalo permitido  | Agente       | O número de conexões ativas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|  Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de conexões permitidas.  |
| Baixo      | Alerta personalizado – uma conexão de saída foi criada para um IP que não é permitido                             | Agente       | Uma conexão de saída foi criada para um IP que está fora da sua lista de IPs permitidos. |Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de IPs permitidos.                        |
| Baixo      | Alerta personalizado – o número de logons locais com falha está fora do intervalo permitido                               | Agente       | O número de logons locais com falha em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |   |
| Baixo      | Alerta personalizado – logon de um usuário que não está na lista de usuários permitidos | Agente       | Um usuário local fora da lista de usuários permitidos, conectado ao dispositivo.|  Se você estiver salvando dados brutos, navegue até sua conta da análise de logs e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações.|
| Baixo      | Alerta personalizado – um processo não permitido foi executado | Agente       | Um processo não permitido foi executado no dispositivo. |Se você estiver salvando dados brutos, navegue até sua conta da análise de logs e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações.  |
|


## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implantar um agente de segurança](how-to-deploy-agent.md)
