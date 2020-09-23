---
title: Configurar a segurança para seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
description: Configurar a segurança para seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b166348031e9f72e8005e866a198855db9c01a9c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933225"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Configurar a segurança para seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

Este documento descreve vários aspectos relacionados à segurança do seu grupo de servidores:
- Criptografia em repouso
- Gerenciamento de Usuários
   - Perspectivas gerais
   - Alterar a senha do usuário administrativo do _postgres_

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Criptografia em repouso
Você pode implementar a criptografia em repouso criptografando os discos nos quais você armazena os bancos de dados e/ou usando as funções de banco de dado para criptografar os dados inseridos ou atualizados.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: criptografia de volume do host do Linux
Implemente a criptografia de dados do sistema para proteger os dados que residem nos discos usados pela instalação dos serviços de dados habilitados para o Azure Arc. Você pode ler mais sobre este tópico:
- [Criptografia de dados em repouso](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) no Linux em geral 
- Criptografia de disco com o `cryptsetup` comando luks Encrypt (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) especificamente, uma vez que os serviços de dados habilitados para Arc do Azure são executados na infraestrutura física que você forneceu, você é responsável por proteger a infraestrutura.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: Use a `pgcrypto` extensão PostgreSQL no seu grupo de servidores
Além de criptografar os discos usados para hospedar sua configuração de arco do Azure, você pode configurar seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure ARC para expor mecanismos que seus aplicativos podem usar para criptografar dados no (s) banco (es). A `pgcrypto` extensão faz parte das `contrib` extensões do postgres e está disponível em seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Você encontra detalhes sobre a `pgcrypto` extensão [aqui](https://www.postgresql.org/docs/current/pgcrypto.html).
Em resumo, com os comandos a seguir, você habilita a extensão, você a cria e a usa:


#### <a name="create-the-pgcrypto-extension"></a>Criar a `pgcrypto` extensão
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
CREATE EXTENSION pgcrypto;
```

> Você encontra detalhes [aqui](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) sobre como se conectar.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Verifique a lista de extensões prontas para uso em seu grupo de servidores
Você pode verificar se a `pgcrypto` extensão está pronta para uso listando as extensões disponíveis no seu grupo de servidores.
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
select * from pg_extension;
```
Você deve ver `pgcrypto` se habilitou e criou com os comandos indicados acima.

#### <a name="use-the-pgcrypto-extension"></a>Usar a `pgcrypto` extensão
Agora você pode ajustar o código dos seus aplicativos para que eles usem qualquer uma das funções oferecidas pelo `pgcrypto` :
- Funções de hash gerais
- Funções de hash de senha
- Funções de criptografia PGP
- Funções de criptografia bruta
- Funções de dados aleatórios

Por exemplo, para gerar valores de hash. Execute o comando:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Retorna o seguinte hash:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Ou, por exemplo:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

Retorna o seguinte hash:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Ou, por exemplo, para armazenar dados criptografados, como uma senha:

Vamos supor que meu aplicativo armazene segredos na tabela a seguir:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

E eu criptografe sua senha ao criar um usuário:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Agora, vejo que minha senha está criptografada:

```console
select * from mysecrets;
```

Saída:

- USERid: 1
- Nome de usuário: Eu
- UserPassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Quando eu me conecto ao meu aplicativo e passo uma senha, ele será pesquisado na `mysecrets` tabela e retornará o nome do usuário se houver uma correspondência entre a senha fornecida para o aplicativo e as senhas armazenadas na tabela. Por exemplo:

- Eu passo a senha incorreta:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Saída 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Eu passo a senha correta:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Saída:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Este pequeno exemplo demonstra que você pode criptografar dados em repouso (armazenar dados criptografados) em hiperescala PostgreSQL habilitada para Arc do Azure usando a `pgcrypto` extensão postgres e seus aplicativos podem usar funções oferecidas pelo `pgcrypto` para manipular esses dados criptografados.

## <a name="user-management"></a>Gerenciamento de Usuários
### <a name="general-perspectives"></a>Perspectivas gerais
Você pode usar a maneira postgres padrão para criar usuários ou funções. No entanto, se você fizer isso, esses artefatos só estarão disponíveis na função de coordenador. Durante a visualização, esses usuários/funções ainda não poderão acessar os dados distribuídos fora do nó de coordenador e nos nós de trabalho do seu grupo de servidores. O motivo é que, na visualização, a definição de usuário não é replicada para os nós de trabalho.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Alterar a senha do usuário administrativo do _postgres_
A hiperescala do PostgreSQL habilitado para Arc do Azure vem com o _postgres_ de usuário administrativo postgres padrão para o qual você define a senha ao criar o grupo de servidores.
O formato geral do comando para alterar sua senha é:
```console
azdata arc postgres server edit --name <server group name> --admin-password <new password>
```
A senha será definida como o valor da variável de ambiente AZDATA_PASSWORD **sessão**, se existir. Caso contrário, o usuário será solicitado a fornecer um valor.
Para verificar se a variável de ambiente da sessão de AZDATA_PASSWORD existe e/ou para qual valor ele está definido, execute:
```console
printenv AZDATA_PASSWORD
```
Talvez você queira excluir seu valor se preferir ser solicitado a inserir uma nova senha.


## <a name="next-steps"></a>Próximas etapas
- Leia os detalhes sobre a `pgcrypto` extensão [aqui](https://www.postgresql.org/docs/current/pgcrypto.html).
- Leia os detalhes sobre como usar as extensões postgres [aqui](using-extensions-in-postgresql-hyperscale-server-group.md).

