---
title: Gerenciar o console de gerenciamento local
description: Saiba mais sobre as opções do console de gerenciamento local, como backup e restauração, definição do nome do host e configuração de um proxy para sensores.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 34efef4a01b58cc26fd1567336184837a703ade2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837671"
---
# <a name="manage-the-on-premises-management-console"></a>Gerenciar o console de gerenciamento local

Este artigo aborda as opções do console de gerenciamento local, como backup e restauração, download do arquivo de ativação de dispositivo do Comitê, atualização de certificados e configuração de um proxy para sensores.

Você integra o console de gerenciamento local do portal do Azure.

## <a name="upload-an-activation-file"></a>Carregar um arquivo de ativação

Quando você entra pela primeira vez, um arquivo de ativação para o console de gerenciamento local é baixado. Esse arquivo contém os dispositivos confirmados agregados que são definidos durante o processo de integração. A lista inclui sensores associados a várias assinaturas.

Após a ativação inicial, o número de dispositivos monitorados pode exceder o número de dispositivos confirmados definidos durante a integração. Esse evento pode acontecer, por exemplo, se você conectar mais sensores ao console de gerenciamento. Se houver uma discrepância entre o número de dispositivos monitorados e o número de dispositivos confirmados, um aviso aparecerá no console de gerenciamento. Se esse evento ocorrer, você deverá carregar um novo arquivo de ativação.

Para carregar um arquivo de ativação:

1. Acesse a página de **preços** do Azure defender para IOT.
1. Selecione a guia **baixar o arquivo de ativação para o console de gerenciamento** . O arquivo de ativação é baixado.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Baixe o arquivo de ativação.":::

1. Selecione **configurações do sistema** no console de gerenciamento.
1. Selecione **ativação**.
1. Selecione **escolher um arquivo** e selecione o arquivo que você salvou.

## <a name="manage-certificates"></a>Gerenciar certificados

Após a instalação do console de gerenciamento no local, um certificado autoassinado local é gerado e usado para acessar o aplicativo Web do console de gerenciamento. Quando os usuários administradores entram no console de gerenciamento pela primeira vez, eles são solicitados a fornecer um certificado SSL/TLS. Para obter mais informações sobre a primeira configuração, consulte [ativar e configurar seu console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md).

As seções a seguir fornecem informações sobre como atualizar certificados, trabalhar com comandos da CLI de certificados e certificados com suporte e parâmetros de certificado.

### <a name="about-certificates"></a>Sobre certificados

O Azure defender para IoT usa certificados SSL e TLS para:

- Atenda aos requisitos específicos de certificado e criptografia solicitados por sua organização carregando o certificado assinado por AC.

- Permita a validação entre o console de gerenciamento e os sensores conectados e entre um console de gerenciamento e um console de gerenciamento de alta disponibilidade. A validação é avaliada em relação a uma lista de certificados revogados e à data de expiração do certificado. *Se a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação aparecerá no console.* Essa opção é habilitada por padrão após a instalação.

As regras de encaminhamento de terceiros não são validadas. Exemplos são informações de alerta enviadas para SYSLOG, Splunk ou ServiceNow; e comunicação com Active Directory.

### <a name="update-certificates"></a>Atualizar certificados

Os usuários administradores do console de gerenciamento local podem atualizar certificados.

Para atualizar um certificado:  

1. Selecione **configurações do sistema**.
1. Selecione **certificados SSL/TLS**.
1. Exclua ou edite o certificado e adicione um novo.
   
   - Adicione um nome de certificado.
   - Carregue um arquivo e chave do CRT e insira uma frase secreta.
   - Carregue um arquivo PEM, se necessário.

Para alterar a configuração de validação:

1. Ative ou desative a alternância **Habilitar validação de certificado** .
1. Clique em **Salvar**.

Se a opção estiver habilitada e a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação aparecerá no console.

### <a name="certificate-support"></a>Suporte a certificados

Há suporte para os seguintes certificados:

- Infraestrutura privada e de chave corporativa (PKI particular) 
- Infraestrutura de chave pública (PKI pública) 
- Gerado localmente no dispositivo (autoassinado localmente) 

  > [!IMPORTANT]
  > Não recomendamos que você use certificados autoassinados. Essa conexão não é segura e deve ser usada somente para ambientes de teste. O proprietário do certificado não pode ser validado e a segurança do sistema não pode ser mantida. Os certificados autoassinados nunca devem ser usados para redes de produção.  

Os seguintes parâmetros são compatíveis: 

**CRT de certificado**

