---
title: Autenticar-se no Azure Key Vault
description: Saiba como autenticar com o Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589975"
---
# <a name="authenticate-to-azure-key-vault"></a>Autenticar-se no Azure Key Vault

## <a name="overview"></a>Visão geral

O Azure Key Vault é uma solução de gerenciamento de segredos que permite centralizar o armazenamento de segredos do aplicativo e controlar a distribuição deles. O Azure Key Vault elimina a necessidade de armazenar credenciais em aplicativos. Seu aplicativo pode se autenticar no Key Vault para recuperar as credenciais necessárias. Este documento abordará os conceitos básicos da autenticação no Key Vault.

Este documento ajudará você a entender como funciona a autenticação no Key Vault. Este documento explicará o fluxo de autenticação, mostrará como permitir acesso ao seu Key Vault e incluirá um tutorial para recuperar um segredo armazenado no Key Vault de um aplicativo Python de exemplo.

Este documento abordará:

* Conceitos Principais
* Registro da entidade de segurança
* Noções básicas sobre o fluxo de autenticação do Key Vault
* Permitir à entidade de serviço acesso ao Key Vault
* Tutorial (Python)

## <a name="key-concepts"></a>Conceitos Principais

### <a name="azure-active-directory-concepts"></a>Conceitos do Azure Active Directory

* AAD (Azure Active Directory) – É o serviço de gerenciamento de identidade e de acesso baseado em nuvem da Microsoft, que ajuda seus funcionários a se conectar e acessar recursos

* Definição de função – É uma coleção de permissões.  O AAD tem funções padrão (Proprietário, Colaborador ou Leitor) que contêm níveis de permissões para executar operações como ler, gravar e excluir em um recurso do Azure. As funções também podem ser definições personalizadas criadas por usuários com permissões granulares específicas.

* Registro de aplicativo – Quando você registra um aplicativo do Azure AD, dois objetos são criados no locatário do Azure AD, que são um objeto de aplicativo e um objeto de entidade de serviço. Você pode considerar o aplicativo como a representação global de seu aplicativo para uso em todos os locatários e a entidade de serviço como a representação local para uso em um locatário específico.

### <a name="security-principal-concepts"></a>Conceitos de entidade de segurança

* Entidade de segurança – Um objeto que representa um usuário, grupo, entidade de serviço ou uma identidade gerenciada que está solicitando acesso aos recursos do Azure.

* Usuário – Um indivíduo que tem um perfil no Azure Active Directory.

* Grupo - Um grupo de usuários criados no Azure Active Directory. Quando você atribuir uma função a um grupo, todos os usuários dentro desse grupo têm essa função.

* Entidade de serviço - Uma identidade de segurança usada por aplicativos ou serviços para acessar recursos específicos do Azure. Você pode pensar nela como uma identidade do usuário (nome de usuário e senha ou certificado) para um aplicativo.

* Identidade Gerenciada – uma identidade no Azure Active Directory que é gerenciada automaticamente pelo Azure.

* ID do Objeto (ID do Cliente) – um identificador exclusivo gerado pelo Azure AD vinculado a uma entidade de serviço durante o provisionamento inicial dessa entidade.

## <a name="security-principal-registration"></a>Registro da entidade de segurança

1. O administrador registra um usuário ou aplicativo (entidade de serviço) no Azure Active Directory.

2. O administrador cria um Azure Key Vault e configura as ACLs (políticas de acesso).

3. (Opcional) O administrador configura o Firewall do Azure Key Vault.

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Noções básicas sobre o fluxo de autenticação do Key Vault

1. Uma entidade de serviço faz uma chamada para autenticar no AAD. Isso pode acontecer de várias maneiras:
    * Um usuário pode fazer logon no portal do Azure usando um nome de usuário e senha.
    * Um aplicativo usa uma ID do cliente e apresenta um certificado do cliente ou segredo do cliente para o AAD
    * Um recurso do Azure, como uma máquina virtual, tem um MSI atribuído e entra em contato com o ponto de extremidade REST do IMDS para obter um token de acesso.

