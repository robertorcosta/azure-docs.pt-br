---
title: Certificados necessários para permitir servidores backend
titleSuffix: Azure Application Gateway
description: Este artigo fornece exemplos de como um certificado TLS/SSL pode ser convertido em certificado de autenticação e certificado raiz confiável que são necessários para permitir instâncias de back-end no Gateway de aplicativos do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133039"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Crie certificados para permitir o backend com o Gateway de aplicativos do Azure

Para fazer TLS de ponta a ponta, o Application Gateway exige que as instâncias de back-end sejam permitidas carregando certificados de autenticação/raiz confiáveis. Para o V1 SKU, os certificados de autenticação são necessários, mas para os certificados de raiz confiáveis V2 SKU são necessários para permitir os certificados.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> - Certificado de autenticação de exportação de um certificado backend (para v1 SKU)
> - Exportar certificado raiz confiável a partir de um certificado backend (para v2 SKU)

## <a name="prerequisites"></a>Pré-requisitos

Um certificado de backend existente é necessário para gerar os certificados de autenticação ou certificados de raiz confiáveis necessários para permitir instâncias de backend com o Application Gateway. O certificado backend pode ser o mesmo que o certificado TLS/SSL ou diferente para maior segurança. O Application Gateway não fornece nenhum mecanismo para criar ou comprar um certificado TLS/SSL. Para fins de teste, você pode criar um certificado auto-assinado, mas não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Certificado de autenticação de exportação (para v1 SKU)

Um certificado de autenticação é necessário para permitir instâncias de backend no Application Gateway v1 SKU. O certificado de autenticação é a chave pública dos certificados de servidor backend na Base-64 codificada X.509(. FORMATO CER). Neste exemplo, você usará um certificado TLS/SSL para o certificado backend e exportará sua chave pública para ser usada como certificação de autenticação. Além disso, neste exemplo, você usará a ferramenta Gerenciador de Certificados do Windows para exportar os certificados necessários. Você pode optar por usar qualquer outra ferramenta que seja conveniente.

A partir do certificado TLS/SSL, exporte o arquivo .cer da chave pública (não a chave privada). As etapas a seguir ajudam a exportar o arquivo .cer na Base-64 codificada X.509(. FORMATO CER) para o seu certificado:

1. Para obter um arquivo .cer do certificado, abra **Gerenciar certificados de usuário**. Localize o certificado, normalmente em 'Certificados - Usuário atual\Personal\Certificates' e clique com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**. Se você não encontrar o certificado em Usuário\Pessoal\Certificados, pode ser que você tenha aberto acidentalmente “Certificados - Computador Local” em vez de “Certificados - Usuário Atual”. Se você quiser abrir o Gerenciador de Certificados no escopo do usuário atual usando o PowerShell, digite *certmgr* na janela do console.

   ![Exportação](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Avançar**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não exportar a chave privada** e clique em **Avançar**.

   ![Não exportar a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**.

   ![Codificado em Base 64](./media/certificates-for-backend-authentication/base64.png)

5. Para o **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.

   ![Procurar](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/certificates-for-backend-authentication/finish.png)

7. O certificado foi exportado com êxito.

   ![Sucesso](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante a este:

   ![Exportado](./media/certificates-for-backend-authentication/exported.png)

8. Se você abrir o certificado exportado usando o Bloco de Notas, verá algo semelhante a este exemplo. A seção em azul contém as informações que são enviadas para o gateway do aplicativo. Se você abrir seu certificado com o Bloco de Notas e ele não se parecer com isso, normalmente isso significa que você não exportou usando o X.509 codificado base-64(. FORMATO CER). Além disso, se você quiser usar um editor de texto diferente, é importante saber que alguns editores podem introduzir formatação não intencional em segundo plano. Isso pode criar problemas ao fazer upload do texto desse certificado para o Azure.

   ![Abrir com Bloco de Notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportar certificado raiz confiável (para v2 SKU)

O certificado raiz confiável é necessário para as instâncias de backend da lista branca no gateway de aplicativo v2 SKU. O certificado raiz é um Base-64 codificado X.509(. CER) formato certificado raiz a partir dos certificados de servidor backend. Neste exemplo, usaremos um certificado TLS/SSL para o certificado backend, exportaremos sua chave pública e, em seguida, exportaremos o certificado raiz da CA confiável da chave pública no formato codificado base64 para obter o certificado raiz confiável. Os certificados intermediários devem ser empacotados com o certificado do servidor e instalados no servidor backend.

As seguintes etapas ajudam a exportar o arquivo .cer para o seu certificado:

1. Use as etapas 1-9 mencionadas na seção **Certificado de autenticação de exportação de um certificado backend (para v1 SKU)** acima para exportar a chave pública do seu certificado backend.

2. Uma vez que a chave pública tenha sido exportada, abra o arquivo.

   ![Certificado de autorização aberta](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre certificado](./media/certificates-for-backend-authentication/general.png)

3. Mova-se para a visualização do Caminho de Certificação para visualizar a autoridade de certificação.

   ![detalhes cert](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado raiz e clique em **Exibir Certificado**.

   ![caminho cert](./media/certificates-for-backend-authentication/rootcert.png)

   Você deve ver os detalhes do certificado raiz.

   ![cert informações](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Mova-se para a exibição **Detalhes** e clique **em Copiar para Arquivo...**

   ![copiar raiz cert](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste ponto, você extraiu os detalhes do certificado raiz do certificado backend. Você verá o **Assistente de Exportação de Certificados**. Agora use as etapas 2-9 mencionadas na seção **Certificado de autenticação de exportação de um certificado backend (para v1 SKU)** acima para exportar o certificado raiz confiável no X.509 codificado base-64(. FORMATO CER).

## <a name="next-steps"></a>Próximas etapas

Agora você tem o certificado de autenticação/certificado raiz confiável na Base-64 codificada X.509(. FORMATO CER). Você pode adicionar isso ao gateway de aplicativo para listar os servidores back-end para criptografia TLS de ponta a ponta. Consulte [Configurar TLS de ponta a ponta usando o Gateway de aplicativo com PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

