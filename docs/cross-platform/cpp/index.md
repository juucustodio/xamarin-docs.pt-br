---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Usar bibliotecas de C/C++ com o Xamarin
description: 'O Visual Studio para Mac pode ser usado para criar e integrar o código do C/C++ de plataforma cruzada em aplicativos móveis para Android e iOS, usando o Xamarin e C#. Este artigo explica como configurar e depurar um projeto do C++ em um aplicativo Xamarin.'
author: mikeparker104
ms.author: miparker
ms.date: 12/17/20178
---
# <a name="use-cc-libraries-with-xamarin"></a>Usar bibliotecas de C/C++ com o Xamarin

## <a name="overview"></a>Visão geral

O Xamarin permite que os desenvolvedores criem aplicativos móveis nativos de plataforma cruzada com o Visual Studio. Em geral, C# associações são usadas para expor componentes existentes da plataforma para desenvolvedores. No entanto, há vezes quando necessidade de aplicativos do Xamarin para trabalhar com existente bases de código. Às vezes, as equipes simplesmente não têm o tempo, orçamento ou recursos para a porta de uma grande, bem testado e altamente otimizado base de código para C#.

[Visual C++ para desenvolvimento móvel multiplataforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite que o C/C++ e C# código a ser criado como parte da mesma solução, oferecer muitas vantagens, incluindo uma experiência unificada de depuração. A Microsoft já usou C/C++ e o Xamarin dessa maneira para entregar aplicativos, como [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [Pix câmera](https://www.microsoft.com/microsoftpix).

No entanto, em alguns casos, há um desejo (ou o requisito) para manter ferramentas C/C++ e processos em vigor e para manter o código de biblioteca dissociado do aplicativo, tratando a biblioteca como se fosse semelhante a um componente de terceiros existentes. Nessas situações, o desafio é não apenas expor os membros relevantes para C# , mas o gerenciamento da biblioteca como uma dependência. E, obviamente, automatizar grande parte desse processo possível.  

Esta postagem descreve uma abordagem de alto nível para esse cenário e explica um exemplo simple.

## <a name="background"></a>Informações preliminares

C/C++ é considerada uma linguagem de plataforma cruzada, mas é ótimo deve ter cuidado para garantir que o código-fonte é, de fato, plataforma cruzada, usando apenas C/C++ tem suporte por todos os compiladores de destino e que contém pouca ou nenhuma plataforma incluídas condicionalmente ou código específico do compilador.

Por fim, o código deve compilar e executar com êxito em todas as plataformas de destino, portanto, isso se resume na semelhança entre as plataformas (e compiladores) de destino. Ainda podem surgir problemas de pequenas diferenças entre os compiladores e tão completo de testes (preferencialmente automatizado) em cada destino plataforma se torna cada vez mais importante.  

## <a name="high-level-approach"></a>Abordagem de alto nível

A ilustração a seguir representa a abordagem de quatro estágios usada para transformar o código-fonte C/C++ em uma biblioteca do Xamarin de plataforma cruzada que é compartilhada por meio do NuGet e, em seguida, é consumida em um aplicativo xamarin. Forms.
 

![Abordagem de alto nível para o uso de C/C++ com o Xamarin](images/cpp-steps.jpg)

Os 4 estágios são:

1.  Compilando o código-fonte C/C++ em bibliotecas nativas específicas da plataforma.
2.  Encapsulando as bibliotecas nativas com uma solução do Visual Studio.
3.  Empacotar e enviar por push a um pacote do NuGet para o wrapper do .NET.
4.  Consumindo o pacote NuGet de um aplicativo Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Estágio 1: Compilando o código-fonte C/C++ em bibliotecas nativas específicas da plataforma

O objetivo desse estágio é criar bibliotecas nativas que podem ser chamadas pelo C# wrapper. Isso pode ou não ser relevante, dependendo da sua situação. As ferramentas e processos que podem ser colocados para se ter nesse cenário comum de muitos estão além do escopo deste artigo. Considerações importantes são o C/C++ codebase em sincronia com qualquer código de invólucro nativo, suficiente testes de unidade de manutenção e automação de compilação. 

As bibliotecas no passo a passo foram criadas usando o Visual Studio Code com um script de shell que acompanha este artigo. Uma versão estendida neste passo a passo pode ser encontrada na [repositório GitHub do Mobile CAT](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md) que aborda essa parte do exemplo em maior profundidade. As bibliotecas nativas são tratadas como uma dependência de terceiros nesse caso, no entanto, esse estágio é ilustrado para o contexto.


Para simplificar, o passo a passo destina-se apenas um subconjunto de arquiteturas. Para iOS, ele usa o utilitário lipo para criar uma única fat binários dos binários específicos de arquitetura individuais. Android usará binários dinâmicos com uma extensão. SO e iOS usará uma binário de fat estática com uma extensão de 1).a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Etapa 2: Quebra automática de bibliotecas nativas com uma solução do Visual Studio

