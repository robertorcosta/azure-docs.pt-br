---
title: Solução de problemas do Azure FarmBeats
description: Este artigo descreve como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113501"
---
# <a name="troubleshoot"></a>Solucionar problemas

Este artigo fornece soluções para problemas comuns do FarmBeats do Azure. Para obter ajuda adicional, entre em contato com nosso [Fórum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) de farmbeatssupport@microsoft.comsuporte ou envie um email para.

> [!NOTE]
  > Se você tiver instalado o FarmBeats durante abril e seus trabalhos estiverem falhando com uma mensagem de erro vazia, talvez sua instalação não tenha sido alocada a nenhuma cota de lote para priorizar o suporte para as organizações de saúde e segurança críticas. Consulte [aqui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) para obter mais informações. Você precisará solicitar que as VMs sejam alocadas para a conta do lote para executar trabalhos com êxito.

## <a name="install-issues"></a>Problemas de instalação

  > [!NOTE]
  > Se você estiver reiniciando a instalação devido a um erro, certifique-se de excluir o **grupo de recursos** ou excluir todos os recursos do grupo de recursos antes de disparar novamente a instalação.

### <a name="invalid-sentinel-credentials"></a>Credenciais de sentinela inválidas

As credenciais de sentinela fornecidas durante a instalação estão incorretas. Reinicie a instalação com as credenciais corretas.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>A cota da conta regional das contas do lote para a assinatura especificada foi atingida

Aumente a cota ou exclua as contas do lote não usadas e reinicie a instalação.

### <a name="invalid-resource-group-location"></a>Local do grupo de recursos inválido

Verifique se o **grupo de recursos** está no mesmo local que a **região** especificada durante a instalação.

### <a name="other-install-issues"></a>Outros problemas de instalação

Entre em contato conosco com os seguintes detalhes:

- Sua ID de assinatura
- Nome do Grupo de Recursos
- Siga as etapas abaixo para anexar o arquivo de log para a falha de implantação:

    1. Navegue até o **grupo de recursos** na portal do Azure.

    2. Selecione **implantações** na seção **configurações** no lado esquerdo.

    3. Para cada implantação que mostra **falha**, selecione até os detalhes e baixe os detalhes da implantação. Anexe este arquivo ao email.

## <a name="sensor-telemetry"></a>Telemetria do sensor

### <a name="cant-view-telemetry-data"></a>Não é possível exibir dados de telemetria

**Sintoma**: dispositivos ou sensores são implantados e você vinculou o FarmBeats ao seu parceiro de dispositivo, mas não pode obter ou exibir dados de telemetria no FarmBeats.

**Ação Corretiva**

1. Vá para o grupo de recursos do FarmBeats Datahub.
2. Selecione o **Hub de eventos** (DatafeedEventHubNamespace) e, em seguida, verifique o número de mensagens de entrada.
3. Execute um destes procedimentos:

   - Se não houver *nenhuma mensagem de entrada*, entre em contato com o parceiro do dispositivo.  
   - Se houver *mensagens de entrada*, entre em contato conosco com os logs do Datahub e do acelerador e a telemetria capturada.

