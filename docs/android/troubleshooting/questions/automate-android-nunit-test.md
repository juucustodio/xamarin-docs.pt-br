---
title: Como automatizar a um projeto de teste do NUnit Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: a5f98fc351c879be55475808b5ab412449dadc7d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Como automatizar a um projeto de teste do NUnit Android?

> [!NOTE]
> **Observação:** este guia aborda as etapas para configurar um projeto de teste do NUnit Android, não em um projeto Xamarin.UITest. Xamarin.UITest guias podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando você cria um projeto de teste de unidade Android [Visual Studio para Mac] ou o aplicativo de teste de unidade (Android) [Visual Studio], por padrão ele não executará automaticamente seus testes.
Para automatizar o teste de unidade android: para executar testes NUnit em um dispositivo de destino, usamos um `Android.App.Instrumentation` subclasse, que pode ser criado e executado usando o `adb shell am instrument` comando.

Primeiro, criamos o **TestInstrumentation.cs** arquivo, que cria uma subclasse de `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (declarado em `Xamarin.Android.NUnitLite.dll`). O `TestInstrumentation(IntPtr, JniHandleOwnership)` construtor _deve_ ser fornecida e o virtual `AddTests()` método deve ser substituído.
`AddTests()` Controla quais testes é realmente executada. Esse arquivo é amplamente boilerplate.

Em seguida, o `.csproj` devem ser modificados para adicionar `TestInstrumentation.cs`.

Opcionalmente, o `.csproj` pode ser modificado para adicionar o `RunTests` destino do MSBuild, que permite que invocar os testes de unidade como:

```shell
msbuild /t:RunTests Project.csproj
```

Usar um novo destino não é necessário; o comando adb correspondente pode ser usado em vez disso:

```shell
adb shell am instrument -w @PACKAGE_NAME@/app.tests.TestInstrumentation
```

Substituir `@PACKAGE\_NAME@` como apropriado; ele é o valor presente no **AndroidManifest.xml** `/manifest/@package` atributo.

*Observação importante*: com o [xamarin 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) versão, os nomes de pacote padrão para o Android Callable Wrappers será baseado no MD5SUM do nome do assembly qualificado do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado para ser fornecido de dois assemblies diferentes e não receber um erro de empacotamento. Assim, certifique-se de que você use o \`nome\` propriedade no \`instrumentação\` atributo para gerar um nome de classe/ACW legível.

_O nome ACW deve ser usado no `adb` comando_. Renomear/refatoração classe c#, portanto, exigirá modificando o `RunTests` comando para usar o nome correto do ACW.

Adições para o arquivo. csproj:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Include="TestInstrumentation.cs" />
    </ItemGroup>
    <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
        <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
    </Target>
</Project>
```

**TestInstruments.cs**:

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

