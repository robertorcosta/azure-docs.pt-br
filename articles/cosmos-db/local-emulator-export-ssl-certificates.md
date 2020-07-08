---
title: Exportar os certificados do Emulador do Azure Cosmos DB
description: Ao desenvolver em linguagens e tempos de execução que não usam o repositório de certificados do Windows, você precisará exportar e gerenciar os certificados TLS/SSL. Esta postagem fornece instruções passo a passo.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: tracking-python
ms.openlocfilehash: ae4840f5ca31f9bbef1fa5f9ffd175a1f1d7696b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85832212"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js

[**Baixar o Emulador**](https://aka.ms/cosmosdb-emulator)

O emulador de Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento, incluindo o uso de conexões TLS. Esta postagem demonstra como exportar os certificados TLS/SSL para uso em linguagens e tempos de execução que não se integram ao repositório de certificados do Windows, como Java, que usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python, que usa [invólucros de soquete](https://docs.python.org/2/library/ssl.html) e Node.js que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Leia mais sobre o emulador em [Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Rotação de certificados
> * Exportando o certificado TLS/SSL
> * Aprendendo a usar o certificado no Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do Azure Cosmos DB são gerados na primeira vez que o emulador é executado. Há dois certificados. Um usado para conexão com o emulador do local, e outro para gerenciar segredos no emulador. O certificado que você quer exportar é o certificado de conexão com o nome amigável "DocumentDBEmulatorCertificate".

Os dois certificados podem ser gerados novamente clicando em **Redefinir Dados**, conforme mostrado abaixo no Emulador do Azure Cosmos DB em execução na Bandeja do Windows. Se você gerar novamente os certificados e instalá-los no repositório de certificados Java, ou usá-los em outro lugar, será necessário atualizá-los, caso contrário, seu aplicativo não conectará mais ao emulador local.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Redefinição de dados do emulador local do Azure Cosmos DB":::

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Como exportar o certificado Azure Cosmos DB TLS/SSL

1. Inicie o Gerenciador de certificados do Windows executando certlm. msc e navegue até a pasta certificados >pessoal e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Etapa 1 de exportação do emulador local do Azure Cosmos DB":::

2. Clique em **Detalhes** e depois em **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Etapa 2 de exportação do emulador local do Azure Cosmos DB":::

3. Clique em **Copiar para arquivo...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Etapa 3 de exportação do emulador local do Azure Cosmos DB":::

4. Clique em **Próximo**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Etapa 4 de exportação do emulador local do Azure Cosmos DB":::

5. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Etapa 5 de exportação do emulador local do Azure Cosmos DB":::

6. Clique em **X.509 codificado em Base-64 (.CER)** e depois em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Etapa 6 de exportação do emulador local do Azure Cosmos DB":::

7. Dê um nome ao certificado. Nesse caso, **documentdbemulatorcert** e, em seguida, clique em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Etapa 7 de exportação do emulador local do Azure Cosmos DB":::

8. Clique em **Concluir**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Etapa 8 de exportação do emulador local do Azure Cosmos DB":::

## <a name="how-to-use-the-certificate-in-java"></a>Como usar o certificado em Java

Durante a execução de aplicativos Java ou MongoDB que usam o cliente Java, é mais fácil instalar o certificado no repositório de certificados Java padrão do que passar os sinalizadores `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Por exemplo, o aplicativo de demonstração Java incluído ( `https://localhost:8081/_explorer/index.html` ) depende do repositório de certificados padrão.

Siga as instruções em [Adicionar um certificado ao Repositório de Certificados de AC Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado X.509 no repositório de certificados Java padrão. Lembre-se de que você trabalhará no diretório %JAVA_HOME% durante a execução da keytool.

Como alternativa, você pode criar e executar um script "bash" que faz isso automaticamente:
```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Depois que o certificado TLS/SSL "CosmosDBEmulatorCertificate" for instalado, seu aplicativo deverá ser capaz de se conectar e usar o emulador de Azure Cosmos DB local. Se você continuar tendo problemas, convém seguir o artigo [depuração de conexões SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . É muito provável que o certificado não esteja instalado no repositório %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se você tiver várias versões do Java instaladas, seu aplicativo poderá usar um repositório de cacerts diferente daquele que você atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como usar o certificado em Python

Por padrão, o [SDK do Python (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para a API do SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de TLS, poderá seguir os exemplos na documentação de [wrappers de soquete Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como usar o certificado no Node.js

Por padrão, o [SDK doNode.js (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API do SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de TLS, poderá seguir os exemplos na [documentação doNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Trocou certificados
> * Exportou o certificado TLS/SSL
> * Aprendeu a usar o certificado no Java, Python e Node.js

Agora você pode seguir para a seção Conceitos para saber mais sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)
