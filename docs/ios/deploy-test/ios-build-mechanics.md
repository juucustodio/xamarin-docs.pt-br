---
title: "Mecânica de Compilação do iOS"
description: "Este guia explora como determinar o ritmo dos seus aplicativos e como usar métodos que podem ser empregados para compilações mais rápidas para todas as configurações de compilação."
ms.topic: article
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 44813ea6c1e795889444d949ca2706c0f50483c6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="ios-build-mechanics"></a>Mecânica de Compilação do iOS

_Este guia explora como determinar o ritmo dos seus aplicativos e como usar métodos que podem ser empregados para compilações mais rápidas para todas as configurações de compilação._

Desenvolver excelentes aplicativos é mais do que apenas escrever um código de funcione. Um aplicativo bem escrito deve conter otimizações que conseguem compilações mais rápidas com aplicativos que são menores e que executam mais rápido. Essas otimizações não apenas resultarão em uma melhor experiência para o usuário, mas também para você ou qualquer desenvolvedor trabalhando no projeto. É fundamental garantir que ao gerenciar seu aplicativo tudo seja sincronizado com frequência. 

Lembre-se de que as opções padrão são seguras e rápidas, mas não são ideias para todas as situações. Além disso, muitas opções podem reduzir ou aumentar a velocidade do ciclo de desenvolvimento, dependendo do projeto individual. Por exemplo, a remoção nativa requer tempo, mas se pouco tamanho for obtido, o tempo gasto com a remoção não será recuperado por uma implantação mais rápida. Por outro lado, a remoção nativa pode reduzir o aplicativo significativamente, nesse caso, a implantação será mais rápida. Isso varia de projeto para projeto e a única maneira de saber é testando.

A velocidade de compilação do Xamarin também pode ser afetada por vários recursos e entre os recursos de um computador que podem afetar o desempenho estão: capacidade do processador, velocidades de barramento, quantidade de memória física, velocidade do disco, velocidade da rede. Essas limitações de desempenho estão além do escopo deste documento e são de responsabilidade do desenvolvedor.


## <a name="timing-apps"></a>Sincronizar aplicativos

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para habilitar os resultados de diagnóstico do MSBuild no Visual Studio para Mac:

