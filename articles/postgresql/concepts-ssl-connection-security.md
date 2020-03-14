---
title: SSL-banco de dados do Azure para PostgreSQL-servidor único
description: Instruções e informações sobre como configurar a conectividade SSL para o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371657"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar a conectividade SSL no banco de dados do Azure para PostgreSQL-servidor único

O Banco de dados do Azure para PostgreSQL prefere conectar-se seus aplicativos cliente ao serviço PostgreSQL usando o protocolo SSL. A imposição de conexões SSL entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão SSL. Você pode optar por desabilitar a exigência de SSL se o aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="enforcing-ssl-connections"></a>Impor conexões SSL

Para todos os Bancos de Dados do Azure para servidores PostgreSQL provisionados com o Portal e a CLI do Azure, a imposição de conexões SSL está habilitada por padrão. 

Da mesma forma, cadeias de conexão previamente definidas nas configurações de "Cadeias de Conexão" em seu servidor no Portal do Azure incluem os parâmetros necessários para linguagens comuns a fim de se conectar ao seu servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL

Como opção, você pode desabilitar a imposição da conectividade SSL. O Microsoft Azure recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Visite seu servidor de Banco de Dados do Azure para PostgreSQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Em seguida, clique em **Salvar**.

![Segurança de Conexão - Desabilitar a imposição de SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores `Enabled` ou `Disabled` respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL

Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o SSL por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões SSL, mas o aplicativo não está configurado para SSL, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL

Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um servidor de banco de dados do Azure para PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Baixe o arquivo de certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conectar usando psql

O exemplo a seguir mostra como se conectar ao seu servidor PostgreSQL usando o utilitário de linha de comando psql. Use a configuração `sslmode=verify-full` cadeia de conexão para impor a verificação de certificado SSL. Passe o caminho do arquivo de certificado local para o parâmetro `sslrootcert`.

O comando a seguir é um exemplo da cadeia de conexão psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>Conectividade TLS no banco de dados do Azure para PostgreSQL servidor único

Banco de dados do Azure para PostgreSQL-o servidor único dá suporte à criptografia para clientes que se conectam ao seu servidor de banco de dados usando o protocolo TLS. O TLS é um protocolo padrão do setor que garante conexões de rede seguras entre o servidor de banco de dados e os aplicativos cliente, permitindo que você atenda aos requisitos de conformidade.

### <a name="tls-settings"></a>Configurações de protocolo TLS

Agora, os clientes têm a capacidade de impor a versão de TLS para o cliente que se conecta ao banco de dados do Azure para PostgreSQL servidor único. Para usar a opção TLS, use a configuração de opção de **versão mínima do TLS** . Os valores a seguir são permitidos para esta configuração de opção:

|  Configuração de TLS mínima             | Versão de TLS com suporte                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e superior |
| TLS1_1                           | TLS 1,1, TLS 1,2 e superior          |
| TLS1_2                           | TLS versão 1,2 e superior           |


Por exemplo, definir essa versão de configuração de TLS mínima como TLS 1,0 significa que o servidor permitirá conexões de clientes usando TLS 1,0, 1,1 e 1.2 +. Como alternativa, definir isso como 1,2 significa que você só permite conexões de clientes que usam TLS 1,2 e todas as conexões com TLS 1,0 e TLS 1,1 serão rejeitadas.

> [!Note] 
> O banco de dados do Azure para PostgreSQL usa padrões de servidor único para TLS desabilitado para todos os novos servidores.
>
> Atualmente, as versões do TLS com suporte para o banco de dados pelo para PostgreSQL são TLS 1,0, 1,1 e 1,2.

Para saber como definir a configuração de TLS para o servidor único do banco de dados do Azure para PostgreSQL, consulte [como definir a configuração de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Próximas etapas

Examine várias opções de conectividade de aplicativo em [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
