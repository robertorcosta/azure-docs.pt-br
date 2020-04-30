---
title: TLS-banco de dados do Azure para PostgreSQL-servidor único
description: Instruções e informações sobre como configurar a conectividade TLS para o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141736"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar a conectividade TLS no banco de dados do Azure para PostgreSQL-servidor único

O banco de dados do Azure para PostgreSQL prefere conectar seus aplicativos cliente ao serviço PostgreSQL usando TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL). A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados PostgreSQL está configurado para exigir conexão TLS. Você pode optar por desabilitar a exigência de TLS se o aplicativo cliente não oferecer suporte à conectividade TLS.

## <a name="enforcing-tls-connections"></a>Impondo conexões TLS

Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure e da CLI, a imposição de conexões TLS é habilitada por padrão. 

Da mesma forma, cadeias de conexão predefinidas nas configurações de "cadeias de conexão" em seu servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando TLS. O parâmetro TLS varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

## <a name="configure-enforcement-of-tls"></a>Configurar a imposição de TLS

Opcionalmente, você pode desabilitar a imposição de conectividade TLS. O Microsoft Azure recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Visite seu servidor de Banco de Dados do Azure para PostgreSQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Clique em **Salvar**.

![Segurança de conexão-desabilitar impor TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores `Enabled` ou `Disabled` respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS

Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o TLS por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões TLS, mas o aplicativo não está configurado para TLS, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS

Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um servidor de banco de dados do Azure https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pempara PostgreSQL está localizado em. Baixe o arquivo de certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conectar usando psql

O exemplo a seguir mostra como se conectar ao seu servidor PostgreSQL usando o utilitário de linha de comando psql. Use a `sslmode=verify-full` configuração cadeia de conexão para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local `sslrootcert` para o parâmetro.

O comando a seguir é um exemplo da cadeia de conexão psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas

Examine várias opções de conectividade de aplicativo em [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
