---
title: Auditoria de instância gerenciada
description: Saiba como começar a auditoria da Instância Gerenciada do Banco de Dados SQL do Azure usando o T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 66c2c5e52ef81f74f7177af897f33eec4ae1779e
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860636"
---
# <a name="azure-sql-database-managed-instance-auditing"></a>Auditoria de Instância Gerenciada do Banco de Dados SQL do Azure

A auditoria de [instância gerenciada](sql-database-managed-instance.md) rastreia eventos de banco de dados e os grava em um log de auditoria na conta de armazenamento do Azure. A auditoria também:

- Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.
- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para saber mais sobre os programas do Azure com suporte à conformidade de padrões, confira a [Central de Confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), onde é possível encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria para o servidor no Armazenamento do Azure

A seção a seguir descreve a configuração da auditoria na instância gerenciada.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Crie um **contêiner** do Armazenamento do Azure no qual os logs de auditoria são armazenados.

   1. Navegue até o Armazenamento do Microsoft Azure onde você deseja armazenar seus logs de auditoria.

      > [!IMPORTANT]
      > - Use uma conta de armazenamento na mesma região da instância gerenciada para evitar leituras/gravações entre regiões. 
      > - Se sua conta de armazenamento estiver atrás de uma Rede Virtual ou um firewall, consulte [Conceder acesso de uma rede virtual](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).
      > - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (os logs gravados durante o período em que a retenção era definida como ilimitada serão preservados, mesmo após a retenção ser habilitada).

   1. Na página da conta de armazenamento, vá até **Visão geral** e clique em **Blobs**.

      ![Widget de Blobs do Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. No menu superior, clique em **+ Contêiner** para criar um novo contêiner.

      ![Ícone de Criar contêiner de blobs](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Forneça um contêiner **Nome**, defina o nível de acesso Público para **Privado** e, em seguida, clique em **OK**.

      ![Criar a configuração do contêiner de blobs](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > O cliente que quer configurar um repositório de logs imutável para seus eventos de auditoria no nível de servidor ou de banco de dados deve seguir as [instruções de fornecidas pelo Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (verifique se você selecionou **Permitir acréscimos adicionais** ao configurar o armazenamento de blob imutável)
  
3. Depois de criar o contêiner para os Logs de auditoria, há duas maneiras de configurá-lo como o destino dos logs de auditoria: [usando o T-SQL](#blobtsql) ou [usando a interface do usuário do SSMS (SQL Server Management Studio)](#blobssms):

   - <a id="blobtsql"></a>Configure o armazenamento de blobs para os logs de auditoria usando o T-SQL:

     1. Na lista de contêineres, clique no contêiner recém-criado e, em seguida, clique em **Propriedades do contêiner**.

        ![Botão Propriedades do contêiner de blobs](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie a URL do contêiner, clicando no ícone de cópia e salve-a (por exemplo, no bloco de notas) para uso futuro. O formato da URL do contêiner deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia do contêiner de blobs](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Gere um **Token SAS** do Armazenamento do Azure para conceder direitos de acesso de auditoria da instância gerenciada à conta de armazenamento:

        - Navegue até a Conta de Armazenamento do Microsoft Azure em que você criou o contêiner na etapa anterior.

        - Clique em **Assinatura de acesso compartilhado** no menu Configurações de Armazenamento.

          ![Ícone de assinatura de acesso compartilhado no menu de configurações de armazenamento](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure a SAS da seguinte forma:

          - **Serviços permitidos**: Blob

          - **Data de início**: para evitar problemas relacionados a fuso horário, é recomendável usar a data de ontem

          - **Data de término**: escolha a data de expiração desse Token SAS

            > [!NOTE]
            > Renove o token após o vencimento para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.
            
            ![Configuração do SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Depois de clicar em Gerar SAS, o Token SAS aparece na parte inferior. Copie o token clicando no ícone de cópia e salve-o (por exemplo, no bloco de notas) para uso futuro.

          ![Copiar o token SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Remova o caractere de ponto de interrogação ("?") do início do token.

     1. Conecte-se à instância gerenciada por meio do SSMS (SQL Server Management Studio) ou de outra ferramenta com suporte.

     1. Execute a seguinte instrução T-SQL para **criar uma nova Credencial** usando a URL do Contêiner e o Token SAS criado nas etapas anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a instrução T-SQL a seguir para criar uma Auditoria de Servidor (escolha seu próprio nome de auditoria e use a URL do Contêiner criada nas etapas anteriores). Se não for especificado, o padrão de `RETENTION_DAYS` será 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Continue [criando uma Especificação de Auditoria de Servidor ou uma Especificação de Auditoria de Banco de Dados](#createspec)

   - <a id="blobssms"></a>Configure o armazenamento de blobs para os logs de auditoria usando o SSMS (SQL Server Management Studio) 18 (Versão Prévia):

     1. Conecte-se à Instância Gerenciada usando a interface do usuário do SSMS (SQL Server Management Studio).

     1. Expanda o nó raiz do Pesquisador de Objetos.

     1. Expanda o nó **Segurança**, clique com o botão direito do mouse no nó **Auditorias** e clique em "Nova Auditoria":

        ![Expandir os nós de segurança e auditoria](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Verifique se a "URL" está selecionada no **Destino de auditoria** e clique em **Procurar**:

        ![Procurar no Armazenamento do Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Entre em sua conta do Azure:

        ![Entrar no Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma assinatura, uma conta de armazenamento e um contêiner de Blobs nos menus suspensos ou crie seu próprio contêiner clicando em **Criar**. Quando terminar, clique em **OK**:

        ![Selecionar assinatura, conta de armazenamento e contêiner de blobs do Azure](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** na caixa de diálogo "Criar Auditoria".

4. <a id="createspec"></a>Depois de configurar o contêiner de blobs como destino para os logs de auditoria, crie e habilite uma Especificação de Auditoria de Servidor ou uma Especificação de Auditoria de Banco de Dados como você faria para o SQL Server:

   - [Guia Criar T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guia Criar T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Habilite a auditoria de servidor criada na etapa 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para informações adicionais:

- [Diferenças de auditoria entre bancos de dados individuais, pools elásticos e instâncias gerenciadas no Banco de Dados SQL do Azure e em bancos de dados do SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTERAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurar a auditoria do seu servidor para o hub de eventos ou logs do Azure Monitor

Os logs de auditoria de uma instância gerenciada podem ser enviados aos hubs de eventos ou aos logs do Azure Monitor. Esta seção descreve como fazer essa configuração:

1. No [Portal do Azure](https://portal.azure.com/), navegue até a instância gerenciada.

2. Clique em **Configurações de diagnóstico**.

3. Clique em **Ativar diagnóstico**. Se o diagnóstico já estiver habilitado, *+Adicionar configuração de diagnóstico* será exibido.

4. Selecione **SQLSecurityAuditEvents** na lista de logs.

5. Selecione um destino para os eventos de auditoria: hub de eventos, logs do Azure Monitor ou ambos. Configure os parâmetros necessários (por exemplo, espaço de trabalho do Log Analytics) para cada destino.

6. Clique em **Save** (Salvar).

    ![Definir as configurações de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Conecte-se à instância gerenciada usando o **SSMS (SQL Server Management Studio)** ou qualquer outro cliente com suporte.

8. Execute a seguinte instrução T-SQL para criar uma auditoria de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie e habilite uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como faria para o SQL Server:

   - [Guia Criar T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guia Criar T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Habilite a auditoria de servidor criada na etapa 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir logs de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir logs armazenados no Armazenamento do Azure

Há vários métodos que podem ser usados para exibir os logs de auditoria de blob.

- Use a função do sistema caractere `sys.fn_get_audit_file` (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, consulte a [documentação de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Explore os logs de auditoria usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os logs de auditoria são salvos como uma coleção de arquivos de blob em um contêiner que foi definido para armazenar os logs de auditoria. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e o formato do log, consulte a [Referência de formato do log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Consumir logs armazenados no Hub de Eventos

Para consumir dados de logs de auditoria do Hub de Eventos, você precisará configurar um fluxo para consumir eventos e gravá-las em um destino. Para obter mais informações, veja a Documentação de Hubs de Eventos do Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e analisar logs armazenados nos logs do Azure Monitor

Se os logs de auditoria forem gravados nos logs do Azure Monitor, eles estarão disponíveis no workspace do Log Analytics, onde você poderá executar pesquisas avançadas nos dados de auditoria. Como ponto de partida, navegue até o workspace do Log Analytics e, na seção *Geral*, clique em *Logs* e insira uma consulta simples, como: `search "SQLSecurityAuditEvents"` para exibir os logs de auditoria.  

Os logs do Azure Monitor fornecem insights operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores. Para ver informações úteis adicionais sobre o idioma e os comandos da pesquisa de logs do Azure Monitor, consulte [Referência de pesquisa dos logs do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no Banco de Dados SQL do Azure, a auditoria funciona no nível do servidor e armazena os arquivos de log `.xel` no armazenamento de Blobs do Azure.
- No SQL Server local/em máquinas virtuais, a auditoria funciona no nível do servidor, mas armazena os eventos em logs de eventos do Windows/do sistema de arquivos.

A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. **Não há suporte** para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de blobs do Azure onde arquivos `.xel` são colocados.
- Uma nova sintaxe `TO EXTERNAL MONITOR` é fornecida para habilitar destinos de hubs de eventos e de logs do Azure Monitor.
- A sintaxe `TO FILE`**não tem suporte** porque a instância gerenciada do Banco de Dados SQL não pode acessar compartilhamentos de arquivos do Windows.
- A opção de desligamento **não é compatível**.
- **Não há suporte** para `queue_delay` de 0.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](sql-database-auditing.md).
- Para saber mais sobre os programas do Azure com suporte à conformidade de padrões, confira a [Central de Confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), onde é possível encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

<!--Image references-->









