---
title: Exportar os certificados do Emulador do Azure Cosmos DB
description: Ao desenvolver em idiomas e tempos de execução que não usam a Loja de Certificados do Windows, você precisará exportar e gerenciar os certificados TLS/SSL. Esta postagem fornece instruções passo a passo.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: b4283ea7d500ca038d9f1cade89c772880ece199
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409074"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js

[**Baixar o Emulador**](https://aka.ms/cosmosdb-emulator)

O Azure Cosmos DB Emulator fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento, incluindo o uso de conexões TLS. Este post demonstra como exportar os certificados TLS/SSL para uso em linguagens e runtimes que não se integram com a Loja de Certificados do Windows, como java, que usa sua própria [loja de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python, que usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html) e Node.js que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Leia mais sobre o emulador em [Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Rotação de certificados
> * Certificado TLS/SSL de exportação
> * Aprendendo a usar o certificado no Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do Azure Cosmos DB são gerados na primeira vez que o emulador é executado. Há dois certificados. Um usado para conexão com o emulador do local, e outro para gerenciar segredos no emulador. O certificado que você quer exportar é o certificado de conexão com o nome amigável "DocumentDBEmulatorCertificate".

Os dois certificados podem ser gerados novamente clicando em **Redefinir Dados**, conforme mostrado abaixo no Emulador do Azure Cosmos DB em execução na Bandeja do Windows. Se você gerar novamente os certificados e instalá-los no repositório de certificados Java, ou usá-los em outro lugar, será necessário atualizá-los, caso contrário, seu aplicativo não conectará mais ao emulador local.

![Redefinição de dados do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Como exportar o certificado Azure Cosmos DB TLS/SSL

1. Inicie o gerenciador de certificados do Windows executando certlm.msc e navegue até a pasta De Certificados de >Pessoal e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    ![Etapa 1 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Clique em **Detalhes** e depois em **OK**.

    ![Etapa 2 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Clique em **Copiar para arquivo...**.

    ![Etapa 3 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Clique em **Avançar**.

    ![Etapa 4 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Avançar**.

    ![Etapa 5 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Clique em **X.509 codificado em Base-64 (.CER)** e depois em **Avançar**.

    ![Etapa 6 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Dê um nome ao certificado. Nesse caso, **documentdbemulatorcert** e, em seguida, clique em **Avançar**.

    ![Etapa 7 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Clique em **concluir**.

    ![Etapa 8 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Como usar o certificado em Java

Durante a execução de aplicativos Java ou MongoDB que usam o cliente Java, é mais fácil instalar o certificado no repositório de certificados Java padrão do que passar os sinalizadores `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Por exemplo, o [aplicativo de demonstração Java](https://localhost:8081/_explorer/index.html) incluído depende do repositório de certificados padrão.

Siga as instruções em [Adicionar um certificado ao Repositório de Certificados de AC Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado X.509 no repositório de certificados Java padrão. Lembre-se de que você trabalhará no diretório %JAVA_HOME% durante a execução da keytool.

Uma vez que o certificado TLS/SSL "CosmosDBEmulator" seja instalado, seu aplicativo deve ser capaz de conectar e usar o Emulador DB do Azure Cosmos local. Se você continuar a ter problemas, você pode querer seguir o artigo [Debugging SSL/TLS Connections.](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) É muito provável que o certificado não esteja instalado no repositório %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se você tiver várias versões do Java instaladas, seu aplicativo poderá usar um repositório de cacerts diferente daquele que você atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como usar o certificado em Python

Por padrão, o [Python SDK (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para a API SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se no entanto você quiser usar a validação TLS, você pode seguir os exemplos na documentação de [invólucros do soquete Python.](https://docs.python.org/2/library/ssl.html)

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como usar o certificado no Node.js

Por padrão, o [Node.js SDK (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se no entanto você quiser usar a validação TLS, você pode seguir os exemplos na [documentação Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Trocou certificados
> * Exportou o certificado TLS/SSL
> * Aprendeu a usar o certificado no Java, Python e Node.js

Agora você pode seguir para a seção Conceitos para saber mais sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)
