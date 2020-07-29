---
title: Implantar o SQL do Azure no Edge (versão prévia) usando o portal do Azure
description: Saiba como implantar o SQL do Azure no Edge (versão prévia) usando o portal do Azure
keywords: implantar o SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 43359b66ba747dba7b3294d022a2c1aa2a3e624c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233251"
---
# <a name="deploy-azure-sql-edge-preview"></a>Implantar o SQL do Azure no Edge (versão prévia) 

O SQL do Azure no Edge (versão prévia) é um mecanismo de banco de dados relacional otimizado para implantações de IoT e do Azure IoT Edge. Ele oferece funcionalidades para a criação de uma camada de processamento e armazenamento de dados de alto desempenho para aplicativos e soluções de IoT. Este início rápido mostra como começar a criar um módulo do SQL do Azure no Edge por meio do Azure IoT Edge usando o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Entre no [portal do Azure](https://portal.azure.com/).
* Crie um [Hub IoT do Azure](../iot-hub/iot-hub-create-through-portal.md).
* Registre um [dispositivo IoT Edge no portal do Azure](../iot-edge/how-to-register-device-portal.md).
* Prepare o dispositivo IoT Edge para [implantar o módulo IoT Edge do portal do Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Para implantar uma VM do Linux no Azure como um dispositivo IoT Edge, confira este [guia de início rápido](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Implantar o módulo do SQL no Edge por meio do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O SQL do Azure no Edge pode ser implantado em um dispositivo de borda por meio do marketplace.

1. Encontre o módulo do SQL do Azure no Edge no Azure Marketplace.<br><br>

   ![SQL no Edge no Marketplace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software mais adequado às suas necessidades e clique em **Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. Na página Dispositivos de Destino para o Módulo do IoT Edge, especifique os detalhes a seguir e clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscription  |  A assinatura do Azure na qual o Hub IoT foi criado |
   |Hub IoT   |  Nome do Hub IoT em que o dispositivo IoT Edge está registrado e selecione a opção "Implantar em um dispositivo"|
   |Nome do Dispositivo IoT Edge  |  Nome do dispositivo IoT Edge no qual o SQL no Edge será implantado |

4. Na página **Definir Módulos**, navegue até a seção sobre módulos de implantação e clique em **Configurar** no módulo do SQL no Edge. 

5. No painel **Módulos Personalizados do IoT Edge**, especifique os valores desejados para as variáveis de ambiente e/ou personalize as opções de criação e as propriedades desejadas para o módulo. Para obter uma lista completa de variáveis de ambiente compatíveis, confira [Variáveis de ambiente de contêiner do SQL Server](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | Nome | Nome do módulo. |
   |SA_PASSWORD  | Especifique uma senha forte para a conta do administrador do SQL no Edge. |
   |MSSQL_LCID   | Define a ID de idioma a ser usada para o SQL Server. Por exemplo, 1036 é francês. |
   |MSSQL_COLLATION | Define a ordenação padrão para o SQL Server. Essa configuração substitui o mapeamento padrão do LCID (ID de idioma) na ordenação. |

   > [!NOTE]
   > Não altere nem atualize o **URI da Imagem** nem as configurações de **ACCEPT_EULA** no módulo.

6. No painel **Módulos Personalizados do IoT Edge**, atualize o valor desejado das opções de criação de contêiner para a **Porta do Host**. Caso você precise implantar mais de um módulo do BD SQL no Edge, atualize a opção de montagens para criar um par de origem e destino para o volume persistente. Para obter mais informações sobre montagens e volume, veja [Usar volumes](https://docs.docker.com/storage/volumes/) na documentação do Docker. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. No painel **Módulos Personalizados do IoT Edge**, atualize as *propriedades desejadas de Definir módulos gêmeos* para incluir a localização do pacote do SQL e as informações do trabalho do Stream Analytics. Esses dois campos são opcionais e devem ser usados se você deseja implantar o módulo do SQL no Edge com um banco de dados e um trabalho de streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. No painel **Módulos Personalizados do IoT Edge**, defina *Política de Reinicialização* como sempre e *Status Desejado* como em execução.
9. No painel **Módulos Personalizados do IoT Edge**, clique em **Salvar**.
10. Na página **Definir módulos** clique **Avançar**.
11. Em **Especificar a Rota (opcional)** da página **Definir Módulos**, especifique as rotas da comunicação entre módulos ou do módulo para o Hub do IoT Edge; confira [Implantar módulos e estabelecer rotas no IoT Edge](../iot-edge/module-composition.md).
12. Clique em **Próximo**.
13. Clique em **Enviar**.

Neste início rápido, você implantou um módulo do SQL no Edge em um dispositivo IoT Edge.

## <a name="next-steps"></a>Próximas etapas

- [Machine learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).
- [Como criar uma solução de IoT de ponta a ponta com o SQL no Edge usando o IoT Edge](tutorial-deploy-azure-resources.md).