- O arquivo de certificado primário para seu nome de domínio
- Algoritmo de assinatura = SHA256RSA
- Algoritmo de hash de assinatura = SHA256
- Válido a partir de = data anterior válida
- Válido para = data futura válida
- Chave pública = RSA 2048 bits (mínimo) ou 4096 bits
- Ponto de distribuição de CRL = URL para arquivo. CRL
- O assunto CN = URL, pode ser um certificado curinga; por exemplo, www.contoso.com ou \* . contoso.com
- Subject (C) ountry = definido, por exemplo, nós
- Unidade organizacional (OU) da organização = definida; por exemplo, laboratórios da contoso
- Subject (O) rganização = definido; por exemplo, Contoso Inc

**Arquivo chave**

- O arquivo de chave gerado quando você criou o CSR
- RSA 2048 bits (mínimo) ou 4096 bits

**Cadeia de certificados**

- O arquivo de certificado intermediário (se houver) fornecido pela sua autoridade de certificação.
- O certificado de autoridade de certificação que emitiu o certificado do servidor deve ser o primeiro no arquivo, seguido por qualquer outro até a raiz. 
- A cadeia pode incluir atributos de conjunto.

**Senha**

- Há suporte para uma chave.
- Configurar quando você estiver importando o certificado.

Certificados com outros parâmetros podem funcionar, mas a Microsoft não oferece suporte a eles.

#### <a name="encryption-key-artifacts"></a>Artefatos de chave de criptografia

**. pem: arquivo de contêiner de certificado**

O nome é de Privacy Enhanced Mail (PEM), um método histórico para email seguro. O formato do contêiner é uma tradução Base64 das chaves X509 ASN. 1.  

Esse arquivo é definido nas RFCs 1421 a 1424: um formato de contêiner que pode incluir apenas o certificado público (como, por exemplo, instalações do Apache, arquivos de certificado de autoridade de certificação e certificados SSL, ETC.). Ou pode incluir uma cadeia de certificados inteira, incluindo uma chave pública, uma chave privada e certificados raiz.  

Ele também pode codificar um CSR, pois o formato PKCS10 pode ser convertido em PEM.

**. cert. cer. CRT: arquivo de contêiner de certificado**

Este é um arquivo formatado. PEM (ou raramente,. der) com uma extensão diferente. O explorador de arquivos do Windows o reconhece como um certificado. O explorador de arquivos não reconhece o arquivo. PEM.

**. chave: arquivo de chave privada**

Um arquivo de chave é do mesmo formato que um arquivo PEM, mas tem uma extensão diferente. 

#### <a name="cli-commands"></a>Comandos de CLI

Use o `cyberx-xsense-certificate-import` comando da CLI para importar certificados. Para usar essa ferramenta, você precisa carregar arquivos de certificado para o dispositivo (usando ferramentas como WinSCP ou wget).

O comando dá suporte aos seguintes sinalizadores de entrada:

- `-h`: Mostra a sintaxe de ajuda da linha de comando.

- `--crt`: Caminho para um arquivo de certificado (extensão. CRT).

- `--key`\*arquivo de chave:. O comprimento da chave deve ser no mínimo 2.048 bits.

- `--chain`: Caminho para um arquivo de cadeia de certificados (opcional).

- `--pass`: Senha usada para criptografar o certificado (opcional).

- `--passphrase-set`: Padrão = `False` , não usado. Defina como `True` para usar a frase secreta anterior fornecida com o certificado anterior (opcional).

Quando você estiver usando o comando da CLI:

- Verifique se os arquivos de certificado estão legíveis no dispositivo.

- Verifique se o nome de domínio e o IP no certificado correspondem à configuração que o departamento de ti planejou.

## <a name="define-backup-and-restore-settings"></a>Definir configurações de backup e restauração

O backup do sistema do console de gerenciamento local é executado automaticamente, diariamente. Os dados são salvos em um disco diferente. O local padrão é `/var/cyberx/backups`. 

Você pode transferir automaticamente esse arquivo para a rede interna. 

> [!NOTE]
> Você pode executar o procedimento de backup e restauração somente na mesma versão. 

Para fazer backup do computador do console de gerenciamento local: 

- Entre em uma conta administrativa e digite `sudo cyberx-management-backup -full` .

Para restaurar o arquivo de backup mais recente:

- Entre em uma conta administrativa e digite `$ sudo cyberx-management-system-restore` .

Para salvar o backup em um servidor SMB Externo:

