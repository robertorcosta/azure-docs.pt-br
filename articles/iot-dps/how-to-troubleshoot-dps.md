---
title: Diagnosticar e solucionar problemas se desconecta com o Azure IoT Hub DPS
description: Aprenda a diagnosticar e solucionar erros comuns com conectividade de dispositivos para O Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646465"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Solução de problemas com o serviço de provisionamento de dispositivos do Hub Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar, pois há muitos pontos possíveis de falhas, como falhas de atestado, falhas de registro etc. Este artigo fornece orientações sobre como detectar e solucionar problemas de conectividade de dispositivos através [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Usando o Monitor do Azure para visualizar métricas e configurar alertas

O procedimento a seguir descreve como visualizar e configurar alerta na métrica do Serviço de Provisionamento de Dispositivos IoT Hub. 

1. Faça login no [portal Azure](https://portal.azure.com).

2. Navegue pelo seu Serviço de Provisionamento de Dispositivos Hub IoT.

3. Selecione **Métricas**.

4. Selecione a métrica desejada. 
   <br />Atualmente existem três métricas para DPS:

    | Nome da métrica | Descrição |
    |-------|------------|
    | Tentativas de atestado | Número de dispositivos que tentaram autenticar com o Serviço de Provisionamento de Dispositivos|
    | Tentativas de Registro | Número de dispositivos que tentaram registrar-se no IoT Hub após autenticação bem sucedida|
    | Dispositivo atribuído | Número de dispositivos atribuídos com sucesso ao IoT Hub|

5. Selecione o método de agregação desejado para criar uma visão visual da métrica. 

6. Para configurar um alerta de uma métrica, selecione **Novas regras** de alerta no canto superior direito da lâmina métrica, da mesma forma, você pode ir para a lâmina **Alerta** e selecionar Novas regras **de alerta**.

7. Selecione **Adicionar condição**e selecione a métrica e o limiar desejados seguindo as instruções.

Para saber mais, veja [Quais são os alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Usando o Log Analytic para visualizar e resolver erros

1. Faça login no [portal Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **configurações de diagnóstico**.

4. Selecione **Ativar diagnóstico**.

5. Permitir que os registros desejados sejam coletados.

    | Nome do log | Descrição |
    |-------|------------|
    | DeviceOperations | Logs relacionados a eventos de conexão de dispositivos |
    | ServiceOperations | Registros de eventos relacionados ao uso do SDK de serviço (por exemplo, criar ou atualizar grupos de inscrição)|

6. Ativar **Enviar para Log Analytics** [(ver preços).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Vá para a guia **Logs** no portal Azure em recurso do Serviço de Provisionamento de Dispositivos.

8. Clique **em Correr** para ver eventos recentes.

9. Se houver resultados, `OperationName`procure `ResultType` `ResultSignature`, `ResultDescription` e (mensagem de erro) para obter mais detalhes sobre o erro.


## <a name="common-error-codes"></a>Códigos de erro comuns
Use esta tabela para entender e resolver erros comuns.

| Código do Erro| Descrição | Código de status HTTP |
|-------|------------|------------|
| 400 | O corpo da solicitação não é válido; por exemplo, ele não pode ser analisado ou o objeto não pode ser validado.| 400 Formato ruim |
| 401 | O token de autorização não pode ser validado; por exemplo, ele está expirado ou não se aplica ao URI da solicitação. Este código de erro também é devolvido aos dispositivos como parte do fluxo de atestado TPM. | 401 Não Autorizado|
| 404 | A instância do Serviço de Provisionamento de Dispositivos, ou um recurso (por exemplo, uma inscrição) não existe. |404 Não Encontrado |
| 412 | O ETag na solicitação não corresponde ao ETag do recurso existente, conforme RFC7232. | Falha na pré-condição 412 |
| 429 | As operações estão sendo estranguladas pelo serviço. Para limites de serviço específicos, consulte limites de [serviço de provisionamento de dispositivos do Hub IoT](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Muitos pedidos |
| 500 | Ocorreu um erro interno. | 500 Erro Interno do Servidor|
