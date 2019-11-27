---
title: Migrar instância gerenciada do certificado TDE
description: Migrar o certificado que protege a Chave de Criptografia do Banco de Dados de um banco de dados com Transparent Data Encryption para a Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 6ef8d49ba7c9ac2c3c60197c11b9bf5936171f9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420748"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrar o certificado do banco de dados protegido por TDE para a Instância Gerenciada do Banco de Dados SQL do Azure

Durante a migração de um banco de dados protegido por [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) para a Instância Gerenciada do Banco de Dados SQL do Azure usando a opção de restauração nativa, o certificado correspondente do SQL Server local ou de IaaS deve ser migrado antes da restauração do banco de dados. Este artigo orienta você pelo processo de migração manual do certificado para a Instância Gerenciada do Banco de Dados SQL do Azure:

> [!div class="checklist"]
> * Exportação do certificado para um arquivo .pfx (Troca de Informações Pessoais)
> * Extração do certificado do arquivo para a cadeia de caracteres base 64
> * Upload do certificado usando o cmdlet do PowerShell

Para uma opção alternativa usando o serviço totalmente gerenciado para migração direta do banco de dados protegido por TDE e o certificado correspondente, consulte [Como migrar seu banco de dados local para a Instância Gerenciada usando o Serviço de Migração de Banco de Dados do Azure ](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> O certificado migrado é usado apenas para a restauração do banco de dados protegido por TDE. Logo após a conclusão da restauração, o certificado migrado é substituído por um protetor diferente, o certificado gerenciado pelo serviço ou a chave assimétrica do cofre de chaves, dependendo do tipo da criptografia de dados transparente definida na instância.

## <a name="prerequisites"></a>pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes pré-requisitos:

- Ferramenta de linha de comando [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor local ou em outro computador com acesso ao certificado exportado como arquivo. A ferramenta Pvk2Pfx faz parte do [Kit de drivers corporativos do Windows](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), um ambiente de linha de comando autônomo independente.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) versão 5.0 ou posterior instalada.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Verifique se você tem o seguinte:

- Azure PowerShell módulo [instalado e atualizado](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Módulo AZ. SQL](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Execute os seguintes comandos no PowerShell para instalar/atualizar o módulo:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportação do certificado TDE para um arquivo .pfx (Troca de Informações Pessoais)

O certificado pode ser exportado diretamente do SQL Server de origem ou do repositório de certificados, se estiver nesse local.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportar o certificado do SQL Server de origem

Use as etapas a seguir para exportar o certificado com o SQL Server Management Studio e convertê-la no formato pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão sendo usados para nomes e caminhos de certificado e de arquivo por meio das etapas. Eles devem ser substituídos por nomes reais.

1. No SSMS, abra uma nova janela de consulta e conecte-se ao SQL Server de origem.

1. Use o script a seguir para listar os bancos de dados protegidos por TDE e obter o nome da criptografia de proteção de certificado do banco de dados a ser migrado:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![lista de certificados TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Execute o script a seguir para exportar o certificado para um par de arquivos (.cer e .pvk), mantendo as informações de chave pública e privada:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![backup de certificado TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Use o console do PowerShell para copiar informações do certificado de um par de arquivos recém-criados para um arquivo .pfx (Troca de Informações Pessoais), usando a ferramenta Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportar o certificado do repositório de certificados

Se o certificado for mantido no repositório de certificados do computador local do SQL Server, será possível exportá-lo usando as seguintes etapas:

1. Abra o console do PowerShell e execute o comando a seguir para abrir o snap-in de Certificados do Console de Gerenciamento da Microsoft:

   ```cmd
   certlm
   ```

2. Nos snap-in Certificados do MMC, expanda o caminho Pessoal -> Certificados para ver a lista de certificados

3. Clique com botão direito no certificado e clique em Exportar…

4. Siga o assistente para exportar o certificado e a chave privada para um formato de Troca de Informações Pessoais

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Carregar o certificado na Instância Gerenciada do Banco de Dados SQL do Azure usando o cmdlet do Azure PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Comece com as etapas de preparação no PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Após a conclusão de todas as etapas de preparação, execute os comandos a seguir para carregar o certificado codificado em base-64 para a Instância Gerenciada de destino:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, você precisa [configurar um Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) com o arquivo *. pfx* .

1. Comece com as etapas de preparação no PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Após a conclusão de todas as etapas de preparação, execute os comandos a seguir para carregar o certificado codificado em base-64 para a Instância Gerenciada de destino:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Agora, o certificado está disponível para a Instância Gerenciada especificada e o backup do banco de dados protegido por TDE correspondente pode ser restaurado com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a migrar a chave de criptografia de proteção de certificado do banco de dados com a Transparent Data Encryption, do SQL Server local ou de IaaS para a Instância Gerenciada do Banco de Dados SQL do Azure.

Confira [Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started-restore.md) para saber como restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure.
