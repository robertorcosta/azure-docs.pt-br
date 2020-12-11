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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109735"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Solucionar problemas Azure Data Factory segurança e controle de acesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas de segurança e controle de acesso no Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Problema de conectividade da atividade de cópia no armazenamento de dados de nuvem

#### <a name="symptoms"></a>Sintomas

Vários tipos de mensagens de erro podem ser retornados quando ocorreu um problema de conectividade para o armazenamento de dados de origem/coletor.

#### <a name="cause"></a>Causa 

O problema é causado principalmente pelos seguintes fatores:

1. Configuração de proxy no nó de IR auto-hospedado (se você estiver usando o IR de hospedagem interna)

1. Configuração de firewall no nó do IR auto-hospedado (se você estiver usando o IR de hospedagem interna)

1. Configuração de firewall no armazenamento de dados em nuvem

#### <a name="resolution"></a>Resolução

1. Primeiro verifique os seguintes pontos para ter certeza de que o problema é causado pelo problema de conectividade:

   - O erro é gerado a partir dos conectores de origem/coletor.

   - A atividade falha no início da cópia

   - É uma falha consistente para o Azure IR ou o IR auto-hospedado com um nó, pois pode ser uma falha aleatória para IR de hospedagem interna de vários nós se apenas parte dos nós tiver o problema.

1. Verifique as configurações de proxy, firewall e rede se você estiver usando o **ir auto-hospedado** , pois a execução para o mesmo armazenamento de dados pode ter sucesso no Azure ir. Veja os links a seguir para solucionar problemas:

   [Portas e firewalls](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    de ir hospedados internamente [Conector do ADLS](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Se você estiver usando **Azure ir**, tente desabilitar a configuração de firewall do armazenamento de dados. Dessa forma, o problema das duas circunstâncias a seguir pode ser corrigido:
  
   * [Azure ir endereços IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) não estão na lista de permissões.

   * *Permitir que serviços da Microsoft confiáveis acessem este* recurso de conta de armazenamento está desativado para o [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) e o [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Permitir acesso aos serviços do Azure* não está habilitado para ADLS Gen1.

1. Se os métodos acima não estiverem funcionando, contate a Microsoft para obter ajuda.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema de chave de autenticação inválido ou vazio após o acesso à rede pública ser desabilitado

#### <a name="symptoms"></a>Sintomas

Depois de desabilitar o acesso à rede pública para o Data Factory, como o tempo de execução de integração auto-hospedado gera o seguinte erro: "a chave de autenticação é inválida ou está vazia".

#### <a name="cause"></a>Causa

O problema é provavelmente causado pelo problema de resolução de DNS, pois a desabilitação da conectividade pública e o estabelecimento de um ponto de extremidade privado não ajuda a reconexão.

Você pode seguir as etapas abaixo para verificar se Data Factory FQDN é resolvido para o endereço IP público:

1. Confirme que você criou a VM do Azure na mesma VNET que Data Factory ponto de extremidade privado.

2. Execute PsPing e ping da VM do Azure para Data Factory FQDN:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > Uma porta deve ser especificada para o comando PsPing, enquanto a porta 443 não é necessária.

3. Verifique se ambos os comandos foram resolvidos para um IP público do ADF com base na região especificada (formato xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Resolução

- Você pode consultar o artigo no [link privado do Azure para Azure data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). A instrução é para configurar o servidor/zona DNS privado para resolver Data Factory FQDN para o endereço IP privado.

- Se você não estiver disposto a configurar o servidor/zona DNS privado no momento, execute as etapas abaixo como solução temporária. No entanto, o DNS personalizado ainda é recomendado como solução de longo prazo.

  1. Altere o arquivo de host no Windows e mapeie o IP privado (ponto de extremidade do ADF) para o FQDN do ADF.
  
     Navegue até o caminho "C:\WINDOWS\system32\drivers\etc." na VM do Azure e abra o arquivo de **host** com o bloco de notas. Adicione a linha de mapeamento de IP privado ao FQDN no final do arquivo e salve a alteração.
     
     ![Adicionar mapeamento ao host](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Execute novamente os mesmos comandos nas etapas de verificação acima para verificar a resposta, que deve conter o IP privado.

  1. Registre novamente o tempo de execução de integração auto-hospedado e o problema deve ser resolvido.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registrar a chave de autenticação IR em VMs auto-hospedadas devido ao link privado

#### <a name="symptoms"></a>Sintomas

Não é possível registrar a chave de autenticação IR na VM hospedada internamente devido ao link privado habilitado.

As informações de erro são mostradas abaixo:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

O problema pode ser causado pela VM na qual você está tentando instalar o IR auto-hospedado. Para se conectar à nuvem, você deve garantir que o acesso à rede pública esteja habilitado.

#### <a name="resolution"></a>Resolução

 **Solução 1:** Você pode seguir as etapas abaixo para resolver o problema:

1. Vá para a página [fábricas – atualização](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. No canto superior direito, selecione o botão **experimentar** .

1. Em **parâmetros**, conclua as informações necessárias. 

1. Em **corpo**, Cole a seguinte propriedade:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecione **executar** para executar a função. 

1. Confirme se o **código de resposta: 200** é exibido. A propriedade colada também deve ser exibida na definição de JSON.

1. Adicione a chave de autenticação IR novamente no Integration Runtime.


**Solução 2:** Você pode consultar o artigo abaixo para obter a solução:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Tente habilitar o acesso à rede pública na interface do usuário, conforme mostrado na seguinte captura de tela:

![Habilitar acesso à rede pública](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Link privado para Data Factory](data-factory-private-link.md)
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&uma página](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