A próxima etapa é encapsular as bibliotecas nativas para que eles são usados facilmente pelo .NET. Isso é feito com uma solução do Visual Studio com quatro projetos. Um projeto compartilhado contém o código comum. Projetos direcionados para cada um xamarin. Android, xamarin. IOS e .NET Standard permitem que a biblioteca a ser referenciado de maneira independente de plataforma.

Usa o wrapper[o truque de isca](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/),' descrito por Paul Betts. Isso não é a única maneira, mas ela torna mais fácil de fazer referência à biblioteca e evita a necessidade de gerenciar explicitamente a implementações específicas à plataforma dentro do próprio aplicativo de consumo. O truque essencialmente é garantir que os destinos (.NET Standard, Android, iOS) compartilham o mesmo namespace, o nome do assembly e a estrutura de classe. Uma vez que o NuGet irá sempre preferir uma biblioteca específica da plataforma, a versão do .NET Standard nunca é usada em tempo de execução.

A maioria do trabalho nesta etapa focará usando P/Invoke para chamar os métodos de biblioteca nativa e gerenciar as referências aos objetos subjacentes. O objetivo é expor a funcionalidade da biblioteca para o consumidor ao abstrair qualquer complexidade. Os desenvolvedores de xamarin. Forms não é necessário ter conhecimento prático sobre os funcionamentos internos de biblioteca não gerenciada. Ele deve se sentir como eles estão usando qualquer outro gerenciado C# biblioteca.

Por fim, a saída desse estágio é um conjunto de bibliotecas do .NET, um por um destino, juntamente com um documento de nuspec que contém as informações necessárias a fim de criar o pacote na próxima etapa.

**Estágio 3: Empacotar e enviar um pacote do NuGet para o wrapper do .NET**

A terceira etapa é criar um pacote NuGet usando os artefatos de compilação da etapa anterior. O resultado desta etapa é um pacote NuGet que pode ser consumido de um aplicativo Xamarin. O passo a passo usa um diretório local para servir como o feed do NuGet. Em produção, essa etapa deve publicar um pacote para um público ou privado NuGet feed e deve ser totalmente automatizado.

**Etapa 4: Consumir o pacote NuGet de um aplicativo xamarin. Forms**

A etapa final é referenciar e usar o pacote NuGet de um aplicativo xamarin. Forms. Isso exige a configuração do NuGet feed no Visual Studio para usar o feed definido na etapa anterior.

Depois que o feed for configurado, o pacote precisa ser referenciado em cada projeto no aplicativo xamarin. Forms multiplataforma. 'O truque bait-and-switch' fornece interfaces idênticas, portanto, a funcionalidade de biblioteca nativa pode ser chamada usando o código definido em um único local.

O repositório de código fonte contém um [lista de leitura adicional](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up) que inclui artigos sobre como configurar um feed no DevOps do Azure do NuGet privado e como enviar por push o pacote para o feed. Ao exigir que um pouco mais tempo do programa de instalação que um diretório local, esse tipo de feed é melhor em um ambiente de desenvolvimento de equipe.

