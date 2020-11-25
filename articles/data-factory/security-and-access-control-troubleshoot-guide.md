---
title: Solucionar problemas de segurança e controle de acesso
description: Saiba como solucionar problemas de segurança e controle de acesso no Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008711"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Solucionar problemas Azure Data Factory segurança e controle de acesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas de segurança e controle de acesso no Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Problema de chave de autenticação inválido ou vazio após desabilitar o acesso à rede pública

#### <a name="symptoms"></a>Sintomas

O tempo de execução de integração auto-hospedado gera o erro "a chave de autenticação é inválida ou está vazia" depois de desabilitar o acesso à rede pública para Data Factory.

#### <a name="cause"></a>Causa

O problema é provavelmente causado pelo problema de resolução de DNS, pois a desabilitação da conectividade pública e o estabelecimento de um ponto de extremidade privado não ajuda a reconexão.

#### <a name="resolution"></a>Resolução

1. PsPing o FQDN do ADF e descobri que o buffer estava indo para um ponto de extremidade público do ADF, mesmo depois de tê-lo desabilitado.

1. Altere o arquivo de host na VM para mapear o IP privado para o FQDN e executar um PsPing novamente. O buffer poderá ir para o IP privado correto do ADF.

1. Registre novamente o tempo de execução de integração auto-hospedado e vamos encontrá-lo em funcionamento.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registrar a chave de autenticação IR em VMs auto-hospedadas devido ao link privado

#### <a name="symptoms"></a>Sintomas

Não é possível registrar a chave de autenticação IR na VM hospedada internamente devido ao link privado habilitado.

As informações de erro são mostradas abaixo:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

O problema pode ser causado pela VM em que o SHIR está tentando ser instalado. O acesso à rede pública deve ser habilitado para se conectar à nuvem.

#### <a name="resolution"></a>Resolução

 **Solução 1:** Você pode seguir as etapas abaixo para resolver o problema:

1. Navegue até o link abaixo: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Clique na opção **Experimente** e preencha todos os detalhes necessários. Cole a propriedade abaixo no **corpo** também:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Clique em **executar** no final da página para executar a função. Certifique-se de que você obteria o código de resposta 200. A propriedade colada também será mostrada na definição de JSON.

1. Em seguida, você pode tentar adicionar a chave de autenticação IR novamente no Integration Runtime.


**Solução 2:** Você pode consultar o artigo abaixo para obter a solução:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Tente habilitar o acesso à rede pública com a interface do usuário.

![Habilitar acesso à rede pública](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Link privado para Data Factory](data-factory-private-link.md)
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de P e R da Microsoft](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)