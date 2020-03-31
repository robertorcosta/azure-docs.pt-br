---
title: Problemas/limitações de migração conhecidas com o uso do modo Híbrido
description: Saiba mais sobre problemas/limitações de migração conhecidas com o uso do Azure Database Migration Service no modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649595"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Problemas/limitações de migração conhecidas com o uso do modo híbrido

Problemas e limitações conhecidas associadas ao uso do Azure Database Migration Service no modo híbrido são descritos nas seções a seguir.

## <a name="installer-fails-to-authenticate"></a>Instalador falha em autenticar

Depois de carregar o certificado no seu AdApp, há um atraso de até alguns minutos antes que ele possa autenticar com o Azure. O instalador tentará novamente com algum atraso, mas é possível que o atraso de propagação seja maior do que a repetição, e você verá uma mensagem **FailedToGetGetTokenException.** Se o certificado foi carregado no AdApp correto e o AppId correto foi fornecido em dmsSettings.json, tente executar o comando instalar novamente.

## <a name="service-offline-after-successful-installation"></a>Serviço "offline" após instalação bem sucedida

Se o serviço aparecer como offline após o processo de instalação ser concluído com sucesso, tente usar as seguintes etapas.

1. No portal Azure, no seu caso do Serviço de Migração de Banco de Dados Do Azure, navegue até a guia **configurações híbridas** e verifique se o trabalhador está cadastrado verificando a grade de trabalhadores registrados.

    O status deste trabalhador deve ser **Online,** mas pode mostrar como **Offline** se houver um problema.

2. No computador do trabalhador, verifique o status do serviço executando o seguinte comando PowerShell:

    ```
    Get-Service Scenario*
    ```

    Este comando lhe dá o status do serviço Windows executando o trabalhador. Só deve haver um único resultado. Se o trabalhador for interrompido, você pode tentar reiniciá-lo usando o seguinte comando PowerShell:

    ```
    Start-Service Scenario*
    ```

    Você também pode verificar o serviço na ui do Windows Services.

3. Se o serviço do Windows ciclos entre Running e Stopped, então o trabalhador encontrou problemas de inicialização. Verifique os registros de funcionários híbridos do Serviço de Migração do Banco de Dados do Azure para determinar o problema.

    - Os registros do processo de instalação são armazenados na pasta "logs" dentro da pasta a partir da qual o executável do instalador foi executado.
    - Os logs de trabalho híbrido saem armazenados na pasta **WorkerLogs,** na pasta em que o trabalhador está instalado. O local padrão para os arquivos de log do trabalhador híbrido é **C:\Arquivos do programa\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Usando seu próprio certificado assinado

O certificado gerado pela ação GenerateCert é um certificado auto-assinado, que pode não ser aceitável com base em suas políticas internas de segurança. Em vez de usar este certificado, você pode fornecer seu próprio certificado e fornecer a impressão digital em dmsSettings.json. Este certificado precisará ser carregado no seu AdApp e instalado no computador no qual você está instalando o funcionário híbrido do Serviço de Migração do Banco de Dados Azure. Em seguida, instale este certificado com a chave privada na loja de certificados da Máquina Local.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Executando o serviço do trabalhador como uma conta de baixo privilégio

Por padrão, o serviço de migração de banco de dados do Azure é executado como a conta do Sistema Local. Você pode alterar a conta usada para este serviço desde que a conta que você usa tenha permissões de rede. Para alterar a conta 'execute as' do serviço, use o seguinte processo.

1. Interrompa o serviço, seja através do Windows Services ou usando o comando Stop-Service no PowerShell.

2. Atualize o serviço para usar uma conta de logon diferente.

3. No certmgr para certificados de computador local, dê permissões de chave privadas para a nova conta para os **certificados DMS Hybrid App Key** e **DMS Scenario Engine Key Pair.**

    a. Abra o certmgr para visualizar as seguintes teclas:

    - Chave do aplicativo híbrido DMS
    - Chave de configuração do trabalhador híbrido Do DMS
    - DMS Scenario Engine Key Par

    b. Clique com o botão direito do mouse na entrada **da chave do aplicativo híbrido DMS,** aponte para Todas as **tarefas**e selecione **Gerenciar chaves privadas**.

    c. Na guia **Segurança,** **selecione Adicionar**e digite o nome da conta.

    d. Use as mesmas etapas para conceder permissão de chave privada para a nova conta ao certificado **DMS Scenario Engine Key Pair.**

## <a name="unregistering-the-worker-manually"></a>Descadastrar o trabalhador manualmente

Se você não tiver mais acesso ao computador do trabalhador, você pode cancelar o registro do trabalhador e reutilizar a instância do Serviço de Migração do Banco de Dados do Azure, executando as seguintes etapas:

1. No portal Azure, acesse a instância do Serviço de Migração do Banco de Dados do Azure e navegue até a página de configurações **híbridas.**

   Sua entrada do trabalhador aparece na lista, com o status exibido como **Offline**.

2. À extrema direita da lista de entrada do trabalhador, selecione as elipses e, em seguida, **selecione 'Não registrar 'Não registrar'.**

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Abordando problemas para cenários de migração específicos

As seções abaixo descrevem problemas específicos de cenário relacionados ao uso do modo híbrido do Azure Database Migration Service para realizar uma migração on-line.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Migrações on-line para a instância gerenciada do Azure SQL Database

**Alto uso da CPU**

**Problema**: Para migrações on-line para a instância gerenciada do SQL Database, o computador que executa o funcionário híbrido encontrará alto uso de CPU se houver muitos backups ou se os backups forem muito grandes.

**Mitigação**: Para mitigar esse problema, use backups compactados, divida a migração para que ele use várias ações ou dimensione o computador executando o trabalhador híbrido.