## <a name="walk-through"></a>Passo a passo

As etapas fornecidas são específicas para **Visual Studio para Mac**, mas a estrutura funciona em **Visual Studio 2017** também.

### <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, o desenvolvedor precisará de:

-   [Linha de comando (CLI) do NuGet](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Um ativo [**conta de desenvolvedor Apple**](https://developer.apple.com/) é necessário para implantar aplicativos em um iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Criando bibliotecas nativas (estágio 1)

A funcionalidade de biblioteca nativa é baseada no exemplo de [passo a passo: Criando e usando uma biblioteca estática (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Este passo a passo ignora o primeiro estágio, a criação de bibliotecas nativas, já que a biblioteca é fornecida como uma dependência de terceiros nesse cenário. As bibliotecas nativas pré-compiladas estão incluídas junto com o [código de exemplo](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin) ou pode ser [baixado](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) diretamente.

### <a name="working-with-the-native-library"></a>Trabalhando com a biblioteca nativa

O original *MathFuncsLib* exemplo inclui uma única classe denominada MyMathFuncs com a seguinte definição: 

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

Uma classe adicional define as funções de wrapper que permitem que um consumidor do .NET criar, descartar e interagir com a classe MyMathFuncs nativa subjacente.

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

Ele será essas funções de wrapper são usadas na [Xamarin](https://visualstudio.microsoft.com/xamarin/) lado.

## <a name="wrapping-the-native-library-stage-2"></a>Encapsular a biblioteca nativa (estágio 2)

Esse estágio exige a [pré-compilado bibliotecas](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) descrito o [seção anterior](https://docs.microsoft.com/xamarin/cross-platform/cpp/index).

### <a name="creating-the-visual-studio-solution"></a>Criando a solução do Visual Studio

1. No **Visual Studio para Mac**, clique em **novo projeto** (da *página de boas-vindas*) ou **nova solução** (da *arquivo* menu).
2. Dos **novo projeto** janela, escolha **projeto compartilhado** (de dentro *multiplataforma > biblioteca*) e, em seguida, clique em **Avançar**.
3. Atualize os campos a seguir e clique em **criar**:

    - **Nome do projeto:** MathFuncs.Shared  
    - **Nome da solução:** MathFuncs  
    - **Local:** Use o padrão local de salvamento (ou escolha uma alternativa)   
    - **Crie um projeto no diretório da solução:** Defina como verificadas
4. Partir **Gerenciador de soluções**, clique duas vezes no **MathFuncs.Shared** do projeto e navegue até **principais configurações do**.
5. Remover **. Compartilhado** do **Namespace padrão** para que ele é definido como **MathFuncs** somente, em seguida, clique em **Okey**.
6. Abra **MyClass.cs** (criados pelo modelo), em seguida, renomeie a classe e o nome do arquivo para **MyMathFuncsWrapper** e altere o namespace a ser **MathFuncs**.
7. **CTRL + clique** na solução **MathFuncs**, em seguida, escolha **adicionar novo projeto...**  do **Add** menu.
8. Dos **novo projeto** janela, escolha **.NET Standard Library** (de dentro *multiplataforma > biblioteca*) e, em seguida, clique em **Avançar**.
9. Escolher **.NET Standard 2.0** e, em seguida, clique em **próxima**.
10. Atualize os campos a seguir e clique em **criar**:

    - **Nome do projeto:** MathFuncs.Standard  
    - **Local:** Use o mesmo local como o projeto compartilhado de salvamento   

11. Partir **Gerenciador de soluções**, clique duas vezes no **MathFuncs.Standard** projeto.
12. Navegue até **principais configurações do**, em seguida, atualize **Namespace padrão** para **MathFuncs**.
13. Navegue até a **saída** as configurações, em seguida, atualize **nome do Assembly** para **MathFuncs** , em seguida, clique em **Okey**.
14. Navegue até a **compilador** alterar configurações, o **Configuration** para **versão**, definir **informações de depuração** para  **Símbolos somente**.
15. Exclua **Class1.cs/Getting iniciado** do projeto (se um deles tiver sido incluído como parte do modelo).
16. **CTRL + clique** no projeto **dependências/referências** pasta, em seguida, escolha **Editar referências**.
17. Selecione **MathFuncs.Shared** da **projetos** guia e, em seguida, clique em **Okey**.
18. Repita as etapas de 7-17 (ignora etapa 9) usando as seguintes configurações:

    | **NOME DO PROJETO**  | **NOME DO MODELO**   | **MENU NOVO PROJETO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Biblioteca de Classes       | Android > biblioteca      |
    | MathFuncs.iOS     | Biblioteca de vinculação     | iOS > biblioteca          |

19. Da **Gerenciador de soluções**, clique duas vezes no **MathFuncs.Android** do projeto e, em seguida, navegue até o **compilador** configurações.

20. Com o **Configuration** definido como **depurar**, edite **definir símbolos** incluir **Android;**.

21. Alteração a **Configuration** para **versão**, em seguida, edite **definir símbolos** incluir também **Android;**.

22. Repita as etapas de 19 e 20 para **MathFuncs.iOS**, edição **definir símbolos** incluir **iOS;** , em vez de **Android;** em ambos os casos.

23. Compile a solução **Release** configuration (**CONTROL + comando + B**) e valide que todos os assemblies de saída de três (Android, iOS, .NET Standard) (em pastas de bin do projeto respectivos) compartilham o mesmo nome da **MathFuncs.dll**.

Nesse estágio, a solução deve ter três destinos, um relativamente para Android, iOS e .NET Standard e um projeto compartilhado que é referenciado por cada um dos três destinos. Eles devem ser configurados para usar os mesmos assemblies de namespace e a saída padrão com o mesmo nome. Isso é necessário para que a abordagem de 'isco' mencionada anteriormente.

### <a name="adding-the-native-libraries"></a>Adicionar as bibliotecas nativas

O processo de adicionar as bibliotecas nativas para a solução de wrapper varia ligeiramente entre Android e iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Referências nativas para MathFuncs.Android

1. **CTRL + clique** no **MathFuncs.Android** do projeto e, em seguida, escolha **nova pasta** do **Add** menu nomeando-o **libs**.

2. Para cada **ABI** (Interface binária de aplicativo), **Ctrl + clique** no **libs** pasta, em seguida, escolha ** Nova pasta** da **Add** menu, nomeando-o depois que respectivos **ABI**. Nesse caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obter uma visão mais detalhada, consulte o [arquiteturas e CPUs](https://developer.android.com/ndk/guides/arch) tópico do [guia do desenvolvedor do NDK](https://developer.android.com/ndk/guides/), especificamente a seção sobre endereçamento [código nativo em pacotes de aplicativos ](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Verifique se a estrutura de pastas:  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Adicionar o correspondente **. SO** bibliotecas para cada um dos **ABI** pastas com base no seguinte mapeamento:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Para adicionar arquivos, **Ctrl + clique** na pasta que representa os respectivos **ABI**, em seguida, escolha **adicionar arquivos...**  do **Add** menu. Escolha a biblioteca apropriada (da **PrecompiledLibs** diretório), em seguida, clique em **abra** e, em seguida, clique em **Okey** deixando a opção padrão para *cópia a arquivo para o diretório*.

5. Para cada um dos **. SO** arquivos, **Ctrl + clique** , em seguida, escolha o **EmbeddedNativeLibrary** opção o **Build Action** menu.

Agora o **libs** pasta deve aparecer da seguinte maneira:

```bash
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

1. **CTRL + clique** no **MathFuncs.iOS** do projeto e, em seguida, escolha **adicionar referência nativa** do **Add** menu. 
2. Escolha o **libMathFuncs.a** biblioteca (de bibliotecas/ios sob o **PrecompiledLibs** diretório), em seguida, clique em **aberto** 
3. **CTRL + clique** no **libMathFuncs** arquivo (dentro a **referências nativas** pasta, em seguida, escolha o **propriedades** opção do menu  
4. Configurar o **referência nativa** propriedades para que eles são verificados (mostrando um ícone de tique) na **propriedades** painel:
        
    - Forçar carregamento
    - Is C++
    - Smart Link 

    > [!NOTE]
    > Usando um tipo de projeto de biblioteca de ligação junto com um [referência nativa](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpora a biblioteca estática e permite que ele seja automaticamente vinculado ao aplicativo xamarin. IOS que faz referência a ela (mesmo quando ele é incluído por meio de um pacote do NuGet). 

5. Aberto **ApiDefinition.cs**, excluindo com o modelo de código comentado (deixando somente a **MathFuncs** namespace), em seguida, executar a mesma etapa para **Structs.cs** 

    > [!NOTE]
    > Um projeto de biblioteca de associação requer que esses arquivos (com o *ObjCBindingApiDefinition* e *ObjCBindingCoreSource* ações de build) a fim de criar. No entanto, vamos escrever o código, para chamar nossa biblioteca nativa, fora desses arquivos de forma que podem ser compartilhados entre os destinos de biblioteca Android e iOS usando o padrão de P/Invoke.

### <a name="writing-the-managed-library-code"></a>Escrever o código de biblioteca gerenciada

Agora, escrever o C# código para chamar a biblioteca nativa. A meta é ocultar qualquer complexidade subjacente. O consumidor não deve precisar de qualquer conhecimento de trabalho itens internos de biblioteca nativa ou dos conceitos de P/Invoke.  

#### <a name="creating-a-safehandle"></a>Criando um SafeHandle

1. **CTRL + clique** sobre o **MathFuncs.Shared** do projeto e, em seguida, escolha **Add File...**  do **Add** menu. 
2. Escolher **classe vazia** da **novo arquivo** janela, nomeie- **MyMathFuncsSafeHandle** e, em seguida, clique em **novo**
3. Implemente a **MyMathFuncsSafeHandle** classe:

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Um [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) é a maneira preferencial para trabalhar com recursos não gerenciados em código gerenciado. Isso abstrai muito código clichê relacionado ao ciclo de vida objeto e finalização crítico. O proprietário deste identificador, subsequentemente, trate-o como qualquer outro recurso gerenciado e não terá de implementar o completo [padrão descartável](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose). 

#### <a name="creating-the-internal-wrapper-class"></a>Criando a classe wrapper interna

1. Abra **MyMathFuncsWrapper.cs**, alterando-o para uma classe estática interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. No mesmo arquivo, adicione a seguinte instrução condicional à classe:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Isso define a **DllName** valor constante com base em se a biblioteca está sendo compilada **Android** ou **iOS**. Isso é para abordar as convenções de nomenclatura diferentes usadas por cada respectiva plataforma, mas também o tipo de biblioteca que está sendo usado neste caso. Android está usando uma biblioteca dinâmica e então espera que um nome de arquivo incluindo a extensão. Para iOS, '*__Internal*' é necessária, pois estamos usando uma biblioteca estática.

3. Adicione uma referência ao **InteropServices** na parte superior do **MyMathFuncsWrapper.cs** arquivo

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Adicione os métodos de invólucro para lidar com a criação e descarte do **MyMathFuncs** classe:

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Estamos passando nossa constante **DllName** para o **DllImport** atributo junto com o **EntryPoint** explicitamente que informa o tempo de execução do .NET o nome da função a ser chamada dentro dessa biblioteca. Tecnicamente, não precisamos fornecer o **EntryPoint** valor se nossos nomes de método gerenciado foram idênticos àquele não gerenciado. Se nenhum for fornecido, o nome do método gerenciado seria usado como o **EntryPoint** em vez disso. No entanto, é melhor ser explícita.  

5. Adicione os métodos de invólucro para podermos trabalhar com o **MyMathFuncs** classe usando o seguinte código:

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
    > Estamos usando tipos simples para os parâmetros neste exemplo. Como realizar marshaling é uma cópia bit a bit nesse caso, ele não requer trabalho adicional da nossa parte. Além disso, observe o uso do **MyMathFuncsSafeHandle** classe em vez do padrão **IntPtr**. O **IntPtr** é mapeada automaticamente para o **SafeHandle** como parte do processo de empacotamento.

6. Verifique o terminar **MyMathFuncsWrapper** classe aparece como abaixo:

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

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Concluindo a classe MyMathFuncsSafeHandle
1. Abra o **MyMathFuncsSafeHandle** classe, navegue até o espaço reservado **TODO** comentário dentro a **ReleaseHandle** método:
    ```csharp
    // TODO: Release the handle here
    ```
2. Substitua os **TODO** linha:

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escrever a classe MyMathFuncs

Agora que o wrapper estiver concluído, crie uma classe de MyMathFuncs que gerenciará a referência ao objeto MyMathFuncs C++ não gerenciado.  

1. **CTRL + clique** sobre o **MathFuncs.Shared** do projeto e, em seguida, escolha **Add File...**  do **Add** menu. 
2. Escolher **classe vazia** da **novo arquivo** janela, nomeie- **MyMathFuncs** e, em seguida, clique em **novo**
3. Adicione os seguintes membros para o **MyMathFuncs** classe:

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implementar o construtor da classe, de modo que ele cria e armazena um identificador para o nativo **MyMathFuncs** objeto quando a classe é instanciada:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implemente a **IDisposable** interface usando o seguinte código:

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

6. Implemente a **MyMathFuncs** métodos usando o **MyMathFuncsWrapper** classe para executar o trabalho real nos bastidores, passando o ponteiro que podemos ter armazenado para o objeto não gerenciado subjacente. O código deve ser da seguinte maneira:

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

#### <a name="creating-the-nuspec"></a>Criar o nuspec

Para ter a biblioteca empacotados e distribuídos por meio do NuGet, a solução precisa de uma **nuspec** arquivo. Isso será identificar qual assembly resultante será incluído para cada plataforma com suporte.

1.  **CTRL + clique** na solução **MathFuncs**, em seguida, escolha **adicionar pasta de solução** do **adicionar** menu nomeando-o  **SolutionItems**.
2.  **CTRL + clique** na **SolutionItems** pasta, em seguida, escolha **novo arquivo... **  da **Add** menu.
3.  Escolher **arquivo XML vazio** da **novo arquivo** janela, nomeie- **MathFuncs.nuspec** e, em seguida, clique em **Novas**.
4.  Atualização **MathFuncs.nuspec** com os metadados de pacote básico a ser exibida para o **NuGet** consumidor. Por exemplo:


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
    >  Consulte a [referência de nuspec](https://docs.microsoft.com/nuget/reference/nuspec) documento para obter detalhes adicionais sobre o esquema usado para esse manifesto.

5. Adicionar um `<files>` elemento como um filho de `<package>` elemento (logo abaixo `<metadata>`), identificando cada arquivo com um separado `<file>` elemento:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando um pacote é instalado em um projeto e em que existem em vários assemblies especificados com o mesmo nome, o NuGet efetivamente escolherá o assembly que é o mais específico para determinada plataforma.

6. Adicione a `<file>` elementos para o **Android** assemblies:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Adicione a `<file>` elementos para o **iOS** assemblies:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Adicione a `<file>` elementos para o **netstandard2.0** assemblies:

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verifique se o **nuspec** manifesto:

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
    > Esse arquivo Especifica os caminhos de saída do assembly de um **Release** build, portanto, certifique-se de criar a solução usando essa configuração.

Neste ponto, a solução contém 3 assemblies do .NET e dar suporte a **nuspec** de manifesto.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuindo o wrapper do .NET com o NuGet

A próxima etapa é empacotar e distribuir o pacote do NuGet para que ele pode ser facilmente consumido pelo aplicativo e gerenciado como uma dependência. O encapsulamento e o consumo poderiam tudo feitos em uma única solução, mas distribuindo a biblioteca por meio do NuGet ajuda a dissociação e permite gerenciar essas bases de código independentemente.

### <a name="preparing-a-local-packages-directory"></a>Preparando um diretório local de pacotes

A forma mais simples de feed do NuGet é um diretório local:

1.  Na **Finder**, navegue até um diretório conveniente. Por exemplo, **/Users**.
2.  Escolher **nova pasta** da **arquivo** menu, fornecer um nome significativo, como **feed do nuget de local**.

### <a name="creating-the-package"></a>Criação do pacote

1.  Defina as **configuração de Build** para **versão**e executar uma compilação usando **COMMAND + B**.
2.  Abra **Terminal** e altere o diretório para a pasta que contém o **nuspec** arquivo.
3.  Na **Terminal**, execute o **pacote do nuget** comando especificando o **nuspec** arquivo, o **versão**  (por exemplo, 1.0.0) e o **OutputDirectory** usando a pasta criada no [etapa anterior](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), ou seja, ** feed do nuget de local**. Por exemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirme** que **MathFuncs.1.0.0.nupkg** foi criado na **feed do nuget de local** directory.

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[OPCIONAL] Feed do NuGet usando uma privada com DevOps do Azure

Uma técnica mais robusta é descrita em [começar com pacotes do NuGet em DevOps do Azure](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), que mostra como criar um feed privado e enviar por push o pacote (gerado na etapa anterior) para o feed.

Ele é ideal para ter esse fluxo de trabalho totalmente automatizado, por exemplo, usando [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Para obter mais informações, consulte [começar com Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumindo o wrapper do .NET de um aplicativo xamarin. Forms
Para concluir o passo a passo, crie uma **xamarin. Forms** publicados do aplicativo para consumir o pacote apenas local **NuGet** feed.

### <a name="creating-the-xamarinforms-project"></a>Criando o **xamarin. Forms** projeto

1. Abra uma nova instância da **Visual Studio para Mac**. Isso pode ser feito a partir **Terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. No **Visual Studio para Mac**, clique em **novo projeto** (da *página de boas-vindas*) ou **nova solução** (da *arquivo* menu).
3. Dos **novo projeto** janela, escolha **aplicativo de formulários em branco** (de dentro *multiplataforma > aplicativo*) e, em seguida, clique em **Avançar**.
4. Atualize os campos a seguir e clique em **próxima**:

    - **Nome do aplicativo:** MathFuncsApp.
    - **Identificador de organização:** Usar um namespace inverso, por exemplo, _com. {your_org}_.
    - **Plataformas de destino:** Use o padrão (destinos Android e iOS).
    - **Código compartilhado:** Defina isso para o .NET Standard (uma solução de "Biblioteca compartilhada" é possível, mas está além do escopo deste passo a passo).

5. Atualize os campos a seguir e clique em **criar**:

    - **Nome do projeto:** MathFuncsApp.
    - **Nome da solução:** MathFuncsApp.  
    - **Local:** Use o padrão local de salvamento (ou escolha uma alternativa).

6. Na **Gerenciador de soluções**, **Ctrl + clique** no destino (**MathFuncsApp.Android** ou **MathFuncs.iOS**) para o teste inicial, em seguida, escolher **definir como projeto de inicialização**.
7. Escolha a preferência **dispositivo** ou **simulador**/**emulador**. 
8. Executar a solução (**comando + RETURN**) para validar que o modelo **xamarin. Forms** projeto compilado e executado okey. 

    > [!NOTE]
    > **iOS** (especificamente o simulador) tende a ter a hora de criação/implantação mais rápida.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Adição do NuGet local de feed na configuração do NuGet

1. Na **Visual Studio**, escolha **preferências** (da **Visual Studio** menu).
2. Escolher **fontes** de sob o **NuGet** seção e, em seguida, clique em **adicionar**.
3. Atualize os campos a seguir e clique em **adicionar fonte**:

    - **Nome:** Forneça um nome significativo, por exemplo, pacotes de Local.  
    - **Local:** Especifique o **feed do nuget de local** pasta criada na [etapa anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > Nesse caso, não é necessário especificar uma **nome de usuário** e **senha**. 

4. Clique em **OK**.

### <a name="referencing-the-package"></a>Referenciando o pacote

Repita as etapas a seguir para cada projeto (**MathFuncsApp**, **MathFuncsApp.Android**, e **MathFuncsApp.iOS**).

1. **CTRL + clique** no projeto, em seguida, escolha **adicionar pacotes NuGet...**  do **Add** menu.
2. Pesquise **MathFuncs**. 
3. Verifique se o **versão** do pacote é **1.0.0** e outros detalhes aparecem conforme o esperado, como o **título** e **descrição**, que é , *MathFuncs* e *biblioteca Wrapper de C++ de exemplo*. 
4. Selecione o **MathFuncs** de pacote e, em seguida, clique em **Adicionar pacote**.

### <a name="using-the-library-functions"></a>Usando as funções de biblioteca

Agora, com uma referência para o **MathFuncs** pacote em cada um dos projetos, as funções estão disponíveis para o C# código.

1.  Abra **MainPage.xaml.cs** de dentro de **MathFuncsApp** comuns **xamarin. Forms**projeto (referenciada por ambos os **MathFuncsApp.Android** e **MathFuncsApp.iOS**).
2.  Adicione **usando** instruções para **System. Diagnostics** e **MathFuncs** na parte superior do arquivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare uma instância das `MyMathFuncs` classe na parte superior do `MainPage` classe:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Substituir a `OnAppearing` e `OnDisappearing` métodos a partir de `ContentPage` classe base:

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

5. Atualizar o `OnAppearing` método para inicializar o `myMathFuncs` variável declarada anteriormente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Atualizar o `OnDisappearing` método para chamar o `Dispose` método em `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implementar um método particular chamado **TestMathFuncs** da seguinte maneira:

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

8. Por fim, chame `TestMathFuncs` no final o `OnAppearing` método:

    ```csharp
    TestMathFuncs();
    ```

9. Execute o aplicativo em cada plataforma de destino e validar a saída a **saída do aplicativo** painel aparece da seguinte maneira:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se você encontrar um '*DLLNotFoundException*' quando um teste no Android ou um erro de compilação no iOS, não se esqueça de verificar que a arquitetura de CPU do dispositivo/emulador/simulador você está usando é compatível com o subconjunto que escolhemos para suporte. 

## <a name="summary"></a>Resumo

Este artigo explicou como criar um aplicativo xamarin. Forms que usa as bibliotecas nativas por meio de um wrapper .NET comuns distribuídos por meio de um pacote do NuGet. O exemplo fornecido neste passo a passo é intencionalmente muito simples para demonstrar mais facilmente a abordagem. Um aplicativo real, terá de lidar com as complexidades como a manipulação de exceção, os retornos de chamada, o marshalling dos tipos mais complexos e vinculação com outras bibliotecas de dependência. Uma consideração importante é o processo pelo qual a evolução do código C++ é coordenada e sincronizada com os aplicativos cliente e o wrapper. Esse processo pode variar dependendo se um ou ambos com essas preocupações estão a responsabilidade de uma única equipe. De qualquer forma, a automação é um benefício real. Abaixo estão alguns recursos fornecendo leitura adicional em torno de alguns dos principais conceitos, juntamente com os downloads relevantes. 

### <a name="downloads"></a>Downloads

- [Ferramentas de linha de comando (CLI do NuGet)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Exemplos

- [Desenvolvimento móvel de plataforma cruzada de Hyperlapse com C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ e Xamarin)](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Porta de exemplo do mono San Angeles](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>Leitura adicional

[Artigos relacionados ao conteúdo desta postagem](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