Para entender como baixar logs, vá para a seção ["coletar logs manualmente"](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível exibir dados de telemetria depois de ingerir dados históricos/de streaming de seus sensores

**Sintoma**: dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e a telemetria ingerida para o EventHub, mas não pode obter ou exibir dados de telemetria no FarmBeats.

**Ação Corretiva**

1. Verifique se você fez o registro do parceiro corretamente-você pode verificar isso indo para o Swagger do datahub, navegue até a API do/Partner, faça uma obtenção e verifique se o parceiro está registrado. Caso contrário, siga estas [etapas](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.

2. Verifique se você usou o formato de mensagem de telemetria correto:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tem a cadeia de conexão dos hubs de eventos do Azure

**Ação Corretiva**

1. No Datahub Swagger, vá para a API de parceiro.
2. Selecione **Get** > **Experimente** > **executar**.

> [!NOTE]
> A ID de parceiro do parceiro do sensor no qual você está interessado.

3. Volte para a API de parceiro e selecione **obter/\<ID>**.
4. Especifique a ID de parceiro da etapa 3 e, em seguida, selecione **executar**.

   A resposta da API deve ter a cadeia de conexão dos hubs de eventos.

### <a name="device-appears-offline"></a>O dispositivo aparece offline

**Sintomas**: os dispositivos são instalados e você vinculou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas aparecem offline.

**Ação corretiva** O intervalo de relatório não está configurado para este dispositivo. Para definir o intervalo de relatórios, contate o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro ao excluir um dispositivo

Enquanto estiver excluindo um dispositivo, você poderá encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "o dispositivo é referenciado em sensores: há um ou mais sensores associados ao dispositivo. Exclua os sensores e, em seguida, exclua o dispositivo. "  

**Significando**: o dispositivo está associado a vários sensores que são implantados no farm.

**Ação Corretiva**  

1. Exclua os sensores associados ao dispositivo por meio do acelerador.  
2. Se você quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Exclua o dispositivo usando uma `DELETE API` chamada e defina o parâmetro Force como *true*.  

**Mensagem**: "o dispositivo é referenciado em dispositivos como ParentDeviceId: há um ou mais dispositivos associados a este dispositivo como dispositivos filho. Exclua-os e exclua este dispositivo. "  

**Significado**: seu dispositivo tem outros dispositivos associados a ele.  

**Ação Corretiva**

1. Exclua os dispositivos associados a este dispositivo específico.  
2. Exclua o dispositivo específico.  

    > [!NOTE]
    > Você não poderá excluir um dispositivo se os sensores estiverem associados a ele. Para obter mais informações sobre como excluir sensores associados, consulte a seção **excluir sensor** em [obter dados de sensor de parceiros de sensor](get-sensor-data-from-sensor-partner.md).
    > Os parceiros não têm permissão para excluir um dispositivo ou sensor. Somente administradores têm permissão para excluir.

## <a name="issues-with-jobs"></a>Problemas com trabalhos

### <a name="farmbeats-internal-error"></a>Erro interno de FarmBeats

**Mensagem**: "erro interno do FarmBeats, consulte o guia de solução de problemas para obter mais detalhes".

**Ação corretiva** Esse problema pode resultar de uma falha temporária no pipeline de dados. Crie o trabalho novamente. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

## <a name="accelerator-troubleshooting"></a>Solução de problemas do acelerador

### <a name="access-control"></a>Controle de acesso

**Problema**: você recebe um erro enquanto está adicionando uma atribuição de função.

**Mensagem**: "nenhum usuário correspondente encontrado."

**Ação corretiva** Verifique a ID de email para a qual você está tentando adicionar uma atribuição de função. A ID de email deve ser uma correspondência exata da ID, que é registrada para esse usuário na Active Directory. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer logon no acelerador

**Mensagem**: "erro: você não está autorizado a chamar o serviço. Contate o administrador para autorização. "

**Ação corretiva** Peça ao administrador para autorizar você a acessar a implantação do FarmBeats. Isso pode ser feito fazendo uma POSTAgem das APIs RoleAssignment ou por meio do controle de acesso no painel **configurações** no acelerador.  

Se você já tiver recebido o acesso e voltado para esse erro, tente novamente atualizando a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

![FarmBeats do projeto](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas do acelerador  

**Problema**: você recebeu um erro de acelerador de causa indeterminada.

**Mensagem**: "erro: ocorreu um erro desconhecido."

**Ação corretiva** Esse erro ocorrerá se você deixar a página ociosa por muito tempo. Atualize a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

**Problema**: o acelerador de FarmBeats não está mostrando a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva** Esse erro ocorre devido à persistência de trabalho do serviço no navegador. Faça o seguinte:

1. Feche todas as guias do navegador que têm o acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o URI do acelerador. Essa ação carrega a nova versão do acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinela: problemas relacionados a imagens

### <a name="wrong-username-or-password"></a>Nome de usuário ou senha incorreto

**Mensagem de falha de trabalho**: "a autenticação completa é necessária para acessar este recurso."

**Ação corretiva**: siga um destes procedimentos:

- Atualize FarmBeats com o nome de usuário/senha corretos usando as etapas abaixo e repita o trabalho.

  **Atualizar nome de usuário do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na caixa de **pesquisa** , procure o grupo de recursos FarmBeats Datahub.
    3. Selecione armazenamento da conta de armazenamento * * * * * > **contêineres** > **lote-Prep-files** > **to_vm** > **config. ini**
    4. Selecione **Editar**
    5. Atualizar o nome de usuário na seção sentinel_account

  **Atualizar senha do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na caixa de **pesquisa** , procure o grupo de recursos FarmBeats Datahub.
    3. Selecione keyvault-* * * * *
    4. Selecionar políticas de acesso em configurações
    5. Selecione **Adicionar política de acesso**
    6. Usar o **Gerenciamento de segredo** para configurar a partir do modelo e adicionar a si mesmo ao principal
    7. Selecione **Adicionar**e, em seguida, selecione **salvar** na página **políticas de acesso**
    8. Selecione os **segredos** em **configurações**
    9. Selecionar **sentinela-senha**
    10. Crie uma nova versão do valor e habilite-a.

- Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub Sentinel: URL ou site incorreto inacessível

**Mensagem de falha de trabalho**: "Opa, algo deu errado. A página que você estava tentando acessar está (temporariamente) indisponível. "

**Ação corretiva**:

1. Abra o [Sentinel](https://scihub.copernicus.eu/dhus/) em seu navegador para ver se o site está acessível.
2. Se o site não estiver acessível, verifique se algum firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e execute as etapas necessárias para permitir a URL do sentinela. 
3. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: inoperante para manutenção

**Mensagem de falha do trabalho**: "o Hub de acesso aberto do Copernicus será feito em breve! Lamentamos o inconveniente, estamos realizando alguma manutenção no momento. Voltaremos a ficar online em breve! " 

**Ação corretiva**:

Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo. 

   Para obter informações sobre qualquer atividade de manutenção de sentinela planejada ou não planejada, vá para o site de [notícias do hub de acesso do Copernicus Open](https://scihub.copernicus.eu/news/) .  

2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinela: número máximo de conexões atingido

**Mensagem de falha de trabalho**: "número máximo de dois fluxos simultâneos obtidos pelo usuário\<' username> '."

**Significando**: se um trabalho falhar porque o número máximo de conexões foi atingido, a mesma conta do Sentinel está sendo usada em vários trabalhos.

**Ação corretiva**: Tente uma das seguintes opções:

* Aguarde até que os outros trabalhos sejam concluídos antes de executar novamente o trabalho com falha.
* Crie uma nova conta do Sentinel e, em seguida, atualize o nome de usuário e a senha do Sentinel no FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: conexão recusada

**Mensagem de falha do trabalho**: "o servidor recusou http://172.30.175.69:8983/solr/dhusa conexão em:."

**Ação corretiva**: esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo.

   Para obter informações sobre qualquer atividade de manutenção de sentinela planejada ou não planejada, vá para o site de [notícias do hub de acesso do Copernicus Open](https://scihub.copernicus.eu/news/) .  

2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa de umidade de solo tem áreas brancas

**Problema**: o **mapa de umidade do solo** foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: esse problema pode ocorrer se os índices satélite gerados para o momento para o qual o mapa foi solicitado tiverem valores NDVI menores que 0,3. Para obter mais informações, visite o [guia técnico do Sentinela](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Execute novamente o trabalho para um intervalo de datas diferente e verifique se os valores de NDVI nos índices satélite são maiores que 0,3.

## <a name="collect-logs-manually"></a>Coletar logs manualmente

[Instalar e implantar Gerenciador de armazenamento do Azure]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Coletar Azure Data Factory logs de trabalho ou logs do serviço de aplicativo no Datahub

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos FarmBeats Datahub.
3. No painel do **grupo de recursos** , procure a conta de armazenamento *datahublogs\* * . Por exemplo, *datahublogsmvxmq*.  
4. Na coluna **nome** , selecione a conta de armazenamento para exibir o painel da **conta de armazenamento** .
5. No painel **datahubblogs\* ** , selecione **abrir no Explorer** para exibir o aplicativo **aberto Gerenciador de armazenamento do Azure** .
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **logs de trabalhos** para Azure data Factory logs ou logs de **appinsights** para logs do serviço de aplicativo.
7. Selecione **baixar** e baixar os logs em uma pasta local em seu computador.

    ![FarmBeats do projeto](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Coletar Azure Data Factory logs de trabalho ou logs do serviço de aplicativo para acelerador

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos do acelerador FarmBeats.
3. No painel do **grupo de recursos** , procure a conta de armazenamento de *armazenamento\* * . Por exemplo, *storagedop4k\**.
4. Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5. No painel **armazenamento\* ** , selecione **abrir no Explorer** para abrir o aplicativo Gerenciador de armazenamento do Azure.
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **logs de trabalhos** para Azure data Factory logs ou logs de **appinsights** para logs do serviço de aplicativo.
7. Selecione **baixar** e baixar os logs em uma pasta local em seu computador.

## <a name="high-cpu-usage"></a>Alto uso da CPU

**Erro**: você recebe um alerta de email que se refere a um **alerta de alto uso da CPU**.

**Ação corretiva**:

1. Vá para o grupo de recursos do FarmBeats Datahub.
2. Selecione o **serviço de aplicativo**.  
3. Vá para a página escalar verticalmente os [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/)e selecione um tipo de preço apropriado.
