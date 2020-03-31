---
title: Implantar o SQL Database Edge Preview usando o portal Azure | Microsoft Docs
description: Saiba como implantar o Azure SQL Database Edge usando o portal Azure
keywords: implantar borda de banco de dados sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246714"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Implantar visualização de borda do banco de dados Azure SQL

O Azure SQL Database Edge Preview é um mecanismo de banco de dados relacional otimizado para implantações de IoT e Azure IoT Edge. Ele fornece recursos para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicativos e soluções de IoT. Este quickstart mostra como começar com a criação de um módulo Azure SQL Database Edge através do Azure IoT Edge usando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Faça login no [portal Azure](https://portal.azure.com/).
* Envie uma solicitação [aqui,](https://azure.microsoft.com/services/sql-database-edge/#contact)para que a assinatura seja ativada para implantação do SQL Database Edge.
* Crie [um Hub Azure IoT](../iot-hub/iot-hub-create-through-portal.md).
* Registre um [dispositivo de borda IoT no portal Azure](../iot-edge/how-to-register-device-portal.md).
* Prepare o dispositivo IoT Edge para [implantar o módulo IoT Edge a partir do portal Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Para implantar um Azure Linux VM como um dispositivo IoT Edge, consulte este [guia de partida rápida](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Implantar módulo de borda do banco de dados SQL do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure SQL Database Edge pode ser implantado em um dispositivo de borda através do mercado.

1. Encontre o módulo Azure SQL Database Edge no Azure Marketplace.<br><br>

   ![SQL Database Edge em MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software que melhor corresponda aos seus requisitos e clique **em Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. Na página Dispositivos de destino para Módulo de Borda IoT, especifique os seguintes detalhes e clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscription  |  A assinatura do Azure a qual o IoT Hub foi criado |
   |Hub IoT   |  Nome do IoT Hub onde o dispositivo IoT Edge está registrado e, em seguida, selecione a opção "Implantar em um dispositivo"|
   |Nome do dispositivo de borda IoT  |  Nome do dispositivo IoT Edge onde o SQL Database Edge seria implantado |

4. Na página **Definir módulos,** navegue até a seção em módulos de implantação e clique em **Configurar** no módulo SQL Database Edge. 

5. No painel **Módulos Personalizados de Borda ioT,** especifique os valores desejados para as variáveis de ambiente e/ou personalize as opções de criação e propriedades desejadas para o módulo. Para obter uma lista completa de variáveis de ambiente suportadas, consulte [variáveis do ambiente do servidor SQL .](/sql/linux/sql-server-linux-configure-environment-variables/)

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | Nome | Nome do módulo. |
   |SA_PASSWORD  | Especifique uma senha forte para a conta de administração sql database Edge. |
   |MSSQL_LCID   | Define a ID de idioma a ser usada para o SQL Server. Por exemplo, 1036 é francês. |
   |MSSQL_COLLATION | Define a ordenação padrão para o SQL Server. Esta configuração substitui o mapeamento padrão do ID de linguagem (LCID) à colagem. |

   > [!NOTE]
   > Por favor, não altere ou atualize o **URI de imagem** ou as configurações **ACCEPT_EULA** no módulo.

6. No painel **Módulos Personalizados da Borda IoT,** atualize o contêiner para criar opções de valor desejado para a **porta host**. Se você precisar implantar mais de um módulo SQL DB Edge, certifique-se de atualizar a opção de montagem para criar uma nova fonte & par de destino para o volume persistente. Para obter mais informações sobre montagens e volumes, consulte [Usar volumes](https://docs.docker.com/storage/volumes/) na documentação do docker. 

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

7. No painel **Módulos Personalizados de Borda ioT,** atualize as *propriedades desejadas do conjunto de módulos duplos* para incluir a localização do pacote SQL e as informações de trabalho de análise de fluxo. Esses dois campos são opcionais e devem ser usados se você quiser implantar o módulo SQL Database Edge com um banco de dados e um trabalho de streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. No painel **Módulos personalizados da Borda IoT,** defina a *política de reinicialização* para sempre e *o status desejado* para ser executado.
9. No painel **Módulos personalizados da Borda IoT,** clique **em Salvar**.
10. Na página **Definir módulos** clique **em "A seguir".**
11. Na **página Especificar rota (opcional)** da página **Definir módulos,** especifique as rotas para módulo para módulo ou módulo para comunicação do IoT Edge Hub, consulte [Implantar módulos e estabelecer rotas em IoT Edge](../iot-edge/module-composition.md).
12. Clique em **Avançar**.
13. Clique em **Enviar**.

Neste quickstart, você implantou um Módulo de Borda do Banco de Dados SQL em um dispositivo IoT Edge.

## <a name="next-steps"></a>Próximas etapas

- [Machine Learning e Inteligência Artificial com ONNX em SQL Database Edge](onnx-overview.md).
- Construindo uma solução de IoT de ponta a ponta com a borda do banco de dados SQL usando o IoT Edge.
