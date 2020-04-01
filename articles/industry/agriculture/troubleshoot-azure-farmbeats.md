---
title: Solução de problemas do Azure FarmBeats
description: Este artigo descreve como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422716"
---
# <a name="troubleshoot"></a>Solucionar problemas

Este artigo fornece soluções para problemas comuns do Azure FarmBeats.

Para obter ajuda adicional, entre em contato conosco em farmbeatssupport@microsoft.com. Certifique-se de incluir o arquivo **deployer.log** em seu e-mail.

Para baixar o arquivo **deployer.log,** faça o seguinte:

1. Faça login no **portal Azure** e selecione sua assinatura e inquilino azure AD.
2. Inicie o Cloud Shell no painel de navegação superior do portal do Azure.
3. Selecione **Bash** como a experiência preferida do Cloud Shell.
4. Selecione o ícone destacado e, em seguida, na lista de parada, selecione **Download**.

    ![Projeto FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. No painel seguinte, digite o caminho para o arquivo **deployer.log.** Por exemplo, **digite farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Telemetria de sensores

### <a name="cant-view-telemetry-data"></a>Não é possível visualizar dados de telemetria

**Sintoma**: Dispositivos ou sensores são implantados, e você ligou o FarmBeats com o seu parceiro de dispositivo, mas você não pode obter ou visualizar dados de telemetria no FarmBeats.

**Ação corretiva:**

1. Vá para o seu grupo de recursos FarmBeats Datahub.   

2. Selecione o **Event Hub** (DatafeedEventHubNamespace) e verifique o número de mensagens recebidas.

3. Execute um destes procedimentos:   
   - Se não houver *mensagens recebidas,* entre em contato com o parceiro do dispositivo.  
   - Se houver *mensagens recebidas,* entre em contato. farmbeatssupport@microsoft.com Anexe seus registros datahub e acelerador e telemetria capturada.

Para entender como baixar logs, vá para a seção ["Coletar logs manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerindo dados históricos/de streaming de seus sensores

**Sintoma**: Dispositivos ou sensores são implantados, e você criou os dispositivos/sensores no FarmBeats e ingerida telemetria para o EventHub, mas você não pode obter ou visualizar dados de telemetria no FarmBeats.

**Ação corretiva:**

1. Certifique-se de ter feito o registro do parceiro corretamente - você pode verificar isso indo para o seu datahub swagger, navegar para API /Partner, Fazer um Get e verificar se o parceiro está registrado. Caso assim, siga os [passos aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar parceiro.

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

**Ação corretiva:**

1. No Datahub Swagger, vá para a API do parceiro.
2. Selecione **'Tentar** > **executá-lo** > **'''**

> [!NOTE]
> A id do parceiro de sensor que você está interessado.

3. Volte para a API do parceiro e selecione **>De\<Obter/ ID **.
4. Especifique o ID do parceiro na etapa 3 e, em seguida, **selecione Executar**.

   A resposta da API deve ter a seqüência de conexões Event Hubs.

### <a name="device-appears-offline"></a>Dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e você vinculou o FarmBeats com o seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas eles aparecem offline.

**Ação corretiva**: O intervalo de relatórios não está configurado para este dispositivo. Para definir o intervalo de relatórios, entre em contato com o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro de exclusão de um dispositivo

Enquanto você está excluindo um dispositivo, você pode encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado em sensores: Há um ou mais sensores associados ao dispositivo. Exclua os sensores e exclua o dispositivo."  

**Significado**: O dispositivo está associado a múltiplos sensores que são implantados na fazenda.   

**Ação corretiva:**  

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

    > Os parceiros não têm acesso para excluir um dispositivo ou sensor. Apenas os admins têm acesso para fazer o mesmo.


## <a name="issues-with-jobs"></a>Problemas com empregos

### <a name="farmbeats-internal-error"></a>Erro interno do FarmBeats

**Mensagem**: "Erro interno do FarmBeats, consulte o guia de solução de problemas para obter mais detalhes".

**Ação corretiva**: Este problema pode resultar de uma falha temporária no pipeline de dados. Crie o trabalho de novo. Se o erro persistir, adicione a mensagem de erro em FarmBeatsSupport@microsoft.comum post no fórum FarmBeats ou entre em contato .

## <a name="accelerator-troubleshooting"></a>Solução de problemas do acelerador

### <a name="access-control"></a>Controle de acesso

**Problema**: Você recebe um erro enquanto adiciona uma atribuição de função.

**Mensagem:**"Não foram encontrados usuários correspondentes".

**Ação corretiva**: Verifique o ID de e-mail para o qual você está tentando adicionar uma atribuição de função. O ID de e-mail deve ser uma correspondência exata do ID, que está registrado para esse usuário no Active Directory. Se o erro persistir, adicione a mensagem de erro em FarmBeatsSupport@microsoft.comum post no fórum FarmBeats ou entre em contato .

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer login no Acelerador

**Mensagem**: "Erro: Você não está autorizado a ligar para o serviço. Entre em contato com o administrador para obter autorização."

**Ação corretiva**: Peça ao administrador para autorizar você a acessar a implantação do FarmBeats. Isso pode ser feito fazendo um POST das APIs roleassignment ou através do Controle de Acesso no painel **Configurações** no Acelerador.  

Se você já teve acesso e está enfrentando esse erro, tente novamente atualizando a página. Se o erro persistir, adicione a mensagem de erro em FarmBeatsSupport@microsoft.comum post no fórum FarmBeats ou entre em contato .

![Projeto FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas do acelerador  

**Problema**: Você recebeu um erro do Acelerador por causa indeterminada.

**Mensagem**: "Erro: ocorreu um erro desconhecido".

**Ação corretiva**: Este erro ocorre se você deixar a página ociosa por muito tempo. Atualize a página.  

Se o erro persistir, adicione a mensagem de erro em FarmBeatsSupport@microsoft.comum post no fórum FarmBeats ou entre em contato .

**Problema**: FarmBeats Accelerator não está mostrando a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva**: Este erro ocorre devido à persistência do trabalhador de serviço no navegador. Faça o seguinte:

1. Feche todas as guias do navegador que tenham o Acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o Uri do Acelerador. Esta ação carrega a nova versão do Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemas relacionados com imagens

### <a name="wrong-username-or-password"></a>Nome de usuário ou senha errados

**Mensagem de falha de emprego**: "A autenticação completa é necessária para acessar esse recurso."

**Ação corretiva:**

Realize um dos seguintes procedimentos:

- Execute o instalador para atualizar o Datahub com o nome de usuário e senha corretos.
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

**Significado**: Se um trabalho falhar porque o número máximo de conexões foi atingido, a mesma conta sentinela está sendo usada em outra implantação de software.

**Ação corretiva**: Tente qualquer um dos seguintes:

* Crie uma nova conta do Sentinel e, em seguida, execute o instalador para atualizar o Datahub usando um novo nome de usuário e senha do Sentinel.  
* Reexecute o trabalho fracassado ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: Conexão recusada 

**Mensagem de falha**no trabalho: "Servidor recusou conexão em: http://172.30.175.69:8983/solr/dhus." 

**Ação corretiva**: Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel. 
1. Se algum trabalho ou tubulação falhar porque a manutenção está sendo realizada, reenvie o trabalho depois de algum tempo. 

   Para obter informações sobre qualquer atividade de manutenção sentinela planejada ou não planejada, acesse o site de notícias do [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecute o trabalho fracassado, ou execute um trabalho de índices de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verifique se o trabalho é bem sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa de umidade do solo tem áreas brancas 

**Edição**: O mapa de umidade do solo foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: Este problema pode ocorrer se os índices de satélite gerados para o tempo para o qual o mapa foi solicitado tiver valores NDVI inferiores a 0,3. Para obter mais informações, visite [o Guia Técnico do Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).
1. Reexecute o trabalho para uma faixa de data diferente e verifique se os valores de NDVI nos índices de satélite são superiores a 0,3

## <a name="collect-logs-manually"></a>Coletar registros manualmente

[Instale e implante o Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Coletar registros de trabalho da Fábrica de Dados do Azure no Datahub

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na **caixa pesquisar,** procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que você especificou durante a instalação do FarmBeats.

3. No **painel do Grupo de recursos,** procure a conta de armazenamento *datahublogs.\* * Por exemplo, pesquise por **datahublogsmvxmq**.  
4. Na coluna **Nome,** selecione a conta de armazenamento para exibir o painel **da conta** de armazenamento.
5. No **painel datahubblogs,\* ** selecione **Abrir no Explorer** para visualizar o aplicativo Open **Azure Storage Explorer.**
6. No painel esquerdo, selecione **Recipientes Blob**e selecione **registros de trabalho**.
7. No **painel de logs de trabalho,** selecione **Download**.
8. Baixe os logs para uma pasta local em sua máquina.
9. Envie um e-mail farmbeatssupport@microsoft.compara o arquivo .zip baixado para .

    ![Projeto FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Coletar registros de trabalho da Fábrica de Dados do Azure no Acelerador

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa **pesquisar,** procure o grupo de recursos FarmBeats Accelerator.

    > [!NOTE]
    > Selecione o grupo de recursos Do Accelerator que você especificou durante a instalação do FarmBeats.

3. No **painel do Grupo de recursos,** procure *a\* * conta de armazenamento. Por exemplo, procure por **storagedop4k\***.
4. Selecione a conta de armazenamento na coluna **Nome** para exibir o painel da conta de **armazenamento.**
5. No painel de **armazenamento,\* ** selecione **Abrir no Explorer** para abrir o aplicativo Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**e selecione **registros de trabalho**.
7. No **painel de logs de trabalho,** selecione **Download**.
8. Baixe os logs para uma pasta local em sua máquina.
9. Envie um e-mail farmbeatssupport@microsoft.compara o arquivo .zip baixado para .


### <a name="collect-datahub-app-service-logs"></a>Coletar logs de serviço do aplicativo Datahub

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na **caixa pesquisar,** procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que você especificou durante a instalação do FarmBeats.

3. No grupo de recursos, procure a conta de armazenamento *datahublogs.\* * Por exemplo, procure **pordatahublogsmvxmq\***.
4. Selecione a conta de armazenamento na coluna **Nome** para exibir o painel da conta de **armazenamento.**
5. No **painel datahubblogs,\* ** selecione **Abrir no Explorer** para abrir o aplicativo Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**e selecione **appinsights-logs**.
7. No painel **appinsights-logs,** selecione **Download**.
8. Baixe os logs para uma pasta local em sua máquina.
9. Envie um e-mail farmbeatssupport@microsoft.compara o arquivo .zip baixado para .

### <a name="collect-accelerator-app-service-logs"></a>Coletar logs de serviço de aplicativos do Accelerator

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa **pesquisar,** procure o grupo de recursos FarmBeats Accelerator.

    > [!NOTE]
    > Selecione o grupo de recursos FarmBeats Accelerator fornecido durante a instalação do FarmBeats.

3. No grupo de recursos, procure a conta de *armazenamento.\* * Por exemplo, procure por **storagedop4k\***.
4. Selecione a conta de armazenamento na coluna **Nome** para exibir o painel da conta de **armazenamento.**
5. No painel de **armazenamento,\* ** selecione **Abrir no Explorer** para abrir o aplicativo Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**e selecione **appinsights-logs**.
7. No painel **appinsights-logs,** selecione **Download**.
8. Baixe os logs para uma pasta local em sua máquina.
9. Envie um e-mail para a pasta baixada para farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Problemas conhecidos

## <a name="batch-related-issues"></a>Problemas relacionados a lotes

**Mensagem de erro**: "A cota de conta regional das Contas em Lote para a assinatura especificada foi alcançada."

**Ação corretiva**: Aumente a cota ou exclua as contas de lote não utilizadas e execute a implantação.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemas relacionados ao Azure Active Directory (Azure AD)

**Mensagem de erro**: "Não foi possível atualizar as configurações necessárias para o Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Privilégios insuficientes para concluir a operação. Certifique-se de que as configurações acima estão configuradas corretamente para o aplicativo Azure AD."

**Ou seja:** A configuração de registro do aplicativo Azure AD não foi concluída corretamente.  

**Ação corretiva**: Peça ao administrador de TI (a pessoa com acesso ao inquilino ler) para usar nosso [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) para criar o registro do aplicativo Azure AD. Este script também cuida automaticamente das etapas de configuração.

**Mensagem de erro**: "Não foi\<possível\>criar o nome do aplicativo do aplicativo do Active Directory ' neste inquilino: Outro objeto com o mesmo valor para URIs identificador de propriedade já existe."

**Ou seja:** Já existe um registro de aplicativo Azure AD com o mesmo nome.

**Ação corretiva**: Exclua o registro do aplicativo Azure AD existente ou reutilize-o para instalação. Se você estiver reutilizando o registro do aplicativo Azure AD existente, passe o ID do aplicativo e o segredo do cliente para o instalador e reimplante.

## <a name="issues-with-the-inputjson-file"></a>Problemas com o arquivo input.json

**Erro**: Há uma entrada de leitura de erro do arquivo *input.json.*

**Ação corretiva**: Esse problema geralmente surge devido a um erro na especificação do caminho ou nome do arquivo *input.json* correto para o instalador. Faça as correções apropriadas e tente novamente a implantação.

**Erro**: Há um erro de análise de valores no arquivo *input.json.*

**Ação corretiva**: Este problema surge principalmente devido a valores incorretos dentro do arquivo *input.json.* Faça as correções apropriadas e tente novamente a implantação.

## <a name="high-cpu-usage"></a>Alto uso da CPU

**Erro**: Você recebe um alerta de e-mail que se refere a um **alerta de uso de CPU alto**. 

**Ação corretiva:** 
1. Vá para o seu grupo de recursos FarmBeats Datahub.
2. Selecione o **serviço app**.  
3. Vá para a [página de preços](https://azure.microsoft.com/pricing/details/app-service/windows/)do App Service e selecione um nível de preços apropriado.

## <a name="next-steps"></a>Próximas etapas

Se você ainda estiver enfrentando problemas com o FarmBeats, entre em contato com nosso [Fórum de Suporte](https://aka.ms/farmbeatssupport).
