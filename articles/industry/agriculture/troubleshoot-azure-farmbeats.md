---
title: Solução de problemas do Azure FarmBeats
description: Este artigo descreve como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 74522502f5405b56a3a604f20a695de83ef3c3a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886476"
---
# <a name="troubleshoot"></a>Solucionar problemas

Este artigo fornece soluções para problemas comuns do Azure FarmBeats. Para obter ajuda adicional, entre em farmbeatssupport@microsoft.comcontato com nosso Fórum de [Suporte](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) ou envie um e-mail para nós.

> [!NOTE]
  > Se você instalou o FarmBeats durante abril e seus trabalhos estão falhando com uma mensagem de erro vazia, sua instalação pode não ter sido alocada em qualquer cota de lote devido a todos os recursos que estão sendo direcionados para emergências, cuidados de saúde e outras necessidades críticas. Você precisará solicitar que as VMs sejam alocadas na conta Batch.

## <a name="install-issues"></a>Instalar problemas

  > [!NOTE]
  > Se você estiver reiniciando a instalação por causa de um erro, certifique-se de excluir o **Grupo de recursos** ou excluir todos os recursos do Grupo de recursos antes de reativar a instalação.

### <a name="invalid-sentinel-credentials"></a>Credenciais inválidas do Sentinel

As credenciais do Sentinel fornecidas durante a instalação estão incorretas. Reinicie a instalação com as credenciais corretas.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>A cota de conta regional de Contas em Lote para a assinatura especificada foi alcançada

Aumente a cota ou exclua as contas de lote não utilizadas e reinicie a instalação.

### <a name="invalid-resource-group-location"></a>Localização de grupo de recursos inválido

Certifique-se de que o **Grupo de recursos** esteja no mesmo local que a **Região** especificada durante a instalação.

### <a name="other-install-issues"></a>Outros problemas de instalação

Entre em contato conosco com os seguintes detalhes:

- Seu ID de Assinatura
- Nome do Grupo de Recursos
- Siga as etapas abaixo para anexar o arquivo de log para a falha de implantação:

    1. Navegue até o **Grupo de Recursos** no portal Azure.
    2. Selecione **Implantações** na seção **Configurações** no lado esquerdo.
    3. Para cada implantação que mostra **Falha,** selecione até os detalhes e baixe os detalhes de implantação. Anexe este arquivo ao correio.

## <a name="sensor-telemetry"></a>Telemetria de sensores

### <a name="cant-view-telemetry-data"></a>Não é possível visualizar dados de telemetria

**Sintoma**: Dispositivos ou sensores são implantados, e você ligou o FarmBeats com o seu parceiro de dispositivo, mas você não pode obter ou visualizar dados de telemetria no FarmBeats.

**Ação Corretiva**

1. Vá para o seu grupo de recursos FarmBeats Datahub.
2. Selecione o **Event Hub** (DatafeedEventHubNamespace) e verifique o número de mensagens recebidas.
3. Execute um destes procedimentos:

   - Se não houver *mensagens recebidas,* entre em contato com o parceiro do dispositivo.  
   - Se houver *mensagens recebidas,* entre em contato conosco com seus registros datahub e acelerador e telemetria capturada.

