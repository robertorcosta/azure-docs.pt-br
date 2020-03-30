---
title: Solucionar problemas de latência usando logs da Análise de Armazenamento
description: Identifique e soluciona problemas de latência usando logs do Azure Storage Analytic e otimize o aplicativo cliente.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196515"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Solucionar problemas de latência usando logs da Análise de Armazenamento

Diagnosticar e solucionar problemas é uma habilidade fundamental para construir e apoiar aplicativos de clientes com o Azure Storage.

Devido à natureza distribuída de um aplicativo Azure, diagnosticar e solucionar problemas de erros e desempenho pode ser mais complexo do que em ambientes tradicionais.

As etapas a seguir demonstram como identificar e solucionar problemas de latência usando logs do Azure Storage Analytic e otimizar o aplicativo cliente.

## <a name="recommended-steps"></a>Etapas recomendadas

1. Baixe os [registros do Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Use o seguinte script PowerShell para converter os logs de formato bruto em formato tabular:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. O script iniciará uma janela de GUI onde você pode filtrar as informações por colunas, conforme mostrado abaixo.

   ![Janela do analisador de log de armazenamento](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Reduza as entradas de registro com base em "tipo de operação" e procure a entrada de registro criada durante o período de tempo do problema.

   ![Entradas de log tipo operação](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Durante o período em que ocorreu o problema, os seguintes valores são importantes:

   * Tipo de operação = GetBlob
   * status de solicitação = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Servidor-Latência-em-Ms = 391

   A latência de ponta a ponta é calculada usando a seguinte equação:

   * Latência de ponta a ponta = Latência do servidor + latência do cliente

   Calcular a Latência do Cliente usando a entrada de log:

   * Latência do cliente = Latência de ponta a ponta - Latência do servidor

          * Example: 8453 – 391 = 8062ms

   A tabela a seguir fornece informações sobre os resultados do OperationType e requestStatus de alta latência:

   |   |Status de solicitação=<br>Sucesso|Status de solicitação=<br>(SAS) Erro de rede|Recomendação|
   |---|---|---|---|
   |GetBlob|Sim|Não|[**Operação GetBlob:** Status de solicitação = Sucesso](#getblob-operation-requeststatus--success)|
   |GetBlob|Não|Sim|[**Operação GetBlob:** Status de solicitação = (SAS)Erro de rede](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Sim|Não|[**Operação de colocação:** Status de solicitação = Sucesso](#put-operation-requeststatus--success)|
   |PutBlob|Não|Sim|[**Operação de colocação:** Status de solicitação = (SAS)Erro de rede](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Resultados de status

### <a name="getblob-operation-requeststatus--success"></a>Operação GetBlob: RequestStatus = Sucesso

Verifique os valores a seguir mencionados na etapa 5 da seção "Etapas recomendadas":

* Latência de ponta a ponta
* Latência do servidor
* Latência cliente-cliente

Em uma **operação GetBlob** com **RequestStatus = Sucesso**, se Max **Time** for gasto em **Latência cliente,** isso indica que o Azure Storage está gastando um grande volume de tempo escrevendo dados para o cliente. Esse atraso indica um problema do lado do cliente.

**Recomendação:**

* Investigue o código do seu cliente.
* Use Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operação GetBlob: Status de solicitação = (SAS)Erro de rede

Verifique os valores a seguir mencionados na etapa 5 da seção "Etapas recomendadas":

* Latência de ponta a ponta
* Latência do servidor
* Latência cliente-cliente

Em uma **operação GetBlob** com **RequestStatus = (SAS)NetworkError**, se **Max Time** for gasto em **Latência cliente,** o problema mais comum é que o cliente está desconectando antes que um tempo expirar no serviço de armazenamento.

**Recomendação:**

* Investigue o código no cliente para entender porque e quando o cliente desconecta do serviço de armazenamento.
* Use Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--success"></a>Operação de colocar: RequestStatus = Sucesso

Verifique os valores a seguir mencionados na etapa 5 da seção "Etapas recomendadas":

* Latência de ponta a ponta
* Latência do servidor
* Latência cliente-cliente

Em uma **Operação de Put** com **RequestStatus = Sucesso**, se Max **Time** for gasto em **Latência cliente,** isso indica que o Cliente está levando mais tempo para enviar dados para o Armazenamento Azure. Esse atraso indica um problema do lado do cliente.

**Recomendação:**

* Investigue o código do seu cliente.
* Use Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operação de colocar: RequestStatus = (SAS)NetworkError

Verifique os valores a seguir mencionados na etapa 5 da seção "Etapas recomendadas":

* Latência de ponta a ponta
* Latência do servidor
* Latência cliente-cliente

Em uma **operação PutBlob** com **RequestStatus = (SAS)NetworkError**, se **Max Time** for gasto em **Latência cliente,** o problema mais comum é que o cliente está desconectando antes que um tempo expirar no serviço de armazenamento.

**Recomendação:**

* Investigue o código no cliente para entender porque e quando o cliente desconecta do serviço de armazenamento.
* Use Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente.

