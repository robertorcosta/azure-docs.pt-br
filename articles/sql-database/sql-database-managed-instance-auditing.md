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
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387760"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introdução à Auditoria da instância gerenciada do Banco de Dados SQL do Azure

A auditoria [de instância gerenciada](sql-database-managed-instance.md) rastreia eventos do banco de dados e os grava em um registro de auditoria em sua conta de armazenamento Do Azure. A auditoria também:

- Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.
- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas do Azure que suportam a conformidade com os padrões, consulte o [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria para o servidor no Armazenamento do Azure

A seção a seguir descreve a configuração da auditoria na instância gerenciada.

1. Vá para o [portal Azure.](https://portal.azure.com)
2. Crie um **contêiner** do Armazenamento do Azure no qual os logs de auditoria são armazenados.

   1. Navegue até o Armazenamento do Microsoft Azure onde você deseja armazenar seus logs de auditoria.

      > [!IMPORTANT]
      > Use uma conta de armazenamento na mesma região da instância gerenciada para evitar leituras/gravações entre regiões.

   1. Na página da conta de armazenamento, vá até **Visão geral** e clique em **Blobs**.

      ![Widget de Blobs do Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. No menu superior, clique em **+ Contêiner** para criar um novo contêiner.

      ![Ícone de Criar contêiner de blobs](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Forneça um contêiner **Nome**, defina o nível de acesso Público para **Privado** e, em seguida, clique em **OK**.

      ![Criar a configuração do contêiner de blobs](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > O cliente que deseja configurar um armazenamento de log imutável para os eventos de auditoria em nível de servidor ou de banco de dados deve seguir as [instruções fornecidas pelo Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Certifique-se de ter selecionado **Permitir anexos adicionais** quando configurar o armazenamento imutável do blob)
  
3. Depois de criar o contêiner para os Logs de auditoria, há duas maneiras de configurá-lo como o destino dos logs de auditoria: [usando o T-SQL](#blobtsql) ou [usando a interface do usuário do SSMS (SQL Server Management Studio)](#blobssms):

   - <a id="blobtsql"></a>Configure o armazenamento de blobs para os logs de auditoria usando o T-SQL:

     1. Na lista de contêineres, clique no contêiner recém-criado e, em seguida, clique em **Propriedades do contêiner**.

        ![Botão Propriedades do contêiner de blobs](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie a URL do contêiner, clicando no ícone de cópia e salve-a (por exemplo, no bloco de notas) para uso futuro. O formato da URL do contêiner deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia do contêiner de blobs](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Gerar um **Token SAS** de armazenamento do Azure para conceder direitos de acesso de auditoria de instância gerenciada à conta de armazenamento:

        - Navegue até a Conta de Armazenamento do Microsoft Azure em que você criou o contêiner na etapa anterior.

        - Clique em **Assinatura de acesso compartilhado** no menu Configurações de Armazenamento.

          ![Ícone de assinatura de acesso compartilhado no menu de configurações de armazenamento](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure a SAS da seguinte forma:

          - **Serviços permitidos**: Blob

          - **Data de início**: para evitar problemas relacionados ao fuso horário, recomenda-se usar a data de ontem

          - **Data de término**: escolha a data em que este Token SAS expira

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

        ![Selecione a assinatura do Azure, a conta de armazenamento e o contêiner blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** na caixa de diálogo "Criar Auditoria".

4. <a id="createspec"></a>Depois de configurar o contêiner Blob como alvo para os logs de auditoria, crie e habilite uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como você faria para o SQL Server:

   - [Guia Criar T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guia Criar T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Habilite a auditoria do servidor que você criou na etapa 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para informações adicionais:

- [Auditoria de diferenças entre bancos de dados únicos, pools elásticos e instâncias gerenciadas no Banco de Dados SQL do Azure e bancos de dados no SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configure a auditoria para o seu servidor nos registros do Event Hub ou do Monitor do Azure

Os registros de auditoria de uma instância gerenciada podem ser enviados para registros even hubs ou Azure Monitor. Esta seção descreve como fazer essa configuração:

1. No [Portal do Azure](https://portal.azure.com/), navegue até a instância gerenciada.

2. Clique em **Configurações de diagnóstico**.

3. Clique em **Ativar diagnóstico**. Se o diagnóstico já estiver habilitado, *+Adicionar configuração de diagnóstico* será exibido.

4. Selecione **SQLSecurityAuditEvents** na lista de logs.

5. Selecione um destino para os eventos de auditoria - Event Hub, Azure Monitor logs ou ambos. Configure os parâmetros necessários (por exemplo, espaço de trabalho do Log Analytics) para cada destino.

6. Clique em **Salvar**.

    ![Definir as configurações de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Conecte-se à instância gerenciada usando **o SQL Server Management Studio (SSMS)** ou qualquer outro cliente suportado.

8. Execute a seguinte instrução T-SQL para criar uma auditoria de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Criar e habilitar uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como você faria para o SQL Server:

   - [Guia Criar T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guia Criar T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Habilite a auditoria do servidor criada na etapa 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir logs de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir logs armazenados no Armazenamento do Azure

Há vários métodos que podem ser usados para exibir os logs de auditoria de blob.

- Use a função do sistema caractere `sys.fn_get_audit_file` (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, consulte a [documentação de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Você pode explorar logs de auditoria usando uma ferramenta como [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os logs de auditoria são salvos como uma coleção de arquivos de blob em um contêiner que foi definido para armazenar os logs de auditoria. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, nomeações de convenções e formato de log, consulte a [referência de formato de log de auditoria blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Consumir logs armazenados no Hub de Eventos

Para consumir dados de logs de auditoria do Hub de Eventos, você precisará configurar um fluxo para consumir eventos e gravá-las em um destino. Para obter mais informações, veja a Documentação de Hubs de Eventos do Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e analisar registros armazenados em logs do Monitor do Azure

Se os registros de auditoria forem gravados nos registros do Azure Monitor, eles estão disponíveis no espaço de trabalho do Log Analytics, onde você pode executar pesquisas avançadas nos dados da auditoria. Como ponto de partida, navegue até o espaço de trabalho do Log Analytics e, na seção *Geral,* clique em *Logs* e insira uma consulta simples, como: `search "SQLSecurityAuditEvents"` para exibir os logs de auditoria.  

Os logs do Monitor do Azure fornecem insights operacionais em tempo real usando painéis integrados de pesquisa e personalizados para analisar facilmente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre o idioma e os comandos de pesquisa do Azure Monitor, consulte [a referência de pesquisa de logs do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no Banco de Dados SQL do Azure, a auditoria funciona no nível do servidor e armazena os arquivos de log `.xel` no armazenamento de Blobs do Azure.
- No SQL Server local/em máquinas virtuais, a auditoria funciona no nível do servidor, mas armazena os eventos em logs de eventos do Windows/do sistema de arquivos.

A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. **Não há suporte** para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de blobs do Azure onde arquivos `.xel` são colocados.
- Uma nova sintaxe `TO EXTERNAL MONITOR` é fornecida para permitir metas de logs even Hub e Azure Monitor.
- A sintaxe `TO FILE`**não tem suporte** porque a instância gerenciada do Banco de Dados SQL não pode acessar compartilhamentos de arquivos do Windows.
- A opção de desligamento **não é compatível**.
- **Não há suporte** para `queue_delay` de 0.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](sql-database-auditing.md).
- Para obter mais informações sobre os programas do Azure que suportam a conformidade com os padrões, consulte o [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

<!--Image references-->









