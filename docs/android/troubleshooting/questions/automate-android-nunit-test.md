---
title: Como automatizar um projeto de Teste NUnit do Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: ae33472fb32b3cf1a7bfe3c967c4279f38d415cd
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928523"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Como automatizar um projeto de Teste NUnit do Android?

> [!NOTE]
> Este guia explica como automatizar um projeto de teste do Android NUnit, não um projeto Xamarin. UITest. Os guias do Xamarin. UITest podem ser encontrados [aqui](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Quando você cria um projeto de **aplicativo de teste de unidade (Android)** no Visual Studio (ou no projeto de **teste de unidade do Android** no Visual Studio para Mac), este projeto não executa automaticamente os testes por padrão.
Para executar testes NUnit em um dispositivo de destino, você pode criar uma subclasse de [Android. app. Instrumentation](xref:Android.App.Instrumentation) que é iniciada usando o seguinte comando:

```shell
adb shell am instrument
```

As etapas a seguir explicam esse processo:

1. Crie um novo arquivo chamado **TestInstrumentation.cs** :

    ```cs
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;

    namespace App.Tests {

        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {

            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }

            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```

    Neste arquivo, `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (de **Xamarin.Android.NUnitLite.dll** ) é subclasse para criar `TestInstrumentation` .

2. Implemente o `TestInstrumentation` Construtor e o `AddTests` método. O `AddTests` método controla quais testes são realmente executados.

3. Modifique o `.csproj` arquivo para adicionar **TestInstrumentation.cs** . Por exemplo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

4. Implante seu aplicativo no modo de depuração ou versão e, em seguida, interrompa-o.

5. Use o comando a seguir para executar os testes de unidade. Substitua `PACKAGE_NAME` pelo nome do pacote do aplicativo (o nome do pacote pode ser encontrado no atributo do aplicativo `/manifest/@package` localizado em **AndroidManifest.xml** ):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

6. Opcionalmente, você pode modificar o `.csproj` arquivo para adicionar o `RunTests` destino do MSBuild. Isso possibilita invocar os testes de unidade com um comando semelhante ao seguinte:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Observe que o uso desse novo destino não é necessário; o `adb` comando anterior pode ser usado em vez de `msbuild` ).

Para obter mais informações sobre como usar o `adb shell am instrument` comando para executar testes de unidade, consulte o tópico Android Developer [running tests with ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) .

> [!NOTE]
> Com a versão [Xamarin. Android 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) , os nomes de pacote padrão para wrappers que podem ser chamados pelo Android serão baseados no md5sum do nome qualificado pelo assembly do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado seja fornecido de dois assemblies diferentes e não obtenha um erro de empacotamento. Portanto, certifique-se de usar a `Name` propriedade no `Instrumentation` atributo para gerar um nome legível de ACW/classe.

_O nome do ACW deve ser usado no `adb` comando acima_ .
A renomeação/refatoração da classe C# exigirá, portanto, a modificação do `RunTests` comando para usar o nome de ACW correto.
