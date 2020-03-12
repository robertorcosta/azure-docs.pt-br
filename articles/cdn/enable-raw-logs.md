---
title: Logs brutos de HTTP da CDN do Azure
description: Este artigo descreve os logs brutos de HTTP do Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2020
ms.author: allensu
ms.openlocfilehash: 79ced4df8df12bf2ef1fbe0075b53d02fafb2aff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129862"
---
# <a name="azure-cdn-http-raw-logs"></a>Logs brutos de HTTP da CDN do Azure
Os logs brutos fornecem informações avançadas sobre operações e erros que são importantes para auditoria e solução de problemas. Os logs brutos diferem dos logs de atividade. Os logs de atividade fornecem visibilidade das operações realizadas nos recursos do Azure. Os logs brutos fornecem um registro das operações do recurso.

> [!IMPORTANT]
> O recurso de logs brutos de HTTP está disponível para a CDN do Azure da Microsoft.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuração

Para configurar logs brutos para seu perfil do Azure CDN do Microsoft: 

1. No menu portal do Azure, selecione **todos os recursos** >>  **\<seu > de perfil-CDN**.

2. Em **monitoramento**, selecione **configurações de diagnóstico**.

3. Selecione **+ Adicionar configuração de diagnóstico**.

    ![Configuração de diagnóstico da CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Os logs brutos só estão disponíveis no nível do perfil enquanto os logs de código de status http agregados estão disponíveis no nível do ponto de extremidade.

4. Em **configurações de diagnóstico**, insira um nome para a configuração de diagnóstico em **nome das configurações de diagnóstico**.

5. Selecione o **log** e defina a retenção em dias.

6. Selecione os **detalhes de destino**. As opções de destino são:
    * **Enviar para Log Analytics**
        * Selecione a **assinatura** e **log Analytics espaço de trabalho**.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a **assinatura** e a **conta de armazenamento**.
    * **Transmitir para um hub de eventos**
        * Selecione a **assinatura**, o **namespace do hub de eventos**, o **nome do hub de eventos (opcional)** e o nome da política do hub de **eventos**.

    ![Configuração de diagnóstico da CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecione **Salvar**.

## <a name="raw-logs-properties"></a>Propriedades de logs brutos

No momento, o Azure CDN do serviço da Microsoft fornece logs brutos (em lotes por hora). Os logs brutos fornecem solicitações de API individuais com cada entrada com o seguinte esquema: 

| Propriedade              | Descrição                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente, também enviada como o cabeçalho X-Azure-ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| HttpMethod            | Método HTTP usado pela solicitação.                                                                                                                                                                     |
| HttpVersion           | Tipo de solicitação ou conexão.                                                                                                                                                                   |
| RequestUri            | URI da solicitação recebida.                                                                                                                                                                         |
| RequestBytes          | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação.                                                                                                   |
| ResponseBytes         | Bytes enviados pelo servidor de back-end como a resposta.                                                                                                                                                    |
| UserAgent             | O tipo de navegador que o cliente usou.                                                                                                                                                               |
| ClientIp              | Endereço IP do cliente que fez a solicitação.                                                                                                                                                  |
| TimeTaken             | O período de tempo que a ação levou, em milissegundos.                                                                                                                                            |
| SecurityProtocol      | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia.                                                                                                                           |
| Ponto de extremidade              | O host do ponto de extremidade da CDN foi configurado no perfil da CDN pai.                                                                                                                                   |
| Nome do host de back-end     | O nome do host de back-end ou da origem em que as solicitações estão sendo enviadas.                                                                                                                                |
| Enviado à blindagem de origem | Se for true, significa que a solicitação foi respondida do cache da blindagem de origem em vez do pop de borda. A blindagem de origem é um cache pai usado para melhorar a taxa de acertos do cache.                                       |
| HttpStatusCode        | O código de status HTTP retornado do proxy.                                                                                                                                                        |
| HttpStatusDetails     | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status.                                                                                              |
| pop                   | O pop de borda, que respondeu à solicitação do usuário. As abreviações dos POPs são códigos de aeroporto de seus respectivos metrôs.                                                                                   |
| Status do cache          | Significa se o objeto foi retornado do cache ou veio da origem.                                                                                                             |

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
Neste artigo, você habilitou os logs brutos de HTTP para o serviço de CDN da Microsoft.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de uso da CDN do Azure.

* Saiba mais sobre o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configure [log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).