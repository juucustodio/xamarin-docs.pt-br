---
title: Como automatizar um projeto de Teste NUnit do Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: b785ef171d2cb00d4f8f5a17f37d49de17fd3da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153276"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Como automatizar um projeto de Teste NUnit do Android?

> [!NOTE]
> Este guia explica como automatizar um projeto de teste do NUnit do Android, não em um projeto xamarin. uitest. Xamarin. uitest guias podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando você cria um **aplicativo de teste de unidade (Android)** projeto no Visual Studio (ou **teste de unidade Android** projeto no Visual Studio para Mac), este será de projeto não automaticamente executar os testes por padrão.
Para executar testes NUnit em um dispositivo de destino, você pode criar uma [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) subclasse é iniciada usando o comando a seguir: 

```shell
adb shell am instrument 
```

As etapas a seguir explicam a esse processo:

1.  Criar um novo arquivo chamado **TestInstrumentation.cs**: 

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
    Nesse arquivo, [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (da **Xamarin.Android.NUnitLite.dll**) é uma subclasse para criar `TestInstrumentation`.

2.  Implemente a [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor e o [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) método. O `AddTests` controles de método, na verdade, os quais testes são executados.

3.  Modificar a `.csproj` arquivo a ser adicionado **TestInstrumentation.cs**. Por exemplo:

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

3.  Use o seguinte comando para executar os testes de unidade. Substitua `PACKAGE_NAME` com o nome do pacote do aplicativo (o nome do pacote pode ser encontrado no aplicativo do `/manifest/@package` atributo localizado na **androidmanifest. XML**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  Opcionalmente, você pode modificar os `.csproj` arquivo para adicionar o `RunTests` destino do MSBuild. Isso torna possível invocar os testes de unidade com um comando semelhante ao seguinte:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Observe que não é necessário usar esse novo destino; o anterior `adb` comando pode ser usado em vez de `msbuild`.)

Para obter mais informações sobre como usar o `adb shell am instrument` comando para executar testes de unidade, consulte o desenvolvedor do Android [executando testes com o ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) tópico.


> [!NOTE]
> Com o [xamarin. Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release, os nomes de pacote padrão para Callable Wrappers do Android se baseará o MD5SUM do nome qualificado pelo assembly do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado para ser fornecidos por meio de dois assemblies diferentes e não receber um erro de empacotamento. Portanto, certifique-se de que você use o `Name` propriedade no `Instrumentation` atributo para gerar um nome de classe/ACW legível.

_O nome ACW deve ser usado na `adb` comando acima_.
Refatoração/renomeação o C# classe, portanto, será necessário modificar o `RunTests` comando para usar o nome correto do ACW.

