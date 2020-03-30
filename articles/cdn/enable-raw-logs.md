---
title: Logs brutos do Azure CDN HTTP
description: Este artigo descreve os logs brutos do Azure CDN HTTP.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371626"
---
# <a name="azure-cdn-http-raw-logs"></a>Logs brutos do Azure CDN HTTP
Os registros brutos fornecem informações ricas sobre operações e erros que são importantes para auditoria e solução de problemas. Os registros brutos diferem dos registros de atividade. Os registros de atividades fornecem visibilidade às operações realizadas nos recursos do Azure. Os registros brutos fornecem um registro das operações do seu recurso.

> [!IMPORTANT]
> O recurso HTTP raw logs está disponível para CDN Azure da Microsoft.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuração

Para configurar registros Raw para seu CDN do Azure a partir do perfil da Microsoft: 

1. No menu portal Do Zure, selecione **Todos os recursos** >> **\<do seu perfil CDN>**.

2. Em **Monitoramento,** **selecione Configurações de Diagnóstico**.

3. Selecione **+ Adicione a configuração de diagnóstico**.

    ![Configuração de diagnóstico de CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Os logs brutos só estão disponíveis no nível do perfil enquanto os logs de código de status http agregados estão disponíveis no nível de ponto final.

4. Em **Configurações de diagnóstico,** digite um nome para a configuração de diagnóstico em **Nome das configurações de diagnóstico**.

5. Selecione o **registro** e defina a retenção em dias.

6. Selecione os **detalhes destino**. As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione o **espaço de trabalho De assinatura** e log **analytics**.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a **Assinatura** e a **Conta de Armazenamento**.
    * **Fluxo para um hub de eventos**
        * Selecione o **nome de assinatura,** **o namespace do hub de eventos,** **o nome do hub de eventos (opcional)** e **o nome da política do hub de eventos**.

    ![Configuração de diagnóstico de CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecione **Salvar**.

## <a name="raw-logs-properties"></a>Propriedades de troncos brutos

O CDN do Azure do Microsoft Service atualmente fornece logs Raw. Os logs brutos fornecem solicitações individuais de API com cada entrada com o seguinte esquema: 

| Propriedade              | Descrição                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Referência de rastreamento     | A seqüência de referência única que identifica uma solicitação atendida pelo Front Door, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registros de acesso para uma solicitação específica. |
| HttpMethod            | Método HTTP usado pela solicitação.                                                                                                                                                                     |
| HttpVersion           | Tipo de solicitação ou conexão.                                                                                                                                                                   |
| Requesturi            | URI da solicitação recebida.                                                                                                                                                                         |
| RequestBytes          | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o órgão de solicitação.                                                                                                   |
| ResponseBytes         | Bytes enviados pelo servidor backend como resposta.                                                                                                                                                    |
| UserAgent             | O tipo de navegador que o cliente usou.                                                                                                                                                               |
| ClientIp              | Endereço IP do cliente que fez a solicitação.                                                                                                                                                  |
| TimeTaken             | O tempo que a ação levou, em milissegundos.                                                                                                                                            |
| Securityprotocol      | A versão do protocolo TLS/SSL usada pela solicitação ou nula se não houver criptografia.                                                                                                                           |
| Ponto de extremidade              | O host de ponto final do CDN foi configurado o perfil CDN pai.                                                                                                                                   |
| Nome do host backend     | O nome do host backend ou origem onde as solicitações estão sendo enviadas.                                                                                                                                |
| Enviado para o escudo de origem | Se for verdade, significa que a solicitação foi respondida a partir do cache do escudo de origem em vez do pico de borda. O Origin Shield é um cache pai usado para melhorar a relação de acerto de cache.                                       |
| HttpStatusCode        | O código de status HTTP retornou do proxy.                                                                                                                                                        |
| Detalhes do Status http     | Status resultante na solicitação. O significado desse valor de string pode ser encontrado em uma tabela de referência status.                                                                                              |
| Pop                   | O edge pop, que respondeu ao pedido do usuário. As abreviaturas dos POPs são códigos aeroportuários de seus respectivos metros.                                                                                   |
| Cache Status          | Significa se o objeto foi devolvido do cache ou veio da origem.                                                                                                             |
> [!IMPORTANT]
> O recurso HTTP Raw logs está disponível automaticamente para quaisquer perfis criados ou atualizados após **25 de fevereiro de 2020**. Para perfis de CDN criados anteriormente, deve-se atualizar o ponto final do CDN após configurar o registro. Por exemplo, pode-se navegar para filtragem geográfica em pontos finais de CDN e bloquear qualquer país que não seja relevante para sua carga de trabalho e salvar. 

> [!NOTE]
> Os logs podem ser visualizados no perfil do Log Analytics executando uma consulta. Uma consulta de exemplo seria pareada com o AzureDiagnostics | onde categoria == "AzureCdnAccessLog"

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você habilitou registros brutos HTTP para o serviço Microsoft CDN.

Para obter mais informações sobre o Azure CDN e os outros serviços do Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de uso do Azure CDN.

* Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configure [o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
