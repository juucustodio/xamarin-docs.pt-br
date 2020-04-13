---
title: Como automatizar um projeto de Teste NUnit do Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: 1246eeac63a0ae232396d4c2fd69d8bf516f5e3e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026998"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Como automatizar um projeto de Teste NUnit do Android?

> [!NOTE]
> Este guia explica como automatizar um projeto de teste Android NUnit, não um projeto Xamarin.UITest. Os guias xamarin.uITest podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Quando você cria um projeto **de aplicativo de teste unitário (Android)** no Visual Studio (ou projeto de teste **unitário do Android** no Visual Studio para Mac), este projeto não executará automaticamente seus testes por padrão.
Para executar testes NUnit em um dispositivo de destino, você pode criar uma subclasse [Android.App.Instrumentation](xref:Android.App.Instrumentation) que é iniciada usando o seguinte comando: 

```shell
adb shell am instrument 
```

As seguintes etapas explicam este processo:

1. Crie um novo arquivo chamado **TestInstrumentation.cs:** 

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

    Neste arquivo, `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (de **Xamarin.Android.NUnitLite.dll**) é `TestInstrumentation`subclassificada para criar .

2. Implemente `TestInstrumentation` o construtor `AddTests` e o método. O `AddTests` método controla quais testes são realmente executados.

3. Modifique `.csproj` o arquivo para adicionar **TestInstrumentation.cs**. Por exemplo:

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

4. Use o seguinte comando para executar os testes da unidade. Substitua pelo `PACKAGE_NAME` nome do pacote do aplicativo (o nome `/manifest/@package` do pacote pode ser encontrado no atributo do aplicativo localizado no **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Opcionalmente, você pode `.csproj` modificar o `RunTests` arquivo para adicionar o destino MSBuild. Isso torna possível invocar os testes unitários com um comando como o seguinte:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Observe que o uso deste novo `adb` destino não é `msbuild`necessário; o comando anterior pode ser usado em vez de .)

Para obter mais `adb shell am instrument` informações sobre como usar o comando para executar testes unitários, consulte o Desenvolvedor Android executando testes com o tópico [ADB.](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)

> [!NOTE]
> Com a versão [Xamarin.Android 5.0,](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) os nomes de pacote padrão para Android Callable Wrappers serão baseados no MD5SUM do nome qualificado para montagem do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado seja fornecido a partir de dois conjuntos diferentes e não obtenha um erro de embalagem. Portanto, certifique-se de `Name` usar `Instrumentation` a propriedade no atributo para gerar um acw/nome de classe legível.

_O nome ACW deve `adb` ser usado no comando acima_.
Renomear/refatorar a classe C#exigirá, `RunTests` assim, modificar o comando para usar o nome ACW correto.
