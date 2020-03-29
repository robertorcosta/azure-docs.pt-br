---
title: Dimensionamento de aplicações HPC - Azure Virtual Machines | Microsoft Docs
description: Saiba como dimensionar aplicativos HPC em VMs do Azure.
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
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707830"
---
# <a name="scaling-hpc-applications"></a>Dimensionamento de aplicativos HPC

O desempenho ideal de scale-up e scale-out de aplicativos HPC no Azure requer experiências de ajuste de desempenho e otimização para a carga de trabalho específica. Esta seção e as páginas específicas da série VM oferecem orientação geral para dimensionar seus aplicativos.

## <a name="compiling-applications"></a>Compilação de aplicativos

Embora não seja necessário, a compilação de aplicativos com sinalizadores de otimização adequados fornece o melhor desempenho de escala em VMs da série HB e HC.

### <a name="amd-optimizing-cc-compiler"></a>Compilador AMD Otimizando C/C++

O sistema de compilador AMD Optimizing C/C++ (AOCC) oferece um alto nível de otimizações avançadas, suporte a vários threads e processadores que incluem otimização global, vetorização, análises interprocessuais, transformações de loop e geração de código. Binários do compilador AOCC são adequados para sistemas Linux com GNU C Library (glibc) versão 2.17 ou superior. A suíte do compilador consiste em um compilador C/C++ (clang), um compilador Fortran (FLANG) e um front end Fortran para Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang é um compilador C, C++e Objective-C que manuseia o pré-processamento, análise, otimização, geração de código, montagem e vinculação. Clang suporta `-march=znver1` o sinalizador para permitir a melhor geração de código e ajuste para a arquitetura Zen baseada em Zen da AMD x86.

### <a name="flang"></a>FLANG

O compilador FLANG é uma adição recente ao conjunto AOCC (adicionado em abril de 2018) e está atualmente em pré-lançamento para desenvolvedores para download e teste. Com base no Fortran 2008, a AMD estendehttps://github.com/flangcompiler/flang)a versão GitHub do FLANG ( . O compilador FLANG suporta todas as opções de compilador Clang e um número adicional de opções de compilador específicos do FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg é um plugin gcc que substitui os otimizadores e geradores de código da GCC com os do projeto LLVM. DragonEgg que vem com AOCC funciona com gcc-4.8.x, foi testado para x86-32/x86-64 e tem sido usado com sucesso em várias plataformas Linux.

O GFortran é o frontend real dos programas fortran responsáveis pelo pré-processamento, análise e análise semântica que geram a representação intermediária GCC GIMPLE (IR). DragonEgg é um plugin GNU, conectando-se ao fluxo de compilação GFortran. Ele implementa a API de plugin GNU. Com a arquitetura plugin, DragonEgg torna-se o driver compilador, conduzindo as diferentes fases de compilação.  Depois de seguir as instruções de download e instalação, Dragon Egg pode ser invocado usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador PGI
PGI Community Edition ver. 17 está confirmado para trabalhar com AMD EPYC. Uma versão compilada com PGI do STREAM fornece largura de banda de memória completa da plataforma. A edição 18.10 da Comunidade mais nova (novembro de 2018) também deve funcionar bem. Abaixo está a amostra DE CLI para compilar de forma ideal com o Compilador Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador Intel
Intel Compiler ver. 18 está confirmado para trabalhar com AMD EPYC. Abaixo está a amostra DE CLI para compilar de forma ideal com o Compilador Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para HPC, a AMD recomenda o compilador GCC 7.3 ou mais novo. Versões mais antigas, como 4.8.5 incluídas com RHEL/CentOS 7.4, não são recomendadas. O GCC 7.3, e mais novo, fornecerá um desempenho significativamente maior nos testes HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Dimensionamento de aplicativos 

As seguintes sugestões se aplicam para a eficiência, desempenho e consistência de dimensionamento de aplicativos ideais:

* Processos de pinos para núcleos 0-59 usando uma abordagem de fixação seqüencial (em oposição a uma abordagem de equilíbrio automático). 
* A vinculação do Numa/Core/HwThread é melhor do que a vinculação padrão.
* Para aplicações paralelas híbridas (OpenMP+MPI), use 4 threads e 1 MPI por CCX.
* Para aplicações MPI puras, experimente com 1-4 mpi classificações por CCX para um desempenho ideal.
* Alguns aplicativos com extrema sensibilidade à largura de banda de memória podem se beneficiar do uso de um número reduzido de núcleos por CCX. Para esses aplicativos, o uso de 3 ou 2 núcleos por CCX pode reduzir a contenção da largura de banda de memória e produzir maior desempenho no mundo real ou escalabilidade mais consistente. Em particular, o MPI Allreduce pode se beneficiar disso.
* Para corridas em escala significativamente maior, recomenda-se usar transportes UD ou HÍBRIDORC+UD. Muitas bibliotecas mpi/bibliotecas de tempo de execução fazem isso internamente (como UCX ou MVAPICH2). Verifique as configurações de transporte para corridas em larga escala.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
