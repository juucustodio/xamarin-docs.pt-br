---
title: Como automatizar a um projeto de teste do NUnit Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Como automatizar a um projeto de teste do NUnit Android?

> [!NOTE]
> Este guia explica como automatizar um projeto de teste do NUnit Android, não em um projeto Xamarin.UITest. Xamarin.UITest guias podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando você cria um **aplicativo de teste de unidade (Android)** projeto no Visual Studio (ou **Android teste de unidade** projeto no Visual Studio para Mac), esse projeto serão automaticamente os testes são executados por padrão.
Para executar testes NUnit em um dispositivo de destino, você pode criar um [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) subclasse é iniciada usando o seguinte comando: 

```shell
adb shell am instrument 
```

As etapas a seguir explicam o processo:

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
    Nesse arquivo, [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (de **Xamarin.Android.NUnitLite.dll**) é uma subclasse para criar `TestInstrumentation`.

2.  Implementar o [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor e o [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) método. O `AddTests` controles do método que testes são realmente executados.

3.  Modificar o `.csproj` arquivo para adicionar **TestInstrumentation.cs**. Por exemplo:

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

3.  Use o seguinte comando para executar os testes de unidade. Substituir `PACKAGE_NAME` com o nome do pacote do aplicativo (o nome do pacote pode ser encontrado no aplicativo do `/manifest/@package` atributo localizado em **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  Opcionalmente, você pode modificar o `.csproj` arquivo a ser adicionado a `RunTests` destino do MSBuild. Isso torna possível invocar os testes de unidade com um comando semelhante ao seguinte:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Observe que não é necessário usar esse novo destino; o anterior `adb` comando pode ser usado em vez de `msbuild`.)

Para obter mais informações sobre como usar o `adb shell am instrument` comando para executar testes de unidade, consulte o desenvolvedor Android [executando testes com ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) tópico.


> [!NOTE]
> Com o [xamarin 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) versão, os nomes de pacote padrão para o Android Callable Wrappers será baseado no MD5SUM do nome do assembly qualificado do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado para ser fornecido de dois assemblies diferentes e não receber um erro de empacotamento. Assim, certifique-se de que você use o `Name` propriedade no `Instrumentation` atributo para gerar um nome de classe/ACW legível.

_O nome ACW deve ser usado no `adb` comando acima_.
Renomear/refatoração classe c#, portanto, exigirá modificando o `RunTests` comando para usar o nome correto do ACW.

