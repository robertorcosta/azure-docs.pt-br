---
title: Configurar interface de passagem de mensagens para HPC - Azure Virtual Machines | Microsoft Docs
description: Saiba como configurar o MPI para HPC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023983"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurar interface de passagem de mensagens para HPC

As cargas de trabalho da Interface de Passagem de Mensagem (MPI) são uma parte significativa das cargas de trabalho tradicionais do HPC. Os tamanhos vm habilitados para SR-IOV no Azure permitem que quase qualquer sabor de MPI seja usado. 

Executar trabalhos mpi em VMs requer a configuração de chaves de partição (p-keys) em um inquilino. Siga as etapas na seção [Obter tectas de partição](#discover-partition-keys) discover para obter detalhes sobre como determinar os valores de chave p.

## <a name="ucx"></a>UCX

[UcX](https://github.com/openucx/ucx) oferece o melhor desempenho em IB e trabalha com MPICH e OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Instale o UCX como descrito anteriormente.

```bash
sudo yum install –y openmpi
```

Construa OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Execute OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Verifique sua chave de partição como mencionado acima.

## <a name="mpich"></a>Mpich

Instale o UCX como descrito anteriormente.

Construa MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Executando MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Verifique sua chave de partição como mencionado acima.

## <a name="mvapich2"></a>MVAPICH2

Construir MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Executando MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Plataforma MPI Community Edition

Instale os pacotes necessários para a plataforma MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Siga o processo de instalação.

## <a name="intel-mpi"></a>Intel MPI

[Baixe Intel MPI](https://software.intel.com/mpi-library/choose-download).

Alterar a variável de ambiente I_MPI_FABRICS dependendo da versão. Para Intel MPI 2018, use `I_MPI_FABRICS=shm:ofa` e para `I_MPI_FABRICS=shm:ofi`2019, use .

A fixação do processo funciona corretamente para 15, 30 e 60 PPN por padrão.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmarks

[Baixe OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Construa benchmarks usando uma biblioteca MPI específica:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Os benchmarks mpi estão em pasta. `mpi/`


## <a name="discover-partition-keys"></a>Descubra as chaves de partição

Descubra as teclas de partição (p-keys) para se comunicar com outras VMs dentro do mesmo inquilino (Conjunto de disponibilidade ou conjunto de escala vm).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

O maior dos dois é a chave de inquilino que deve ser usada com MPI. Exemplo: Se as seguintes são as teclas p, 0x800b deve ser usado com MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Use a tecla de partição diferente da padrão (0x7fff). UCX requer que o MSB da chave p seja limpo. Por exemplo, defina UCX_IB_PKEY como 0x000b para 0x800b.

Observe também que enquanto o inquilino (AVSet ou VMSS) existir, os PKEYs permanecem os mesmos. Isso é verdade mesmo quando os nós são adicionados/excluídos. Novos inquilinos recebem PKEYs diferentes.


## <a name="set-up-user-limits-for-mpi"></a>Configurar limites de usuário para MPI

Configure os limites de usuário para MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Configurar as chaves SSH para MPI

Configure as chaves SSH para tipos de MPI que o requerem.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A sintaxe acima pressupõe um diretório doméstico compartilhado, caso não seja copiado para cada nó.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
