---
title: Garanta a comunicação de dispositivos OPC UA com o OPC Vault - Azure | Microsoft Docs
description: Como registrar aplicativos OPC UA e como emitir certificados de solicitação assinados para seus dispositivos OPC UA com OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454203"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Use o serviço de gerenciamento de certificados OPC Vault

Este artigo explica como registrar aplicativos e como emitir certificados de solicitação assinados para seus dispositivos OPC UA.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="deploy-the-certificate-management-service"></a>Implantar o serviço de gerenciamento de certificados

Primeiro, implante o serviço na nuvem do Azure. Para obter detalhes, consulte [Implantar o serviço de gerenciamento de certificados OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Criar o certificado CA emissor

Se você ainda não fez isso, crie o certificado CA emissor. Para obter detalhes, consulte [Criar e gerenciar o certificado Emissor para Cofre OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Aplicativos OPC UA seguros

### <a name="step-1-register-your-opc-ua-application"></a>Passo 1: Registre seu aplicativo OPC UA 

> [!IMPORTANT]
> A função de Escritor é necessária para registrar um aplicativo.

1. Abra seu serviço `https://myResourceGroup-app.azurewebsites.net`de certificado em , e faça login.
2. Vá para **Registrar Novo**. Para um registro de aplicativo, um usuário precisa ter pelo menos a função de Escritor atribuída.
2. O formulário de inscrição segue convenções de nomeação no OPC UA. Por exemplo, na captura de tela a seguir, as configurações da amostra [do Servidor de Referência OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) na pilha Padrão OPC UA .NET são mostradas:

   ![Captura de tela do registro do servidor de referência do UA](media/howto-opc-vault-secure/reference-server-registration.png "Registro do servidor de referência do UA")

5. Selecione **Registrar** para registrar o aplicativo no banco de dados do aplicativo de serviço de certificado. O fluxo de trabalho orienta diretamente o usuário para a próxima etapa para solicitar um certificado assinado para o aplicativo.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passo 2: Proteja sua solicitação com um certificado de solicitação assinado pela CA

Proteja seu aplicativo OPC UA emitindo um certificado assinado com base em uma Solicitação de Assinatura de Certificado (CSR). Alternativamente, você pode solicitar um novo par de chaves, que inclui uma nova chave privada no formato PFX ou PEM. Para obter informações sobre qual método é suportado para sua aplicação, consulte a documentação do seu dispositivo OPC UA. Em geral, o método de RSE é recomendado, porque não requer uma chave privada para ser transferida por um fio.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Solicite um novo certificado com um novo par de chaves

1. Vá para **Aplicações**.
3. Selecione **Nova solicitação** para um aplicativo listado.

   ![Captura de tela do novo certificado de solicitação](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Selecione **Solicitar novo KeyPair e Certificate** para solicitar uma chave privada e um novo certificado assinado com a chave pública para o seu aplicativo.

   ![Captura de tela de Gerar um novo keypair e certificado](media/howto-opc-vault-secure/generate-new-key-pair.png "Gerar novo par de chaves")

4. Preencha o formulário com um assunto e os nomes de domínio. Para a chave privada, escolha PEM ou PFX com senha. Selecione **Gerar novas chavesparepara** criar a solicitação de certificado.

   ![Captura de tela de detalhes da solicitação do certificado de exibição](media/howto-opc-vault-secure/approve-reject.png "Aprovar Certificado")

5. A aprovação requer um usuário com a função Approver e com permissões de assinatura no Azure Key Vault. No fluxo de trabalho típico, as funções Approver e Requester devem ser atribuídas a diferentes usuários. Selecione **Aprovar** ou **Rejeitar** para iniciar ou cancelar a criação real do par de chaves e da operação de assinatura. O novo par de chaves é criado e armazenado com segurança no Azure Key Vault, até ser baixado pelo solicitante de certificado. O certificado resultante com chave pública é assinado pelo CA. Estas operações podem levar alguns segundos para terminar.

   ![Captura de tela de detalhes da solicitação do certificado de visualização, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-key-pair.png "Exibir par de chaves")

7. A chave privada resultante (PFX ou PEM) e o certificado (DER) podem ser baixados a partir daqui no formato selecionado como download de arquivos binários. Uma versão codificada base64 também está disponível, por exemplo, para copiar e colar o certificado em uma linha de comando ou entrada de texto. 
8. Depois que a chave privada for baixada e armazenada com segurança, você pode selecionar **Excluir chave privada**. O certificado com a chave pública permanece disponível para uso futuro.
9. Devido ao uso de um certificado assinado pela CA, a Lista de Revogação de Certificados e Cert (CRL) deve ser baixada aqui também.

Agora depende do dispositivo OPC UA como aplicar o novo par de chaves. Normalmente, o CERT e o CRL `trusted` da CA são copiados para uma pasta, `own` enquanto as chaves públicas e privadas do certificado de aplicação são aplicadas a uma pasta na loja de certificados. Alguns dispositivos já podem suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Solicite um novo certificado com um CSR 

1. Vá para **Aplicações**.
3. Selecione **Nova solicitação** para um aplicativo listado.

   ![Captura de tela do novo certificado de solicitação](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Selecione **Solicitar novo Certificado com Solicitação de Assinatura** para solicitar um novo certificado assinado para sua solicitação.

   ![Captura de tela de Gerar um novo Certificado](media/howto-opc-vault-secure/generate-new-certificate.png "Gerar novo certificado")

4. Carregue a RSE selecionando um arquivo local ou colando uma RSE codificada base64 no formulário. Selecione **Gerar novo certificado**.

   ![Captura de tela de detalhes da solicitação do certificado de exibição](media/howto-opc-vault-secure/approve-reject-csr.png "Aprovar CSR")

5. A aprovação requer um usuário com a função Approver e com permissões de assinatura no Azure Key Vault. Selecione **Aprovar** ou **Rejeitar** para iniciar ou cancelar a operação de assinatura real. O certificado resultante com chave pública é assinado pelo CA. Esta operação pode levar alguns segundos para terminar.

   ![Captura de tela de detalhes da solicitação do certificado de visualização, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-cert-csr.png "Exibir Certificado")

6. O certificado resultante (DER) pode ser baixado a partir daqui como arquivo binário. Uma versão codificada base64 também está disponível, por exemplo, para copiar e colar o certificado em uma linha de comando ou entrada de texto. 
10. Depois que o certificado é baixado e armazenado com segurança, você pode selecionar **Excluir Certificado**.
11. Devido ao uso de um certificado assinado pela CA, o CA cert e o CRL também devem ser baixados aqui.

Agora depende do dispositivo OPC UA como aplicar o novo certificado. Normalmente, o CERT e o CRL `trusted` da CA são copiados para `own` uma pasta, enquanto o certificado de solicitação é aplicado a uma pasta na loja de certificados. Alguns dispositivos já podem suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-3-device-secured"></a>Passo 3: Dispositivo protegido

O dispositivo OPC UA está agora pronto para se comunicar com outros dispositivos OPC UA protegidos por certificados assinados pela CA, sem configuração adicional.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como proteger dispositivos OPC UA, você pode:

> [!div class="nextstepaction"]
> [Execute um serviço seguro de gerenciamento de certificados](howto-opc-vault-secure-ca.md)