1. Crie uma pasta compartilhada no servidor SMB Externo.  

   Obtenha o caminho da pasta, o nome de usuário e a senha necessários para acessar o servidor SMB. 

2. No defender para IoT, crie um diretório para os backups:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Editar fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Edite ou crie credenciais para o servidor SMB compartilhar: 

   - `sudo nano /etc/samba/user` 

5. Adicionar: 

   - `username=<user name>`

   - `password=<password>` 

6. Monte o diretório: 

   - `sudo mount -a` 

7. Configure um diretório de backup para a pasta compartilhada no console de gerenciamento local do defender para IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Editar o nome do host

Para editar o nome de host do console de gerenciamento configurado no servidor DNS organizacional:

1. No painel esquerdo do console de gerenciamento, selecione **configurações do sistema**.  

2. Na seção rede do console, selecione **rede**. 

3. Insira o nome do host configurado no servidor DNS organizacional. 

4. Clique em **Salvar**.

## <a name="define-vlan-names"></a>Definir nomes de VLAN

Os nomes de VLAN não são sincronizados entre o sensor e o console de gerenciamento. Você deve definir nomes idênticos nos componentes.

Na área rede, selecione **VLAN** e adicione nomes às IDs de VLAN descobertas. Em seguida, selecione **Salvar**.

## <a name="define-a-proxy-to-sensors"></a>Definir um proxy para sensores

Aprimore a segurança do sistema impedindo que o usuário entre diretamente no sensor. Em vez disso, use o túnel de proxy para permitir que os usuários acessem o sensor do console de gerenciamento local com uma única regra de firewall. Esse aprimoramento reduz a possibilidade de acesso não autorizado ao ambiente de rede além do sensor.

Use um proxy em ambientes em que não há conectividade direta com sensores.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Usuário.":::

O procedimento a seguir conecta um sensor ao console de gerenciamento local e habilita o túnel nesse console:

1. Entre na CLI do dispositivo do console de gerenciamento local com credenciais administrativas.

2. Digite `sudo cyberx-management-tunnel-enable` e selecione **Enter**.

4. Digite `--port 10000` e selecione **Enter**.

## <a name="adjust-system-properties"></a>Ajustar propriedades do sistema

As propriedades do sistema controlam várias operações e configurações no console de gerenciamento. Editar ou modificá-los pode danificar a operação do console de gerenciamento. Consulte [suporte da Microsoft](https://support.microsoft.com) antes de alterar as configurações.

Para acessar as propriedades do sistema: 

1. Entre no console de gerenciamento local ou no sensor.

2. Selecione **configurações do sistema**.

3. Selecione **Propriedades do sistema** na seção **geral** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>Alterar o nome do console de gerenciamento local

Você pode alterar o nome do console de gerenciamento local. Os novos nomes aparecem no navegador da Web do console, em várias janelas de console e em logs de solução de problemas. O nome padrão é **console de gerenciamento**.

Para alterar o nome:

1. Na parte inferior do painel esquerdo, selecione o nome atual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Captura de tela da versão do console de gerenciamento local.":::

2. Na caixa de diálogo **Editar configuração do console de gerenciamento** , digite o novo nome. O nome não pode ter mais de 25 caracteres.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Captura de tela da edição da configuração da plataforma defender para IoT.":::

3. Clique em **Salvar**. O novo nome é aplicado.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Captura de tela que mostra o nome alterado do console.":::

## <a name="password-recovery"></a>Recuperação de senha

A recuperação de senha para seu console de gerenciamento local está vinculada à assinatura à qual o dispositivo está anexado. Você não poderá recuperar uma senha se não souber a qual assinatura um dispositivo está anexado.

Para redefinir sua senha:

1. Vá para a página de entrada do console de gerenciamento local.
1. Selecione **recuperação de senha**.
1. Copie o identificador exclusivo.
1. Vá para a página de **sensores e sites** do defender para IOT e selecione a guia **recuperar minha senha** .
1. Insira o identificador exclusivo e selecione **recuperar**. O arquivo de ativação é baixado.
1. Vá para a página **recuperação de senha** e carregue o arquivo de ativação.
1. Selecione **Avançar**.
 
   Agora você tem seu nome de usuário e uma nova senha gerada pelo sistema.

> [!NOTE]
> O sensor está vinculado à assinatura à qual ele foi conectado originalmente. Você pode recuperar a senha somente usando a mesma assinatura à qual ela está anexada.

## <a name="see-also"></a>Consulte também

[Gerenciar sensores do console de gerenciamento](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Gerenciar sensores individuais](how-to-manage-individual-sensors.md)
