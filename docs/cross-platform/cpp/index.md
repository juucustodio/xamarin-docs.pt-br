---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Use bibliotecas C/C++ com Xamarin
description: Visual Studio para Mac pode ser usado para construir e integrar código C/C++ multiplataforma em aplicativos móveis para Android e iOS, usando Xamarin e C#. Este artigo explica como configurar e depurar um projeto C++ em um aplicativo Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73842814"
---
# <a name="use-cc-libraries-with-xamarin"></a>Use bibliotecas C/C++ com Xamarin

## <a name="overview"></a>Visão geral

O Xamarin permite que os desenvolvedores criem aplicativos móveis nativos multiplataforma com o Visual Studio. Geralmente, as ligações C# são usadas para expor os componentes da plataforma existentes aos desenvolvedores. No entanto, há momentos em que os aplicativos Xamarin precisam trabalhar com bases de código existentes. Às vezes, as equipes simplesmente não têm tempo, orçamento ou recursos para portar uma grande, bem testada e altamente otimizada base de código para C#.

[O Visual C++ para desenvolvimento móvel multiplataforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite que o código C/C++ e C# seja construído como parte da mesma solução, oferecendo muitas vantagens, incluindo uma experiência unificada de depuração. A Microsoft tem usado C/C++ e Xamarin desta forma para fornecer aplicativos como [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [Pix Camera](https://www.microsoft.com/microsoftpix).

No entanto, em alguns casos, há um desejo (ou requisito) de manter as ferramentas e processos C/C++ existentes em vigor e manter o código da biblioteca dissociado do aplicativo, tratando a biblioteca como se fosse semelhante a um componente de terceiros. Nessas situações, o desafio não é apenas expor os membros relevantes ao C#, mas gerenciar a biblioteca como uma dependência. E, claro, automatizar o máximo possível desse processo.  

Este post descreve uma abordagem de alto nível para este cenário e passa por um exemplo simples.

## <a name="background"></a>Segundo plano

C/C++ é considerado um idioma multiplataforma, mas deve-se tomar muito cuidado para garantir que o código-fonte seja de fato multiplataforma, usando apenas C/C++ suportado por todos os compiladores de destino e contendo pouca ou nenhuma plataforma incluída condicionalmente ou código específico do compilador.

Em última análise, o código deve compilar e executar com sucesso em todas as plataformas de destino, portanto, isso se resume à comunhão entre as plataformas (e compiladores) que estão sendo alvo. Os problemas ainda podem surgir de pequenas diferenças entre os compiladores e, portanto, testes minuciosos (preferencialmente automatizados) em cada plataforma-alvo se tornam cada vez mais importantes.  

## <a name="high-level-approach"></a>Abordagem de alto nível

A ilustração abaixo representa a abordagem de quatro estágios usada para transformar o código fonte C/C++ em uma biblioteca Xamarin multiplataforma que é compartilhada via NuGet e depois é consumida em um aplicativo Xamarin.Forms.

![Abordagem de alto nível para usar C/C++ com Xamarin](images/cpp-steps.jpg)

Os 4 estágios são:

1. Compilando o código-fonte C/C++ em bibliotecas nativas específicas da plataforma.
2. Embrulhando as bibliotecas nativas com uma solução visual studio.
3. Empacotar e empurrar um pacote NuGet para o invólucro .NET.
4. Consumindo o pacote NuGet de um aplicativo Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Estágio 1: Compilar o código-fonte C/C++ em bibliotecas nativas específicas da plataforma

O objetivo desta etapa é criar bibliotecas nativas que podem ser chamadas pelo invólucro C#. Isso pode ou não ser relevante dependendo da sua situação. As muitas ferramentas e processos que podem ser trazidos para suportar neste cenário comum estão além do escopo deste artigo. As principais considerações são manter a base de código C/C++ em sincronia com qualquer código de invólucro nativo, testes de unidade suficientes e automação de construção. 

As bibliotecas no walk-through foram criadas usando o Visual Studio Code com um script shell que acompanha. Uma versão estendida deste walk-through pode ser encontrada no [repositório Mobile CAT GitHub](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) que discute esta parte da amostra em maior profundidade. As bibliotecas nativas estão sendo tratadas como uma dependência de terceiros neste caso, no entanto, esta etapa é ilustrada para o contexto.

Para simplificar, o passo a passo tem como alvo apenas um subconjunto de arquiteturas. Para iOS, ele usa o utilitário lipo para criar um binário de gordura único a partir dos binários específicos da arquitetura individual. O Android usará binários dinâmicos com uma extensão .so e o iOS usará um binário de gordura estática com uma extensão .a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Estágio 2: Embrulhar as bibliotecas nativas com uma solução visual studio

A próxima etapa é envolver as bibliotecas nativas para que sejam facilmente usadas a partir de .NET. Isso é feito com uma solução visual studio com quatro projetos. Um projeto compartilhado contém o código comum. Projetos direcionados a cada um dos Xamarin.Android, Xamarin.iOS e .NET Standard permitem que a biblioteca seja referenciada de forma agnóstica de plataforma.

O invólucro usa '[o truque de isca e troca](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)', descrito por Paul Betts. Essa não é a única maneira, mas facilita a referência à biblioteca e evita a necessidade de gerenciar explicitamente implementações específicas da plataforma dentro do próprio aplicativo de consumo. O truque é essencialmente garantir que os alvos (.NET Standard, Android, iOS) compartilhem o mesmo namespace, nome de montagem e estrutura de classe. Como o NuGet sempre prefere uma biblioteca específica da plataforma, a versão .NET Standard nunca é usada em tempo de execução.

A maior parte do trabalho nesta etapa se concentrará em usar P/Invoke para chamar os métodos de biblioteca nativa e gerenciar as referências aos objetos subjacentes. O objetivo é expor a funcionalidade da biblioteca ao consumidor, ao mesmo tempo em que abstrai qualquer complexidade. Os desenvolvedores do Xamarin.Forms não precisam ter conhecimento de trabalho sobre o funcionamento interno da biblioteca não gerenciada. Deve parecer que eles estão usando qualquer outra biblioteca C# gerenciada.

Em última análise, a saída desta etapa é um conjunto de bibliotecas .NET, uma por destino, juntamente com um documento nuspec que contém as informações necessárias para construir o pacote na próxima etapa.

**Estágio 3: Empacotar e empurrar um pacote NuGet para o invólucro .NET**

O terceiro estágio é a criação de um pacote NuGet usando os artefatos de compilação da etapa anterior. O resultado desta etapa é um pacote NuGet que pode ser consumido a partir de um aplicativo Xamarin. O passo a passo usa um diretório local para servir como feed NuGet. Na produção, esta etapa deve publicar um pacote para um feed NuGet público ou privado e deve ser totalmente automatizado.

**Estágio 4: Consumir o pacote NuGet de um aplicativo Xamarin.Forms**

O passo final é referenciar e usar o pacote NuGet a partir de um aplicativo Xamarin.Forms. Isso requer a configuração do feed NuGet no Visual Studio para usar o feed definido na etapa anterior.

Uma vez configurado o feed, o pacote precisa ser referenciado a partir de cada projeto no aplicativo xamarin.forms cross-platform. 'O truque de isca e switch' fornece interfaces idênticas, de modo que a funcionalidade da biblioteca nativa pode ser chamada usando código definido em um único local.

O repositório de código fonte contém uma [lista de leitura suplementar](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) que inclui artigos sobre como configurar um feed NuGet privado no Azure DevOps e como empurrar o pacote para esse feed. Embora exija um pouco mais de tempo de configuração do que um diretório local, esse tipo de alimentação é melhor em um ambiente de desenvolvimento de equipe.

## <a name="walk-through"></a>Passo a passo

As etapas fornecidas são específicas para **o Visual Studio para Mac,** mas a estrutura funciona no Visual Studio **2017** também.

### <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, o desenvolvedor precisará:

- [Linha de comando NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Estúdio Visual* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Uma [**conta**](https://developer.apple.com/) ativa do Desenvolvedor Apple é necessária para implantar aplicativos em um iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Criando as bibliotecas nativas (Estágio 1)

A funcionalidade da biblioteca nativa é baseada no exemplo de [Passo a Passo: Criando e Usando uma Biblioteca Estática (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Esse passo a passo pula a primeira etapa, construindo as bibliotecas nativas, uma vez que a biblioteca é fornecida como uma dependência de terceiros neste cenário. As bibliotecas nativas pré-compiladas estão incluídas ao lado do [código de amostra](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) ou podem ser [baixadas](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) diretamente.

### <a name="working-with-the-native-library"></a>Trabalhando com a biblioteca nativa

O exemplo original *do MathFuncsLib* `MyMathFuncs` inclui uma única classe chamada com a seguinte definição:

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Uma classe adicional define funções de invólucro que permitem que um consumidor `MyMathFuncs` .NET crie, dispe e interaja com a classe nativa subjacente.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Serão essas funções de invólucro que são usadas no lado [xamarin.](https://visualstudio.microsoft.com/xamarin/)

## <a name="wrapping-the-native-library-stage-2"></a>Embrulhando a biblioteca nativa (Estágio 2)

Esta etapa requer as [bibliotecas pré-compiladas descritas](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) na [seção anterior](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Criando a solução visual studio

1. No **Visual Studio for Mac,** clique em Novo **Projeto** (da Página de *Boas-Vindas)* ou **Nova Solução** (no menu *Arquivo).*
2. Na janela **Novo Projeto,** escolha **Projeto Compartilhado** (dentro da Biblioteca *> Multiplataforma)* e clique em **Next**.
3. Atualize os seguintes campos e clique em **Criar**:

    - **Nome do projeto:** MathFuncs.Shared  
    - **Nome da solução:** MathFuncs  
    - **Localização:** Use o local de salvamento padrão (ou escolha uma alternativa)   
    - **Crie um projeto dentro do diretório de soluções:** Defina isso como verificado
4. Do **Solution Explorer**, clique duas vezes no projeto **MathFuncs.Shared** e navegue até **as configurações principais**.
5. Remover **. Compartilhado** no **Namespace padrão para** que ele seja definido apenas para **MathFuncs** e clique em **OK**.
6. Abra **MyClass.cs** (criado pelo modelo), depois renomeie a classe e o nome de arquivo para **MyMathFuncsWrapper** e altere o namespace para **MathFuncs**.
7. **CONTROL + CLIQUE** na solução **MathFuncs,** em seguida, escolha **Adicionar novo projeto...** no menu **Adicionar.**
8. Na janela **Novo projeto,** escolha **.NET Standard Library** (de dentro da Biblioteca > *Multiplataforma)* e clique em **Next**.
9. Escolha **.NET Padrão 2.0** e clique **em Seguir**.
10. Atualize os seguintes campos e clique em **Criar**:

    - **Nome do projeto:** MathFuncs.Standard  
    - **Localização:** Use o mesmo local de salvamento que o projeto compartilhado   

11. Do **Solution Explorer**, clique duas vezes no projeto **MathFuncs.Standard.**
12. Navegue até **configurações principais**e **atualize o namespace padrão** para **MathFuncs**.
13. Navegue até as configurações **de saída** e atualize o nome **do Conjunto** para **MathFuncs**.
14. Navegue até as configurações do **Compilador,** altere a **Configuração** para **Liberar**, definindo As informações **de depuração** para **Símbolos Somente** então clique em **OK**.
15. Excluir **Class1.cs/Getting Iniciado** a partir do projeto (se um deles tiver sido incluído como parte do modelo).
16. **CONTROLE + CLIQUE** na pasta **Dependências/Referências** do projeto e escolha **Editar referências**.
17. Selecione **MathFuncs.Shared** na guia **Projetos** e clique em **OK**.
18. Repita as etapas 7-17 (ignorando a etapa 9) usando as seguintes configurações:

    | **NOME DO PROJETO**  | **NOME DO MODELO**   | **NOVO MENU DO PROJETO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Biblioteca de Classes       | Biblioteca > Android      |
    | MathFuncs.iOS     | Biblioteca de Vinculação     | Biblioteca > iOS          |

19. Do **Solution Explorer**, clique duas vezes no projeto **MathFuncs.Android** e navegue até as configurações do **Compilador.**

20. Com a **configuração** definida **como Depuração,** **edite Definir símbolos** para incluir **Android;**.

21. Alterar a **configuração** para **liberar,** em seguida, editar **Definir símbolos** para também incluir **Android;**.

22. Repita as etapas 19-20 para **MathFuncs.iOS**, edição **Definir Símbolos** para incluir **iOS;** em vez de **Android;** em ambos os casos.

23. Construa a solução na configuração **de versão** ( CONTROL + COMMAND **+ B**) e valide se todos os três conjuntos de saída (Android, iOS, .NET Standard) (nas respectivas pastas de bin de projeto) compartilham o mesmo nome **MathFuncs.dll**.

Nesta fase, a solução deve ter três alvos, um para Android, iOS e .NET Standard, e um projeto compartilhado que é referenciado por cada um dos três alvos. Estes devem ser configurados para usar o mesmo namespace padrão e conjuntos de saída com o mesmo nome. Isso é necessário para a abordagem "isca e troca" mencionada anteriormente.

### <a name="adding-the-native-libraries"></a>Adicionando as bibliotecas nativas

O processo de adicionar as bibliotecas nativas à solução de invólucro varia ligeiramente entre Android e iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Referências nativas para MathFuncs.Android

1. **CONTROL + CLIQUE** no projeto **MathFuncs.Android** e escolha **Nova pasta** no menu **Adicionar** **libs**.

2. Para cada **ABI** (Interface Binária de Aplicativo), **CONTROL + CLIQUE** na pasta **libs** e escolha **Nova pasta** no menu **Adicionar,** nomeando-a após essa respectiva **ABI**. Nesse caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obter uma visão geral mais detalhada, consulte o tópico [Arquiteturas e CPUs](https://developer.android.com/ndk/guides/arch) do [guia de desenvolvedores NDK,](https://developer.android.com/ndk/guides/)especificamente a seção sobre endereçamento ao [código nativo em pacotes de aplicativos](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Verifique a estrutura da pasta:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Adicione as bibliotecas **.so** correspondentes a cada uma das pastas **ABI** com base no seguinte mapeamento:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/braço  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Para adicionar arquivos, **CONTROL + CLIQUE** na pasta representando o respectivo **ABI,** em seguida, escolha **Adicionar arquivos...** no menu **Adicionar.** Escolha a biblioteca apropriada (do diretório **PrecompileDLibs)** e clique em **Abrir** e clique em **OK** deixando a opção padrão *de Copiar o arquivo para o diretório*.

5. Para cada um dos arquivos **.so,** **CONTROL + CLICK,** então escolha a opção **EmbeddedNativeLibrary** no menu **Build Action.**

Agora, a pasta **libs** deve aparecer da seguinte forma:

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Referências nativas para MathFuncs.iOS

1. **CONTROLE + CLIQUE** no projeto **MathFuncs.iOS** e escolha **Adicionar referência nativa** no menu **Adicionar.** 
2. Escolha a biblioteca **libMathFuncs.a** (de libs/ios sob o diretório **PrecompileDLibs)** e clique em **Abrir** 
3. **CONTROLE + CLIQUE** no arquivo **libMathFuncs** (dentro da pasta **Referências nativas** e escolha a opção **Propriedades** no menu  
4. Configure as propriedades **de referência nativa** para que sejam verificadas (mostrando um ícone de tique-taque) no Bloco de **propriedades:**

    - Carga de Força
    - É C++
    - Link Inteligente

    > [!NOTE]
    > O uso de um tipo de projeto de biblioteca de vinculação, juntamente com uma [referência nativa,](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpora a biblioteca estática e permite que ela seja automaticamente vinculada ao aplicativo Xamarin.iOS que o faz referência (mesmo quando ele é incluído através de um pacote NuGet).

5. Abra **ApiDefinition.cs**, excluindo o código comentou `MathFuncs` modelado (deixando apenas o namespace), em seguida, execute a mesma etapa para **Structs.cs** 

    > [!NOTE]
    > Um projeto de biblioteca de vinculação requer esses arquivos (com as ações de compilação *ObjCBindingApiDefinition* e *ObjCBindingCoreSource)* para serem construídos. No entanto, vamos escrever o código, para chamar nossa biblioteca nativa, fora desses arquivos de uma maneira que pode ser compartilhada entre alvos de biblioteca Android e iOS usando p/invoke padrão.

### <a name="writing-the-managed-library-code"></a>Escrevendo o código da biblioteca gerenciada

Agora, escreva o código C# para chamar a biblioteca nativa. O objetivo é esconder qualquer complexidade subjacente. O consumidor não deve precisar de qualquer conhecimento de trabalho dos internos da biblioteca nativa ou dos conceitos P/Invoke.  

#### <a name="creating-a-safehandle"></a>Criando um SafeHandle

1. **CONTROL + CLIQUE** no projeto **MathFuncs.Shared** e escolha **Adicionar arquivo...** no menu **Adicionar.** 
2. Escolha **Classe vazia** na janela Novo **arquivo,** nomeie-a **MyMathFuncsSafeHandle** e clique em **Novo**
3. Implementar a classe **MyMathFuncsSafeHandle:**

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Um [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) é a maneira preferida de trabalhar com recursos não gerenciados em código gerenciado. Isso abstrai um monte de código de caldeira relacionado à finalização crítica e ao ciclo de vida do objeto. O proprietário desta alça pode posteriormente tratá-lo como qualquer outro recurso gerenciado e não terá que implementar o [padrão descartável](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)completo . 

#### <a name="creating-the-internal-wrapper-class"></a>Criando a classe de invólucro interno

1. Abra **MyMathFuncsWrapper.cs**, alterando-a para uma classe estática interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. No mesmo arquivo, adicione a seguinte declaração condicional à classe:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Isso define o valor constante do **DllName** com base em se a biblioteca está sendo construída para **Android** ou **iOS**. Isso é para abordar as diferentes convenções de nomeação usadas por cada plataforma respectiva, mas também o tipo de biblioteca que está sendo usada neste caso. O Android está usando uma biblioteca dinâmica e, portanto, espera um nome de arquivo, incluindo extensão. Para iOS, '*__Internal*' é necessário, uma vez que estamos usando uma biblioteca estática.

3. Adicione uma referência ao **System.Runtime.InteropServices** na parte superior do arquivo **MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Adicione os métodos de invólucro para lidar com a criação e eliminação da classe **MyMathFuncs:**

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Estamos passando nosso **DllName** constante para o atributo **DllImport** juntamente com o **EntryPoint** que informa explicitamente ao .NET runtime o nome da função para chamar dentro dessa biblioteca. Tecnicamente, não precisamos fornecer o valor do **EntryPoint** se nossos nomes de método gerenciados fossem idênticos ao não gerenciado. Se um não for fornecido, o nome do método gerenciado será usado como **EntryPoint.** No entanto, é melhor ser explícito.  

5. Adicione os métodos de wrapper para nos permitir trabalhar com a classe **MyMathFuncs** usando o seguinte código:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Estamos usando tipos simples para os parâmetros deste exemplo. Uma vez que marshalling é uma cópia pouco sábio neste caso, não requer nenhum trabalho adicional de nossa parte. Observe também o uso da classe **MyMathFuncsSafeHandle** em vez do **IntPtr**padrão . O **IntPtr** é automaticamente mapeado para o **SafeHandle** como parte do processo de marshalling.

6. Verifique se a classe **MyMathFuncsWrapper** finalizada aparece como abaixo:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Concluindo a aula MyMathFuncsSafeHandle

1. Abra a classe **MyMathFuncsSafeHandle,** navegue até o comentário **todo** do espaço reservado dentro do método **ReleaseHandle:**

    ```csharp
    // TODO: Release the handle here
    ```

1. Substitua a **linha TODO:**

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escrevendo a aula myMathFuncs

Agora que o invólucro está completo, crie uma classe MyMathFuncs que gerenciará a referência ao objeto C++ MyMathFuncs não gerenciado.  

1. **CONTROL + CLIQUE** no projeto **MathFuncs.Shared** e escolha **Adicionar arquivo...** no menu **Adicionar.** 
2. Escolha **Classe vazia** na janela Novo **arquivo,** nomeie-a **MyMathFuncs** e clique em **Novo**
3. Adicione os seguintes membros à classe **MyMathFuncs:**

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implemente o construtor para a classe para que ele crie e armazena uma alça no objeto **MyMathFuncs** nativo quando a classe estiver instanciada:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implemente a interface **IDescartável** usando o seguinte código:

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Implemente os métodos **MyMathFuncs** usando a classe **MyMathFuncsWrapper** para executar o trabalho real sob o capô, passando o ponteiro que armazenamos para o objeto não gerenciado subjacente. O código deve ser o seguinte:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>Criando a nuspec

Para ter a biblioteca empacotada e distribuída via NuGet, a solução precisa de um arquivo **nuspec.** Isso identificará quais das assembléias resultantes serão incluídas para cada plataforma suportada.

1. **CONTROLE + CLIQUE** na solução **MathFuncs,** em seguida, escolha **Adicionar pasta** de solução a partir do menu **Adicionar** o nomeação **de soluçõesItens**.
2. **CONTROL + CLIQUE** na pasta **SolutionItems** e escolha **Novo Arquivo...** no menu **Adicionar.**
3. Escolha **arquivo XML vazio** na janela Novo **arquivo,** nomeie-o **MathFuncs.nuspec** e clique em **Novo**.
4. Atualize **MathFuncs.nuspec** com os metadados básicos do pacote a serem exibidos ao consumidor **NuGet.** Por exemplo:

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > Consulte o documento [de referência nuspec](https://docs.microsoft.com/nuget/reference/nuspec) para obter mais detalhes sobre o esquema utilizado para este manifesto.

5. Adicione `<files>` um elemento como `<package>` filho do `<metadata>`elemento (logo abaixo), `<file>` identificando cada arquivo com um elemento separado:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando um pacote é instalado em um projeto e onde há vários conjuntos especificados pelo mesmo nome, o NuGet escolherá efetivamente o conjunto mais específico para a plataforma dada.

6. Adicione `<file>` os elementos para **os** conjuntos Android:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Adicione `<file>` os elementos para os conjuntos **do iOS:**

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Adicione `<file>` os elementos para os conjuntos **netstandard2.0:**

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verifique o manifesto **nuspec:**

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Este arquivo especifica os caminhos de saída de montagem a partir de uma **compilação De suma,** por isso certifique-se de construir a solução usando essa configuração.

Neste ponto, a solução contém 3 assembléias .NET e um manifesto **nuspec** de apoio.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuindo o invólucro .NET com NuGet

O próximo passo é empacotar e distribuir o pacote NuGet para que ele possa ser facilmente consumido pelo aplicativo e gerenciado como uma dependência. O embrulho e o consumo poderiam ser feitos em uma única solução, mas distribuir a biblioteca através do NuGet ajuda na dissociação e nos permite gerenciar essas bases de código de forma independente.

### <a name="preparing-a-local-packages-directory"></a>Preparando um diretório local de pacotes

A forma mais simples de alimentação NuGet é um diretório local:

1. No **Finder,** navegue até um diretório conveniente. Por exemplo, **/Users**.
2. Escolha **Nova pasta** no menu **Arquivo,** fornecendo um nome significativo, como **local-nuget-feed**.

### <a name="creating-the-package"></a>Como criar o pacote

1. Defina a **configuração de compilação** para **liberar**e execute uma compilação usando **COMMAND + B**.
2. Abrir **terminal** e alterar diretório para a pasta que contém o arquivo **nuspec.**
3. Em **Terminal,** execute o comando **nuget pack** especificando o arquivo **nuspec,** a **Versão** (por exemplo, 1.0.0) e o **OutputDirectory** usando a pasta criada na [etapa anterior,](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)ou seja, **local-nuget-feed**. Por exemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirme** se **o MathFuncs.1.0.0.nupkg** foi criado no diretório **local-nuget-feed.**

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[OPCIONAL] Usando um feed NuGet privado com DevOps Do Zure

Uma técnica mais robusta é descrita em [Get started with NuGet packages in Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), que mostra como criar um feed privado e empurrar o pacote (gerado na etapa anterior) para esse feed.

O ideal é ter esse fluxo de trabalho totalmente automatizado, por exemplo, usando [o Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Para obter mais informações, consulte [Comece com o Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumindo o invólucro .NET de um aplicativo Xamarin.Forms

Para completar o passo a passo, crie um aplicativo **Xamarin.Forms** para consumir o pacote publicado no feed **nuget** local.

### <a name="creating-the-xamarinforms-project"></a>Criando o projeto **Xamarin.Forms**

1. Abra uma nova instância do **Visual Studio para Mac**. Isso pode ser feito a partir do **Terminal:**

    ```bash
    open -n -a "Visual Studio"
    ```

2. No **Visual Studio for Mac,** clique em Novo **Projeto** (da Página de *Boas-Vindas)* ou **Nova Solução** (no menu *Arquivo).*
3. Na janela **Novo Projeto,** escolha o **aplicativo Formulários em Branco** (de dentro do aplicativo *Multiplataforma >*) e clique em **Next**.
4. Atualize os seguintes campos e clique **em Seguir**:

    - **Nome do aplicativo:** MathFuncsApp.
    - **Identificador de organização:** Use um namespace reverso, por exemplo, _com.{your_org}_.
    - **Plataformas-alvo:** Use o padrão (alvos para Android e iOS).
    - **Código compartilhado:** Defina isso como .NET Standard (uma solução "Biblioteca Compartilhada" é possível, mas além do escopo deste passo a passo).

5. Atualize os seguintes campos e clique em **Criar**:

    - **Nome do projeto:** MathFuncsApp.
    - **Nome da solução:** MathFuncsApp.  
    - **Localização:** Use o local de salvamento padrão (ou escolha uma alternativa).

6. No **Solution Explorer**, CONTROL + **CLICK** on the target **(MathFuncsApp.Android** ou **MathFuncs.iOS)** para testes iniciais e, em seguida, escolha **Set As Startup Project**.
7. Escolha o **dispositivo** preferido ou**o Emulador de** **Simulador.**/ 
8. Execute a solução (**COMMAND + RETURN**) para validar que o projeto **Xamarin.Forms** modelado constrói e executa bem. 

    > [!NOTE]
    > **O iOS** (especificamente o simulador) tende a ter o tempo de compilação/implantação mais rápido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Adicionando o feed NuGet local à configuração NuGet

1. No **Visual Studio,** escolha **Preferências** (no menu **Do Visual Studio).**
2. Escolha **Fontes** na seção **NuGet** e clique **em Adicionar**.
3. Atualize os seguintes campos e clique **em Adicionar origem:**

    - **Nome:** Forneça um nome significativo, por exemplo, Pacotes Locais.  
    - **Localização:** Especifique a pasta **local-nuget-feed** criada na [etapa anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > Neste caso, não há necessidade de especificar um **nome de usuário** e **senha**. 

4. Clique em **OK**.

### <a name="referencing-the-package"></a>Referenciando o pacote

Repita as seguintes etapas para cada projeto (**MathFuncsApp,** **MathFuncsApp.Android**e **MathFuncsApp.iOS**).

1. **CONTROL + CLIQUE** no projeto e escolha **Adicionar pacotes NuGet...** no menu **Adicionar.**
2. Procure por **MathFuncs**. 
3. Verifique se a **versão** do pacote é **1.0.0** e os outros detalhes aparecem como esperado, como o **Título** e **a Descrição,** ou seja, *MathFuncs* e *Sample C++ Wrapper Library*. 
4. Selecione o pacote **MathFuncs** e clique **em Adicionar pacote**.

### <a name="using-the-library-functions"></a>Usando as funções da biblioteca

Agora, com uma referência ao pacote **MathFuncs** em cada um dos projetos, as funções estão disponíveis para o código C#.

1. Abra **MainPage.xaml.cs** dentro do projeto **MathFuncsApp** common **Xamarin.Forms**(referenciado por **MathFuncsApp.Android** e **MathFuncsApp.iOS**).
2. Adicionar **usando** instruções para **System.Diagnostics** e **MathFuncs** na parte superior do arquivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare uma instância `MyMathFuncs` da classe no `MainPage` topo da classe:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Anular os `OnAppearing` `OnDisappearing` métodos da `ContentPage` classe base:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Atualize `OnAppearing` o método `myMathFuncs` para inicializar a variável declarada anteriormente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Atualize `OnDisappearing` o método `Dispose` para `myMathFuncs`chamar o método em:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implemente um método privado chamado **TestMathFuncs** da seguinte forma:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Finalmente, `TestMathFuncs` ligue no final `OnAppearing` do método:

    ```csharp
    TestMathFuncs();
    ```

9. Execute o aplicativo em cada plataforma de destino e valide a saída na plataforma **de saída do aplicativo:**

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se você encontrar um '*DLLNotFoundException*' ao testar no Android, ou um erro de compilação no iOS, certifique-se de verificar se a arquitetura da CPU do dispositivo/emulador/simulador que você está usando é compatível com o subconjunto que escolhemos para suportar. 

## <a name="summary"></a>Resumo

Este artigo explicou como criar um aplicativo Xamarin.Forms que usa bibliotecas nativas através de um invólucro .NET comum distribuído através de um pacote NuGet. O exemplo fornecido neste passo a passo é intencionalmente muito simplista para demonstrar mais facilmente a abordagem. Uma aplicação real terá que lidar com complexidades, como o tratamento de exceções, os retornos de chamadas, a marshalling de tipos mais complexos e a vinculação com outras bibliotecas de dependência. Uma consideração fundamental é o processo pelo qual a evolução do código C++ é coordenada e sincronizada com o invólucro e aplicativos clientes. Esse processo pode variar dependendo se uma ou ambas as preocupações são de responsabilidade de uma única equipe. De qualquer forma, a automação é um verdadeiro benefício. Abaixo estão alguns recursos que fornecem mais leitura em torno de alguns dos conceitos-chave, juntamente com os downloads relevantes. 

### <a name="downloads"></a>Downloads

- [Ferramentas de NuGet Command Line (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Exemplos

- [Desenvolvimento móvel multiplataforma hyperlapse com C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ e Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Porto de Amostra Mono San Angeles](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Leitura adicional

[Artigos relacionados com o conteúdo deste post](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)