1. Clique em **Visual Studio para Mac > Preferências...**
2. No modo de exibição de árvore à esquerda, selecione **Projetos > Compilar**
3. No painel à direita, defina o menu suspenso de detalhamento do Log como **Diagnóstico**: [![](ios-build-mechanics-images/image2.png "Configurando o nível de detalhes do Log")](ios-build-mechanics-images/image2.png#lightbox)
4. Clique em **OK**
5. Reiniciar o Visual Studio para Mac
6. Limpar e recompilar o seu pacote
7. Exibir os resultados de diagnóstico no Painel de Erros (Exibição > Painéis > Erros) clicando no botão de Resultado de Compilação


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para habilitar os resultados de diagnóstico do MSBuild no Visual Studio para Mac:

1. Clique em **Ferramentas > Opções...**
2. No modo de exibição de árvore à esquerda, selecione **Projetos e Soluções > Compilar e Executar**
3. No painel à direito, defina o *Menu suspenso de detalhamento de resultados de build do MSBuild* como **Diagnóstico**: [![](ios-build-mechanics-images/image2-vs.png "Configurando o nível de detalhamento dos resultados de build do MSBuild")](ios-build-mechanics-images/image2-vs.png#lightbox)
4. Clique em **OK**
5. Limpe e recompile o seu pacote.
6. Os resultados de diagnóstico estão visíveis no painel Resultados.

-----

## <a name="timing-mtouch"></a>Sincronizar o mtouch

Para exibir informações específicas para o processo de compilação do mtouch, passe o `--time --time` para os argumentos do mtouch nas **Opções de Projeto**. Os resultados são encontrados no Resultado da Compilação, pesquisando a tarefa `MTouch`:

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>Conectando do Visual Studio com o Host de Compilação

As ferramentas do Xamarin, tecnicamente, funcionam em qualquer Mac que possa executar o OS X 10.10 Yosemite ou posterior. No entanto, as experiências de desenvolvedor e tempos de compilação podem ser prejudicados pelo desempenho do Mac.

No estado desconectado, o Visual Studio no Windows só realizará a fase de compilação do C# e não tentará executar a vinculação ou compilação AOT, empacotar o aplicativo em um pacote _.app_ ou conectar-se ao pacote de aplicativos. (A fase de compilação do C# raramente é um gargalo de desempenho.) Tente identificar em que parte do pipeline a compilação está lenta, fazendo a compilando diretamente no host de compilação do Mac no Visual Studio para Mac.


Além disso, um dos locais mais comuns para a lentidão é a conexão de rede entre o computador Windows e o host de compilação do Mac. Isso pode ocorrer devido a um obstáculo físico na rede, usar uma conexão sem fio ou ter que viajar por um computador saturado (como um serviço Mac na nuvem).

## <a name="simulator-tricks"></a>Truques do Simulador

 
Ao implantar aplicativos móveis, é fundamental implantar o código rapidamente. Por uma série de motivos, incluindo a velocidade e a falta de requisitos de provisionamento de dispositivos, os desenvolvedores, muitas vezes, optam por implantar um emulador ou simulador pré-instalado. Para os fabricantes de ferramentas de desenvolvedor, a decisão de fornecer um simulador ou emulador se resume a uma compensação entre a velocidade e compatibilidade. 

A Apple fornece um simulador para desenvolvimento do iOS, promovendo a velocidade sobre a compatibilidade, com a criação de um ambiente menos restritivo para executar o código. Esse ambiente menos restritivo permite ao Xamarin usar o compilador Em Tempo Real (JIT) do simulador (em vez do [AOT](~/ios/internals/architecture.md) em um dispositivo), o que significa que a compilação é compilada no código nativo em tempo de execução. Como o Mac é muito mais rápido do que um dispositivo, isso permite um melhor desempenho.

O simulador usa um inicializador do aplicativo compartilhado, permitindo que o iniciador seja reutilizado, em vez de ser compilado cada vez, como é necessário no dispositivo.

Levando em consideração as informações acima, a lista abaixo fornece algumas informações sobre etapas a serem executadas ao compilar e implantar seu aplicativo no simulador para fornecer o melhor desempenho.
 
### <a name="tips"></a>Dicas

- Para compilações: 
  - Desmarque a opção **Otimizar imagens PNG** nas Opções de Projeto. Essa otimização não é necessária para compilações no simulador.
  - Defina o vinculador como **Não Vincular**. Desabilitar o vinculador é mais rápido, porque executá-lo requer um tempo significativamente maior.
  - Desabilitar o inicializador do aplicativo compartilhado usando o sinalizador `--nofastsim` faz com que as compilações do simulador fiquem muito mais lentas. Remova esse sinalizador quando ele não for mais necessário.
  - Usar bibliotecas nativas é mais lento porque o arquivo executável principal do inicializador de simulação compartilhado não pode ser reutilizado em tais casos e um arquivo executável específico do aplicativo precisa ser compilado para cada compilação.
- Para implantação
  - Sempre que possível mantenha o simulador em execução. São necessários até 12 segundos para inicialização a frio o simulador.
- Dicas Adicionais
  - Prefira Compilar a Recompilar, porque Recompilar limpa antes de compilar. A limpeza pode demorar muito tempo para remover as referências que podem ser usadas.
  - Aproveite o fato de que o simulador não impõe a área restrita. Ter recursos grandes como vídeos ou outros ativos no projeto pode criar operações de cópia de arquivos dispendiosas sempre que o aplicativo é inicializado no simulador. Evite essas operações dispendiosas colocando os arquivos no diretório base e faça referência a eles no seu aplicativo pelo caminho completo.  
  - Em caso de dúvida, use o sinalizador `–time –time` para medir sua alteração

A captura de tela abaixo ilustra como definir essas opções para o simulador nas opções do iOS:

[![](ios-build-mechanics-images/image3.png "Definindo as opções")](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>Truques de dispositivos

A implantação no dispositivo é semelhante à implantação no simulador, uma vez que o simulador é um pequeno subconjunto de compilação usado para o dispositivo iOS. A compilação para o dispositivo requer várias etapas, mas tem a vantagem de fornecer outras oportunidades para otimizar o seu aplicativo.

### <a name="build-configurations"></a>Configurações de compilação

Uma série de configurações de compilação é oferecida ao implantar aplicativos iOS. É importante ter uma boa compreensão de cada configuração, saber quando e por que você deve otimizar.

 - Depurar
  - Essa é a configuração principal que deve ser usada enquanto um aplicativo está em desenvolvimento e deve, portanto, ser o mais rápido possível.
 - Versão
  - As compilações de versão são aquelas que são enviadas aos seus usuários e o foco no desempenho é fundamental. Ao usar a configuração de Versão, convém usar o compilador de otimização do LLVM e otimizar arquivos PNG.

 
Também é importante entender a relação entre compilação e implantação. O tempo de implantação é uma função do tamanho do aplicativo. Um aplicativo maior leva mais tempo para implantação. Minimizando o tamanho do aplicativo, é possível reduzir o tempo de implantação.

Minimizar o tamanho do aplicativo também pode reduzir o tempo de compilação. Isso ocorre porque a remoção do código do aplicativo leva menos tempo do que compilar nativamente o código que não será usado. Arquivos de objetos menores significam vinculação mais rápida, o que cria um arquivo executável menor e com menos símbolos para gerar. Portanto, economizar espaço tem um benefício duplo, e é por isso que **Vincular SDK** é o padrão para todas as compilações de dispositivos. 

> [!NOTE]
> A opção **Vincular SDK** pode ser exibida como Vincular somente SDKs de estruturas ou Vincular somente assemblies de SDK, dependendo do IDE que está sendo usado.
 

### <a name="tips"></a>Dicas

- Build: 
  - Compilar uma única arquitetura (por exemplo, ARM64) é mais rápido do que um binário FAT (por exemplo, ARMv7 + ARM64)
  - Evite otimizar arquivos PNG ao depurar
  - Considere Vincular todos os assemblies. Otimizar cada assembly 
  - Desabilite a criação de símbolos de depuração usando `--dsym=false`. No entanto, saiba que desabilitar essa opção significa que os relatórios de falhas só poderão ser simbolizados na máquina que compilou o aplicativo e somente se o aplicativo não tiver sido eliminado.

 
O que evitar:

- Binários FAT (depuração) 
- Desabilitar o vinculador `–nolink` 
- Desabilitar a remoção 
  - Símbolos `--nosymbolstrip` 
  - IL (versão) `--nostrip`.  
 
Dicas adicionais 

- Como no simulador, prefira Compilar a Recompilar 
  - Assemblies do AOT (arquivos de objetos) são armazenados em cache 
- Depurar compilações demora mais tempo devido aos símbolos que executam o dsymutil e como eles acabam sendo maiores, é necessário mais tempo para carregar os dispositivos. 
- As compilações de versão farão, por padrão, uma faixa de IL dos assemblies. Isso requer pouco de tempo, o qual será provavelmente recuperado ao implantar um .app menor no dispositivo.
- Evitar a implantação de arquivos estáticos grandes em cada compilação (depuração) 
  - Use UIFileSharingEnabled (info.plist) 
    - Os ativos podem ser carregados uma vez 
- Em caso de dúvida, use o sinalizador `–time –time` para medir sua alteração

A captura de tela abaixo ilustra como definir essas opções para o simulador nas opções do iOS:

[![](ios-build-mechanics-images/image4.png "Definindo as opções")](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>Usar o Vinculador

Ao compilar seu aplicativo, o mtouch usa um vinculador para o código gerenciado, que remove o código que o aplicativo não está usando. Na teoria, isso oferece compilações menores e, portanto, mais rápidas. Para obter mais informações sobre o vinculador, consulte o guia [Vinculação no iOS](~/ios/deploy-test/linker.md).

Considere as seguintes opções ao usar o Vinculador:

- Selecionar **Não Vincular** para uma compilação de Dispositivos requer uma grande quantidade de tempo e gera um aplicativo maior. 
  - A Apple rejeitará aplicativos com tamanho acima do limite. Dependendo do `MinimumOSVersion`, o limite pode ser pequeno como 60 MB. 
  - O arquivo executável nativo está sendo incluído. 
  - Usar Não vincular é mais rápido para compilações de simulador porque a compilação JIT é usada (em vez de AOT em um dispositivo).
- Vincular o SDK é a opção padrão.
- Usar Vincular Todos pode não ser seguro, especialmente se você estiver usando um código que não seja o seu próprio, como um NuGets ou Componentes. Se você optar por não vincular assemblies, todo o código desses serviços será incluído no seu aplicativo, possivelmente criando aplicativos maiores. 
  - No entanto, se você escolher **Vincular Todos** o aplicativo poderá falhar, principalmente se os componentes externos forem usados. Isso ocorre porque alguns componentes usam Reflexão em determinados tipos.
  - A reflexão e análise estática não funcionam em conjunto. 

As ferramentas podem ser instruídas a manterem os itens dentro do aplicativo usando o [atributo `[Preserve]`](~/ios/deploy-test/linker.md). 

Caso você não tenha acesso ao código-fonte ou ele seja gerado por uma ferramenta e você não queira alterá-lo, ele ainda poderá ser vinculado criando um arquivo XML que descreve todos os tipos e membros que precisam ser preservados. Em seguida, é possível adicionar o sinalizador `--xml={file.name}.xml` às opções de projeto, que processaram o código exatamente como se você estivesse usando Atributos.


### <a name="partially-linking-applications"></a>Vinculação Parcial de Aplicativos 

Também é possível vincular parcialmente os aplicativos, para ajudar a otimizar o tempo de compilação do seu aplicativo:

- Use `Link All` e ignore alguns assemblies 
  - Algumas das otimizações de tamanho do aplicativo serão perdidas.
  - Não é necessário acesso ao código-fonte.
  - Por exemplo, `--linkall --linkskip=fieldserviceiOS`.
 
- Use a opção `Link SDK` e o atributo `[LinkerSafe]` nos assemblies necessários 
  - É necessário acesso ao código-fonte.
  - Informa ao sistema que o assembly é seguro para vincular e é processado como se ele fosse um SDK do Xamarin.
 
### <a name="objective-c-bindings"></a>Associações do Objective-C 

- Usar o atributo `[Assembly: LinkerSafe]` nas suas associações pode economizar tempo e reduzir o tamanho.

- SmartLink 
  - Feito no lado Nativo 
  - Use o atributo `[LinkWith (SmartLink=true)]`
  - Isso ajuda o vinculador nativo a eliminar o código nativo da biblioteca à qual você está vinculando. 
  - Observe que a pesquisa dinâmica de símbolos não funcionará com isso. 

## <a name="summary"></a>Resumo

Este guia explorou como determinar o ritmo de um aplicativo iOS e as opções a considerar que dependem da configuração da compilação do projeto. 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 
 
The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 
 
 
 
### Clean build time 
178 seconds 
 
 
### Build again (without cleaning) after making _no changes_ 
12.5 seconds 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
 
3 trials: 45 seconds, 45 seconds, 45 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 
 
45 seconds 
 
 
 
 
 
 
## Layer 2: "app thinning" aka "device specific builds" 
 
The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 
 
As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 
 
Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 
 
 
 
(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 
 
### Clean build time without "device specific builds" 
177 seconds 
 
 
 
### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 
 
 
* * * 
 
 
## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 
 
(These builds were again run on the command line using `xbuild`.) 
 
 
 
### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 
 
 
 
 
 
[1] Mac system used for testing: MacBookAir5,2 
 
- 2.0 GHz Core i7 (I7-3667U) 
 
2 Cores with hyper-threading 
 
L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 
 
- Standard MacBook soldered-in solid-state storage 
 
- 8 GB RAM 
---->


## <a name="related-links"></a>Links relacionados

- [Publicação do Blog](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [Vinculação no iOS](~/ios/deploy-test/linker.md)
- [Configuração Personalizada do Vinculador](~/cross-platform/deploy-test/linker.md)
