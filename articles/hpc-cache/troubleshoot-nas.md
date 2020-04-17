---
title: Solucionar problemas Alvos de armazenamento do Azure HPC Cache NFS
description: Dicas para evitar e corrigir erros de configuração e outros problemas que podem causar falha ao criar um alvo de armazenamento NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rohogue
ms.openlocfilehash: 0a24530810a448a713c01efbc8933b9f22d15b3b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536362"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Solucionar problemas de configuração nas e problemas de destino de armazenamento NFS

Este artigo oferece soluções para alguns erros de configuração comuns e outros problemas que poderiam impedir o Cache Azure HPC de adicionar um sistema de armazenamento NFS como alvo de armazenamento.

Este artigo inclui detalhes sobre como verificar portas e como habilitar o acesso raiz a um sistema NAS. Ele também inclui informações detalhadas sobre problemas menos comuns que podem fazer com que a criação de destino de armazenamento NFS falhe.

> [!TIP]
> Antes de usar este guia, leia [os pré-requisitos para os alvos de armazenamento NFS](hpc-cache-prereqs.md#nfs-storage-requirements).

Se a solução para o seu problema não estiver incluída aqui, [abra um ticket](hpc-cache-support-ticket.md) de suporte para que o Microsoft Service e o Support possam trabalhar com você para investigar e resolver o problema.

## <a name="check-port-settings"></a>Verifique as configurações da porta

O Cache Azure HPC precisa de acesso de leitura/gravação a várias portas UDP/TCP no sistema de armazenamento NAS back-end. Certifique-se de que essas portas estão acessíveis no sistema NAS e também que o tráfego é permitido nessas portas através de quaisquer firewalls entre o sistema de armazenamento e a sub-rede de cache. Você pode precisar trabalhar com firewall e administradores de rede para o seu data center para verificar essa configuração.

As portas são diferentes para sistemas de armazenamento de diferentes fornecedores, então verifique os requisitos do sistema ao configurar um alvo de armazenamento.

Em geral, o cache precisa de acesso a essas portas:

| Protocolo | Porta  | Serviço  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | montado   |
| TCP/UDP  | 4047  | status   |

Para aprender as portas específicas necessárias ``rpcinfo`` para o seu sistema, use o seguinte comando. Este comando abaixo lista as portas e formata os resultados relevantes em uma tabela. (Use o endereço IP do sistema no lugar do *<storage_IP>* termo.)

Você pode emitir este comando de qualquer cliente Linux que tenha a infra-estrutura NFS instalada. Se você usar um cliente dentro da sub-rede de cluster, ele também pode ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Certifique-se de que todas as ``rpcinfo`` portas retornadas pela consulta permitem tráfego irrestrito da sub-rede do Cache Azure HPC.

Verifique essas configurações tanto no próprio NAS quanto em quaisquer firewalls entre o sistema de armazenamento e a sub-rede de cache.

## <a name="check-root-access"></a>Verifique o acesso raiz

O Cache Azure HPC precisa ter acesso às exportações do seu sistema de armazenamento para criar o alvo de armazenamento. Especificamente, ele monta as exportações como ID do usuário 0.

Diferentes sistemas de armazenamento usam métodos diferentes para permitir esse acesso:

* Os servidores Linux ``no_root_squash`` geralmente adicionam ao ``/etc/exports``caminho exportado em .
* Os sistemas NetApp e EMC normalmente controlam o acesso com regras de exportação vinculadas a endereços ou redes IP específicos.

Se usar as regras de exportação, lembre-se de que o cache pode usar vários endereços IP diferentes da sub-rede de cache. Permitir o acesso a partir de toda a gama de possíveis endereços IP de sub-rede.

> [!NOTE]
> Por padrão, o Cache Azure HPC esmaga o acesso raiz. Ler [Configure configurações adicionais de cache](configuration.md#configure-root-squash) para detalhes.

Trabalhe com o fornecedor de armazenamento NAS para habilitar o nível certo de acesso para o cache.

### <a name="allow-root-access-on-directory-paths"></a>Permitir acesso raiz em caminhos de diretório
<!-- linked in prereqs article -->

Para sistemas NAS que exportam diretórios hierárquicos, o Cache Azure HPC precisa de acesso raiz a cada nível de exportação.

Por exemplo, um sistema pode mostrar três exportações como estas:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

A ``/ifs/accounting/payroll`` exportação é ``/ifs/accounting``uma ``/ifs/accounting`` criança de ``/ifs``, e é em si uma criança de .

Se você ``payroll`` adicionar a exportação como um alvo de ``/ifs/`` armazenamento de cache HPC, o cache realmente será montado e acessará o diretório de folha de pagamento a partir daí. Assim, o Cache Azure HPC precisa de acesso raiz para ``/ifs`` acessar a ``/ifs/accounting/payroll`` exportação.

Esse requisito está relacionado à forma como o cache indexa arquivos e evita colisões de arquivos, usando alças de arquivo que o sistema de armazenamento fornece.

Um sistema NAS com exportações hierárquicas pode dar diferentes alças de arquivo para o mesmo arquivo se o arquivo for recuperado de diferentes exportações. Por exemplo, um ``/ifs/accounting`` cliente pode ``payroll/2011.txt``montar e acessar o arquivo . Outro cliente ``/ifs/accounting/payroll`` monta e acessa o arquivo ``2011.txt``. Dependendo de como o sistema de armazenamento atribui as alças do arquivo, esses ``<mount2>/payroll/2011.txt`` dois clientes podem receber o mesmo arquivo com diferentes alças de arquivo (uma para e outra para ``<mount3>/2011.txt``).

O sistema de armazenamento back-end mantém aliases internas para alças de arquivo, mas o Cache Azure HPC não pode dizer quais alças de arquivo em seu índice fazem referência ao mesmo item. Assim, é possível que o cache possa ter gravações diferentes armazenadas em cache para o mesmo arquivo, e aplicar as alterações incorretamente porque não sabe que são o mesmo arquivo.

Para evitar essa possível colisão de arquivos para arquivos em várias exportações, o Cache``/ifs`` Azure HPC monta automaticamente a exportação mais rasa disponível no caminho (por exemplo) e usa a alça de arquivo dada a partir dessa exportação. Se várias exportações usarem o mesmo caminho base, o Cache Azure HPC precisa de acesso raiz a esse caminho.

## <a name="enable-export-listing"></a>Habilitar a lista de exportação
<!-- link in prereqs article -->

O NAS deve listar suas exportações quando o Cache Azure HPC o consulta.

Na maioria dos sistemas de armazenamento NFS, você pode testá-lo enviando a seguinte consulta de um cliente Linux:``showmount -e <storage IP address>``

Use um cliente Linux da mesma rede virtual que seu cache, se possível.

Se esse comando não listar as exportações, o cache terá problemas para se conectar ao seu sistema de armazenamento. Trabalhe com seu fornecedor NAS para habilitar a listagem de exportação.

## <a name="adjust-vpn-packet-size-restrictions"></a>Ajuste as restrições de tamanho do pacote VPN
<!-- link in prereqs article and configuration article -->

Se você tiver uma VPN entre o cache e o seu dispositivo NAS, a VPN poderá bloquear pacotes Ethernet de tamanho completo de 1500 bytes. Você pode ter esse problema se grandes trocas entre o NAS e a instância de cache Do Zure HPC não forem concluídas, mas atualizações menores funcionarem como esperado.

Não há uma maneira simples de dizer se seu sistema tem ou não esse problema, a menos que você saiba os detalhes da sua configuração VPN. Aqui estão alguns métodos que podem ajudá-lo a verificar este problema.

* Use sniffers de pacotes em ambos os lados da VPN para detectar quais pacotes são transferidos com sucesso.
* Se sua VPN permitir comandos ping, você pode testar o envio de um pacote em tamanho real.

  Execute um comando ping sobre a VPN para o NAS com essas opções. (Use o endereço IP do seu sistema de armazenamento no lugar do *<storage_IP>* valor.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Estas são as opções no comando:

  * ``-M do``- Não se fragmente
  * ``-c 1``- Envie apenas um pacote
  * ``-s 1472``- Definir o tamanho da carga útil para 1472 bytes. Esta é a carga útil de tamanho máximo para um pacote de 1500 bytes depois de contabilizar a sobrecarga de Ethernet.

  Uma resposta bem-sucedida tem a seguinte aparência:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se o ping falhar com 1472 bytes, provavelmente haverá um problema de tamanho de pacote.

Para corrigir o problema, talvez seja necessário configurar a fixação mSS na VPN para fazer com que o sistema remoto detecte corretamente o tamanho máximo do quadro. Leia a documentação de [parâmetros do VPN Gateway IPsec/IKE](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) para saber mais.

Em alguns casos, alterar a configuração MTU para o Cache Azure HPC para 1400 pode ajudar. No entanto, se você restringir o MTU no cache, você também deve restringir as configurações de MTU para clientes e sistemas de armazenamento back-end que interagem com o cache. Leia [Configurar configuração adicional de cache Azure HPC](configuration.md#adjust-mtu-value) para obter detalhes.

## <a name="check-for-acl-security-style"></a>Verifique se há estilo de segurança da ACL

Alguns sistemas NAS usam um estilo de segurança híbrido que combina listas de controle de acesso (ACLs) com a segurança tradicional POSIX ou UNIX.

Se o sistema relatar seu estilo de segurança como UNIX ou POSIX sem incluir a sigla "ACL", este problema não o afetará.

Para sistemas que usam ACLs, o Cache Azure HPC precisa rastrear valores adicionais específicos do usuário para controlar o acesso a arquivos. Isso é feito habilitando um cache de acesso. Não há um controle voltado para o usuário para ativar o cache de acesso, mas você pode abrir um ticket de suporte para solicitar que ele seja habilitado para os alvos de armazenamento afetados no sistema de cache.

## <a name="next-steps"></a>Próximas etapas

Se você tem um problema que não foi abordado neste artigo, [abra um ticket de suporte](hpc-cache-support-ticket.md) para obter ajuda especializada.