Para entender como baixar logs, vá para a seção ["Coletar logs manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerindo dados históricos/de streaming de seus sensores

**Sintoma**: Dispositivos ou sensores são implantados, e você criou os dispositivos/sensores no FarmBeats e ingerida telemetria para o EventHub, mas você não pode obter ou visualizar dados de telemetria no FarmBeats.

**Ação Corretiva**

1. Certifique-se de ter feito o registro do parceiro corretamente - você pode verificar isso indo para o seu datahub swagger, navegar para API /Partner, Fazer um Get e verificar se o parceiro está registrado. Caso assim, siga [estas etapas](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar parceiro.

2. Certifique-se de que você usou o formato correto da mensagem de telemetria:

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

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tenha a seqüência de conexões Azure Event Hubs

**Ação Corretiva**

1. No Datahub Swagger, vá para a API do parceiro.
2. Selecione **'Tentar** > **executá-lo** > **'''**

> [!NOTE]
> A id do parceiro de sensor que você está interessado.

3. Volte para a API do parceiro e selecione **>De\<Obter/ ID **.
4. Especifique o ID do parceiro na etapa 3 e, em seguida, **selecione Executar**.

   A resposta da API deve ter a seqüência de conexões Event Hubs.

### <a name="device-appears-offline"></a>Dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e você vinculou o FarmBeats com o seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas eles aparecem offline.

**Ação corretiva** O intervalo de relatórios não está configurado para este dispositivo. Para definir o intervalo de relatórios, entre em contato com o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro de exclusão de um dispositivo

Enquanto você está excluindo um dispositivo, você pode encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado em sensores: Há um ou mais sensores associados ao dispositivo. Exclua os sensores e exclua o dispositivo."  

**Significado**: O dispositivo está associado a múltiplos sensores que são implantados na fazenda.

**Ação Corretiva**  

1. Exclua os sensores associados ao dispositivo através do Acelerador.  
2. Se você quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Exclua o dispositivo `DELETE API` usando uma chamada e defina o parâmetro de força como *verdadeiro*.  

**Mensagem:**"O dispositivo é referenciado em dispositivos como ParentDeviceId: Existem um ou mais dispositivos que estão associados a este dispositivo como dispositivos crianças. Exclua-os e, em seguida, exclua este dispositivo."  

**Ou seja:** Seu dispositivo tem outros dispositivos associados a ele.  

**Ação Corretiva**

1. Exclua os dispositivos associados a este dispositivo específico.  
2. Exclua o dispositivo específico.  

    > [!NOTE]
    > Você não pode excluir um dispositivo se os sensores estiverem associados a ele. Para obter mais informações sobre como excluir sensores associados, consulte a seção **Excluir sensores** em [Obter dados do sensor de parceiros de sensores](get-sensor-data-from-sensor-partner.md).
    > Os parceiros não têm permissão para excluir um dispositivo ou sensor. Apenas os admins têm permissão para excluir.

## <a name="issues-with-jobs"></a>Problemas com empregos

### <a name="farmbeats-internal-error"></a>Erro interno do FarmBeats

**Mensagem**: "Erro interno do FarmBeats, consulte o guia de solução de problemas para obter mais detalhes."

**Ação corretiva** Este problema pode resultar de uma falha temporária no pipeline de dados. Crie o trabalho de novo. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

## <a name="accelerator-troubleshooting"></a>Solução de problemas do acelerador

### <a name="access-control"></a>Controle de acesso

**Problema**: Você recebe um erro enquanto adiciona uma atribuição de função.

**Mensagem:**"Não foram encontrados usuários correspondentes".

**Ação corretiva** Verifique o ID de e-mail para o qual você está tentando adicionar uma atribuição de função. O ID de e-mail deve ser uma correspondência exata do ID, que está registrado para esse usuário no Active Directory. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer login no Acelerador

**Mensagem**: "Erro: Você não está autorizado a ligar para o serviço. Entre em contato com o administrador para obter autorização."

**Ação corretiva** Peça ao administrador para autorizar você a acessar a implantação do FarmBeats. Isso pode ser feito fazendo um POST das APIs roleassignment ou através do Controle de Acesso no painel **Configurações** no Acelerador.  

Se você já teve acesso e enfrentando esse erro, tente novamente atualizando a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

![Projeto FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas do acelerador  

**Problema**: Você recebeu um erro do Acelerador por causa indeterminada.

**Mensagem**: "Erro: ocorreu um erro desconhecido".

**Ação corretiva** Esse erro ocorre se você deixar a página ociosa por muito tempo. Atualize a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

**Problema**: FarmBeats Accelerator não está mostrando a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva** Esse erro ocorre devido à persistência do trabalhador de serviço no navegador. Faça o seguinte:

1. Feche todas as guias do navegador que tenham o Acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o Uri do Acelerador. Esta ação carrega a nova versão do Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemas relacionados com imagens

### <a name="wrong-username-or-password"></a>Nome de usuário ou senha errados

**Mensagem de falha de emprego**: "A autenticação completa é necessária para acessar esse recurso."

**Ação corretiva**: Faça um dos seguintes:

- Atualize o FarmBeats com o nome de usuário/senha correto usando as etapas abaixo e tente novamente o trabalho.

  **Atualizar nome de usuário do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na **caixa pesquisar,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione Armazenamento de contas de armazenamento***** > **Contêineres** > **arquivos de preparação em** > lote**to_vm** > **config.ini**
    4. Selecione **Editar**
    5. Atualize o nome de usuário na seção sentinel_account

  **Atualizar senha do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na **caixa pesquisar,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione keyvault-*****
    4. Selecione políticas de acesso em configurações
    5. Selecione **adicionar política de acesso**
    6. Use **o gerenciamento secreto** para configurar a partir do modelo e adicionar-se ao Principal
    7. Selecione **Adicionar**e, em seguida, **selecione Salvar** na página **Políticas de acesso**
    8. Selecione **Segredos** em **Configurações**
    9. Selecione **a senha do Sentinel**
    10. Crie uma nova versão do valor e habilite-o.

- Reexecute o trabalho fracassado, ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub sentinel: URL errado ou site não acessível

**Mensagem de falha de emprego:**"Opa, algo deu errado. A página que você estava tentando acessar está (temporariamente) indisponível."

**Ação corretiva:**

1. Abra [o Sentinel](https://scihub.copernicus.eu/dhus/) no seu navegador para ver se o site está acessível.
2. Se o site não estiver acessível, verifique se algum firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e, em seguida, tome as medidas necessárias para permitir a URL do Sentinel. 
3. Reexecute o trabalho fracassado, ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: Para baixo para manutenção

**Mensagem de falha de emprego**: "O Copernicus Open Access Hub estará de volta em breve! Desculpe pelo inconveniente, estamos fazendo manutenção no momento. Estaremos de volta on-line em breve! 

**Ação corretiva:**

Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou tubulação falhar porque a manutenção está sendo realizada, reenvie o trabalho depois de algum tempo. 

   Para obter informações sobre qualquer atividade de manutenção sentinela planejada ou não planejada, acesse o site de notícias do [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecute o trabalho fracassado, ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Número máximo de conexões atingidas

**Mensagem de falha**de emprego : "Número máximo\<de dois fluxos simultâneos alcançados pelo usuário ' nome de usuário>'."

**Significado**: Se um trabalho falhar porque o número máximo de conexões foi atingido, a mesma conta sentinela está sendo usada em vários trabalhos.

**Ação corretiva**: Tente qualquer um dos seguintes:

* Espere os outros trabalhos terminarem antes de refazer o trabalho fracassado.
* Crie uma nova conta do Sentinel e atualize o nome de usuário e a senha do Sentinel no FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: Conexão recusada

**Mensagem de falha**no trabalho: "Servidor recusou conexão em: http://172.30.175.69:8983/solr/dhus."

**Ação corretiva**: Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou tubulação falhar porque a manutenção está sendo realizada, reenvie o trabalho depois de algum tempo.

   Para obter informações sobre qualquer atividade de manutenção sentinela planejada ou não planejada, acesse o site de notícias do [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecute o trabalho fracassado, ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa de umidade do solo tem áreas brancas

**Edição**: O **mapa de umidade do solo** foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: Este problema pode ocorrer se os índices de satélite gerados para o tempo para o qual o mapa foi solicitado tiver valores NDVI inferiores a 0,3. Para obter mais informações, visite [o Guia Técnico do Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Reexecute o trabalho para uma faixa de data diferente e verifique se os valores de NDVI nos índices de satélite são superiores a 0,3.

## <a name="collect-logs-manually"></a>Coletar registros manualmente

[Instale e implante o Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Colete registros de trabalho da Fábrica de Dados do Azure ou logs de serviço de aplicativo no Datahub

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na **caixa pesquisar,** procure o grupo de recursos FarmBeats Datahub.
3. No **painel do Grupo de recursos,** procure a conta de armazenamento *datahublogs.\* * Por exemplo, *datahublogsmvxmq*.  
4. Na coluna **Nome,** selecione a conta de armazenamento para exibir o painel **da conta** de armazenamento.
5. No **painel datahubblogs,\* ** selecione **Abrir no Explorer** para visualizar o aplicativo Open **Azure Storage Explorer.**
6. No painel esquerdo, selecione **Blob Containers**e selecione **registros de trabalho** para logs da Fábrica de Dados do Azure ou **appinsights-logs** para logs de serviço de aplicativo.
7. Selecione **Baixar** e baixe os logs para uma pasta local em sua máquina.

    ![Projeto FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Colete registros de trabalho da Fábrica de Dados do Azure ou logs de serviço de aplicativo para acelerador

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa **pesquisar,** procure o grupo de recursos FarmBeats Accelerator.
3. No **painel do Grupo de recursos,** procure *a\* * conta de armazenamento. Por exemplo, *storagedop4k\**.
4. Selecione a conta de armazenamento na coluna **Nome** para exibir o painel da conta de **armazenamento.**
5. No painel de **armazenamento,\* ** selecione **Abrir no Explorer** para abrir o aplicativo Azure Storage Explorer.
6. No painel esquerdo, selecione **Blob Containers**e selecione **registros de trabalho** para logs da Fábrica de Dados do Azure ou **appinsights-logs** para logs de serviço de aplicativo.
7. Selecione **Baixar** e baixe os logs para uma pasta local em sua máquina.

## <a name="high-cpu-usage"></a>Alto uso da CPU

**Erro**: Você recebe um alerta de e-mail que se refere a um **alerta de uso de CPU alto**.

**Ação corretiva:**

1. Vá para o seu grupo de recursos FarmBeats Datahub.
2. Selecione o **serviço app**.  
3. Vá para a [página de preços](https://azure.microsoft.com/pricing/details/app-service/windows/)do App Service e selecione um nível de preços apropriado.
