---
title: Guia de implantação do FortiGate | Microsoft Docs
description: Configure e trabalhe com o produto de firewall de próxima geração Fortinet FortiGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273323"
---
# <a name="fortigate-deployment-guide"></a>Guia de implantação do FortiGate

Usando este guia de implantação, você aprende a configurar e trabalhar com o produto de firewall de próxima geração Fortinet FortiGate.

## <a name="redeem-the-fortigate-license"></a>Resgatar a licença do FortiGate

O produto de firewall de próxima geração Fortinet FortiGate está disponível como uma máquina virtual na IaaS (infraestrutura como serviço) do Azure. Há dois modos de licenciamento para essa máquina virtual: pago conforme o uso e traga sua própria licença.

A Fortinet pode fornecer licenças aos membros da equipe do Azure AD (Azure Active Directory) "Ir para o SHA (acesso híbrido seguro) de produção". Nos casos em que nenhuma licença foi fornecida, a implantação paga conforme o uso também funciona.

Se uma licença foi emitida, a Fortinet fornece um código de registro que precisa ser resgatado online.

![Captura de tela do código de registro de VPN SSL do FortiGate.](registration-code.png)

1. Registre-se em https://support.fortinet.com/.
2. Após o registro, entre em https://support.fortinet.com/.
3. Vá para **Ativo** > **Registrar/ativar**.
4. Insira o código de registro fornecido pela Fortinet.
5. Especifique o código de registro, selecione **O produto será usado por um usuário não governamental** e selecione **Avançar**.
6. Insira uma descrição do produto (por exemplo, FortiGate), defina o parceiro Fortinet como **Outros** > **Microsoft** e clique em **Avançar**.
7. Aceite o **Contrato de Registro do Produto da Fortinet** e selecione **Avançar**.
8. Aceite os **Termos** e selecione **Confirmar**.
9. Selecione **Download do Arquivo de Licença** e salve a licença para uso posterior.


## <a name="download-firmware"></a>Baixar firmware

Atualmente, a VM do Fortinet FortiGate no Azure não é fornecida com a versão do firmware necessária para a autenticação SAML. A última versão precisa ser obtida com a Fortinet.

1. Entre em https://support.fortinet.com/.
2. Vá para **Download** > **Imagens de firmware**.
3. À direita das **Notas sobre a versão**, selecione **Download**.
4. Selecione **v6.** > **6.** > **6.4.** .
5. Baixe **FGT_VM64_AZURE-v6-build1637-FORTINET.out** selecionando o link **HTTPS** na mesma linha.
6. Salve o arquivo para uso posterior.


## <a name="deploy-the-fortigate-vm"></a>Implantar a VM do FortiGate

1. Vá até https://portal.azure.com e entre na assinatura na qual deseja implantar a máquina virtual do FortiGate.
2. Crie um grupo de recursos ou abra o grupo de recursos no qual deseja implantar a máquina virtual do FortiGate.
3. Selecione **Adicionar**.
4. Em **Pesquisar no Marketplace**, digite *Forti*. Selecione **Fortinet FortiGate Next-Generation Firewall**.
5. Selecione o plano de software (traga sua própria licença se você tiver uma licença, caso contrário, pago conforme o uso). Selecione **Criar**.
6. Preencha a configuração da VM.

    ![Captura de tela de Criar uma máquina virtual.](virtual-machine.png)

7. Defina o **Tipo de autenticação** como **Senha** e forneça credenciais administrativas para a VM.
8. Selecione **Examinar + Criar** > **Criar**.
9. Aguarde até a implantação da VM ser concluída.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Definir um endereço IP público estático e atribuir um nome de domínio totalmente qualificado

Para uma experiência de usuário consistente, defina o endereço IP público atribuído à VM do FortiGate para que ele seja atribuído estaticamente. Além disso, mapeie-o para um FQDN (nome de domínio totalmente qualificado).

1. Vá até https://portal.azure.com e abra as configurações da VM do FortiGate.
2. Na tela **Visão Geral**, selecione o endereço IP público.

    ![Captura de tela da VPN SSL do FortiGate.](public-ip-address.png)

