---
title: SSL - Banco de dados Azure para PostgreSQL - Servidor Único
description: Instruções e informações sobre como configurar a conectividade SSL para o Banco de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476993"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configure a conectividade SSL no Banco de Dados Azure para PostgreSQL - Servidor Único

O Banco de dados do Azure para PostgreSQL prefere conectar-se seus aplicativos cliente ao serviço PostgreSQL usando o protocolo SSL. A aplicação de conexões SSL entre seu servidor de banco de dados e seus aplicativos clientes ajuda a proteger contra ataques "man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão SSL. Você pode optar por desativar o SSL que exige se o seu aplicativo cliente não suportar a conectividade SSL.

## <a name="enforcing-ssl-connections"></a>Impor conexões SSL

Para todos os Bancos de Dados do Azure para servidores PostgreSQL provisionados com o Portal e a CLI do Azure, a imposição de conexões SSL está habilitada por padrão. 

Da mesma forma, cadeias de conexão previamente definidas nas configurações de "Cadeias de Conexão" em seu servidor no Portal do Azure incluem os parâmetros necessários para linguagens comuns a fim de se conectar ao seu servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL

Como opção, você pode desabilitar a imposição da conectividade SSL. O Microsoft Azure recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Visite seu servidor de Banco de Dados do Azure para PostgreSQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Clique em **Salvar**.

![Segurança de Conexão - Desabilitar a imposição de SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores `Enabled` ou `Disabled` respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL

Alguns frameworks de aplicativos que usam postgreSQL para seus serviços de banco de dados não permitem o SSL por padrão durante a instalação. Se o servidor PostgreSQL forçar as conexões SSL, mas o aplicativo não estiver configurado para SSL, o aplicativo pode não se conectar ao servidor de banco de dados. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL

Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados Azure para servidor PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Baixe o arquivo do certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conecte-se usando psql

O exemplo a seguir mostra como se conectar ao servidor PostgreSQL usando o utilitário de linha de comando psql. Use `sslmode=verify-full` a configuração da seqüência de string de conexão para impor a verificação do certificado SSL. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

O comando a seguir é um exemplo da seqüência de conexão psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme se o `sslrootcert` valor passou para corresponder ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas

Revise várias opções de conectividade de aplicativos em [bibliotecas de conexão para o Banco de Dados Azure para PostgreSQL](concepts-connection-libraries.md).
