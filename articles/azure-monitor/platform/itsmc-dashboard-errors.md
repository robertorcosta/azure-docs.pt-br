---
title: Erros comuns
description: Este documento contém informações sobre erros comuns que existem no painel
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: be6d47d8f40746bfb2154ddb62cf2e9ce93e74aa
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955676"
---
# <a name="errors-in-the-connector-status-section"></a>Erros na seção status do conector

Na seção lista de status do conector no painel, você pode encontrar erros que podem ajudá-lo a corrigir problemas em seu conector ITSM.

## <a name="status-common-errors"></a>Erros comuns de status

Nesta seção, você pode encontrar os erros comuns apresentados na seção status do conector e como você deve resolvê-los:

* **Erro**: "resposta inesperada do ServiceNow junto com o código de status de êxito. Resposta: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultado": [{"transform_map": "incidente do OMS", "tabela": "incidente", "status": "erro", "error_message": "{registro de destino não encontrado | Tabela inválida | Tabela de preparo inválida "}"

    **Causa**: esse erro é retornado do ServiceNow quando:
  * Um script personalizado implantado na instância do ServiceNow faz com que os incidentes sejam ignorados.
  * O próprio código "aplicativo de integrador do OMS" foi modificado no ServiceNow, por exemplo, o script onbefore.

  **Resolução**: Desabilite todos os scripts personalizados ou modificações de código.

* **Erro**: "{" erro ": {" Message ":" falha na operação "," Detail ":" falha na atualização da exceção de ACL devido a restrições de segurança "}"

    **Causa: falha** na configuração de permissões do ServiceNow

    **Resolução**: Verifique se todas as funções foram atribuídas corretamente conforme [especificado](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Erro**: "ocorreu um erro ao enviar a solicitação."

    **Causa**: "instância do ServiceNow não disponível"

    **Resolução**: Verifique sua instância no ServiceNow que pode ser excluída ou indisponível.

* **Erro**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

    **Causa**: os limites de taxa do ServiceNow são muito altos/baixos.

    **Resolução**: aumente ou cancele os limites de taxa na instância do ServiceNow, conforme explicado [aqui](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Erro**: "AccessToken e RefreshToken inválido. O usuário precisa autenticar novamente. "

    **Causa**: o token de atualização expirou.

    **Resolução**: sincronize o conector ITSM para gerar um novo token de atualização, conforme explicado [aqui](./itsmc-resync-servicenow.md).

* **Erro**: "não foi possível criar/atualizar o item de trabalho para o alerta {AlertName}. Conector ITSM {connectionIdentifier} não existe ou foi excluído. "

    **Causa**: conector ITSM foi excluído.

    **Resolução**: a conector ITSM foi excluída, mas ainda há grupos de ações de ITSM definidos associados a ela. Há duas opções para resolver esse problema:
  * Localizar e desabilitar ou excluir esses grupos de ações
  * [Reconfigure o grupo de ações](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) para usar um conector ITSM existente.
  * [Crie um novo conector ITSM](./itsmc-definition.md#create-an-itsm-connection) e [reconfigure o grupo de ações para usá-lo](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Erros comuns da interface do usuário

* **Erro**: "algo deu errado. Não foi possível obter os detalhes da conexão. " Esse erro é apresentado quando o cliente define o grupo de ações de ITSM.

    **Causa**: esse erro é exibido quando:
    * Os Conector ITSM recém-criados ainda têm de concluir a sincronização inicial.
    * O conector não foi definido corretamente

    **Resolução**: 
    * Quando um novo conector ITSM é criado, Conector ITSM inicia a sincronização de informações do sistema ITSM, como modelos de item de trabalho e itens de trabalho. Sincronize o Conector ITSM para gerar um novo token de atualização, conforme explicado [aqui](./itsmc-resync-servicenow.md).
    * Examine os detalhes de conexão no conector ITSM conforme explicado [aqui](./itsmc-connections-servicenow.md#create-a-connection) e verifique se o conector ITSM pode ser [sincronizado](./itsmc-resync-servicenow.md)com êxito.
