---
title: Configurar módulos IoT Edge no SQL do Azure no Edge
description: Na segunda parte deste tutorial do SQL do Azure no Edge de três partes para prever as impurezas do minério de ferro, você configurará módulos e conexões do IoT Edge.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593494"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurar módulos e conexões de IoT Edge

Na segunda parte deste tutorial de três partes para prever as impurezas do minério de ferro no SQL do Azure no Edge, você configurará os seguintes módulos do IoT Edge:

- SQL do Azure no Edge
- Módulo gerador de dados do IoT Edge

## <a name="create-azure-stream-analytics-module"></a>Criar módulo do Azure Stream Analytics

Crie um módulo do Azure Stream Analytics que será usado neste tutorial. Para saber mais sobre como usar trabalhos de streaming com o SQL no Edge, confira [Usando trabalhos de streaming com o Banco de Dados SQL no Edge](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

Após a criação do trabalho do Azure Stream Analytics com o ambiente de hospedagem definido como Edge, configure as entradas e saídas do tutorial.

1. Para criar a **entrada**, clique em **+Adicionar entrada de fluxo**. Preencha a seção de detalhes usando as seguintes informações:

   Campo|Valor
   -----|-----
   Formato de serialização do evento|JSON
   Codificação|UTF-8
   Tipo de compactação do evento|Nenhum

2. Para criar a **saída**, clique em **+Adicionar** e escolha Banco de Dados SQL. Preencha a seção de detalhes usando as informações a seguir.

   > [!NOTE]
   > A senha especificada nesta seção precisa ser especificada na senha SA do SQL ao implantar o módulo SQL no Edge, na seção **"Implantar o módulo do SQL do Azure no Edge"** .

   Campo|Valor
   -----|-----
   Banco de dados|IronOreSilicaPrediction
   Nome do servidor|tcp:.,1433
   Nome de Usuário|sa
   Senha|Especifique uma senha forte
   Tabela|IronOreMeasurements1

3. Navegue até a seção **Consulta** e configure a consulta como segue:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Em **Configurar**, escolha **Publicar** e selecione o botão **Publicar**. Salve o URI do SAS para uso com o módulo do SQL do Azure no Edge.

## <a name="specify-container-registry-credentials"></a>Especificar credenciais do registro de contêiner

As credenciais de acesso aos registros de contêiner que hospedam imagens do módulo precisam ser especificadas. Elas podem ser encontradas no registro de contêiner criado no seu grupo de recursos. Navegue até a seção **Chave de acesso**. Anote os seguintes campos:

- Nome do registro
- Servidor de logon
- Nome de Usuário
- Senha

Agora, especifique as credenciais do contêiner no módulo do IoT Edge.

1. Navegue até o hub IoT criado no seu grupo de recursos.

2. Na seção **IoT Edge**, em **Gerenciamento de Dispositivo Automático**, clique em **ID do dispositivo**. Para este tutorial, o ID é `IronOrePredictionDevice`.

3. Selecione a seção **Definir módulos**.

4. Em **Credenciais de Registro de Contêiner**, insira os seguintes valores:

   _Campo_|_Valor_
   -------|-------
   Nome|Nome do registro
   Endereço|Servidor de logon
   Nome do Usuário|Nome de Usuário
   Senha|Senha
  
## <a name="deploy-the-data-generator-module"></a>Implantar o módulo gerador de dados

1. Na seção **Módulos do IoT Edge**, clique em **+ ADICIONAR** e selecione **Módulo do IoT Edge**.

2. Forneça um nome para o módulo do IoT Edge e o URI da imagem.
   O URI da imagem pode ser encontrado no registro de contêiner, no grupo de recursos. Selecione a seção **Repositórios**, em **Serviços**. Para este tutorial, escolha o repositório chamado `silicaprediction`. Selecione a tag apropriada. O URI da imagem terá o formato:

   *servidor de login do registro de contêiner*/*nome do repositório*:*nome da tag*

   Por exemplo:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Clique em **Adicionar**.

## <a name="deploy-the-azure-sql-edge-module"></a>Implantar o módulo de SQL do Azure no Edge

1. Implante o módulo de SQL do Azure no Edge seguindo as etapas listadas em [Deploy Azure SQL Database Edge Preview](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge) (Implantar SQL do Azure no Edge (versão prévia)).

2. Em **Especificar rota** da página **Definir módulos**, especifique as rotas do módulo para comunicação do hub do IoT Edge como segue. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Por exemplo:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Nas configurações de **module twin**, atualize SQLPackage e ASAJonInfo com as URLs SAS relevantes que você salvou anteriormente, no tutorial.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Próximas etapas

- [Implantar o modelo de ML no SQL do Azure no Edge usando ONNX](tutorial-run-ml-model-on-sql-edge.md)