2. Se a autenticação para o AAD for bem-sucedida, a entidade de serviço receberá um token OAuth.
3. A entidade de serviço faz uma chamada para o Key Vault.
4. O Firewall do Azure Key Vault determina se a chamada deve ser permitida.
    * Cenário 1: o Firewall do Key Vault está desabilitado, o ponto de extremidade público (URI) do Key Vault pode ser acessado pela Internet pública. A chamada é permitida.
    * Cenário 2: o chamador é um serviço confiável do Azure Key Vault. Determinados serviços do Azure podem ignorar o Firewall do Key Vault se a opção estiver selecionada. [Lista de Serviços Confiáveis do Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Cenário 3: o chamador é listado no firewall do Azure Key Vault por endereço IP, rede virtual ou ponto de extremidade de serviço.
    * Cenário 4: o chamador pode acessar Azure Key Vault por meio de uma conexão de link privado configurada.
    * Cenário 5: o chamador não está autorizado e uma resposta proibida é retornada.
5. O Key Vault faz uma chamada para o AAD a fim de validar o token de acesso da entidade de serviço.
6. O Key Vault verifica se a entidade de serviço tem permissões de política de acesso suficientes para executar a operação solicitada; neste exemplo, a operação é obter o segredo.
7. O Key Vault fornece o segredo para a entidade de serviço.

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Permitir à entidade de serviço acesso ao Key Vault

1. Se você ainda não tiver uma entidade de serviço, crie uma. [Criar uma entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Adicione uma atribuição de função à entidade de serviço nas configurações de IAM do Azure Key Vault. Você pode adicionar funções previamente atribuídas de Proprietário, Colaborador ou Leitor. Você também pode criar funções personalizadas para sua entidade de serviço. Você deve seguir a entidade com menores privilégios e fornecer apenas o acesso mínimo necessário para sua entidade de serviço. 
3.  Configure o Firewall do Key Vault. Você pode manter o Firewall do Key Vault desabilitado e permitir o acesso da Internet pública (menos seguro e mais fácil de configurar). Você também pode restringir o acesso a intervalos de IP específicos, pontos de extremidade de serviço, redes virtuais ou pontos de extremidade privados (mais seguro).
4.  Adicionar uma política de acesso à entidade de serviço, essa é uma lista de operações que sua entidade de serviço pode executar no Key Vault. Você deve usar a entidade com os menores privilégios e limitar as operações que a entidade de serviço pode executar. No entanto, se você não fornecer permissões suficientes, o acesso será negado à entidade de serviço.

## <a name="tutorial"></a>Tutorial

Neste tutorial, você aprenderá a configurar uma entidade de serviço para autenticar no Key Vault e recuperar um segredo. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Parte 1:  Criar uma entidade de serviço no portal do Azure

1. Faça logon no Portal do Azure
1. Pesquise o Azure Active Directory
1. Clique na guia "Registros de Aplicativo"
1. Clique em "+ Novo Registro"
1. Criar um nome para a entidade de serviço
1. Selecione Registrar

Neste ponto, você tem uma entidade de serviço registrada. Você pode exibi-la selecionando "Registros de Aplicativo". Sua entidade de serviço agora receberá uma GUID de ID do cliente, considere isso como um "nome de usuário" para sua entidade de serviço. Agora precisamos criar uma "senha" para a entidade de serviço. Você pode usar um segredo do cliente ou um certificado do cliente. Observe que o uso de um segredo do cliente para autenticação não é seguro e só deve ser usado para fins de teste. Este tutorial mostrará a você como usar um certificado do cliente.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Parte 2: criar um certificado do cliente para a entidade de serviço

1. Criar um certificado

    * Opção 1: criar um certificado usando [OpenSSL](https://www.openssl.org/) (somente para fins de teste, não use certificados autoassinados em produção)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Opção 2: criar um certificado usando o Key Vault. [Criar um certificado no Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Baixar o certificado no formato PEM
1. Abra o portal do Azure e navegue até o Azure Active Directory
1. Clique em "Registros de Aplicativo"
1. Selecione a entidade de serviço que você criou na Parte 1.
1. Clique na guia "Certificados e Segredos" de sua entidade de serviço
1. Carregue o certificado usando o botão "Carregar Certificado"

### <a name="part-3-configure-an-azure-key-vault"></a>Parte 3: configurar um Azure Key Vault

1. Criar um [Link](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) do Azure Key Vault

2. Configurar permissões de IAM do Key Vault
    1. Navegue até o Key Vault
    1. Selecione a guia "Controle de Acesso (IAM)"
    1. Clique em Adicionar Atribuição de Função
    1. Selecione "Colaborador" na lista suspensa
    1. Insira o nome ou a ID do cliente da entidade de serviço que você criou
    1. Clique em "Exibir Atribuições de Função" para confirmar se a entidade de serviço está listada

3. Configure as permissões de política de acesso para o Key Vault
    1. Navegue até o Key Vault
    1. Selecione a guia "Políticas de Acesso" em "Configurações"
    1. Selecione o link "+ Adicionar Política de Acesso"
    1. No menu suspenso Permissões do Segredo, marque as permissões "Obter" e "Listar".
    1. Selecione sua entidade de serviço por nome ou ID do cliente.
    1. Selecione "Adicionar"
    1. Selecione "Salvar"

4. Crie um segredo em seu Key Vault.
    1. Navegue até o Key Vault
    1. Clique na guia "Segredos" em Configurações
    1. Clique em "+ Gerar/Importar"
    1. Crie um nome para o segredo. Neste exemplo, nomearemos o segredo "test"
    1. Crie um valor para o segredo. Neste exemplo, definiremos um valor de "password123"

Agora, quando você executa o código de seu computador local, você pode se autenticar no Key Vault obtendo um token de acesso ao apresentar a ID do cliente e um caminho para o certificado.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Parte 4: recuperar o segredo de seu Azure Key Vault em um aplicativo (Python)

Use o exemplo de código a seguir para testar se seu aplicativo pode recuperar um segredo do seu Key Vault usando a entidade de serviço que você configurou. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>Próximas etapas

1. Saiba como solucionar erros de autenticação no Key Vault. [Guia de solução de problemas do Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
