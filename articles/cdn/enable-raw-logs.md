---
title: Logs brutos de HTTP da CDN do Azure
description: Este artigo descreve os logs brutos de HTTP do Azure CDN.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371626"
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

1. No menu portal do Azure, selecione **todos os recursos** >> **\<seu-CDN-Profile>**.

2. Em **monitoramento**, selecione **configurações de diagnóstico**.

3. Selecione **+ Adicionar configuração de diagnóstico**.

    ![Configuração de diagnóstico da CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Os logs brutos só estão disponíveis no nível do perfil enquanto os logs de código de status http agregados estão disponíveis no nível do ponto de extremidade.

4. Em **configurações de diagnóstico**, insira um nome para a configuração de diagnóstico em **nome das configurações de diagnóstico**.

5. Selecione o **log** e defina a retenção em dias.

6. Selecione os **detalhes de destino**. As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione a **assinatura** e **log Analytics espaço de trabalho**.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a **assinatura** e a **conta de armazenamento**.
    * **Transmitir para um hub de eventos**
        * Selecione a **assinatura**, o **namespace do hub de eventos**, o **nome do hub de eventos (opcional)** e o nome da política do hub de **eventos**.

    ![Configuração de diagnóstico da CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecione **Salvar**.

## <a name="raw-logs-properties"></a>Propriedades de logs brutos

Atualmente, o Azure CDN do serviço Microsoft fornece logs brutos. Os logs brutos fornecem solicitações de API individuais com cada entrada com o seguinte esquema: 

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
| Pop                   | O pop de borda, que respondeu à solicitação do usuário. As abreviações dos POPs são códigos de aeroporto de seus respectivos metrôs.                                                                                   |
| Status do cache          | Significa se o objeto foi retornado do cache ou veio da origem.                                                                                                             |
> [!IMPORTANT]
> O recurso de logs brutos HTTP está disponível automaticamente para todos os perfis criados ou atualizados após **25 de fevereiro de 2020**. Para perfis CDN criados anteriormente, é necessário atualizar o ponto de extremidade CDN depois de configurar o registro em log. Por exemplo, é possível navegar para a filtragem geográfica em pontos de extremidade CDN e bloquear qualquer país não relevante para sua carga de trabalho e clicar em salvar. 

> [!NOTE]
> Os logs podem ser exibidos em seu perfil de Log Analytics executando uma consulta. Uma consulta de exemplo seria semelhante A AzureDiagnostics | onde category = = "AzureCdnAccessLog"

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você habilitou os logs brutos de HTTP para o serviço de CDN da Microsoft.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de uso da CDN do Azure.

* Saiba mais sobre [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configure [log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
