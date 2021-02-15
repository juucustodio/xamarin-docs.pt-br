---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Usar bibliotecas C/C++ com o Xamarin
description: Visual Studio para Mac pode ser usado para criar e integrar código C/C++ entre plataformas em aplicativos móveis para Android e iOS, usando o Xamarin e o C#. Este artigo explica como configurar e depurar um projeto C++ em um aplicativo Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 8422affab86ea176cad4e57833188dcd5738a99a
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928497"
---
# <a name="use-cc-libraries-with-xamarin"></a>Usar bibliotecas C/C++ com o Xamarin

## <a name="overview"></a>Visão geral

O Xamarin permite que os desenvolvedores criem aplicativos móveis nativos de plataforma cruzada com o Visual Studio. Em geral, as associações do C# são usadas para expor componentes de plataforma existentes aos desenvolvedores. No entanto, há ocasiões em que os aplicativos Xamarin precisam trabalhar com bases de código existentes. Às vezes, as equipes simplesmente não têm tempo, orçamento ou recursos para portar uma base de código grande, bem testada e altamente otimizada para C#.

[Visual C++ para o desenvolvimento móvel de plataforma cruzada](/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite que o código C/C++ e C# seja criado como parte da mesma solução, oferecendo muitas vantagens, incluindo uma experiência de depuração unificada. A Microsoft usou o C/C++ e o Xamarin desta forma para fornecer aplicativos, como [hiperlapsar câmera móvel](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [PIX](https://www.microsoft.com/microsoftpix).

No entanto, em alguns casos, há um desejo (ou requisito) para manter as ferramentas e os processos existentes do C/C++ em vigor e manter o código da biblioteca dissociado do aplicativo, tratando a biblioteca como se fosse semelhante a um componente de terceiros. Nessas situações, o desafio não está apenas expondo os membros relevantes para o C#, mas Gerenciando a biblioteca como uma dependência. E, é claro, automatizar o máximo possível do processo.  

Esta postagem descreve uma abordagem de alto nível para esse cenário e percorre um exemplo simples.

## <a name="background"></a>Segundo plano

O c/C++ é considerado uma linguagem de plataforma cruzada, mas deve-se tomar muito cuidado para garantir que o código-fonte seja realmente entre plataformas, usando apenas o C/C++ com suporte de todos os compiladores de destino e que contenham pouca ou nenhuma plataforma ou código específico do compilador.

Em última análise, o código deve ser compilado e executado com êxito em todas as plataformas de destino, portanto, isso se resume à semelhança entre as plataformas (e os compiladores) que estão sendo direcionados. Os problemas ainda podem surgir de pequenas diferenças entre compiladores e, portanto, testes completos (preferencialmente automatizado) em cada plataforma de destino se tornam cada vez mais importantes.  

## <a name="high-level-approach"></a>Abordagem de alto nível

A ilustração a seguir representa a abordagem de quatro estágios usada para transformar o código-fonte C/C++ em uma biblioteca Xamarin de plataforma cruzada que é compartilhada via NuGet e, em seguida, é consumida em um aplicativo Xamarin. Forms.

![Abordagem de alto nível para usar C/C++ com o Xamarin](images/cpp-steps.jpg)

Os quatro estágios são:

1. Compilando o código-fonte C/C++ em bibliotecas nativas específicas da plataforma.
2. Encapsulando as bibliotecas nativas com uma solução do Visual Studio.
3. Empacotando e enviando por push um pacote NuGet para o wrapper .NET.
4. Consumindo o pacote NuGet de um aplicativo Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Estágio 1: Compilando o código-fonte C/C++ em bibliotecas nativas específicas da plataforma

O objetivo deste estágio é criar bibliotecas nativas que podem ser chamadas pelo wrapper do C#. Isso pode ou não ser relevante, dependendo da sua situação. As várias ferramentas e processos que podem ser trazidos neste cenário comum estão além do escopo deste artigo. As principais considerações são manter a base de código C/C++ sincronizada com qualquer código wrapper nativo, teste de unidade suficiente e automação de compilação.

As bibliotecas no passo a passo foram criadas usando Visual Studio Code com um script de shell que o acompanha. Uma versão estendida desse passo a passo pode ser encontrada no [repositório GitHub de Cat móvel](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) que aborda essa parte do exemplo em maior profundidade. As bibliotecas nativas estão sendo tratadas como uma dependência de terceiros nesse caso, no entanto, esse estágio é ilustrado para o contexto.

Para simplificar, a instrução tem como alvo apenas um subconjunto de arquiteturas. Para o iOS, ele usa o utilitário lipo para criar um binário único de Fat a partir dos binários específicos da arquitetura individual. O Android usará binários dinâmicos com um. portanto, a extensão e o iOS usarão um binário Fat estático com uma extensão. a.

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Etapa 2: encapsulando as bibliotecas nativas com uma solução do Visual Studio

O próximo estágio é encapsular as bibliotecas nativas para que elas sejam facilmente usadas no .NET. Isso é feito com uma solução do Visual Studio com quatro projetos. Um projeto compartilhado contém o código comum. Os projetos destinados a cada Xamarin. Android, Xamarin. iOS e .NET Standard permitem que a biblioteca seja referenciada de maneira independente da plataforma.

O wrapper usa '[o Bait e o truque do comutador](https://github.com/JFMG/Bait-and-Switch-PCL-example)'. Essa não é a única maneira, mas facilita a referência à biblioteca e evita a necessidade de gerenciar explicitamente as implementações específicas da plataforma no próprio aplicativo de consumo. O truque é, essencialmente, garantir que os destinos (.NET Standard, Android, iOS) compartilhem o mesmo namespace, nome do assembly e estrutura de classe. Como o NuGet sempre prefere uma biblioteca específica da plataforma, a versão .NET Standard nunca é usada em tempo de execução.

A maior parte do trabalho nesta etapa se concentrará no uso de P/Invoke para chamar os métodos de biblioteca nativos e gerenciar as referências aos objetos subjacentes. O objetivo é expor a funcionalidade da biblioteca para o consumidor e, ao mesmo tempo, abstrair qualquer complexidade. Os desenvolvedores do Xamarin. Forms não precisam ter conhecimento prático sobre o funcionamento interno da biblioteca não gerenciada. Deve parecer que estão usando qualquer outra biblioteca C# gerenciada.

Por fim, a saída desse estágio é um conjunto de bibliotecas .NET, uma por destino, junto com um documento nuspec que contém as informações necessárias para criar o pacote na próxima etapa.

**Estágio 3: empacotando e enviando por push um pacote NuGet para o wrapper .NET**

O terceiro estágio é criar um pacote NuGet usando os artefatos de compilação da etapa anterior. O resultado desta etapa é um pacote NuGet que pode ser consumido de um aplicativo Xamarin. A instrução usa um diretório local para servir como o feed do NuGet. Em produção, essa etapa deve publicar um pacote em um feed do NuGet público ou privado e deve ser totalmente automatizada.

**Estágio 4: consumindo o pacote NuGet de um aplicativo Xamarin. Forms**

A etapa final é fazer referência e usar o pacote NuGet de um aplicativo Xamarin. Forms. Isso requer a configuração do feed do NuGet no Visual Studio para usar o feed definido na etapa anterior.

Depois que o feed é configurado, o pacote precisa ser referenciado de cada projeto no aplicativo Xamarin. Forms de plataforma cruzada. ' O truque bait-and-switch fornece interfaces idênticas, portanto, a funcionalidade da biblioteca nativa pode ser chamada usando o código definido em um único local.

O repositório de código-fonte contém uma [lista de leituras adicionais](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) que inclui artigos sobre como configurar um feed do NuGet privado no Azure DevOps e como enviar por push o pacote para esse feed. Embora exijam um pouco mais de tempo de configuração do que um diretório local, esse tipo de feed é melhor em um ambiente de desenvolvimento de equipe.

## <a name="walk-through"></a>Passo a passo

As etapas fornecidas são específicas para **Visual Studio para Mac** , mas a estrutura funciona no **Visual Studio 2017** também.

### <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, o desenvolvedor precisará de:

- [Linha de comando do NuGet (CLI)](/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Uma [**conta de desenvolvedor Apple**](https://developer.apple.com/) ativa é necessária para implantar aplicativos em um iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Criando as bibliotecas nativas (estágio 1)

A funcionalidade da biblioteca nativa se baseia no exemplo de [passo a passo: Criando e usando uma biblioteca estática (C++)](/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Este passo a passos ignora o primeiro estágio, criando as bibliotecas nativas, pois a biblioteca é fornecida como uma dependência de terceiros nesse cenário. As bibliotecas nativas pré-compiladas são incluídas juntamente com o [código de exemplo](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) ou podem ser [baixadas](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) diretamente.

### <a name="working-with-the-native-library"></a>Trabalhando com a biblioteca nativa

O exemplo *MathFuncsLib* original inclui uma única classe chamada `MyMathFuncs` com a seguinte definição:

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

Uma classe adicional define funções de wrapper que permitem a um consumidor .NET criar, descartar e interagir com a `MyMathFuncs` classe nativa subjacente.

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

Serão essas funções de invólucro que são usadas no lado do [Xamarin](https://visualstudio.microsoft.com/xamarin/) .

## <a name="wrapping-the-native-library-stage-2"></a>Encapsulando a biblioteca nativa (estágio 2)

Este estágio requer as [bibliotecas pré-compiladas](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) descritas na [seção anterior](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Criando a solução do Visual Studio

1. Em **Visual Studio para Mac** , clique em **novo projeto** (na *página de boas-vindas* ) ou em **nova solução** (no menu *arquivo* ).
2. Na janela **novo projeto** , escolha **projeto compartilhado** (de dentro da *biblioteca de > multiplataforma* ) e clique em **Avançar** .
3. Atualize os campos a seguir e clique em **criar** :

    - **Nome do projeto:** MathFuncs. Shared  
    - **Nome da solução:** MathFuncs  
    - **Local:** Usar o local de salvamento padrão (ou escolher uma alternativa)   
    - **Crie um projeto dentro do diretório da solução:** Defina esta opção como marcada
4. Em **Gerenciador de soluções** , clique duas vezes no projeto **MathFuncs. Shared** e navegue até **configurações principais** .
5. Remover **. Compartilhado** do **namespace padrão** para que ele seja definido somente como **MathFuncs** e, em seguida, clique em **OK** .
6. Abra **MyClass.cs** (criado pelo modelo), em seguida, renomeie a classe e o nome de arquivo como **MyMathFuncsWrapper** e altere o namespace para **MathFuncs** .
7. **Controle + clique** na solução **MathFuncs** , em seguida, escolha **Adicionar novo projeto...** no menu **Adicionar** .
8. Na janela **novo projeto** , escolha **.net Standard biblioteca** (de dentro da *biblioteca > multiplataforma* ) e clique em **Avançar** .
9. Escolha **.NET Standard 2,0** e clique em **Avançar** .
10. Atualize os campos a seguir e clique em **criar** :

    - **Nome do projeto:** MathFuncs. Standard  
    - **Local:** Usar o mesmo local de salvamento que o projeto compartilhado   

11. Em **Gerenciador de soluções** , clique duas vezes no projeto **MathFuncs. Standard** .
12. Navegue até **configurações principais** e, em seguida, atualize o **namespace padrão** para **MathFuncs** .
13. Navegue até as configurações de **saída** e, em seguida, atualize o **nome do assembly** para **MathFuncs** .
14. Navegue até as configurações do **compilador** , altere **a configuração** para **liberar** , definindo **informações de depuração** **somente para símbolos** e clique em **OK** .
15. Exclua o **Class1.cs/Getting iniciado** do projeto (se um deles tiver sido incluído como parte do modelo).
16. **Controle + clique** na pasta **dependências/referências** do projeto e escolha **Editar referências** .
17. Selecione **MathFuncs. Shared** na guia **projetos** e clique em **OK** .
18. Repita as etapas 7-17 (ignorando a etapa 9) usando as seguintes configurações:

    | **NOME DO PROJETO**  | **NOME DO MODELO**   | **MENU NOVO PROJETO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs. Android | Biblioteca de Classes       | Biblioteca de > do Android      |
    | MathFuncs. iOS     | Biblioteca de associação     | Biblioteca de > do iOS          |

19. Em **Gerenciador de soluções** , clique duas vezes no projeto **MathFuncs. Android** e navegue até as configurações do **compilador** .

20. Com a **configuração** definida como **depurar** , edite **definir símbolos** para incluir **Android;** .

21. Altere a **configuração** para **liberação** e edite **definir símbolos** para incluir também **Android;** .

22. Repita as etapas de 19-20 para **MathFuncs. Ios** , editando os **símbolos** para incluir o **Ios;** em vez do **Android;** em ambos os casos.

23. Compile a solução na configuração de **versão** ( **Control + Command + B** ) e valide que todos os três assemblies de saída (Android, Ios, .net Standard) (nas respectivas pastas bin do projeto) compartilham o mesmo nome **MathFuncs.dll** .

Neste estágio, a solução deve ter três destinos, um cada para Android, iOS e .NET Standard e um projeto compartilhado que é referenciado por cada um dos três destinos. Eles devem ser configurados para usar o mesmo namespace padrão e assemblies de saída com o mesmo nome. Isso é necessário para a abordagem ' Bait e switch ' mencionada anteriormente.

### <a name="adding-the-native-libraries"></a>Adicionando as bibliotecas nativas

O processo de adicionar as bibliotecas nativas à solução de wrapper varia ligeiramente entre o Android e o iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Referências nativas para MathFuncs. Android

1. **Controle + clique** no projeto **MathFuncs. Android** e, em seguida, escolha **nova pasta** no menu **Adicionar** nomeando-o **bibliotecas** .

2. Para cada **Abi** (interface binária de aplicativo), **controle + clique** na pasta **bibliotecas** , em seguida, escolha **nova pasta** no menu **Adicionar** , nomeando-a depois dessa respectiva **Abi** . Nesse caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obter uma visão geral mais detalhada, consulte o tópico [arquiteturas e CPUs](https://developer.android.com/ndk/guides/arch) do [Guia do desenvolvedor NDK](https://developer.android.com/ndk/guides/), especificamente a seção sobre como endereçar [código nativo em pacotes de aplicativos](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Verifique a estrutura de pastas:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Adicione as bibliotecas **. so** correspondentes a cada uma das pastas da **Abi** com base no seguinte mapeamento:

    **arm64-V8A:** bibliotecas/Android/arm64

    **ARMEABI-v7a:** bibliotecas/Android/ARM  

    **x86:** bibliotecas/Android/x86

    **x86_64:** bibliotecas/Android/x86_64

    > [!NOTE]
    > Para adicionar arquivos, **controle + clique** na pasta que representa o respectivo **Abi** e, em seguida, escolha **Adicionar arquivos...** no menu **Adicionar** . Escolha a biblioteca apropriada (no diretório **PrecompiledLibs** ) e clique em **abrir** e, em seguida, clique em **OK** deixando a opção padrão para *copiar o arquivo para o diretório* .

5. Para cada um dos arquivos **. so** , **controle + clique** e, em seguida, escolha a opção **EmbeddedNativeLibrary** no menu de **ação de Build** .

Agora, a pasta **bibliotecas** deve aparecer da seguinte maneira:

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

#### <a name="native-references-for-mathfuncsios"></a>Referências nativas para MathFuncs. iOS

1. **Controle + clique** no projeto **MathFuncs. Ios** e escolha **Adicionar referência nativa** no menu **Adicionar** .
2. Escolha a biblioteca **libMathFuncs.** a (de bibliotecas/Ios no diretório **PrecompiledLibs** ) e clique em **abrir**
3. **Control + clique** no arquivo **libMathFuncs** (dentro da pasta **referências nativas** e, em seguida, escolha a opção **Propriedades** no menu  
4. Configure as propriedades de **referência nativa** para que elas sejam marcadas (mostrando um ícone de tique) no painel de **Propriedades** :

    - Forçar carga
    - É C++
    - Smart link

    > [!NOTE]
    > Usar um tipo de projeto de biblioteca de associação junto com uma [referência nativa](../macios/native-references.md) incorpora a biblioteca estática e permite que ela seja automaticamente vinculada ao aplicativo Xamarin. Ios que faz referência a ela (mesmo quando ela é incluída por meio de um pacote NuGet).

5. Abra **ApiDefinition.cs** , excluindo o código comentado do modelo (deixando apenas o `MathFuncs` namespace) e, em seguida, execute a mesma etapa para **structs.cs**

    > [!NOTE]
    > Um projeto de biblioteca de associação requer esses arquivos (com as ações de compilação *ObjCBindingApiDefinition* e *ObjCBindingCoreSource* ) para compilar. No entanto, escreveremos o código para chamar nossa biblioteca nativa, fora desses arquivos de uma maneira que possa ser compartilhada entre destinos de biblioteca do Android e iOS usando P/Invoke padrão.

### <a name="writing-the-managed-library-code"></a>Gravando o código da biblioteca gerenciada

Agora, escreva o código C# para chamar a biblioteca nativa. O objetivo é ocultar qualquer complexidade subjacente. O consumidor não deve precisar de nenhum conhecimento funcional dos conceitos internos da biblioteca nativa nem dos principais aspectos do P/Invoke.  

#### <a name="creating-a-safehandle"></a>Criando um SafeHandle

1. **Controle + clique** no projeto **MathFuncs. Shared** e, em seguida, escolha **Adicionar arquivo...** no menu **Adicionar** .
2. Escolha **classe vazia** na janela **novo arquivo** , nomeie-a **MyMathFuncsSafeHandle** e clique em **novo**
3. Implemente a classe **MyMathFuncsSafeHandle** :

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
    > Um [SafeHandle](/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) é a maneira preferida de trabalhar com recursos não gerenciados em código gerenciado. Isso abstrai muitos códigos clichês relacionados à finalização crítica e ao ciclo de vida do objeto. O proprietário desse identificador pode tratá-lo posteriormente como qualquer outro recurso gerenciado e não precisará implementar o [padrão descartável](/dotnet/standard/garbage-collection/implementing-dispose)completo.

#### <a name="creating-the-internal-wrapper-class"></a>Criando a classe wrapper interna

1. Abrir **MyMathFuncsWrapper.cs** , alterá-lo para uma classe estática interna

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
    > Isso define o valor constante **DllName** com base em se a biblioteca está sendo compilada para **Android** ou **Ios** . Isso é para resolver as diferentes convenções de nomenclatura usadas por cada plataforma respectiva, mas também o tipo de biblioteca que está sendo usado nesse caso. O Android está usando uma biblioteca dinâmica e, portanto, espera um nome de arquivo, incluindo a extensão. Para iOS, ' *__Internal* ' é necessário porque estamos usando uma biblioteca estática.

3. Adicione uma referência a **System. Runtime. InteropServices** na parte superior do arquivo **MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Adicione os métodos de wrapper para manipular a criação e a alienação da classe **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Estamos passando nossa constante **DllName** para o atributo **DllImport** junto com o **EntryPoint** , que informa explicitamente ao tempo de execução do .net o nome da função a ser chamada dentro dessa biblioteca. Tecnicamente, não precisamos fornecer o valor de **EntryPoint** se nossos nomes de método gerenciado fossem idênticos ao não gerenciado. Se um não for fornecido, o nome do método gerenciado será usado como o **EntryPoint** em vez disso. No entanto, é melhor ser explícito.  

5. Adicione os métodos de wrapper para permitir que possamos trabalhar com a classe **MyMathFuncs** usando o seguinte código:

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
    > Estamos usando tipos simples para os parâmetros neste exemplo. Como o Marshalling é uma cópia de bit que não requer nenhum trabalho adicional em nossa parte. Observe também o uso da classe **MyMathFuncsSafeHandle** em vez do **IntPtr** padrão. O **IntPtr** é mapeado automaticamente para o **SafeHandle** como parte do processo de Marshalling.

6. Verifique se a classe **MyMathFuncsWrapper** concluída aparece como abaixo:

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

1. Abra a classe **MyMathFuncsSafeHandle** , navegue até o comentário de **todo** o espaço reservado no método **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Substitua a linha **todo** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escrevendo a classe MyMathFuncs

Agora que o wrapper foi concluído, crie uma classe MyMathFuncs que gerenciará a referência ao objeto C++ MyMathFuncs não gerenciado.  

1. **Controle + clique** no projeto **MathFuncs. Shared** e, em seguida, escolha **Adicionar arquivo...** no menu **Adicionar** .
2. Escolha **classe vazia** na janela **novo arquivo** , nomeie-a **MyMathFuncs** e clique em **novo**
3. Adicione os seguintes membros à classe **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implemente o construtor para a classe para que ele crie e armazene um identificador para o objeto **MyMathFuncs** nativo quando a classe for instanciada:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implemente a interface **IDisposable** usando o seguinte código:

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

6. Implemente os métodos **MyMathFuncs** usando a classe **MyMathFuncsWrapper** para executar o trabalho real nos bastidores passando o ponteiro que armazenamos no objeto não gerenciado subjacente. O código deve ser o seguinte:

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

#### <a name="creating-the-nuspec"></a>Criando o nuspec

Para que a biblioteca seja empacotada e distribuída via NuGet, a solução precisa de um arquivo **nuspec** . Isso identificará quais dos assemblies resultantes serão incluídos para cada plataforma com suporte.

1. **Control + clique** na solução **MathFuncs** , em seguida, escolha **Adicionar pasta de solução** no menu **Adicionar** nomeando **SolutionItems** .
2. **Controle + clique** na pasta **SolutionItems** e escolha **novo arquivo...** no menu **Adicionar** .
3. Escolha **arquivo XML vazio** na janela **novo arquivo** , nomeie-o **MathFuncs. nuspec** e clique em **novo** .
4. Atualize **MathFuncs. nuspec** com os metadados básicos do pacote a serem exibidos para o consumidor do **NuGet** . Por exemplo:

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
    > Consulte o documento de [referência do nuspec](/nuget/reference/nuspec) para obter mais detalhes sobre o esquema usado para esse manifesto.

5. Adicione um `<files>` elemento como um filho do `<package>` elemento (logo abaixo `<metadata>` ), identificando cada arquivo com um `<file>` elemento separado:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando um pacote é instalado em um projeto e onde há vários assemblies especificados pelo mesmo nome, o NuGet escolherá efetivamente o assembly mais específico para a plataforma fornecida.

6. Adicione os `<file>` elementos para os assemblies do **Android** :

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Adicione os `<file>` elementos para os assemblies do **Ios** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Adicione os `<file>` elementos para os assemblies do **netstandard 2.0** :

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verifique o manifesto **nuspec** :

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
    > Esse arquivo especifica os caminhos de saída do assembly de uma compilação de **versão** , portanto, certifique-se de criar a solução usando essa configuração.

Neste ponto, a solução contém 3 assemblies .NET e um manifesto **nuspec** de suporte.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuindo o wrapper do .NET com o NuGet

A próxima etapa é empacotar e distribuir o pacote NuGet para que ele possa ser facilmente consumido pelo aplicativo e gerenciado como uma dependência. A disposição e o consumo podem ser feitos em uma única solução, mas distribuir a biblioteca por meio de auxílios do NuGet na dissociação e nos permite gerenciar essas bases de código de forma independente.

### <a name="preparing-a-local-packages-directory"></a>Preparando um diretório de pacotes locais

A forma mais simples de feed do NuGet é um diretório local:

1. No **Finder** , navegue até um diretório conveniente. Por exemplo, **/Users** .
2. Escolha **nova pasta** no menu **arquivo** , fornecendo um nome significativo, como **local-NuGet-feed** .

### <a name="creating-the-package"></a>Como criar o pacote

1. Defina a **configuração de compilação** como **liberar** e execute uma compilação usando o **comando + B** .
2. Abra o **terminal** e altere o diretório para a pasta que contém o arquivo **nuspec** .
3. No **terminal** , execute o comando do **pacote NuGet** especificando o arquivo **nuspec** , a **versão** (por exemplo, 1.0.0) e o **OutputDirectory** usando a pasta criada na [etapa anterior](#preparing-a-local-packages-directory), ou seja, **local-NuGet-feed** . Por exemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirme** se **MathFuncs. 1.0.0. nupkg** foi criado no diretório **local-NuGet-feed** .

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>ADICIONAL Usando um feed do NuGet privado com o Azure DevOps

Uma técnica mais robusta é descrita em introdução [aos pacotes NuGet no Azure DevOps](/azure/devops/artifacts/get-started-nuget?tabs=new-nav&view=vsts#publish-a-package), que mostra como criar um feed privado e enviar por push o pacote (gerado na etapa anterior) para esse feed.

É ideal que esse fluxo de trabalho seja totalmente automatizado, por exemplo, usando [Azure pipelines](/azure/devops/pipelines/index?view=vsts). Para obter mais informações, consulte Introdução [ao Azure pipelines](/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumindo o wrapper .NET de um aplicativo Xamarin. Forms

Para concluir o passo a passos, crie um aplicativo **Xamarin. Forms** para consumir o pacote que acabou de ser publicado no feed do **NuGet** local.

### <a name="creating-the-xamarinforms-project"></a>Criando o projeto **Xamarin. Forms**

1. Abra uma nova instância do **Visual Studio para Mac** . Isso pode ser feito no **terminal** :

    ```bash
    open -n -a "Visual Studio"
    ```

2. Em **Visual Studio para Mac** , clique em **novo projeto** (na *página de boas-vindas* ) ou em **nova solução** (no menu *arquivo* ).
3. Na janela **novo projeto** , escolha **aplicativo de formulários em branco** (de dentro de *multiplataforma > aplicativo* ) e clique em **Avançar** .
4. Atualize os campos a seguir e clique em **Avançar** :

    - **Nome do aplicativo:** MathFuncsApp.
    - **Identificador da organização:** Use um namespace reverso, por exemplo, _com. {your_org}_ .
    - **Plataformas de destino:** Use o padrão (destinos Android e iOS).
    - **Código compartilhado:** Defina isso como .NET Standard (uma solução de "biblioteca compartilhada" é possível, mas além do escopo deste passo a passos).

5. Atualize os campos a seguir e clique em **criar** :

    - **Nome do projeto:** MathFuncsApp.
    - **Nome da solução:** MathFuncsApp.  
    - **Local:** Use o local de salvamento padrão (ou escolha uma alternativa).

6. Em **Gerenciador de soluções** , **controle + clique** no destino ( **MathFuncsApp. Android** ou **MathFuncs. Ios** ) para teste inicial e escolha **definir como projeto de inicialização** .
7. Escolha o **dispositivo** preferencial ou **Simulator** / **emulador** de simulador.
8. Execute a solução ( **comando + retorno** ) para validar que o projeto **Xamarin. Forms** do modelo é compilado e executado corretamente.

    > [!NOTE]
    > o **Ios** (especificamente o simulador) tende a ter o tempo de compilação/implantação mais rápido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Adicionando o feed do NuGet local à configuração do NuGet

1. No **Visual Studio** , escolha **preferências** (no menu do **Visual Studio** ).
2. Escolha **fontes** na seção **NuGet** e clique em **Adicionar** .
3. Atualize os campos a seguir e clique em **Adicionar fonte** :

    - **Nome:** Forneça um nome significativo, por exemplo, pacotes locais.  
    - **Local:** Especifique a pasta **local NuGet-feed** criada na [etapa anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > Nesse caso, não é necessário especificar um nome de **usuário** e uma **senha** .

4. Clique em **OK** .

### <a name="referencing-the-package"></a>Referenciando o pacote

Repita as etapas a seguir para cada projeto ( **MathFuncsApp** , **MathFuncsApp. Android** e **MathFuncsApp. Ios** ).

1. **Controle + clique** no projeto e, em seguida, escolha **adicionar pacotes NuGet...** no menu **Adicionar** .
2. Procure **MathFuncs** .
3. Verifique se a **versão** do pacote é **1.0.0** e se os outros detalhes aparecem conforme o esperado, **como título** e **Descrição** , ou seja, *MathFuncs* e exemplo de biblioteca de *invólucro C++* .
4. Selecione o pacote **MathFuncs** e clique em **Adicionar pacote** .

### <a name="using-the-library-functions"></a>Usando as funções de biblioteca

Agora, com uma referência ao pacote **MathFuncs** em cada um dos projetos, as funções estão disponíveis para o código C#.

1. Abra **MainPage.XAML.cs** de dentro do projeto **MathFuncsApp** do **Xamarin. Forms** comum (referenciado por **MathFuncsApp. Android** e **MathFuncsApp. Ios** ).
2. Adicione instruções **using** para **System. Diagnostics** e **MathFuncs** na parte superior do arquivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare uma instância da `MyMathFuncs` classe na parte superior da `MainPage` classe:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Substitua os `OnAppearing` `OnDisappearing` métodos e da `ContentPage` classe base:

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

5. Atualize o `OnAppearing` método para inicializar a `myMathFuncs` variável declarada anteriormente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Atualize o `OnDisappearing` método para chamar o `Dispose` método em `myMathFuncs` :

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implemente um método privado chamado **TestMathFuncs** da seguinte maneira:

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

8. Por fim, chame `TestMathFuncs` no final do `OnAppearing` método:

    ```csharp
    TestMathFuncs();
    ```

9. Execute o aplicativo em cada plataforma de destino e valide a saída no painel de **saída do aplicativo** aparece da seguinte maneira:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se você encontrar um ' *DLLNotFoundException* ' ao testar no Android ou um erro de compilação no Ios, certifique-se de verificar se a arquitetura de CPU do dispositivo/emulador/simulador que você está usando é compatível com o subconjunto que escolhemos para dar suporte.

## <a name="summary"></a>Resumo

Este artigo explicou como criar um aplicativo Xamarin. Forms que usa bibliotecas nativas por meio de um wrapper comum do .NET distribuído por meio de um pacote NuGet. O exemplo fornecido neste passo a passos é intencionalmente muito simplista para demonstrar a abordagem com mais facilidade. Um aplicativo real terá que lidar com complexidades, como tratamento de exceção, retornos de chamada, Marshalling de tipos mais complexos e vinculação com outras bibliotecas de dependências. Uma consideração importante é o processo pelo qual a evolução do código C++ é coordenada e sincronizada com o wrapper e os aplicativos cliente. Esse processo pode variar dependendo se uma ou ambas as preocupações são de responsabilidade de uma única equipe. De qualquer forma, a automação é um benefício real. Abaixo estão alguns recursos que fornecem uma leitura adicional sobre alguns dos principais conceitos, juntamente com os downloads relevantes.

### <a name="downloads"></a>Downloads

- [Ferramentas de linha de comando (CLI) do NuGet](/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Exemplos

- [Enlapsar o desenvolvimento móvel de plataforma cruzada com C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft PIX (C++ e Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Porta de exemplo do mono San Angeles](/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Leitura Adicional

[Artigos relacionados ao conteúdo desta postagem](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)