3. Selecione **Estático** > **Salvar**.

Se você tiver um nome de domínio publicamente roteável para o ambiente no qual a VM do FortiGate está sendo implantada, crie um registro de host (A) para a VM. Esse registro é mapeado para o endereço IP público anterior atribuído estaticamente.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Criar uma regra de grupo de segurança de rede de entrada para a porta TCP

1. Vá até https://portal.azure.com e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Rede**. O adaptador de rede é listado e as regras de porta de entrada são exibidas.
3. Selecione **Adicionar regra de porta de entrada**.
4. Crie uma regra de porta de entrada para TCP 8443.

    ![Captura de tela de Adicionar regra de segurança de entrada.](port-rule.png)

5. Selecione **Adicionar**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Criar um aplicativo do Azure personalizado para o FortiGate

1. Vá até https://portal.azure.com e abra o painel do Azure AD do locatário que fornecerá a identidade para as entradas do FortiGate.
2. No menu esquerdo, selecione **Aplicativos Empresariais**.
3. Selecione **Novo aplicativo** > **Aplicativo não pertencente à galeria**.
4. Insira um nome (por exemplo, FortiGate) e selecione **Adicionar**.
5. No menu esquerdo, selecione **Usuários e grupos**.
6. Adicione os usuários que poderão se conectar e selecione **Atribuir**.
7. No menu à esquerda, selecione **Logon único**.
8. Selecione **SAML**.
9. Em **Configuração Básica de SAML**, selecione o lápis para editar a configuração.
10. Configure o seguinte:
    - **Identificador (ID da Entidade)** como `https://<address>/remote/saml/metadata`.
    - **URL de Resposta (URL do Serviço do Consumidor de Declaração)** como `https://<address>/remote/saml/login`.
    - **URL de Logoff** como `https://<address>/remote/saml/logout`.

    `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

11. Registre cada uma dessas URLs para uso posterior: ID da Entidade, URL de Resposta e URL de Logoff.
12. Selecione **Salvar** e feche a **Configuração Básica de SAML**.
13. Em **3 – Certificado de Autenticação SAML**, baixe o **Certificado (Base64)** e salve-o para uso posterior.
14. Em **4 – Configurar (Nome do Aplicativo)** , copie a URL de Logon do Azure, o Identificador do Azure AD e a URL de Logoff do Azure e salve-os para uso posterior.
15. Em **2 – Declarações e Atributos de Usuário**, selecione o ícone de lápis para editar a configuração.
16. Selecione **Adicionar nova declaração** e defina o nome como **nome de usuário**.
17. Defina o atributo de origem como **user.userprincipalname**.
18. Selecione **Salvar** > **Adicionar uma declaração de grupo** > **Todos os grupos**.
19. Selecione **Personalizar o nome da declaração de grupo** e defina o nome como **grupo**.
20. Selecione **Salvar**.


## <a name="prepare-for-group-matching"></a>Preparar a correspondência de grupo

O FortiGate permite diferentes experiências do portal do usuário após a entrada baseada na associação a um grupo. Por exemplo, pode haver uma experiência para o grupo de marketing e outra para o grupo de finanças. Veja como criar grupos para usuários:

1. Vá até https://portal.azure.com e abra o painel do Azure AD do locatário que fornecerá a identidade para as entradas do FortiGate.
2. Selecione **Grupos** > **Novo Grupo**.
3. Crie um grupo com os seguintes detalhes:
    - Tipo de grupo = Segurança
    - Nome do grupo = `a meaningful name`
    - Descrição do grupo = `a meaningful description for the group`
    - Tipo de associação = Atribuído
    - Membros = `users for the user experience that will map to this group`
4. Repita as etapas 3 e 4 para qualquer experiência do usuário adicional.
5. Depois de criar os grupos, selecione cada grupo e registre a **ID do Objeto** de cada um deles.
6. Salve essas IDs do objeto e os nomes dos grupos para uso posterior.


## <a name="configure-the-fortigate-vm"></a>Configurar a VM do FortiGate

As seções a seguir explicam como configurar a VM do FortiGate.

### <a name="install-the-license"></a>Instalar a licença

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. Se a implantação usar o modelo traga sua própria licença, você verá um prompt para carregar uma licença. Selecione o arquivo de licença criado anteriormente e carregue-o. Selecione **OK** e reinicie a VM do FortiGate.

    ![Captura de tela da licença da VM do FortiGate.](license.png)

5. Após a reinicialização, conecte-se novamente com as credenciais de administrador para validar a licença.

### <a name="update-firmware"></a>Atualizar firmware

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu esquerdo, selecione **Sistema** > **Firmware**.
5. Em **Gerenciamento de Firmware**, selecione **Procurar** e selecione o arquivo de firmware baixado anteriormente.
6. Ignore o aviso e selecione **Configuração de backup e atualização**.

    ![Captura de tela do Gerenciamento de Firmware.](backup-configure-upgrade.png)

7. Selecione **Continuar**.
8. Quando precisar salvar a configuração do FortiGate (como um arquivo .conf), selecione **Salvar**.
9. Aguarde que o firmware seja carregado e aplicado. Aguarde a reinicialização da VM do FortiGate.
10. Após a reinicialização da VM do FortiGate, conecte-se novamente com as credenciais de administrador.
11. Quando for solicitado que você configure o painel, selecione **Mais tarde**.
12. Quando o vídeo do tutorial for iniciado, selecione **OK**.

### <a name="change-the-management-port-to-tcp"></a>Alterar a porta de gerenciamento para TCP

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema**.
5. Em **Configurações de Administração**, altere a porta HTTPS para **8443** e selecione **Aplicar**.
6. Depois que a alteração for aplicada, o navegador tentará recarregar a página de administração, mas falhará. De agora em diante, o endereço da página de administração é `https://<address>`.

    ![Captura de tela de Carregar Certificado Remoto.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Carregar o certificado de autenticação SAML do Azure AD

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema** > **Certificados**.
5. Selecione **Importar** > **Certificado Remoto**.
6. Procure o certificado baixado da implantação de aplicativo personalizado do FortiGate no locatário do Azure. Selecione-o e escolha **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Carregar e configurar um certificado SSL personalizado

Talvez você queira configurar a VM do FortiGate com seu certificado SSL com suporte para FQDN que está sendo usado. Se você tiver acesso a um certificado SSL empacotado com a chave privada no formato .pfx, ele poderá ser usado para essa finalidade.

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema** > **Certificados**.
5. Selecione **Importar** > **Certificado Local** > **Certificado PKCS #12**.
6. Procure o arquivo .PFX que contém o certificado SSL e a chave privada.
7. Forneça a senha do .PFX e um nome significativo para o certificado. Depois, selecione **OK**.
8. No menu à esquerda, selecione **Sistema** > **Configurações**.
9. Em **Configurações de Administração**, expanda a lista ao lado de **Certificado do servidor HTTPS** e escolha o certificado SSL importado anteriormente.
10. Escolha **Aplicar**.
11. Feche a janela do navegador e vá para `https://<address>`.
12. Entre com as credenciais de administrador do FortiGate. Agora, você deve ver o certificado SSL correto em uso.


### <a name="perform-command-line-configuration"></a>Executar a configuração de linha de comando

As seções a seguir fornecem etapas para várias configurações usando a linha de comando.

#### <a name="for-saml-authentication"></a>Para autenticação SAML

1. Vá até https://portal.azure.com e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Console Serial**.
3. Entre no console serial com as credenciais de administrador da VM do FortiGate. Na próxima etapa, as URLs registradas anteriormente são necessárias:
    - ID da Entidade
    - URL de resposta
    - URL de logoff
    - URL de Logon do Azure
    - Identificador do Azure AD
    - URL de Logoff do Azure
4. No Console Serial, execute os seguintes comandos:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > A URL de Logoff do Azure contém um caractere `?`. Isso exige uma sequência de chave especial para que ela seja fornecida corretamente ao console serial do FortiGate. Normalmente, a URL é `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`. Para fornecer isso no Console Serial, digite:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Depois, digite CTRL+V e cole o restante da URL para completar a linha: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Para confirmar a configuração, execute o seguinte:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Para a correspondência de grupo

1. Vá até https://portal.azure.com e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Console Serial**.
3. Entre no console serial com as credenciais de administrador da VM do FortiGate.
4. No Console Serial, execute os seguintes comandos:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Para cada grupo adicional que terá uma experiência de portal diferente no FortiGate, repita esses comandos (começando na segunda linha de código).

#### <a name="for-authentication-timeout"></a>Para tempo limite de autenticação

1. Vá até https://portal.azure.com e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Console Serial**.
3. Entre no console serial com as credenciais de administrador da VM do FortiGate.
4. No Console Serial, execute os seguintes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Criar portais de VPN e política de firewall

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
3. No menu à esquerda, selecione **VPN** > **Portais SSL-VPN** > **Criar**.
6. Forneça um nome (geralmente correspondendo-o ao grupo do Azure usado para fornecer a experiência de portal personalizada).
7. Selecione o sinal de adição ( **+** ) ao lado de **Pools de IP de Origem**, selecione o pool padrão e selecione **Fechar**.
8. Personalize a experiência para esse grupo. Como teste, isso poderá ser a personalização da mensagem do portal e do tema. Esse também é o local em que você pode criar indicadores personalizados que direcionam os usuários para os recursos internos.
9. Selecione **OK**.
10. Repita as etapas 5 a 9 para cada grupo do Azure que terá uma experiência de portal personalizada.
11. Em VPN, selecione **Configurações de SSL-VPN**.
12. Selecione o sinal de adição ( **+** ) ao lado de **Escutar em Interfaces**, selecione **Port1** e escolha **Fechar**.
14. Se um certificado SSL personalizado tiver sido instalado anteriormente, altere o **Certificado do Servidor** para usar o certificado SSL personalizado no menu suspenso.
15. Em **Autenticação/Mapeamento do Portal**, selecione **Criar**. Escolha o primeiro grupo do Azure e faça a correspondência dele com o portal de mesmo nome. Depois, selecione **OK**.
18. Repita as etapas 15 a 17 para cada emparelhamento de um grupo do Azure e um portal.
19. Em **Autenticação/Mapeamento do Portal**, edite **Todos os Outros Usuários/Grupos**.
20. Defina o portal como **acesso completo** e selecione **OK** > **Aplicar**.
23. Role até a parte superior da página **Configuração de SSL-VPN** e selecione **Não há nenhuma política de SSL-VPN. Clique aqui para criar uma política de SSL-VPN usando essas configurações.**
24. Forneça um nome, como **VPN Grp**. Em seguida, defina a **Interface de Saída** como **porta** e selecione **Origem**.
27. Em **Endereço**, selecione **tudo**.
28. Em **Usuário**, selecione o primeiro grupo do Azure.
29. Selecione **Fechar** > **Destino**. Em **Endereço**, normalmente ele será a rede interna. Selecione **login.microsoft.com** para o teste.
32. Selecione **Fechar** > **Serviço** > **Todos**. Em seguida, selecione **Fechar** > **OK**.
37. No menu à esquerda, selecione **Política e Objetos** > **Política de Firewall**.
39. Expanda **Interface de túnel SSL-VPN (ssl.root** > **porta**.
40. Clique com o botão direito do mouse na política de VPN criada anteriormente (**VPN Grp 1**) e selecione **Copiar**.
41. Clique com o botão direito do mouse na política de VPN e selecione **Colar** > **Abaixo**.
42. Edite a nova política, fornecendo a ela um nome diferente (por exemplo, **VPN Grp2**). Altere também o grupo ao qual ela se aplica (para outro grupo do Azure).
43. Clique com o botão direito do mouse na nova política e defina o status como **Habilitado**.


## <a name="test-sign-in-by-using-azure"></a>Testar a entrada usando o Azure

1. Usando uma sessão do navegador anônima, vá até `https://<address>`.  
2. A entrada deverá redirecionar você para o Azure AD para a entrada.
3. Depois que você fornecer credenciais para um usuário que foi atribuído ao Aplicativo FortiGate no locatário do Azure, o portal do usuário apropriado aparecerá.

    ![Captura de tela da VPN SSL do FortiGate](test-sign-in.png)
